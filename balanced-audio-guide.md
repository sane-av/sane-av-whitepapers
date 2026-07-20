---
title: "Balanced Audio: What It Actually Does"
description: "A practical guide to balanced connections — why they exist, how they work, and the wiring mistakes that undo them."
pubDate: 2026-07-20
authors: ["SANE Community"]
tags: ["audio", "wiring", "grounding", "signal-integrity"]
---

Every AV tech knows balanced audio is "better" than unbalanced. Fewer can explain why. And honestly, a lot of what gets repeated on job sites is half right at best.

This piece is a ground-up explanation of what balanced audio actually does, where it matters, and the wiring mistakes that quietly defeat it. No math you won't use. No idealized textbook circuits. Just what you need to know when you're holding XLR in one hand and a hum-buster in the other.

## The problem balanced audio solves

Every cable is an antenna.

Run two pieces of equipment across a room and the cable between them will pick up electromagnetic noise — from power lines, lighting dimmers, transformers, motors, radio transmitters. The longer the run, the worse it gets.

In an unbalanced connection, the signal travels on a single conductor referenced to ground. The shield carries both the ground reference _and_ acts as a noise drain. Any noise induced on the signal conductor appears across the input, amplified right along with the audio.

In a balanced connection, the signal travels on two conductors of equal impedance, typically twisted together. The receiving end looks at the _difference_ between them. Noise that hits both conductors equally — and it will, because they're twisted together in the same physical space — cancels out.

That's it. That's the whole trick. Everything else is implementation details.

## How it actually works: common-mode rejection

The key concept is **common-mode rejection**. Any signal that appears identically on both conductors (in phase, same amplitude) is rejected by the differential input. Any signal that appears differentially (opposite polarity on the two conductors) is passed through.

A balanced output puts the signal on the two conductors 180 degrees out of phase. A differential input subtracts one from the other. The result: the intended signal sums constructively (doubles), and any common-mode noise sums destructively (cancels).

The measure of how well this works is called **CMRR** — Common-Mode Rejection Ratio, expressed in dB. A typical balanced input has a CMRR of 40-80 dB. That means common-mode noise is attenuated by a factor of 100 to 10,000.

CMRR degrades at higher frequencies. That spec sheet number? It's usually measured at 60 Hz. At 10 kHz, it may be 20 dB worse. This matters if your noise source is a switch-mode power supply or a dimmer running at 40 kHz.

## Three ways to get a balanced output

Not all "balanced" outputs are created equal. There are three common topologies, and they behave differently in the real world.

### 1. Transformer balanced

The classic method. A transformer with a center-tapped secondary provides a truly floating output — neither leg is referenced to ground. This is bulletproof. It works into balanced and unbalanced inputs without adapters. It breaks ground loops at DC. It's also expensive, heavy, and has limited bandwidth on the low end.

Still the gold standard for stage gear and anything that might see phantom power from the wrong direction.

### 2. Active differential (electronically balanced)

Two op-amp outputs driven 180 degrees apart. No transformer. Low cost, good performance, the standard in most modern gear. Both legs are typically referenced to ground through low-value resistors, so the output isn't truly floating.

This is what you'll find in 90% of modern consoles, interfaces, and DSPs. It works great — as long as both legs are present and the input is differential.

### 3. Impedance balanced (pseudo-balanced)

This is the sneaky one. Only one conductor carries the signal. The other is tied to ground through a resistor matching the output impedance of the active leg. It looks balanced to the receiving end (equal impedance on both lines), so common-mode rejection still works. But there's no differential signal — just a hot conductor and a ground-referenced cold.

Common on budget mixers and some consumer gear with XLR jacks. Works perfectly for noise rejection, but loses 6 dB of signal compared to differential because only one leg is driven.

**Know which one you have.** A lot of "balanced" outputs aren't differential, and a lot of troubleshooting heartache comes from assuming they are.

## The wiring mistakes that undo everything

### Telescoping grounds: why you lift one end

The simplest way to prevent ground loop current from flowing through a shield is to not give it a complete circuit. If you connect the shield at only one end, no current can flow through it — Ohm's Law takes the day off.

This practice is called **telescoping the ground**. It's standard practice in permanent installations, and it's been used in telecom and broadcast for decades. The name comes from the idea of a telescoping antenna — you extend the shield in one direction only.

The convention: ground the shield at the **source end** (the output), lift it at the **receive end** (the input). The source chassis provides the shield's reference, and the balanced signal travels on pins 2 and 3. The receiving differential input doesn't need the shield to function — it's looking at the difference between the two signal conductors. The shield is purely for noise management.

