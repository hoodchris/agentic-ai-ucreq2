---
title: "Agentic AI Use Cases and Requirements"
abbrev: "agentic-ai-ucreq"
docname: draft-agentic-ai-usecases-requirements-latest
category: info
ipr: trust200902
# area: Applications and Real-Time
submissiontype: IETF
keyword: Internet-Draft

stand_alone: yes
pi:
  toc: yes
  sortrefs: yes
  symrefs: yes

author:
 -
    fullname: Tirumaleswar Reddy
    organization: Nokia
    city: Bangalore
    region: Karnataka
    country: India
    email: "kondtir@gmail.com"
 -
    ins: Z. Sarker
    name: Zaheduzzaman Sarker
    organization: Nokia
    email: zaheduzzaman.sarker@nokia.com


informative:
  A2A:
    title: "Agent2Agent Protocol Specification"
    target: https://a2a-protocol.org/latest/specification/
    date: false

  MCP:
    title: "Model Context Protocol Specification"
    target: https://modelcontextprotocol.io/specification/2025-11-25
    date: false

  RFC6749:
    title: "The OAuth 2.0 Authorization Framework"
    author:
      name: Hardt, D.
    target: https://www.rfc-editor.org/rfc/rfc6749
    date: October 2012

  RFC9635:
    title: "Grant Negotiation and Authorization Protocol (GNAP)"
    author:
      - name: Richer, J.
      - name: Granville, F.
    target: https://www.rfc-editor.org/rfc/rfc9635
    date: September 2024

  SCRM:
    title: "Agentic AI Use Cases"
    author:
      name: Schott, R. et al.
    target: https://datatracker.ietf.org/doc/draft-scrm-aiproto-usecases
    date: March 2026

  ROSENBERG:
    title: "Framework, Use Cases and Requirements for AI Agent Protocols"
    author:
      - name: Rosenberg, J.
      - name: Jennings, C.
    target: https://datatracker.ietf.org/doc/draft-rosenberg-aiproto-framework
    date: October 2025

  YAO:
    title: "Problem Space Analysis of AI Agent Protocols in IETF"
    author:
      name: Yao, K. et al.
    target: https://datatracker.ietf.org/doc/draft-yao-catalist-problem-space-analysis
    date: March 2026

  SONG:
    title: "Problem Statement and Requirements for Dynamic Multi-agent Secured Collaboration"
    author:
      name: Song, et al.
    target: https://datatracker.ietf.org/doc/draft-song-dmsc-problem-statement
    date: March 2026

--- abstract

This document describes use cases for agentic AI communication systems and derives
protocol requirements from those use cases.  The requirements are
intended to guide IETF standardization work on protocols in the context of
agent-to-agent communication, agent-to-tool communication, and agent
identity and authorization, including development of a protocol
framework for agentic AI communication systems.

--- middle

# Introduction

An AI agent is an autonomous, adaptive intelligent software system
that uses AI models to complete a specific objective on behalf of a
human user or another AI agent. It makes decisions, executes actions,
and interacts with other agents through tasks and tools. Unlike
traditional software workloads that follow fixed execution paths, an
AI agent dynamically determines at run time which actions to take,
which tools to invoke, and which agents to collaborate with, based on
reasoning over its goals and context.

This document presents use cases that illustrate the key interaction
patterns of agentic communication AI systems, and derives protocol requirements from
those use cases.  The requirements are intended to drive development
of protocols, a protocol framework, and security specifications for
agentic AI systems at the IETF.

The use cases are organized into two groups. Group A covers
Human-to-Agent interaction patterns, where a human user initiates
and directs tasks. Group B covers Agent-to-Agent interaction
patterns, where agents collaborate with each other to complete
tasks. This document takes into account related use case and problem
statement documents including [SCRM], [YAO], [SONG], and [ROSENBERG],
and existing protocol work including [A2A] and [MCP].

# Terminology

**AI Agent**: An autonomous software entity that perceives its
environment, maintains internal state, and executes actions to achieve
specified goals, potentially including communication with other agents
or invocation of external tools.

**Orchestrator Agent**: An agent that coordinates the activity of other
agents by decomposing goals into sub-tasks and delegating those
sub-tasks to appropriate peer agents.

