# Flight Disruption Recovery: Mixed-Integer Optimisation

When airport capacity drops, which flights should an airline keep so that the least revenue is lost? This project models that decision as a mixed-integer program, solved with **AMPL + HiGHS**, and measures how the answer changes as capacity shrinks.

## Problem

- Each aircraft follows a route through a network: source → its flights → sink.
- Binary variables decide whether each flight is operated and which route arcs are used.
- Arrival and departure capacity at each airport is limited to a fraction **α** of its scheduled flights.
- **Objective:** minimise lost revenue (the revenue of cancelled flights).

## Data

Flight rotations, aircraft start/end positions and passenger itineraries come from the public Gurobi [`modeling-examples`](https://github.com/Gurobi/modeling-examples/tree/master/aviation_planning) aviation planning dataset. The notebook loads it directly from GitHub, so no data files are needed.

Reduced scope used here:
- 4 busiest airports (ORY, CDG, LYS, NCE)
- 77 flights with revenue data
- 21 aircraft

## Results: sensitivity to capacity (α)

| α | Lost revenue | Flights operated | Cancelled | Passengers | Aircraft used |
|---|---|---|---|---|---|
| 0.0 | 1,878,476 | 0 | 77 | 0 | 0 |
| 0.1 | 1,615,676 | 6 | 71 | 1,309 | 4 |
| 0.2 | 1,369,613.5 | 13 | 64 | 2,546 | 7 |
| 0.3 | 1,133,163.5 | 21 | 56 | 3,744 | 8 |
| 0.4 | 936,111.5 | 29 | 48 | 4,726 | 9 |
| 0.5 | 793,420.5 | 37 | 40 | 5,352 | 10 |
| 0.6 | 689,401.5 | 44 | 33 | 6,019 | 10 |
| 0.7 | 592,524.5 | 50 | 27 | 6,474 | 10 |
| 0.8 | 522,412 | 55 | 22 | 6,812 | 11 |
| 0.9 | 500,574.5 | 57 | 20 | 6,933 | 11 |
| 1.0 | 500,574.5 | 57 | 20 | 6,933 | 11 |

Full table: `alpha_sensitivity_results.csv`.

## Charts

![Lost revenue vs alpha](Lost%20Revenue%20vs%20Alpha.png)

![Operated flights vs alpha](Operated%20Flights%20vs%20Alpha.png)

![Cancelled flights vs alpha](Cancelled%20Flights%20vs%20Alpha.png)

![Passengers transported vs alpha](Passengers%20Transported%20vs%20Alpha.png)

**Observations**
- Lost revenue falls steadily as capacity rises, then flattens from α = 0.9.
- At α = 1.0, 20 flights are still cancelled. Airport capacity no longer limits the plan at that point, so the cause is likely the routing structure (fixed aircraft assignments and start/end positions).

## Limitations

- Small instance (4 airports, 77 flights).
- Each aircraft can only fly the flights it was already assigned. There is no reassignment between aircraft.
- Flights without itinerary revenue data were excluded.

## Run it

```bash
pip install -r requirements.txt
jupyter notebook flight_recovery_optimization.ipynb
```

`ampl_notebook` may ask for a free AMPL Community Edition license UUID on first run.

## Credits

Dataset and problem setup adapted from the Gurobi `modeling-examples` aviation planning example. Built as coursework for the AI-Driven Decision Making module, MSc in Artificial Intelligence, National College of Ireland.
