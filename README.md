# PracticeSafe

**Live app:** https://sairamvottikonda-tech.github.io/practice-call/

**Demo:** https://sairamvottikonda-tech.github.io/practice-call/demo.html

A coach picks a start time, a length, and a sport. PracticeSafe looks at the forecast air quality for that exact window and answers one question: practice, modify, move indoors, or cancel?

Most air quality tools show one number for right now. But a coach is deciding about a two-hour block that starts later, for athletes who breathe many times harder than someone standing still. PracticeSafe works on the whole window and also suggests a cleaner time if one exists.

Built for Arcadia High School athletics. It also works anywhere via **Use my location**.

## What the coach sees

- **A call** (Practice OK, Modify, Move indoors, or Cancel) with a plain-language reason.
- **Why this call**: the peak AQI in the window, the dose check, and how a live ground sensor compares.
- **An exposure chart** across the practice window.
- **Better times in the next 24 hours**, ranked by how much less PM2.5 athletes would inhale.

## How a call is made

1. **Peak AQI in the window** sets the base call, using the EPA categories:

   | AQI | Call |
   |---|---|
   | 0-100 | Practice |
   | 101-150 | Modify |
   | 151-200 | Move indoors |
   | 201+ | Cancel |

   CIF's air quality guidance says that above AQI 150 schools should seriously consider rescheduling or moving indoors and avoid prolonged exposure and heavy exertion ([cifstate.org/sports-medicine/aqi](https://cifstate.org/sports-medicine/aqi)). The Move indoors line follows that. The Modify band at 101-150 and the Cancel line at 201 are our own choices, because the guidance doesn't set them.

2. **A dose check** can lower a Practice call to Modify. See below.

3. **A ground-sensor cross-check** is shown next to the call. It does not change the call.

### The dose check

AQI is the same for a walkthrough and for sprint conditioning, but the athletes' lungs are not. For each practice we integrate PM2.5 concentration against breathing rate in 5-minute steps:

```
dose = sum( PM2.5 at that minute x breathing rate x 5 minutes )
```

Hourly forecasts are linearly interpolated to minute resolution first. We then ask: *what air quality would a standard practice need to deliver this same dose?* A standard practice is 120 minutes at moderate effort. If that equivalent air is above 35 µg/m³ (the top of the EPA "Moderate" range) while the AQI is above 50, a Practice call becomes Modify.

In practice, a 3-hour sprint-conditioning session at AQI 80 gets flagged, while a 60-minute walkthrough at AQI 90 does not.

**This rule is our design, not a published standard.** The 35 µg/m³ limit comes from EPA's AQI breakpoints, but using a standard practice as the yardstick is our own heuristic and has not been clinically validated. The sport breathing rates are general exercise-physiology estimates, not measurements of any athlete.

### Better times

The app tries start times every 30 minutes over the next 24 hours, computes the dose for each, and lists up to three that are at least 15% lower, spaced at least 90 minutes apart.

## Data sources

| Source | Used for | Notes |
|---|---|---|
| [Open-Meteo Air Quality](https://open-meteo.com/en/docs/air-quality-api) | Hourly PM2.5 and AQI forecast | A model, not a sensor. Coarse grid (about 11 km). No key needed. CC BY 4.0. |
| [WAQI](https://waqi.info) | Nearest real ground station | Live reading, shown with its distance. |
| [OpenStreetMap Nominatim](https://nominatim.org) | Turning GPS into a place name | Only used after you tap Use my location. |

Times are always shown in the local time of the location being checked, not the viewer's device.

## Testing

- **All four calls.** We ran AQI values from 40 to 350 through the band logic and confirmed each one lands in the right call.
- **The dose rule.** Synthetic scenarios (different lengths, sports, and air quality) confirm that session length and sport change the result and that a standard practice right at the limit is not flagged.
- **Timezone bug.** Practice times were originally built in the viewer's device timezone, so someone outside California would have seen shifted times. Found by reasoning about a remote viewer, fixed by using the checked location's timezone from the API.
- **A real smoke day.** We replayed May 19, 2026, when Los Angeles County issued an unhealthy-air advisory for the Sandy Fire and the Santa Rosa Island fire ([county notice](https://lacounty.gov/wp-content/uploads/2026/05/For-Immediate-Release.pdf)). At the school's coordinates the model's AQI peaked at 95 and every start time that day returned Practice. The county notices focus on western LA County and the coast and do not name Arcadia, so we can't say the model was wrong there. We also couldn't get historical readings from a nearby ground sensor, so this replay shows how the app behaves on that data, not that the data was right.
- **One day of model vs. sensor.** On August 7, 2026, the model estimated AQI 72-78 and the Glendora-Laurel station (10.6 miles away) read 75. That is one ordinary day, not evidence about smoke events.

## Limitations

- **The model can miss local smoke.** Modeled air quality is smooth, and smoke plumes are patchy. When the ground sensor reads higher than the call suggests, trust the higher number. A future version could use a sensor on school grounds.
- **The ground sensor can be far away.** The distance is shown every time.
- **Thresholds are not yet Arcadia Unified's official policy.** They come from public EPA and CIF guidance. A district policy would replace them.
- **Only PM2.5.** Heat, ozone, and humidity are not considered.
- **Outside California,** the CIF wording doesn't apply, though the EPA categories do.
- **Decision support, not medical advice.** Coaches should follow district policy and their athletic trainer.

## Privacy

There are no accounts and no database. The app stores nothing. If you tap Use my location, your coordinates are sent to Open-Meteo, WAQI, and OpenStreetMap to get air quality and a place name. We deliberately did not build athlete health profiles: tracking which students have asthma is sensitive medical information, and this app has no login or encryption to protect it.

## Run it

It is one file with no build step. Open `index.html` in a browser, or use the live link. (The location button needs the https link; browsers block it on local files.)

## What we'd build next

- Use Arcadia Unified's official air quality policy once confirmed
- A ground sensor on school grounds as the primary reading during smoke
- Wind direction near active fires
- A season log of decisions, behind proper security

## Team

Sairam Vottikonda, Ronit Rao, Siona Rao - Arcadia High School - Congressional App Challenge 2026

Air quality data: Open-Meteo (CC BY 4.0) and waqi.info. Map data: &copy; OpenStreetMap contributors.