**A2A (Agent-to-Agent) Communication**: Direct or brokered
communication between two or more AI agents, as distinguished from
communication between an agent and a human end-user or a conventional
web service.

**Tool**: external services invoked by the agent to retrieve
  data or perform operations.

**Tool Agent**: An agent that serves as a proxy or mediator for
external tools, APIs, databases, or other resources that other agents
require but cannot directly access.

**Session**: A logical communication context shared between two or more
agents over a period of time, which may persist across multiple
individual message exchanges and network connections.

**Task**: TBD

**Client Agent**: TBD

**Peer Agent**: TBD

# Use Cases

## Group A: Human-to-Agent Interaction

## Simple Single-Agent Task

### Description

A user submits a task to an AI agent via a client application.  The
agent executes the task by invoking one or more tools and returns
results to the user. The tools invoked by the agent may reside in
the same or a different administrative domain.

### Actors

- User: the human who initiates and directs the task via a client
  application.

- Agent: an autonomous software service that receives the task,
  invokes tools, and returns results.

- Tool(s): external services invoked by the agent to retrieve
  data or perform operations.

### Interaction Flow

~~~
+-------------+                      +-----------+
| User Client |<-------------------->|   Agent   |
+-------------+   Agent Protocol     |           |
                                     |           |
                                     +-----------+
                                           |
                                    Tool   |
                                Invocation |
                                 Protocol  |
                                           v
                                      +--------+
                                      | Tool(s)|
                                      +--------+
~~~

### Agent Protocol Requirements

| REQ-ID  | Description |
|---------|-------------|
| A1-SP-1 | The agent protocol is required to be a standard application-layer protocol that allows any client to communicate with any agent service. |
| A1-SP-2 | The agent protocol is required to support incremental streaming of agent output, allowing partial results to be delivered to the client before the agent has completed processing. |
| A1-SP-3 | The protocol is required to define a task cancellation message that the client can issue at any point during task execution. |
| A1-SP-4 | The protocol is required to define structured error message types that distinguish at minimum: transport failure, authentication failure, authorization failure, tool invocation failure, and agent processing failure. |

### Tool Invocation Protocol Requirements

| REQ-ID  | Description |
|---------|-------------|
| A1-TI-1 | A protocol is required to be defined for agent-to-tool invocation, including request, response, and error message types. |
| A1-TI-2 | The protocol is required to support concurrent invocation of multiple tools within a single agent task, where tools may be operated by distinct providers across different administrative domains, each with independent authentication and authorization requirements. |
| A1-TI-3 | The protocol is required to define error message types for tool invocations that distinguish at minimum: tool unavailability, authorization failure, timeout, and tool-internal error. |

### Identity, Authentication, and Delegation Requirements

| REQ-ID   | Description |
|----------|-------------|
| A1-IAD-1 | The agent protocol is required to support mutual authentication between the user client and the agent. |
| A1-IAD-2 | The tool invocation protocol is required to support mutual authentication between the agent and the tool provider, ensuring the agent authenticates to the tool provider and the tool provider authenticates to the agent. |
| A1-IAD-3 | A delegation mechanism is required to be defined by which an agent presents to a tool provider a credential attesting the user's authorization for the requested tool access, without exposing the user's primary credentials. This mechanism may be based on or extend an existing authorization framework such as OAuth 2.0 [RFC6749] or GNAP [RFC9635]. |
| A1-IAD-4 | All agent and tool invocation protocol traffic is required to be encrypted and integrity-protected in transit. |

## Multimodal Human-to-Agent Interaction

### Description

A user interacts with an AI agent using one or more modalities
such as text, audio, or video.  The agent protocol is required
to support multiple modalities and allow the client and agent to
negotiate which modalities are active.  All requirements from
Section 2.2 apply to this use case.

### Additional Agent Protocol Requirements

| REQ-ID  | Description |
|---------|-------------|
| A2-SP-1 | The agent protocol is required to support multiple input and output modalities. |
| A2-SP-2 | The agent protocol is required to support modality negotiation at session setup, allowing the client and agent to agree on which modalities are active for the session. |

## Long-Running Delegated Task with Human Authorization

### Description

