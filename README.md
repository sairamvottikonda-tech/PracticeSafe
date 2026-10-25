# Practice Call

A snapshot AQI reading doesn't tell a coach much. A two-hour practice
during which air quality is climbing, with athletes breathing 5–10× their
resting rate, is a different exposure entirely.

Practice Call pulls the hourly PM2.5 forecast for a school's coordinates,
integrates concentration against ventilation rate across the exact practice
window, and returns a verdict plus a cleaner time slot if one exists in
the next 24 hours.

**Live:** [link once Pages is up]
**Built for:** Arcadia High School athletics
**Thresholds:** [CIF Southern Section]

## How it works
- Air quality data: Open-Meteo Air Quality API (CC BY 4.0), no key required
- Hourly forecast values are linearly interpolated to five-minute resolution
- Inhaled dose = Σ (PM2.5 × ventilation rate × time), summed across the window
- A 24-hour scan surfaces alternate windows that cut exposure by 20% or more

## Team
Sairam Vottikonda, Ronit Rao: Arcadia High School — Congressional App Challenge 2026
