# QC workflow configuration 
## FLP workflow configuration

Details are given [here](https://github.com/sbysiak/FIT_QC_primer/blob/main/FIT_QC_primer.md#the-controlworkflows).
The JSON file should be in [Consul](https://ali-consul-ui.cern.ch/ui/alice-o2-cluster/kv/o2/components/qc/ANY/any/) to be used in production. In the example below, I will use the [FT0 QC JSON](https://ali-consul-ui.cern.ch/ui/alice-o2-cluster/kv/o2/components/qc/ANY/any/ft0-digits-qc-alio2-cr1-flp200/edit)

## EPN workflow configuration

It is complicated to do it from scratch so an existing JSON file should be copied and modified.
The existing JSON files are available in [Consul](https://ali-consul-ui.cern.ch/ui/alice-o2-cluster/kv/o2/components/qc/ANY/any/), for example for [FT0 QC](https://ali-consul-ui.cern.ch/ui/alice-o2-cluster/kv/o2/components/qc/ANY/any/ft0-digits-qc-epn/edit)

## Differencies between FLP and EPN configs

The JSON config files for the QC on FLP and QC on EPN is in the configuration of the remote machines, mainly. Here I use the two JSON files that were mentioned in the previous sections.
