# Install, build, run dockstore foo

Starting from Ubuntu 16.04 on Jetstream.

## Install Java

Add the Oracle Java repo:
```
sudo add-apt-repository ppa:webupd8team/java
```

then install Java:
```
sudo apt-get update && sudo apt-get install -y oracle-java8-set-default

```

## Install the dockstore command line program

```
mkdir ~/bin
curl -L -o ~/bin/dockstore https://github.com/ga4gh/dockstore/releases/download/1.2.3/dockstore 
chmod +x ~/bin/dockstore

# add to path
echo 'export PATH=~/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

and run it:

```
dockstore
```
It will ask you some questions:

* You can find your token at the second page/step 2 of [dockstore onboarding](https://dockstore.org/onboarding).
* The `server url` appears to be `https://dockstore.org:8443`.

## Install `cwltool`

Now, install the Common Workflow Language default/reference runner:
```

pip install setuptools==28.8.0
pip install cwl-runner cwltool==1.0.20170217172322 \
    schema-salad==2.2.20170222151604 avro==1.8.1
```

## Add your user account to the docker group

```
sudo usermod -aG docker $USER
exec newgrp docker
```

The configuration/install stuff is now done and you're ready to run!

## Running a CWL workflow from dockstore

Let's run the [`dockstore-tool-bamstats`](https://dockstore.org/containers/registry.hub.docker.com/cancercollaboratory/dockstore-tool-bamstats) tool from dockstore.

To do this, we already have everything we need with *two exceptions* -- we need to specify the input and output files, and we need an input file to run!  To do this,  we need to create a
JSON file containing that information.  Conveniently the
required information is actually specified in the CWL description file for this task, so we can automatically generate a template like so:

```
dockstore tool convert entry2json \
    --entry registry.hub.docker.com/cancercollaboratory/dockstore-tool-bamstats:develop \
    > params.json
```

Edit the resulting file `params.json` and replace the `path` for `bam_input` (which defaults to `/tmp/fill_me_in.txt`) with 

```
https://github.com/CancerCollaboratory/dockstore-tool-bamstats/raw/develop/rna.SRR948778.bam
```
which is the URL to a BAM file that will work (any BAM file will work as long as it's accessible!).

We also have to specify the output by changing the `bamstats_report` path, but here we get to make it anything we want (as long as it's in a valid directory). I suggest `/tmp/bamstats_report.zip`.

And now, you can run the tool like so --

```
dockstore tool launch \
    --entry registry.hub.docker.com/cancercollaboratory/dockstore-tool-bamstats:develop \
        --json params.json
```

This command will now:
* download the CWL description for that task from the dockstore;
* pull down the docker container for that task;
* download the BAM data file to the local computer;
* execute the specified task;
* place the results in the specified location.

The results should be a zipfile - you can try `unzip -v /tmp/bamstats_report.zip` to see its contents.

Voila!