A user delegates a long-running task to an agent.  The agent executes
the task autonomously and sends progress notifications to the user.
At certain points the agent pauses and requests explicit human
authorization before proceeding.  All requirements from Section 2.2
apply to this use case.

### Additional Agent Protocol Requirements

| REQ-ID  | Description |
|---------|-------------|
| A3-SP-1 | The protocol is required to support agent-initiated notifications to the user during task execution. |
| A3-SP-2 | The protocol is required to define a human authorization request message by which the agent pauses task execution and requests explicit user approval before proceeding. The message is required to include sufficient context for the user to make an informed decision, including the action to be taken and its potential consequences. |
| A3-SP-3 | The protocol is required to define the valid responses to a human authorization request, including at minimum: approve, deny, and approve with modified parameters. The protocol is required to support a response timeout, after which the agent treats the request as denied and halts the affected subtask. |

## Deep Search and Agentic Retrieval

### Description

A user submits a query to an agent that requires retrieval of
information from multiple external knowledge sources. The agent
iteratively invokes retrieval tools, evaluating intermediate
results and refining queries until sufficient information has been
gathered to produce a grounded response. The agent then synthesizes
the retrieved information and returns the result to the user. All
requirements from Section 2.2 apply to this use case.

This use case is described in [SCRM].

### Interaction Flow

~~~
+-------------+                      +-----------+
| User Client |<-------------------->|   Agent   |
+-------------+   Agent Protocol     |           |
                                     +-----------+
                                         |   ^
                              Query-1..N |   | Results-1..N
                                         v   |
                                     +-----------+
                                     | Retrieval |
                                     |  Tool(s)  |
                                     +-----------+
~~~

### Additional Tool Invocation Protocol Requirements

| REQ-ID   | Description |
|----------|-------------|
| A4-TI-1  | The tool invocation protocol is required to support bulk transfer of large retrieval results from a retrieval tool to the agent. |

## Stateful Multi-Turn Agent Session

### Description

A user interacts with an agent across multiple turns over time,
where the agent maintains context from prior interactions,
including previous tasks, intermediate results, and user
preferences, and uses that context to inform subsequent
responses. The session may be interrupted and resumed at a later
point from the same client. All requirements from Section 2.2
apply to this use case.

### Interaction Flow

~~~
+-------------+                      +-----------+
| User Client |<-------------------->|   Agent   |
+-------------+   Agent Protocol     |           |
                  Turn-1             +-----------+
                  Turn-2
                  ...
                  [interruption]
                  Turn-N (resumed)
~~~

### Additional Agent Protocol Requirements

| REQ-ID   | Description |
|----------|-------------|
| A5-SP-1  | The protocol is required to support persistent session identifiers that survive client disconnection, and is required to define a session resumption message by which a client re-attaches to an interrupted session with the agent restoring the prior interaction context. |

## Group B: Agent-to-Agent Interaction

## Orchestrator and Subagent Collaboration

### Description

An orchestrator agent decomposes a task and delegates subtasks
asynchronously to one or more subagents.  Each subagent executes
its subtask independently and reports results back to the
orchestrator.  The orchestrator aggregates the results and continues
task execution.

### Actors

- Orchestrator: an agent that decomposes a task, delegates
  subtasks to subagents, and aggregates results.

- Subagent: an agent that receives a delegated subtask,
  executes it, and returns results to the orchestrator.

### Interaction Flow

~~~
+---------------+                        +------------+
| Orchestrator  |---Task Delegation----->| Subagent-1 |
|               |<--Result Reporting-----|            |
|               |                        +------------+
|               |
|               |                        +------------+
|               |---Task Delegation----->| Subagent-2 |
|               |<--Result Reporting-----|            |
+---------------+                        +------------+
~~~

### Agent-to-Agent Protocol Requirements

| REQ-ID   | Description |
|----------|-------------|
| B1-AA-1  | A protocol is required to be defined for an orchestrator to delegate a subtask to a subagent, including task delegation and acknowledgement message types. |
| B1-AA-2  | The protocol is required to support asynchronous delegation, allowing the orchestrator to delegate to multiple subagents without waiting for each to complete before proceeding. |
| B1-AA-3  | The protocol is required to define a result reporting message by which a subagent returns its completed output to the orchestrator. |
| B1-AA-4  | The protocol is recommended to support streaming of intermediate results from the subagent to the orchestrator during task execution. |
| B1-AA-5  | The protocol is required to define a task cancellation message that the orchestrator can send to a subagent to abort a delegated subtask. |

