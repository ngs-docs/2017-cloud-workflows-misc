# 2017-cloud-workflows-misc

This repo contains some nascent tutorials and examples using
[the Common Workflow Language (CWL)](commonwl.org) to run workflows
at the command line.

## Using the CWL reference implementation

This is the canonical way to do things, matching the usage in documentation
provided by the CWL project and others.

#### Example: [Install and run a workflow with cwlref-runner](install-and-run-with-cwltool.md)

## Using the dockstore tool

This is a full (?) install of the
[dockstore](https://dockstore.org/docs) approach.

A few notes:

* a simplified version of what's in the dockstore docs :)
* Java is needed here!
* Uses the `dockstore`-specific trick of putting output file names in the
  input object.

### Example: [Install and run dockstore](install-and-run-with-dockstore.md)
