# Talos_DT_LNT

This repository contains a formal model of the emergency stop service of a digital twin, developed using the LNT language and the CADP toolbox.

The goal of this project is to model the system behavior as a Labelled Transition System (LTS), analyze its execution traces, and verify selected safety and liveness properties using MCL.

## Project overview

The project models a digital twin emergency-stop scenario in a formal and executable way. The main artifacts are:

- [LNT/dt_system_updated.lnt](LNT/dt_system_updated.lnt): the LNT specification of the system
- [LNT/property.mcl](LNT/property.mcl): the MCL property used for verification
- [LNT/dt_system_updated.bcg](LNT/dt_system_updated.bcg): the generated LTS/BCG model
- [LNT/module_dt_system_is.lnt](LNT/module_dt_system_is.lnt): supporting LNT module

## Prerequisites

Before working with this project, you need:

- A valid CADP installation
- A CADP license to use the toolchain
- A terminal environment with access to CADP commands such as `lnt.open`, `bcg_edit`, and `bcg_open`

## Repository structure

- [README.md](README.md): project documentation
- [Figures/](Figures/): architecture-related figures
- [LNT/](LNT/): model source files, generated BCG files, and MCL properties

## Building the LTS model

From the [LNT](LNT) directory, generate the BCG/LTS model with:

```bash
lnt.open dt_system_updated.lnt generator dt_system_updated.bcg
```

This command compiles the LNT specification and produces the labelled transition system stored in [LNT/dt_system_updated.bcg](LNT/dt_system_updated.bcg).

## Viewing the LTS model

To inspect the generated model interactively, run:

```bash
bcg_edit dt_system_updated.bcg
```

## Running the simulation

To open the generated BCG file for simulation or interactive exploration, run:

```bash
bcg_open dt_system_updated.bcg ocis
```

## Verification with MCL

This project also includes an MCL property in [LNT/property.mcl](LNT/property.mcl). MCL is used in CADP to express temporal properties over the generated LTS.

The current property checks whether the system can reach a deviation-detection event and whether the corresponding response action is eventually executed. This provides a formal way to validate the emergency-stop behavior of the modeled digital twin.

## Suggested workflow

1. Ensure CADP is installed and your license is active.
2. Navigate to the [LNT](LNT) folder.
3. Generate the LTS with `lnt.open`.
4. Inspect the model with `bcg_edit`.
5. Run the simulation with `bcg_open`.
6. Verify the system behavior using the MCL property in [LNT/property.mcl](LNT/property.mcl).

## Notes

This repository is intended as a formal modeling and verification example for the emergency stop service of a digital twin using CADP. The generated BCG model and the MCL property are the core artifacts for analysis and validation.