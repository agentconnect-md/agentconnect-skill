# agentconnect-skill

Skills shipped to the built-in **AgentConnect** preset agent (and installable on any
agent) from the `skills/` directory:

| Skill                                                     | Purpose                                                                                                  |
| --------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| [`agentconnect-platform`](skills/agentconnect-platform)   | Platform guide + administering AgentConnect through the admin MCP tools (or guiding the user otherwise). |
| [`agentconnect-create-agent`](skills/agentconnect-create-agent) | Guided creation of agents from templates, including the code-host connection (GitHub App / GitLab / Gitea) and integration installs the agent needs. Templates: `code-reviewer`, `lead`, `coder`, `qa`. |
| [`agentconnect-create-team`](skills/agentconnect-create-team)   | Guided creation of a team of agents from a team template, one card for the whole team, each member created through `agentconnect-create-agent`. Teams: `engineering-pod` (lead + coder + qa + reviewer on one repository, coordinated through a Linear team or GitHub Issues). |
