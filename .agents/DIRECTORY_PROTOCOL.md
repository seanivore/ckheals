# Directory Protocol 

## Overview 

These are filename with versioning standards that keep project directory structure organized by design. From top to bottom, the files are chronologically in their lifespan.  

## Filename Conventions 

### Planning The Build

  1. `v0_0_1_UPDATE.md` — UPDATE = Start of a new build concept
  2. `v1_0_0_IMPLEMENT.md` — IMPLEMENT = The main planning document that we iterate on and build out each session
  3. `v1_0_1_FEEDBACK.md` — FEEDBACK = Details and commentary about a plan to be integrated into the build guide
  4. `v1_0_2_BUG_REPORT.md` — BUG LOG = Bugs, feedback, gaps, and other details of a build to address
  5. `FUTURE_concept.md` — FUTURE = Concept ideas for future builds

### Validating The Build

  1. `v1_0_0_IMPLEMENT.md`
  2. `v1_0_0_ADDENDUM_DESIGN.md`
  3. `v1_0_0_ADDENDUM_TESTING.md`
  4. `_RATIONALE.md`
  5. `v1_0_0_REVIEW_PROMPTS.md`
  6. `v1_0_0_GAP_REVIEW_A.md`
  7. `v1_0_0_GAP_REVIEW_B.md`
  8. `v1_0_0_GAP_REVIEW_C.md`
  9. `v1_0_0_GAP_REVIEW_D.md`
  10. `v1_0_0_EXECUTION_PROMPT.md`

### Other File Types

You certainly might need other file types. They should be named similarly and use the version number to keep them in the appropriate place in the development cycle.
`v4_0_7_GPT_INSTRUCTIONS.txt`, `v4_0_7_GPT_SCHEMA.txt`, `v4_0_9_CD_HANDOFF.md`, `v0_2_1_CD_DESCRIBE_PROMPT.md`

You also might need to jot down few different versions of functionality concept sketches to place in a version directory ahead of the current state. 
`v3_3_0_FUTURE_design-polish.md`, `v3_3_0_FUTURE_empty-state.md`, etc. these are often created as just `FUTURE_concept.md` and the version number is added later when it is known where they will land because the build has caught up to them. 

## Versioning

A three-part `vMAJOR.MINOR.PATCH` counter lives for the project's life — starting at the first IMPLEMENT draft and continuing through planning rounds into shipped releases. It is one internal counter, not a customer-facing release number. (The *why* — one counter, plan-version-is-ship-version — lives in `.agents/DEV_RULES.md § Versioning`; the mechanics are here.)

  - **MAJOR** — architectural rewrite, deployment-target change, or a breaking external change.
  - **MINOR** — new feature, capability shift, or a breaking-but-internal change.
  - **PATCH** — bug fix, doc-only update, or a micro-tweak that doesn't change the feature surface.

Higher bump resets lower to zero (`v3.1.5` → `v3.2.0`). No change, no bump. Plan version IS ship version when nothing changed between them — a gate-cleared `v5_0_3_IMPLEMENT.md` ships under git tag `v5.0.3`.

**Delimiters:** dots everywhere (`v3.1.2`, git tags, commit messages) **except filenames**, which use underscores (`v3_1_2_IMPLEMENT.md`) — dots in filenames cause tooling issues. **Git tags are pure numeric** (`v3.1.2`, never `v3.1.2-fix`); human labels go in the commit body / GitHub Release; re-pointing a tag = delete + recreate.

## Directory Example 

