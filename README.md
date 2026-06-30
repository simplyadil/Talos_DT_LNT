# Talos_DT_LNT

This repository contains a formal model of the emergency stop service of a digital twin, developed using the LNT language and the CADP toolbox.

The goal of this project is to model the system behavior as a Labelled Transition System (LTS), analyze its execution traces, and verify selected safety and liveness properties using MCL.

## Project overview

The project models a digital twin emergency-stop scenario in a formal and executable way. The main artifacts are:

- [LNT/dt_system_final.lnt](LNT/dt_system_final.lnt): the LNT specification of the system
- [LNT/property.mcl](LNT/property.mcl): the MCL property used for verification
- [LNT/dt_system_final.bcg](LNT/dt_system_final.bcg): the generated LTS/BCG model
- [LNT/module_dt_system_is.lnt](LNT/module_dt_system_is.lnt): supporting LNT module

## Prerequisites

Before working with this project, you need:

- A valid CADP installation
- A CADP license to use the toolchain
- A terminal environment with access to CADP commands such as `lnt.open`, `bcg_edit`, `bcg_open`, and `evaluator4`

## Repository structure

- [README.md](README.md): project documentation
- [Figures/](Figures/): architecture-related figures
- [LNT/](LNT/): model source files and generated BCG files
- [LNT/properties/](LNT/properties/): the individual MCL property files (`p1.mcl` to `p12.mcl`)

## Building the LTS model

From the [LNT](LNT) directory, generate the BCG/LTS model with:

```bash
lnt.open dt_system_final.lnt generator dt_system_final.bcg
```

This command compiles the LNT specification and produces the labelled transition system stored in [LNT/dt_system_final.bcg](LNT/dt_system_final.bcg).

## Viewing the LTS model

To inspect the generated model interactively, run:

```bash
bcg_edit dt_system_final.bcg
```

To print statistics (number of states, transitions, labels, deadlocks):

```bash
bcg_info dt_system_updated.bcg
```

## Running the simulation

To open the generated BCG file for simulation or interactive exploration, run:

```bash
bcg_open dt_system_final.bcg ocis
```

## Verification with MCL

This project includes twelve MCL properties in [LNT/properties/](LNT/properties/), covering both safety and liveness requirements of the emergency-stop workflow.

### Property summary

| Property | Type | Description |
|----------|------|-------------|
| P1  | Liveness | After a deviation is detected, stop delivery to the PT remains reachable |
| P2  | Safety   | No stop is received without a prior deviation detection |
| P3  | Liveness (potentiality) | After a send, delivery through Channel K remains reachable |
| P4  | Fairness | After a send, delivery through Channel K is guaranteed on every path |
| P5  | Liveness | A good terminal state is always reachable |
| P6  | Safety   | No stop is sent into Channel L without a prior deviation detection |
| P7  | Safety   | At most one stop is ever received |
| P8  | Liveness (inevitability) | Every execution path eventually reaches a good terminal |
| P9  | Liveness (livelock freedom) | No livelock occurs before reaching a good terminal |
| P10 | Safety   | The PT never sends more than five sensor packets |
| P11 | Safety   | Both controllers always stop together |
| P12 | Liveness | A successful stop dispatch leads to both controllers halting |

Properties P5, P8, P9, and P12 rely on the `GOOD_TERMINAL` macro, which groups the terminal marker events (`STOP_SENT`, `BASE_STOPPED`, `JOINT_STOPPED`, `DEVIATION_HANDLED`, `STREAM_DONE`) emitted by the processes before they idle.

### Verifying a single property

To evaluate one property against the generated LTS, run from the [LNT](LNT) directory:

```bash
bcg_open dt_system_updated.bcg evaluator4 properties/p1.mcl
```

Replace `p1.mcl` with any property file from `p1.mcl` to `p12.mcl`. The evaluator prints `TRUE` or `FALSE`, along with a diagnostic example or counterexample when applicable.

### Verifying all properties at once

To run the complete suite in sequence:

```bash
for p in p1 p2 p3 p4 p5 p6 p7 p8 p9 p10 p11 p12; do
  echo "=== $p ==="
  bcg_open dt_system_updated.bcg evaluator4 properties/$p.mcl
  echo ""
done
```

To save all results to a file for inclusion in a report:

```bash
for p in p1 p2 p3 p4 p5 p6 p7 p8 p9 p10 p11 p12; do
  echo "=== $p ===" >> results.txt
  bcg_open dt_system_updated.bcg evaluator4 properties/$p.mcl >> results.txt 2>&1
  echo "" >> results.txt
done
```

## Suggested workflow

1. Ensure CADP is installed and your license is active.
2. Navigate to the [LNT](LNT) folder.
3. Generate the LTS with `lnt.open`.
4. Inspect the model with `bcg_edit` and `bcg_info`.
5. Optionally explore execution traces with `bcg_open ... ocis`.
6. Verify the system behavior by running the MCL properties in [LNT/properties/](LNT/properties/).

## Notes

This repository is intended as a formal modeling and verification example for the emergency stop service of a digital twin using CADP. The generated BCG model and the MCL properties are the core artifacts for analysis and validation. The terminal marker events added to the LNT model allow the verification of meaningful liveness and termination properties by distinguishing successful completion from genuine deadlock.