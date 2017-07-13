# Install, build, run Common Workflow Language tools and workflows

Starting from Ubuntu 16.04 on Jetstream.

# Install `cwltool`

Now, install the Common Workflow Language reference runner:
```

pip install setuptools>=28.8.0
pip install -U https://github.com/common-workflow-language/cwltool/archive/generate_input_object.zip

# this can be replaced by:
# pip install cwlref-runner
# once https://github.com/common-workflow-language/cwltool/pull/462 is merged.
```

## Add your user account to the docker group

```
sudo usermod -aG docker $USER
exec newgrp docker
```

The configuration/install stuff is now done and you're ready to run!

## Running a CWL workflow from dockstore

Let's run the [`dockstore-tool-bamstats`](https://dockstore.org/containers/registry.hub.docker.com/cancercollaboratory/dockstore-tool-bamstats) tool from the dockstore.org registry.

To do this, we already have everything we need with *two exceptions* -- we need to specify the inputs, and we need an input file to run!  To do this,  we need to create a
YAML file containing that information.  Conveniently the
required information is actually specified in the CWL description file for this task, so we can automatically generate a template like so:

```
cwltool --make-template https://github.com/CancerCollaboratory/dockstore-tool-bamstats/raw/develop/Dockstore.cwl \
    > params.yaml
```

Edit the resulting file `params.yml` and replace the `path` for `bam_input` (which defaults to `default/file/path`) with 

```
https://github.com/CancerCollaboratory/dockstore-tool-bamstats/raw/develop/rna.SRR948778.bam
```
which is the URL to a BAM file that will work (any BAM file will work as long as it's accessible!).

And now, you can run the tool like so --

```
cwl-runner --outdir /tmp https://github.com/CancerCollaboratory/dockstore-tool-bamstats/raw/develop/Dockstore.cwl \
    params.yaml
```

This command will now:
* download the CWL description for that task from GitHub;
* pull down the docker container for that task;
* download the BAM data file to the local computer;
* execute the specified task;
* place the results in the specified location.

The results should be a zipfile - you can try `unzip -v /tmp/bamstats_report.zip` to see its contents.

Voila!
