# NanoAODJMAR
This is a [NanoAOD](https://twiki.cern.ch/twiki/bin/view/CMSPublic/WorkBookNanoAOD) framework for testing jet algorithms. It takes the AK8 `pat::Jets` and `reco::GenJets` from `MINIAOD`, unpacks their constituents correctly, and writes a NANOAOD flat table of their four vector, pdgid, and charge. This is intended for advanced development using [fastjet](http://fastjet.fr) directly.

## Recipe


For 2016, 2017 and 2018 data and MC:

```
cmsrel  CMSSW_10_2_10
cd  CMSSW_10_2_10/src
cmsenv
git cms-merge-topic cms-nanoAOD:master-102X
git cms-merge-topic  cmantill:lsfinNanoAOD
git cms-merge-topic jmhogan:master-102X
git clone https://github.com/UBParker/NanoAODJMAR.git PhysicsTools/NanoAODJMAR  -b test_102x
scram b -j 10
cd PhysicsTools/NanoAODJMAR/test
```
Note: This configuration has been tested for this combination of CMSSW release, global tag, era and dataset. When running over a new dataset you should check with [the nanoAOD workbook twiki](https://twiki.cern.ch/twiki/bin/view/CMSPublic/WorkBookNanoAOD#Running_on_various_datasets_from) to see if the era modifiers in the CRAB configuration files are correct. The jet correction versions are taken from the global tag.

## Local MC Usage:

2016
```
cmsRun nano102x_on_mini94x_2016_mc_NANO.py
```

2017
```
cmsRun nano102x_on_mini94x_2017_mc_NANO.py
```

2018
```
cmsRun nano102x_on_mini94x_2018_mc_NANO.py
```

## Local Data usage:

2016
```
cmsRun nano102x_on_mini94x_2016_data_NANO.py
```

2017
```
cmsRun nano102x_on_mini94x_2017_data_NANO.py
```

2018
```
cmsRun nano102x_on_mini94x_2018_data_NANO.py
```

## Submission to CRAB

```
python submit_all.py -c nano102x_on_mini94x_2016_mc_NANO.py  -f 2016mc_miniAODv3_DY.txt  -d NANO2016MC

python submit_all.py -c nano102x_on_mini94x_2017_mc_NANO.py -f 2017mc_miniAODv2_DY.txt  -d NANO2017MC

python submit_all.py -c nano102x_on_mini102x_2018_mc_NANO.py -f 2018mc_DY.txt  -d NANO2018MC


python submit_all.py -c nano102x_on_mini94x_2016_data_NANO.py -f 2016data_17Jul2018.txt -d NANO2016 -l Cert_271036-284044_13TeV_23Sep2016ReReco_Collisions16_JSON.txt

python submit_all.py -c nano102x_on_mini94x_2017_data_NANO.py  -f 2017data_31Mar2018.txt  -d NANO2017 -l Cert_294927-306462_13TeV_EOY2017ReReco_Collisions17_JSON.txt



python submit_all.py -c nano102x_on_mini102x_2018_data_abc_NANO.py  -f  2018data_17Sep2018.txt  -d NANO2018 -l Cert_314472-325175_13TeV_PromptReco_Collisions18_JSON.txt

python submit_all.py -c nano102x_on_mini102x_2018_data_d_NANO.py  -f datasets_2018D.txt -d NANO2018 -l Cert_314472-325175_13TeV_PromptReco_Collisions18_JSON.txt

```

## Documenting the Extended NanoAOD Samples

Please document the input and output datasets on the following twiki: https://twiki.cern.ch/twiki/bin/view/CMS/JetMET/JMARNanoAODv1. For the MC, the number of events can be found by looking up the output dataset in DAS. For the data, you will need to run brilcalc to get the total luminosity of the dataset. See the instructions below.

## Running brilcalc
These are condensed instructions from the lumi POG TWiki (https://twiki.cern.ch/twiki/bin/view/CMS/TWikiLUM).

Note: brilcalc should be run on lxplus. It does not work on the lpc.

Instructions:

1.) Add the following lines to your .bashrc file (or equivalent for your shell). 
    ```
    export PATH=$HOME/.local/bin:/cvmfs/cms-bril.cern.ch/brilconda/bin:$PATH
    export PATH=/afs/cern.ch/cms/lumi/brilconda-1.1.7/bin:$HOME/.local/bin:$PATH
    ```
    Don't forget to source this file!
    
2.) Install brilws:
    ```
    pip install --install-option="--prefix=$HOME/.local" brilws
    ```
    
3.) Get the json file for your output dataset. In the area in which you submitted your jobs:
    ```
    crab report -d [your crab directory]
    ```
    The processedLumis.json file will tell you which lumi sections you successfully ran over. If you have incomplete jobs, notFinishedLumis.json will list the unfinished lumi sections.
    
4.) Run brilcalc on lxplus:
    ```
    brilcalc lumi -i processedLumis.json -u /fb --normtag /cvmfs/cms-bril.cern.ch/cms-lumi-pog/Normtags/normtag_PHYSICS.json -b "STABLE BEAMS"
    ```
    The luminosity of interest will be listed under "totrecorded(/fb)." You can also run this over notFinishedLumis.json.
    
  Note: -b "STABLE BEAMS" is optional if you've already run over the golden json. 
        Using the normtag is NOT OPTIONAL, as it defines the final calibrations and detectors that are used for a given run.
