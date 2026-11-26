# refinery-lp-planning
A linear program that decides which crudes a refinery buys, how it runs its process units, and how it ships gasoline, diesel and jet fuel to regional markets at minimum total cost (Using Pure Synthetic Data)

## Refinery Process Flow & Constraints in Data

This model optimizes daily crude oil processing across four units (capacities in barrels/day) to meet regional product demands:

* 1.  CDU (160k): each crude splits into naphtha, jet, diesel, gas oil and fuel oil (yields.csv, unit = CDU). Light sweet yields the most naphtha (0.32); heavy sour yields the most fuel oil (0.28).
* 2.  Reformer (45k): naphtha → gasoline at 0.90.
* 3.  FCC (42k): gas oil → gasoline 0.55, diesel 0.25, fuel oil 0.15.
* 4.  fo_blending (30k): gas oil → fuel oil at 1.0. This absorbs surplus gas oil.
* 5.  Naphtha and gas oil are intermediates (streams.csv). Every barrel made must be fed to a unit.
