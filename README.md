[Simulation_README.md](https://github.com/user-attachments/files/26124124/Simulation_README.md)
# Confetti Tube Production — Discrete-Event Simulation
**Course:** Simulation Modeling and Analysis (ESMA341) — Khalifa University, Fall 2025  
**Tool:** Simio · Python (pandas, numpy, matplotlib)  

---

## Overview
Discrete-event simulation of a 9-stage confetti tube production facility manufacturing three product types (Small, Large, XL). The project covers the full simulation workflow: model building, verification and validation, current-state analysis, bottleneck identification, and future-state improvement modeling.

---

## Files

| File | Description |
|------|-------------|
| `confetti_simulation_analysis.ipynb` | Results analysis — throughput, utilization, WIP, current vs future state comparison |
| `Current_State_Final_Model.spfx` | Simio model — baseline production system |
| `Future_State_Final_Model.spfx` | Simio model — improved system with capacity additions |
| `Simio_Final_Model.spfx` | Final combined Simio submission model |
| `Simulation_Report.pdf` | Full written report — methodology, V&V, results, recommendations |

> `.spfx` files require [Simio](https://www.simio.com/) (free academic licence available at simio.com/academia)

---

## Production System

**9-stage flow line:**
```
Rolling → Trimming → ChemicalPressing → ChemicalMixing → BodyPreparation
        → CNC → GlueAndDrying → FinalAssembly → LabelingAndPackaging → FinishedGoods
```

**3 product types:** Small, Large, Extra Large (XL) — each with different triangular service time distributions

**Shift:** 480 min/day (7:00–12:00, 13:00–16:00)  
**Horizon:** 60 days · **Replications:** 15 · **Confidence:** 95%

---

## Seasonal Demand Scenarios

| Season | Daily Demand | Throughput (Current) | Demand Met |
|--------|-------------|---------------------|------------|
| Fall | 200 tubes/day | ~20 tubes/day | 10% |
| Spring | 300 tubes/day | ~20 tubes/day | 7% |
| Winter | 40 tubes/day | ~20 tubes/day | 50% |

Throughput is **constant at ~20 tubes/day regardless of demand** — definitive evidence of a binding capacity bottleneck.

---

## Bottleneck Analysis

| Station | Fall Utilization | Spring | Winter | Status |
|---------|-----------------|--------|--------|--------|
| ChemicalMixing | **100%** | **100%** | **100%** | 🔴 Primary bottleneck |
| GlueAndDrying | 99.1% | 99.1% | 96.3% | 🟠 Secondary |
| CNC | 96.1% | 95.7% | 94.1% | 🟠 Secondary |
| Rolling / Trimming | ~80–85% | ~82–87% | ~73–78% | 🟡 Moderate |

---

## WIP & Flow Time (Current State)

| Tube Type | Avg. Time in System | Max Time in System | Avg. WIP |
|-----------|--------------------|--------------------|----------|
| Small | ~320 hrs (40 days) | ~1,245 hrs (52 days) | 366 units |
| Large | ~380 hrs (47 days) | ~1,265 hrs (53 days) | 203 units |
| XL | ~350 hrs (44 days) | ~1,255 hrs (52 days) | 268 units |

**Total average WIP: ~837 tubes** — some products spend nearly the entire 60-day simulation in the system.

---

## Future State Improvements

1. **Add server at ChemicalMixing** — primary bottleneck at 100%; adding capacity directly unlocks throughput
2. **Add server at GlueAndDrying** — prevents a new bottleneck forming once ChemicalMixing is relieved
3. **Extend shift to 540 min/day** — provides 12.5% additional processing capacity across all stations
4. **WIP cap (CONWIP logic)** — limits upstream entity creation to reduce blocking and average flow time

**Result:** Throughput increases from ~20 → ~65–70 tubes/day in Fall/Spring; WIP reduced by ~67%; average time in system reduced ~60%.

---

## Model Architecture

The Simio model uses a **table-driven architecture** for maintainability:
- `TubeData` table — product mix weights
- `ProcessSequenceTable` — routing and service time expressions per station/tube type
- Timer-triggered restocking processes for 5 raw material types
- Custom state variables for blocking detection (`BlockedCount`, `BlockedWaitTime`)
- Experiment with 3 seasonal scenarios, 15 replications each

---

## How to Run

1. Install [Simio](https://www.simio.com/academia) (free academic licence)
2. Open `Current_State_Final_Model.spfx` to explore the baseline
3. Open `Future_State_Final_Model.spfx` to see the improved design
4. Run the notebook for results visualisation (requires `pandas`, `numpy`, `matplotlib`)
