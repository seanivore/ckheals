# Gap Review Workflow Prompts

## Clean-Up Grep Usage Mess
Had a lot of surgical edits to the build docs (IMPLEMENT and addenda) over time. Leaves behind stray references, mentions to previous version changes. Time to condense things, remove outdated notes, excessive context or version changelog history. Then run two breadth subagents, folding in changes. Then a straight read from end to end to be sure nothing is missing and everything flows the way it's supposed to. 

## Build Guide Final Cuts
Remove changelog/provenance and other items that are the wrong context for the final orchestrating agent. Remove slipped-scope rationale, resolved-edges, owner-decision tags, gap-review framing, excessive prose. Want those anchors. Probably don't need scope boundaries if it is made clear that all decisions are made already, but a little context doesn't hurt. 

## Gap Review /Compact Note
1. Gap reviews X-Type are complete and ready to be validated and folded it to drive VERSION-IMPLEMENT. This will require a thorough understanding of the final state of our docs and what comes up next.
2. Here is the NUMBER X-Type Gap Review findings. We need to see if their findings are valid to be folded in, driving VERSION-IMPLEMENT-DOCS to VERSION, working towards our exclusively executable end-goal. Let's see what they found. Don't forget sub-agent reviews after driving document updates.