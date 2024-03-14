# QC workflow configuration 
## FLP workflow configuration

Details are given [here](https://github.com/sbysiak/FIT_QC_primer/blob/main/FIT_QC_primer.md#the-controlworkflows).
There is also a very detailed documentation in [O2DPG](https://github.com/AliceO2Group/O2DPG/blob/master/DATA/production/README.md#remarks-on-qc)
but for Quick Start the main differences are pointed out between the FLP and EPN configurations here.

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

| First Header  | Second Header |
| ------------- | ------------- |
| Content Cell  | Content Cell  |
| Content Cell  | Content Cell  |