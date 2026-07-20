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

### Lifting the shield at one end

This is standard practice — connect the shield at the send end, lift at the receive end — but it's also misunderstood.

The shield should only be connected at one end to prevent ground loops. If both ends are grounded, any voltage difference between the two equipment grounds flows through the shield as current, which can induce hum into the signal conductors. But lifting the shield doesn't break the balanced connection — common-mode rejection still works without it.

When you do lift the shield, use a capacitor (0.01-0.1 uF) to ground on the lifted end. This passes RF to ground while blocking power-line frequencies. Without it, you've created a radio receiver.

### The pin-1 problem

This is the most common source of "balanced audio that still hums."

Pin 1 on an XLR is the shield. It should connect directly to the chassis — not to the audio circuit ground, not through a PCB trace, not through a shared return path. Direct to chassis, right at the connector.

When pin 1 connects to the audio ground instead, any shield current (from a ground loop or RF interference) flows through the audio circuit before reaching ground. This injects noise directly into the signal path. The fix is internal to the equipment — it's a design flaw, not a wiring error. But knowing about it helps you identify the problem when swapping cables doesn't fix the buzz.

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
| 60 Hz hum that changes when you touch the connector shell | Ground loop — lift shield at one end |
| Buzz that gets worse near dimmers | RF interference — check shield continuity, add capacitor bypass at lifted end |
| Hum with nothing plugged in | Pin-1 problem in the input device |
| One channel weak or distorted | Cold leg open — check solder joints and cable continuity |
| Intermittent crackle when wiggling connector | Shield touching signal pin inside the connector — re-terminate |
| Everything clean solo, hums when connected to another piece of gear | Ground potential difference — try a 1:1 isolation transformer |

## The bottom line

Balanced audio isn't magic. It's two conductors and a differential receiver. The physics is solid and well-understood. What trips people up is the edge cases — impedance-balanced outputs, pin-1 design flaws, ground loop currents, and unbalanced gear inserted into balanced chains.

Know which kind of balanced output you're dealing with. Keep the shield right. Don't mix unbalanced into a balanced path without thinking. And when you're not sure, put an isolation transformer in line. It's old-school, but it fixes more problems than it creates.
