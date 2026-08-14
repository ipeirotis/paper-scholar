# paper-scholar

A retrieval-grounded agent skill for verifying manuscript citations, investigating uncited factual claims, and finding prior-work leads relevant to novelty claims.

## Install

Copy or clone this repository into your agent's skills directory, for example:

```bash
git clone https://github.com/ipeirotis/paper-scholar.git ~/.agents/skills/paper-scholar
```

Then ask the agent to check specified citations or contribution claims. The skill requires literature search and access to the actual source text.

## Safety model

The skill never verifies a source from memory, distinguishes inaccessible sources from unsupported claims, treats novelty results as leads rather than verdicts, and presents citation or wording changes only as proposals for the author.
