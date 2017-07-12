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

## foo

```
git clone https://github.com/ctb/dockstore-tool-bamstats

cd dockstore-tool-bamstats
docker build -t quay.io/ctb/dockstore-tool-bamstats:1.25-3 .
```

Question:
* how do we know to use 1.25-3? Not clear. See discussion [here](https://dockstore.org/docs/getting-started-with-docker)

```
docker run -it -v $(pwd):/home/ubuntu quay.io/ctb/dockstore-tool-bamstats:1.25-3 /bin/bash
```

Note here that no download is necessary because when we built the container, we named it this exact thing (which is good b/c we haven't pushed to quay.io, so it wouldn't download anyway).

inside the docker container you can run
```
# this is inside the running Docker container
$> cd /home/ubuntu
$> wget ftp://ftp.1000genomes.ebi.ac.uk/vol1/ftp/phase3/data/NA12878/alignment/NA12878.chrom20.ILLUMINA.bwa.CEU.low_coverage.20121211.bam
$> /usr/local/bin/bamstats 4 NA12878.chrom20.ILLUMINA.bwa.CEU.low_coverage.20121211.bam
```

=> this will result in the file `bamstats_report.zip ` in your local directory when you exit the docker container.

to push to quay, go to quay and configure foo and add a robot account and click on it and select docker login and copy/paste that docker login string. Then you can run:

```
docker push quay.io/ctb/dockstore-tool-bamstats
```

At this point you should be able to sync your dockstore account with quay and see this tool there.

## more - CWL

[starting here](https://dockstore.org/docs/getting-started-with-cwl)

```
mkdir /tmp/testrun
cd /tmp/testrun

wget ftp://ftp.1000genomes.ebi.ac.uk/vol1/ftp/phase3/data/NA12878/alignment/NA12878.chrom20.ILLUMINA.bwa.CEU.low_coverage.20121211.bam
```

@CTB here we really want to talk about HTTPS / FTP / S3 inputs.

```
cat > sample_configs.local.json <<EOF
{
    "bam_input": {
        "class": "File",
        "path": "/tmp/testrun/NA12878.chrom20.ILLUMINA.bwa.CEU.low_coverage.20121211.bam"
    },
    "bamstats_report": {
        "class": "File",
        "path": "/tmp/testrun/bamstats_report.zip"
    }
}
EOF
```

```
dockstore tool launch \
    --local-entry ~/dockstore-tool-bamstats/Dockstore.cwl \
    --json sample_configs.local.json
```

@CTB how are these output paths controlled?

```
Registering: #bamstats_report to provision from /tmp/testrun/./datastore/launcher-f878c9d3-07de-42b5-8f14-b2b2ecc28ae3/outputs/bamstats_report.zip to : /tmp/testrun/bamstats_report.zip
```

@CTB note that on this `launch` command you *have* to specify the tag `:latest` or else it doesn't work - dockstore doesn't default to `latest`.

```
dockstore tool launch --entry quay.io/ctb/dockstore-tool-bamstats:latest --json sample_configs.local.json
````

## Next steps

I think skip all the tool registration stuff for now :).

Can we configure toil to actually run things? Does that require tool registry?t

Something that might be useful (?) to build here is something that constructs sample_configs.local.json. Or does toil do that?

## Other references

[CWL user guide](http://www.commonwl.org/v1.0/UserGuide.html)
