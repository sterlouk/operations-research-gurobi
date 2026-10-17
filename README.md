# Operations Research: integer programming with Gurobi

Two optimization problems from the **Operations Research** course (Επιχειρησιακή Έρευνα), Aristotle University
of Thessaloniki, ECE, spring 2025. Both are formulated as (mixed-)integer linear programs in Python and
solved with Gurobi. The code comments and the [report](docs/report-gr.pdf) are in Greek.

## 1. Weekly school timetable

[`01-school-timetabling/timetabling.ipynb`](01-school-timetabling/timetabling.ipynb)

Build the weekly schedule of two classes that share most teachers. All lessons are 2 hours long, and there are
4 slots per day, Monday to Friday. The rules:

- every teacher teaches the required number of lessons to each class
- PE only on Thursday 14:00–16:00, and Monday 08:00–10:00 is reserved for weekly study planning
- the Maths teacher of class 2 is away on Monday mornings, and the Biology teacher doesn't work on Wednesdays
- at most one lesson per subject per class per day
- no class or teacher in two places at once

**Model:** 360 binary variables `x[teacher, class, day, slot]` and 434 constraints, solved as a feasibility
problem (constant objective). Gurobi finds a solution instantly. I also checked the result against every rule
separately: all 30 lessons are placed, with no conflicts.

**Class 1**

| | Mon | Tue | Wed | Thu | Fri |
|---|---|---|---|---|---|
| 08:00–10:00 | *reserved* | History | Physics | Biology | Maths |
| 10:15–12:15 | Physics | Physics | Philosophy | History | — |
| 14:00–16:00 | Maths | — | Maths | PE | English |
| 16:15–18:15 | Biology | — | — | Maths | Biology |

**Class 2**

| | Mon | Tue | Wed | Thu | Fri |
|---|---|---|---|---|---|
| 08:00–10:00 | *reserved* | Biology | Maths | — | Biology |
| 10:15–12:15 | History | Maths | — | Maths | — |
| 14:00–16:00 | Biology | Philosophy | — | PE | Physics |
| 16:15–18:15 | Maths | Physics | Physics | English | History |

The schedule is also exported to [`timetable.xlsx`](01-school-timetabling/timetable.xlsx).

## 2. Warehouse location

[`02-warehouse-location/warehouse_location.ipynb`](02-warehouse-location/warehouse_location.ipynb)

A company can open warehouses at 12 candidate sites to serve 12 sales centers. Each site has a fixed opening
cost and a capacity, and each warehouse-to-center route has a transport cost; some routes are impossible. Demand
must be fully met, and a center may be served by several warehouses.

**Model:** a capacitated facility location problem. It has 12 binary variables `y[i]` (open site *i*) and
144 continuous variables `x[i,j]` (tonnes shipped from *i* to *j*), with capacity constraints that link the two
(`Σⱼ x[i,j] ≤ capacity[i]·y[i]`). The objective minimizes fixed plus transport cost.

**Optimal solution:** open warehouses **1, 5, 8, 9 and 12**, for a total cost of **€17,929k**, proven
optimal (0% gap).

## Running

```bash
pip install gurobipy pandas openpyxl
jupyter lab
```

The free `gurobipy` build from pip includes a size-limited license, which is enough for both models. Both were
re-run with Gurobi 13 and give the same results as the submitted notebooks (which used Gurobi 12).

Author: Stergios Loukas
