# Cycle OSPuter 🚴‍♂️🌬️

> An iOS cycling computer that shows headwind, tailwind, and crosswind relative
> to your direction of travel — the feature missing from every other bike computer.

## The idea

Every cycling computer shows speed, distance, and maybe a map. None of them tell
you the one thing that defines how an outdoor ride actually *feels*: where the wind
is hitting you. **Cycle OSPuter** displays the wind oriented to your direction of
travel, so at a glance you know whether you're fighting a headwind, riding a
tailwind, or getting shoved by a crosswind.

The approach borrows from aviation: relative wind is the difference between the
wind's direction and your **course over ground** — the direction you're actually
moving, from GPS — not simply where the device happens to be pointing.

## Status

🚧 **In active development.** Currently renders a heading value on screen.
Live location and wind data are next.

## Roadmap

- [x] SwiftUI project scaffold
- [ ] Live heading / course over ground (Core Location)
- [ ] Current speed and distance
- [ ] Wind direction + speed (weather API)
- [ ] Relative wind dial (head / tail / crosswind)
- [ ] On-bike display layout

## Built with

- **Swift** + **SwiftUI** — declarative UI
- **Core Location** — GPS heading, course, and speed
- Weather API (TBD) — wind direction and speed

## About

Built by Carlos F. Meneses while transitioning into Apple software development.

- GitHub: [@CarlosFMeneses](https://github.com/CarlosFMeneses)
- LinkedIn: [carlosfmeneses](https://www.linkedin.com/in/carlosfmeneses)