But there's a catch. A shield connected at only one end is an antenna at the other end — specifically, an antenna for radio frequencies. At RF frequencies, the wavelength is short enough that the floating end of the shield can pick up AM radio, cell signals, or the hash from nearby switch-mode supplies. The fix: a small capacitor (0.01 to 0.1 uF) from the lifted end of the shield to chassis ground. The capacitor passes RF to ground — it looks like a short at megahertz frequencies — while blocking power-line frequencies (50/60 Hz), so the ground loop is still broken at audio frequencies.

This capacitor belongs at the connector: pin 1 → capacitor → chassis, directly on the input panel. Don't hide it on the PCB three inches away. At RF, three inches is a transmission line.

**Important:** telescoping only works when the source end has a solid shield-to-chassis connection. If the source device has a pin-1 problem, telescoping the receive end won't help — the noise is already in the signal. More on that in a moment.

### The pin-1 problem: when the shield poisons the signal

This is the single most common cause of "balanced audio that still hums," and it's a design defect — not something you fix with cable tricks.

The name comes from Neil Muncy's landmark 1994 AES paper, "Noise Susceptibility in Analog and Digital Signal Processing Systems." Muncy documented a pattern he'd observed across dozens of audio products: the XLR pin 1 — the shield — was being routed wrong on the PCB. Instead of connecting directly to the chassis at the connector, manufacturers were running it to the audio circuit ground plane. That single design decision created a noise injection mechanism that compromised the entire balanced interface.

Here's what happens step by step:

1. **Shield current exists.** Every real-world installation has some potential difference between equipment chassis. Safety ground wiring, building steel, power distribution — all of it creates small voltage differences between the grounds of different pieces of gear. When you connect a shield between them, that voltage difference drives current through the shield.

2. **Pin 1 picks it up.** The shield current arrives at the XLR connector on the receiving device. If pin 1 connects directly to the chassis, the current flows harmlessly to safety ground through a fat, low-impedance path. End of story.

3. **But if pin 1 goes to the PCB...** The shield current now flows through a thin copper trace on a circuit board. That trace has impedance — maybe only a fraction of an ohm, but when you're dealing with microvolt-level audio signals, it matters. Ohm's Law: current × impedance = voltage drop. That tiny voltage appears in series with the audio ground reference.

4. **The common-mode rejection is defeated.** The differential input that was supposed to subtract common-mode noise now sees a noise voltage that *isn't* common-mode — it appears only on the ground reference side. CMRR doesn't apply because the noise wasn't induced equally on both conductors. It was injected into the ground reference itself. Game over.

The fix is straightforward in principle and infuriatingly rare in practice: **connect pin 1 to the chassis directly at the connector.** No PCB trace. No shared ground plane. No daisy-chaining through a ribbon cable. A short, direct connection — ideally a dedicated wire or a metal standoff — from the XLR shell to the chassis metal, within millimeters of the connector.

This design rule is codified in **AES48** (AES standard on interconnections — grounding and EMC practices). It's been a published standard since 1995. More than 25 years later, new gear still ships with pin-1 problems. Why? Because proper pin-1 routing costs money. It means dedicated chassis connections, careful PCB layout, and a design team that understands why it matters. On a tight BOM, the pin-1 trace gets treated like just another signal connection.

**How to spot it in the field:** you have a balanced connection that hums. You lift the shield at one end. Nothing changes. You swap cables. Nothing changes. You plug the same source into a different input on the same device and... the hum goes away on that channel but not the original. That's a pin-1 problem — the defect is in that specific input connector, and no amount of cable swapping will fix it.

**How to fix it when you find it:** you can't, externally. Not really. You can mask it with a 1:1 isolation transformer, which breaks the galvanic connection entirely. You can use a balancing adapter that regenerates the signal locally. But the underlying problem is inside the chassis. If it's your gear, rework the connector panel. If it's a client's gear, flag it and work around it.

### How telescoping and pin-1 interact

These two concepts are deeply related, and misunderstanding their relationship causes a lot of wasted troubleshooting time.

**Telescoping breaks the ground loop in the cable.** If you have two pieces of properly designed (AES48-compliant) gear and you run a shield between them connected at both ends, shield current flows. The current itself doesn't cause a problem because both ends have solid pin-1-to-chassis connections — the current flows harmlessly through the chassis metal. But in practice, "harmlessly" is relative: any current near audio circuits is a risk. Telescoping eliminates the current path entirely. This is the ideal: **pin-1-compliant gear + telescoped shields = bulletproof.**

