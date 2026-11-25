# Data — Refinery Planning & Distribution LP (SYNTHETIC)

**Stylized practice data for a fictional Gulf Coast refinery.** Prices and yields are realistic in scale but invented. Say so in your project README. All volumes are barrels per day (bbl/day); all money is US dollars.

## Full model files

| File | Contents |
| --- | --- |
| `crudes.csv` | 3 crudes: price, daily availability, API gravity, sulfur |
| `units.csv` | Process units: feed stream, capacity, operating cost per bbl fed |
| `yields.csv` | Long format: `unit, feed, output_stream, yield_bbl_per_bbl_feed` |
| `streams.csv` | Each stream marked `intermediate` (must be fully consumed) or `product` |
| `demand.csv` | Contracted demand by region × product (≥ constraints) |
| `routes.csv` | Region × shipping mode: cost per bbl and capacity (all products combined) |
| `products.csv` | Gulf Coast **spot price** for selling surplus product at the refinery gate |
| `regional_prices.csv` | Delivered price by region × product (for the profit-max variant in Phase 5) |

### Process flow

- **CDU** (160k bbl/day): crude → naphtha, jet, diesel, gas_oil, fuel_oil (yields per crude in `yields.csv`, each crude sums to 1.0).
- **Reformer** (45k): naphtha → gasoline 0.90 (10% volume loss to fuel gas).
- **FCC** (42k): gas_oil → gasoline 0.55, diesel 0.25, fuel_oil 0.15 (5% loss to coke/gas).
- **fo_blending** (30k): gas_oil → fuel_oil 1.0. Surplus gas oil blended into fuel oil.

Naphtha and gas_oil are intermediates: their balance must equal zero (everything made is fed to a unit).

## One addition to the guide's formulation: spot sales

Add a variable `e_p ≥ 0` = barrels of product p sold on the Gulf Coast spot market, so the product balance becomes

`production_p = Σ shipments_p + e_p`

and the objective subtracts the revenue: `min … − Σ_p spot_price_p · e_p`.

**Why:** without it, byproducts are worth $0, so whichever product runs shortest carries the entire barrel's cost (marginal costs of $200–600/bbl) and capacity limits rarely bind. That is correct math but a poor planning model. With spot sales, every extra barrel has market value, the refinery runs hard, and the capacity constraints become the bottlenecks, as in real refinery LPs. Try both versions and explain the difference in your report; it's a strong talking point.

## Reference solution (min cost net of spot sales)

Use this to check your model, then do your own analysis:

- Objective ≈ **$8,944,170 per day**.
- Crude: 80,000 medium sour + 80,000 heavy sour; light sweet unused (look at its reduced cost).
- Binding constraints: FCC capacity (largest shadow price), CDU capacity, heavy-crude availability, and the East Coast, Midwest and Southeast pipelines.
- Reformer has slack. Expanding it is worth $0 at the margin.

Shadow-price signs depend on your solver's convention.

## `toy_excel/` — for the Excel Solver prototype (Phase 3a)

2 crudes, CDU only (yields straight to gasoline/diesel/fuel oil), 2 regions, 1 route each. CDU capacity 80,000 bbl/day, operating cost $1.50/bbl. Pure min cost, **no** spot sales.

Check value: optimal cost **$5,693,594.76 per day** (≈ 45,263 light + 30,526 heavy). Diesel is the limiting product here, a small-scale example of the joint-product effect above.

## Library note

Newer PuLP releases (4.x) changed the variable API. Most tutorials use PuLP 2.x (`pip install "pulp<3"`). Pyomo, the guide's preferred tool, is unaffected.