docs/
├── archive/                               # Find current doc by looking to highest version 
│   ├── images/                            # Used for feedback and planning
│   ├── resources/                         # Two examples; provided for planning at some point
│   │   ├── {SERVICE}_FULL_LLM.txt         # Commonly found in service technical documentation
│   │   └── {SERVICE}_API_DOCS.md          # Many services have agent skills or MCPs with details instead  
│   ├── v1_0/                              # New subdirectory for every MINOR update (only filenames use underscore not dot)
│   │   ├── v0_0_1_UPDATE.md               # Build sketch concept, high level requirements, etc. that need planning and research
│   │   ├── v1_0_0_IMPLEMENT.md            # First sessions build plan results
│   │   ├── v1_0_1_FEEDBACK.md             # Feedback on first build plan
│   │   ├── v1_0_1_IMPLEMENT.md            # Feedback integrated into plan during next session
│   │   └── v1_0_2_FEEDBACK.md
│   ├── v1_1/                              # Significant milestone in build plan reached driving minor number update
│   │   ├── v1_1_0_IMPLEMENT.md
│   │   ├── v1_1_1_FEEDBACK.md             # Continued feedback
│   │   ├── v1_1_1_IMPLEMENT.md            # and feedback integration cycles
│   │   ├── v1_1_2_IMPLEMENT.md            # Internal gap reviews 
│   │   └── v1_1_3_IMPLEMENT.md            # Internal gap reviews
│   ├── v1_2/
│   │   ├── v1_2_0_IMPLEMENT.md            # Designating reaching a state ready for formal gap reviews
│   │   ├── v1_2_0_ADDENDUM_DESIGN.md      # Addendum broken out of build guide to cut down on document size
│   │   ├── v1_2_0_ADDENDUM_TESTING.md     # Addendum broken out of build guide to cut down on document size
│   │   ├── v1_2_0_REVIEW_PROMPTS.md       # Prepared for each type of gap review, and updated with landmine every round
│   │   ├── v1_2_0_GAP_REVIEW_A.md         # First of m any gap review results
│   │   ├── v1_2_1_IMPLEMENT.md            # Gap review results validated and folded in to build guide
│   │   ├── v1_2_1_ADDENDUM_DESIGN.md      # "Build guide" now inherently includes the addenda
│   │   ├── v1_2_1_ADDENDUM_TESTING.md
│   │   ├── v1_2_1_REVIEW_PROMPTS.md
│   │   ├── v1_2_1_GAP_REVIEW_A.md         # First verdict calling for one more narrow pass
│   │   ├── v1_2_2_IMPLEMENT.md
│   │   ├── v1_2_2_ADDENDUM_DESIGN.md
│   │   ├── v1_2_2_ADDENDUM_TESTING.md
│   │   └── v1_2_2_REVIEW_PROMPTS.md
│   ├── v1_3/                              # Designating cleaned documents and moving to B/C/D gap reviews
│   │   ├── v1_3_0_IMPLEMENT.md            # Last version that has had excess archeology removed
│   │   ├── v1_3_0_ADDENDUM_DESIGN.md
│   │   ├── v1_3_0_ADDENDUM_TESTING.md
│   │   ├── v1_3_0_REVIEW_PROMPTS.md
│   │   ├── v1_3_0_GAP_REVIEW_B.md         # First of B gap reviews ran in parallel with C/D reviews 
│   │   ├── v1_3_0_GAP_REVIEW_C.md         # First of C gap reviews ran in parallel with D/B reviews 
│   │   ├── v1_3_0_GAP_REVIEW_D.md         # First of D gap reviews ran in parallel with B/C reviews 
│   │   ├── v1_3_1_IMPLEMENT.md            # Validated and folded in review findings
│   │   ├── v1_3_1_ADDENDUM_DESIGN.md      # Cycle continued until all gates come back with READY TO BUILD verdict
│   │   ├── v1_3_1_ADDENDUM_TESTING.md
│   │   └── v1_3_1_REVIEW_PROMPTS.md
│   ├── v1_4/                              # Driven to designate gap review completion and execution start
│   │   ├── v1_4_0_IMPLEMENT.md            # Final version that was again purged to make more condensed
│   │   ├── v1_4_0_ADDENDUM_DESIGN.md
│   │   ├── v1_4_0_ADDENDUM_TESTING.md
│   │   ├── _RATIONALE.md                  # All logic from build guide
│   │   ├── v1_4_0_EXECUTION_PROMPT.md     # For starting execution 
│   │   ├── v1_4_1_BUG_REPORT.md           # Bugs and fixes found after build completion 
│   │   ├── v1_4_2_BUG_REPORT.md
│   └── v2_0/                              # Driven to designate future build
│       ├── FUTURE_feature-design.md       # Created on the fly to keep details of good ideas that come up in the moment
│       ├── FUTURE_feature-design.md
│       └── FUTURE_feature-design.md
├── research/                          # Below is by no means meant to be absolutely comprehensive or exhaustive 
│   ├── 1_DEEP/                        # Any research needed to define market opportunity, strategic positioning
│   │   ├── business-viability/        # Tech feasibility, service-needs, pricing strategy, backed by market analysis
│   │   ├── competitive-landscape/     # Zoomed in evaluation of rival strategy, strength, weakness
│   │   ├── funding/                   # What opportunities are out there; provide adjacent proof
│   │   ├── market-analysis/           # Broader industry environment, user needs, market size, industry trends
│   │   ├── target-demographics/       # Who you are building for specifically, adjacent opportunity in tiers
│   │   ├── RESEARCH_REVIEW.md         # Feedback on where to look more, what to focus on later
│   │   └── RECOMMENDATIONS.md         # Agent candid thoughts on opportunity based on research
│   ├── 2_FOCUS/                       # Human guided drill-down; focus on moat, realistic finance projections, etc. 
│   │   ├── RESEARCH_FINALIZATION.md   # Report on research and finalization planning 
│   │   └── PRODUCT_OPPORTUNITY.md     # Overview assessment of the project's market placement and more
│   └── 3_FINAL/                       # Polished results ready to be seen by the world whenever and wherever 
│       ├── BUSINESS_PLAN.md           # After drafts, includes 'best practice' business plan essentials 
│       ├── EXEC_SUM_investors.md      # Overview framed specifically for investors 
│       ├── EXEC_SUM_users.md          # Overview framed for any other audience the project might need
│       └── EXECUTIVE_SUMMARY.md       # Use 'best practice' research for current, industry specific framework
├── PROJECT_NAME.md                    # Architecture, technical documentation, context priming agent helper 
├── BRAND.md                           # Voice in various situations, design guide, palettes, etc. 
└── BUSINESS_PLAN.md                   # Any other important docs to keep top level minimal but helpful 