**Telescoping can't fix a pin-1 problem.** If the source device routes pin 1 through its audio ground, the noise injection happens *before* the signal ever reaches the cable. The shield current flows through the PCB ground plane inside the source, creating a noise voltage that gets superimposed on the audio signal at the output driver. By the time the signal arrives at the receiving end, the hum is already part of the differential signal — common-mode rejection can't remove it because it's no longer common-mode. Lifting the shield at the receive end does nothing because the damage was done at the source.

**Telescoping can make a pin-1 problem at the receive end worse.** If the receiving device has pin 1 connected to its audio ground and you lift the shield at the receive end, you've removed the low-impedance path to chassis ground. Now the shield — which is still connected at the source end and still acts as an antenna — injects RF into the floating pin 1 pin, which dumps it into the audio ground through whatever PCB trace connects it. The capacitor trick helps with RF but doesn't fix the underlying topology problem.

**The practical rule:** if telescoping the shield at the receive end doesn't fix the hum, you have a pin-1 problem somewhere in the chain. Try isolation transformers at each device to identify which one is the culprit. Once you find it, you can decide whether to work around it (transformer, balancing adapter) or replace the gear.

### Unbalanced insert points in balanced chains

You can have a completely balanced signal path from the console to the DSP, and then someone patches in an unbalanced compressor on an insert point. Now you have a single-ended section in the middle of your chain.

Effect: the noise rejection of everything downstream is compromised. Worse, if the insert cable is a standard TRS (tip-send, ring-return, sleeve-ground), connecting an unbalanced device shorts the ring to ground, which may or may not cause problems depending on the output stage design.

Use a transformer isolator or a balancing adapter at insert points. Or just keep unbalanced gear out of permanent install signal chains.

### Phantom power and unbalanced gear

Phantom power (+48V DC on pins 2 and 3 referenced to pin 1) is designed for balanced condenser mics. The voltage appears equally on both signal conductors, so a differential input rejects it as common-mode. No problem.

Plug an unbalanced dynamic mic or a consumer device into a phantom-powered input using an XLR-to-TS adapter and you're sending 48V into circuitry that wasn't designed for it. At best it doesn't work. At worst, smoke.

## When do you actually need balanced?

Balanced audio isn't always necessary. Here's a practical guide:

| Situation | Recommendation |
|---|---|
| Mic-level runs over 10 ft | Balanced required |
| Line-level runs under 15 ft in a rack | Unbalanced usually fine |
| Line-level runs across a room (25+ ft) | Balanced recommended |
| Runs near lighting dimmers, motors, or power cables | Balanced strongly recommended |
| Consumer gear with RCA only | Use short cables, keep away from power |
| Digital audio (AES3, S/PDIF) | Different animal — uses 110 ohm cable, balanced AES, but not for the same reasons |

The rule of thumb: if it leaves the rack, balance it. Inside a rack with short patch cables between quality gear, unbalanced is fine. The moment you're crossing power conduits or running parallel to lighting circuits, you want that CMRR working for you.

## Quick diagnosis guide

| Symptom | Likely cause |
|---|---|
| 60 Hz hum, changes when you touch connector shell | Ground loop — telescope shield at receive end |
| Hum unchanged after telescoping shield, different per input channel | Pin-1 problem in the device — check with isolation transformer |
| Buzz that gets worse near dimmers, goes away near dimmers | RF on floating shield — add capacitor bypass at lifted end (0.01-0.1 uF to chassis) |
| Hum with nothing plugged in | Pin-1 problem in the input device — shield current circulating internally |
| One channel weak or distorted | Cold leg open — check solder joints and cable continuity |
| Intermittent crackle when wiggling connector | Shield touching signal pin inside the connector — re-terminate |
| Everything clean solo, hums when connected to another piece of gear | Ground potential difference — telescope shield, or 1:1 isolation transformer |
| Hum persists after telescoping and transformer | Both devices have pin-1 problems — isolate both ends, or rework connector panels |

## The bottom line

Balanced audio isn't magic. It's two conductors and a differential receiver. The physics is solid and well-understood. What trips people up is the edge cases — impedance-balanced outputs, pin-1 design flaws, ground loop currents, and unbalanced gear inserted into balanced chains.

The single most important rule: **connect pin 1 to chassis, not to audio ground.** If every piece of gear followed AES48, half the hum problems in the industry would vanish overnight. The other half would be solvable with a telescoped shield and a capacitor.

Know which kind of balanced output you're dealing with. Telescope your shields at the receive end, with a capacitor to chassis for RF. When telescoping doesn't fix the hum, suspect a pin-1 problem and reach for an isolation transformer. When that doesn't fix it either, you're probably dealing with a pin-1 problem at both ends — a bad day, but at least now you know why.
