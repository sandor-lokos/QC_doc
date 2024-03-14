# QC workflow configuration 
## FLP workflow configuration

Details are given [here](https://github.com/sbysiak/FIT_QC_primer/blob/main/FIT_QC_primer.md#the-controlworkflows).
There is also a very detailed documentation in [O2DPG](https://github.com/AliceO2Group/O2DPG/blob/master/DATA/production/README.md#remarks-on-qc)
where the following can be found as a remark on the QC

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