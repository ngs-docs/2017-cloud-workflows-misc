# Orchestrating bioinformatics workflows using the cloud

Daniel Standage, Luiz Irber, C. Titus Brown

Vision statement: the cloud will provide a major platforms for running bioinformatics workflows at scale in the future. This workshop will teach attendees the concepts, approaches, and details necessary to use the cloud.

## Meta-issues

* What is "our" "vision"? Why are we doing it?
    * Reusability of workflows is obviously the future!
    * Reproducibility/replicability/repeatability - using the cloud at scale means you have to clearly specify your workflow in a portable/transferrable way, which aids in these scientific goals.
    * Planning for poverty - many institutions lack dedicated on-premise HPCs and have no access to significant compute resources other than the cloud, and the cloud provides a way to minimize upfront investment in hardware.
    * Fault tolerance (unstable local infrastructure) - the cloud generally has excellent uptime and considerable robustness.  The cloud is usable even in situations where local power grids are unstable - all you need is occasional internet access.
    * Better bioinformatics training & skill portability - teaching researchers and clinicians to use the cloud means that they can move between institutions, projects, and software stacks easily without learning new infrastructure details.
    * Scalability - the overall capacity of the cloud is much larger than most HPCs and can be used on demand when big data sets come in.
    * Career training (students -> jobs) - this can provide a path for academic trainees to connect with industry and academic staff careers in any area with a lot of demand.
* Practical considerations:
    * what? ~2 day workshop, with maybe 3rd day/build your own workflow
    * when? late fall/winter 2017?
    * where? pilot within lab. perhaps first public at UC Davis, 2nd at Pitt?
    * how much? ~$1000 first time?
    * for whom? facility/core directors and members of their team.
    * helpers/TAs: offer travel etc to come teach - carpentry folk. maybe don't even need.
    * maybe invite some Carpentry folk for the heck of it
* Pre-requisites (in order to actually implement the workshop)
    * Workflow execution setup that actually works (toil, or something else)
    * Clean up / expand / etc. materials!
    * Figure out what cloud to actually do this on and how to set it up, and provide cost estimates for attendees (or give free compute) - Jetstream

## Target audience and questions to answer

For: "bioinformatics facility director"-like people, and their team, who have genomics experience at the command line.

Questions the attendees will want to be able to answer at the end of the workshop:
* What is the right strategic direction for compute & bioinformatics for us?
* Does this cloud workflow stuff work? Will this cloud workflow stuff work? And what will work/be easy, next?
* What are the costs, how do you estimate and control them?
* What technical expertise is required? Can we have someone on our current team work in this area, or do we have to hire, or can we outsource?

## Potential outline/schedule

1. What is "the cloud", and how does it compare to your laptop or HPCs? (Matrix stuff.) Also, explain IaaS, PaaS, SaaS, and develop a glossary. Discuss potential hybrid models and how a big problem is configuration (=> Docker).
2. Hands on practice:
    * start up an instance on Jetstream/AWS
    * Docker
3. Thinking about workflows
    * what does a workflow need to do?
    * CWL
    * GitHub etc.
4. Running workflow tools: CWL and something (toil).
5. Building workflows: what do you need to do?
6. Running at scale - how do you actually run these things robustly and on many data sets?
7. Hands-on/putting together your *own* workflow.
8. Discuss support models for facility customers
9. Discuss support models for facility personnel (where is the deep technical support?)
10. Resources for additional learning.

Additional ideas:

* examples of how people are actually using it (CyVerse, UCSC, Broad, PCAWG, companies...)
* comparison of cloud companies, somehow.
