# QC workflow configuration

## DPL workflow configuration

There is an O2 package called [ControlWorkflows](https://github.com/AliceO2Group/ControlWorkflows/tree/master) which hosts the
configuration tree for AliECS workflow templates (WFT) and task templates (TT). There is an extensive [README](https://github.com/AliceO2Group/ControlWorkflows/blob/master/README.md)
that contains many details. Here a short introduction is given only.

The [ControlWorkflows](https://github.com/AliceO2Group/ControlWorkflows/tree/master) contains three directories
[tasks](https://github.com/AliceO2Group/ControlWorkflows/tree/master/tasks), 
[workflows](https://github.com/AliceO2Group/ControlWorkflows/tree/master/workflows).
The repository contains configuration of DPL workflows that runs on FLP or QC nodes. EPN workflows are defined
in [O2DPG](https://github.com/AliceO2Group/O2DPG/tree/master/DATA/production) as it is described
[below](https://github.com/AliceO2Group/ControlWorkflows/blob/master/README.md#introduction).
In the README a [Quick reference](https://github.com/AliceO2Group/ControlWorkflows/blob/master/README.md#quick-reference) is also
given. By following that one can create a new workflow and test it.

### Introduction

Shortly, master workflow template can be used that is [readout-dataflow](https://github.com/AliceO2Group/ControlWorkflows/blob/master/workflows/readout-dataflow.yaml)
wherein it is declared what DPL workflows are included. For example, for FT0, the ```ft0_dpl_workflow``` is defined [here](https://github.com/AliceO2Group/ControlWorkflows/blob/master/workflows/readout-dataflow.yaml#L278)
and in the array ```values``` the [workflows are listed](https://github.com/AliceO2Group/ControlWorkflows/blob/master/workflows/readout-dataflow.yaml#L278)
that should be executed. These DPL commands are in the [jit](https://github.com/AliceO2Group/ControlWorkflows/tree/master/jit) directory,
These workflows can be then selected in the AliECS GUI for the concrete detectors. During creation of an environment, 
AliECS generates workflow & task templates Just-In-Time (JIT) or reuses the most recent ones if the workflow, software 
version and config files have not changed. Thus, to add a new workflow, one should add a file with the DPL command to 
[jit](https://github.com/AliceO2Group/ControlWorkflows/tree/master/jit) and its name to the corresponding ```values```
array in ```readout-dataflow```. About how the DPL commands should look like and JIT workflow generation, one can read
[here](https://github.com/AliceO2Group/ControlWorkflows/blob/master/README.md) but let's summarize it shortly.

### FLP workflows






>The JSON files for the individual detectors are merged into one JSON file, which is cached during the run on the shared EPN home
>folder. The default JSON file per detector is defined in qc-workflow.sh. JSONs per detector can be overridden by exporting 
>QC_JSON_[DETECTOR_NAME], e.g. QC_JSON_TPC, when creating the workflow. The global section of the merged qc JSON config is taken
>from qc-sync/qc-global.json

Quick Start the main differences are pointed out between the FLP and EPN configurations here.

The configuration JSON files are available in [Consul](https://ali-consul-ui.cern.ch/ui/alice-o2-cluster/kv/o2/components/qc/ANY/any/)
 but Consul must be modified very carefully and for studying it is better to have a copy. Keep in mind that the 
 details of the JSON files could be out-dated, however, the structure should be the same.

For testing, the JSON files should be in Consul to be used in production or [ECS](https://ali-ecs.cern.ch/) in general.
In the example below, the FT0 QC JSON will be used. The [JSON file](https://github.com/sandor-lokos/QC_doc/blob/master/ft0-digits-qc-alio2-cr1-flp200.json)
is copied to here.

## EPN workflow configuration

It is complicated to do it from scratch so an existing JSON file should be copied and modified.

An example JSON file are available in Consul and is copied it to [here](https://github.com/sandor-lokos/QC_doc/blob/master/ft0-digits-qc-epn.json).

## Differencies between FLP and EPN configs

| JSON parameter | FLP config | EPN config |
| -------------  | ------------- | ------------- |
| ```qc/config/database```   | ```ali-qcdb.cern.ch:8083```  | ```qcdb.cern.ch:8083https://github.com/sandor-lokos/QC_doc/blob/master/ft0-ageing-alio2-cr1-flp200.json)```  | and [here]() is one that should work based on the arguments above. 
| ```qc/config/conditionDB```   | ```o2-ccdb.internal```  | ```qcdb.cern.ch:8083```  |
| ```qc/tasks/DigitQcTaskFT0/taskName```   |  x  | ```DigitQcTask```  |
| ```qc/tasks/DigitQcTaskFT0/disableLastCycle```   |  x  | ```true```  |
| ```qc/tasks/DigitQcTaskFT0/mergingMode```   |  x  | ```delta```  |
| ```qc/tasks/DigitQcTaskFT0/mergerCycleMultiplier```   |  x  | ```"1"```  |
| ```qc/tasks/DigitQcTaskFT0/mergersPerLayer```   |  x  | ```["3","1"]```  |
| ```qc/tasks/DigitQcTaskFT0/dataSource/type```   |  ```direct```  | ```dataSamplingPolicy``` (given in the JSON)  |
| ```qc/tasks/DigitQcTaskFT0/dataSource/query```   |  ```digits:FT0/DIGITSBC/0;channels:FT0/DIGITSCH/0```  | ```ft0-digits```  |
| ```qc/tasks/DigitQcTaskFT0/location```   |  x  | ```"epn", "localhost"```  |
| ```qc/tasks/DigitQcTaskFT0/localMachines```   |  x  | ```ft0-digits```  |
| ```qc/tasks/DigitQcTaskFT0/remoteMachine```   |  x  | ```alio2-cr1-qc01.cern.ch```  |
| ```qc/tasks/DigitQcTaskFT0remotePort```   |  x  | ```"29250"```  |
| ```qc/tasks/DigitQcTaskFT0/localControl```   |  x  | ```odc```  |
| ```qc/tasks/DigitQcTaskFT0/RawDataMetricFT0```   |  x  | complete block of settings that is missing from FLP JSON  |
| ```dataSamplingPolicies/*.*```   |  x  | independent block from qc that is referred in ```qc/tasks/DigitQcTaskFT0/dataSource/type```   |

Since the ```taskName``` is specified only in the case of the EPN config file, I suspect that the whole ```qc/tasks/``` should 
be edited according to the needs. E.g., in the case of the aging task the ```taskName``` should be ```AgeingTask```, the
```className``` should be ```DigitQcTaskLaser``` the ```taskParameters``` should be copied from the FLP JSON.

For demonstration, [here](https://github.com/sandor-lokos/QC_doc/blob/master/ft0-ageing-alio2-cr1-flp200.json) is the 
FLP JSON file for the aging task and [here](https://github.com/sandor-lokos/QC_doc/blob/master/ft0-digits-ageing-qc-epn.json)
is an EPN JSON that should work based on the arguments above. Now, this is only for aging test.

I also just assume that the combination of them should work, too, and as many ```qc/tasks/DigitQcTaskFT0``` block can be added
as many the user needs.