# Design Google Doc

- https://systemdesignschool.io/problems/google-doc/solution 
- https://www.systemdesignhandbook.com/guides/google-docs-system-design/ 

## How do we resolve concurrent edits?

### Option 1: Last write wins: 

The server rewrites concurrent operations so each user's intent is preserved.
- Preserves intent for most text edits
- Requires a central ordering point

### Option 2: OT: Operational Transformation

The server rewrites concurrent operations so each user's intent is preserved.
- Preserves intent for most text edits
- Requires a central ordering point

### Option 3: CRDT (Conflict-Free Replicated Data Types)

Clients attach metadata to each edit so replicas can merge without central coordination.
- Great for offline-first collaboration
- More complex metadata and client logic

### Our Choice: 

Use a server-ordered OT-style pipeline. We already have a Collaboration Service 
in the middle of the edit flow, so a single ordering point keeps client logic simpler and 
gives us predictable convergence. (We'll compare OT vs CRDTs in the deep dive.)

## How do you decide between OT and CRDTs for conflict resolution?

The decision depends on where you want complexity and what your product prioritizes. 
Let's reason from the requirements we already have:
- We already run a collaboration service in the middle of every edit.
- We want clients to stay lightweight.
- Offline-first is not a core requirement in our scope.

OT: A central service orders edits and rewrites (transforms) them so user intent is preserved.
- Lighter clients
- Requires a sequencer and server-side transformations

CRDT: Each edit carries metadata so replicas can merge without a central order.
- Great for offline-first collaboration
- More complex data structures and larger metadata

### Our Choice: 
- With a centralized collaboration service and online-first workflow, OT fits better. It keeps client logic lighter and uses the ordering point we already operate.
- If offline-first becomes a primary requirement, CRDTs become more attractive despite their complexity.
