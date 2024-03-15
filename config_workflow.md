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
given. By following that, one can create a new workflow and test it.

Shortly, master workflow template can be used that is [readout-dataflow](https://github.com/AliceO2Group/ControlWorkflows/blob/master/workflows/readout-dataflow.yaml)
wherein it is declared what DPL workflows are included. For example, for FT0, the ```ft0_dpl_workflow``` is defined [here](https://github.com/AliceO2Group/ControlWorkflows/blob/master/workflows/readout-dataflow.yaml#L278)
and in the array ```values``` the [workflows are listed](https://github.com/AliceO2Group/ControlWorkflows/blob/master/workflows/readout-dataflow.yaml#L278)
that should be executed. These DPL commands are in the [jit](https://github.com/AliceO2Group/ControlWorkflows/tree/master/jit) directory,
These workflows can be then selected in the AliECS GUI for the concrete detectors. During creation of an environment, 
AliECS generates workflow & task templates Just-In-Time (JIT) or reuses the most recent ones if the workflow, software 
version and config files have not changed. Thus, to add a new workflow, one should add a file with the DPL command to 
[jit](https://github.com/AliceO2Group/ControlWorkflows/tree/master/jit) and its name to the corresponding ```values```
array in ```readout-dataflow```. About how the DPL commands should look like, check [here](https://github.com/AliceO2Group/ControlWorkflows/blob/master/README.md#flp-workflows)
and for adding the QC to FLP workflows [here](https://github.com/AliceO2Group/ControlWorkflows/blob/master/README.md#adding-qc-to-flp-workflows).


It is also possible to run multinode QC, see [here](https://github.com/AliceO2Group/ControlWorkflows/blob/master/README.md#adding-multinode-qc-to-flps).
To parallel raun on EPNs by adding a few more lines to the configuration as it is written [here](https://github.com/AliceO2Group/ControlWorkflows/blob/master/README.md#adding-multinode-qc-to-flps).
First, make sure that the QC config specify ```"remoteMachine"``` and ```"remotePort"``` parameters. They are not dynamically
assigned for connections between the two control systems. The remote machine name might need the ```.cern.ch``` suffix.
Please use the port number within the range allocated to your subsystem. For ```FDD: 29200-29249```, for ```FT0: 29250-29299```,
for ```FV0: 29300-29349```. It is advised to check the connection with a simple TCP client/server application beforehand
(e.g. ```nc```). Also, do not forget to add ```"localControl" : "odc"``` in the QC task configuration, which will make AliECS
templates avoid dynamic resource assignement. So running QC on EPN requires a DPL command file in the ```jit``` directory, 
similarly to the case of running parallel QC on FLPs.

The documentation also contains the [advice](https://github.com/AliceO2Group/ControlWorkflows/blob/master/README.md#different-parallel-qc-running-on-flps-and-epns)
to combine the FLP and EPN QC config files and use different ```"localMachines"``` for them. Then, one can use different
```--host``` parameter to the local QC workflows to indicate which tasks should be running in the given environment.

The remote QC workflow should be just one.

Overall, the main difference between EPN adn FLP configurations are the specify ```"remoteMachine"``` and ```"remotePort"``` 
parameters in the configuration JSON files.

The configuration JSON files are available in [Consul](https://ali-consul-ui.cern.ch/ui/alice-o2-cluster/kv/o2/components/qc/ANY/any/)
but Consul must be modified very carefully and for studying it is better to have a copy. Keep in mind that the 
details of the JSON files could be out-dated, however, the structure should be the same.

For testing, the JSON files should be in Consul to be used in production or [ECS](https://ali-ecs.cern.ch/) or can be done
in STAGING to be on the safe side. Anyway, on the configuration of the ECS GUI is shown [here](https://github.com/AliceO2Group/O2DPG/tree/master/DATA/aliecs_documentation). 
In the example below, the FT0 QC JSON will be used. 
The [JSON file](https://github.com/sandor-lokos/QC_doc/blob/master/ft0-digits-qc-alio2-cr1-flp200.json) is copied to here.

Example JSON files are available in Consul and is copied it to [here](https://github.com/sandor-lokos/QC_doc/blob/master/ft0-digits-qc-epn.json).

## Differencies between FLP and EPN configs

There are more but only the substantial differences are pointed out here.

| JSON parameter | FLP config | EPN config |
| -------------  | ------------- | ------------- |
| ```qc/config/database```   | ```ali-qcdb.cern.ch:8083```  | ```qcdb.cern.ch:8083https://github.com/sandor-lokos/QC_doc/blob/master/ft0-ageing-alio2-cr1-flp200.json)```  | and [here]() is one that should work based on the arguments above. 
| ```qc/config/conditionDB```   | ```o2-ccdb.internal```  | ```qcdb.cern.ch:8083```  |
| ```qc/tasks/DigitQcTaskFT0/mergingMode```   |  x  | ```delta```  |
| ```qc/tasks/DigitQcTaskFT0/mergerCycleMultiplier```   |  x  | ```"1"```  |
| ```qc/tasks/DigitQcTaskFT0/mergersPerLayer```   |  x  | ```["3","1"]```  |
| ```qc/tasks/DigitQcTaskFT0/location```   |  x  | ```"epn", "localhost"```  |
| ```qc/tasks/DigitQcTaskFT0/localMachines```   |  x  | ```ft0-digits``` (see [here](https://alice-fit-sw.docs.cern.ch/qc/configurations/) ) |
| ```qc/tasks/DigitQcTaskFT0/remoteMachine```   |  x  | ```alio2-cr1-qc01.cern.ch``` (see [here](https://alice-fit-sw.docs.cern.ch/qc/configurations/) )  |
| ```qc/tasks/DigitQcTaskFT0remotePort```   |  x  | ```"29250"``` (see above)  |
| ```qc/tasks/DigitQcTaskFT0/localControl```   |  x  | ```odc```  |


For demonstration, [here](https://github.com/sandor-lokos/QC_doc/blob/master/ft0-ageing-alio2-cr1-flp200.json) is the 
FLP JSON file for the aging task and [here](https://github.com/sandor-lokos/QC_doc/blob/master/ft0-digits-ageing-qc-epn.json)
is an EPN JSON that should work based on the arguments above. Now, this is only for aging test.

I also just assume that the combination of them should work, too, and as many ```qc/tasks/DigitQcTaskFT0``` block can be added
as many the user needs.