### Identity and Authentication Requirements

| REQ-ID   | Description |
|----------|-------------|
| B1-IAD-1 | The protocol is required to support mutual authentication between the orchestrator and each subagent. |
| B1-IAD-2 | All agent-to-agent protocol traffic is required to be encrypted and integrity-protected in transit. |

## Peer Collaborative Multi-Agent Problem Solving

### Description

A user requests a task that requires coordinated problem solving across
multiple agents, where no single agent has full authority or capability
to complete the task alone. The agent that receives the user request
dynamically discovers and delegates subtasks to peer agents based on
their advertised capabilities. Any agent may itself act as a client
and delegate further to other agents, forming a dynamic collaboration
graph. Each agent remains opaque to others, collaborating only through
the protocol interface.

This use case is described in [A2A] and [ROSENBERG].

### Actors

- User: the human principal who initiates the task via a client
  application and receives the final result.

- Client Agent: the agent that receives the user request and
  coordinates the overall task by delegating to peer agents.

- Peer Agents: AI agents that receive delegated subtasks
  and may themselves delegate further to other agents.

### Interaction Flow

~~~
+-------------+
| User Client |
+-------------+
       |
       v
+----------------+
|  Client Agent  |
+----------------+
      |         |
      v         v
 +--------+  +--------+
 |Agent-2 |  |Agent-3 |
 +--------+  +--------+
      |
      v
 +--------+
 |Agent-4 |
 +--------+
~~~

### Protocol Requirements

The protocol requirements for this use case are the same as those
defined in Section 2.8. No additional protocol requirements are
introduced.

## Cooperative Reasoning and Consensus Formation

### Description

A set of peer agents is tasked with analyzing a shared problem
independently and exchanging intermediate reasoning outputs to
converge on a collective conclusion. A coordinator agent distributes
the problem to all participating agents, collects their reasoning
outputs, and drives the convergence process across multiple rounds
until a consensus conclusion is reached. Unlike Section 2.8, all
agents work on the same problem rather than different subtasks.

### Interaction Flow

~~~
                 +--------------+
                 | Coordinator  |
                 +--------------+
                  /      |      \
                 v       v       v
           +--------+ +--------+ +--------+
           |Agent-1 | |Agent-2 | |Agent-3 |
           +--------+ +--------+ +--------+
~~~

### Protocol Requirements

The protocol requirements for this use case are the same as those
defined in Section 2.8. No additional protocol requirements are
introduced.

## Tool, Data, and API Mediation Between Agents

### Description

In many multi-agent deployments, access to external resources — APIs,
databases, enterprise systems, or hardware interfaces — is intentionally
mediated through a designated tool agent.  Other agents request the
tool agent to perform actions or retrieve data on their behalf, rather
than directly invoking external systems.  This architecture allows
access control, auditing, rate limiting, and schema normalization to be
applied uniformly at the mediation layer.

### Interaction flow

+-------------+                      +-----------+
| User Client |<-------------------->|   Agent   |
+-------------+   Agent Protocol     |           |
                                     +-----------+                
                          |----------------|
                          v 
                 +--------------+
                 |   Mediator   |
                 +--------------+
                  /      |      \
                 v       v       v
           +--------+ +--------+ +--------+
           |Agent-1 | |Tool-1 | | Agent-2 |
           +--------+ +--------+ +--------+

### Protocol Requirements
- The protocol MUST maintain a clear separation between the identity of
the requesting agent and the identity of the underlying tool or
service.  Authorization decisions MUST be made with respect to the
agent's identity, not the tool's identity, to prevent privilege
escalation.
- Error responses MUST include a machine-readable error code, a
human-readable description, and a flag indicating whether the error is
transient (and therefore safe to retry) or permanent.  The protocol
SHOULD define a standard taxonomy of error codes applicable across
tool invocations.


# Security Considerations

Security considerations are addressed throughout this document via
the Identity, Authentication, and Delegation requirements defined
for each use case.

# IANA Considerations

This document has no IANA actions.
