---
title: "Agentic AI Use Cases and Requirements"
abbrev: "agentic-ai-ucreq"
docname: draft-agentic-ai-usecases-requirements-latest
category: info
ipr: trust200902
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

  MCP:
    title: "Model Context Protocol Specification"
    target: https://modelcontextprotocol.io/specification/2025-11-25

  RFC6749:
    title: "The OAuth 2.0 Authorization Framework"
    target: https://www.rfc-editor.org/rfc/rfc6749

  RFC9635:
    title: "Grant Negotiation and Authorization Protocol (GNAP)"
    target: https://www.rfc-editor.org/rfc/rfc9635

  SCRM:
    title: "Agentic AI Use Cases"
    target: https://datatracker.ietf.org/doc/draft-scrm-aiproto-usecases

  ROSENBERG:
    title: "Framework, Use Cases and Requirements for AI Agent Protocols"
    target: https://datatracker.ietf.org/doc/draft-rosenberg-aiproto-framework

  YAO:
    title: "Problem Space Analysis of AI Agent Protocols in IETF"
    target: https://datatracker.ietf.org/doc/draft-yao-catalist-problem-space-analysis

  SONG:
    title: "Problem Statement and Requirements for Dynamic Multi-agent Secured Collaboration"
    target: https://datatracker.ietf.org/doc/draft-song-dmsc-problem-statement

  KLRC:
    title: "AI Agent Authentication and Authorization"
    target: https://datatracker.ietf.org/doc/draft-klrc-aiagent-auth

  AUTOGEN:
    title: "AutoGen: A Framework for Multi-Agent Conversation"
    target: https://microsoft.github.io/autogen/stable/

  LANGCHAIN:
    title: "LangChain Agent Framework"
    target: https://python.langchain.com/docs/concepts/agents/

  OPENAI-AGENTS:
    title: "OpenAI Agents SDK"
    target: https://openai.github.io/openai-agents-python/

--- abstract

This document describes use cases for agentic AI communication systems
and derives protocol requirements from those use cases. The requirements
are intended to guide IETF standardization work on protocols in the
context of agent-to-agent communication, agent-to-tool communication,
and agent identity and authorization, including development of a
protocol framework for agentic AI communication systems.

--- middle

# Introduction

An AI agent is an autonomous, adaptive intelligent software system
that uses AI models to complete a specific objective on behalf of
a user or another AI agent. It makes decisions, executes actions,
and interacts with other agents through tasks and tools. Unlike
traditional software workloads that follow fixed execution paths, an
AI agent dynamically determines at run time which actions to take,
which tools to invoke, and which agents to collaborate with, based on
reasoning over its goals and context.

This document presents use cases that illustrate the key interaction
patterns of agentic AI communication systems, and derives protocol
requirements from those use cases. The requirements are intended to
drive development of protocols, a protocol framework, and security
specifications for agentic AI systems.

The use cases in this document cover interaction patterns for
agentic AI communication systems. This document takes into account
related use case and problem statement documents including [SCRM],
[YAO], [SONG], and [ROSENBERG], and existing protocol work including
[A2A] and [MCP].

# Terminology {#terminology}

**AI Agent**: An autonomous software entity that perceives its
environment, maintains internal state, and executes actions to achieve
specified goals, potentially including communication with other agents
or invocation of external tools.

**Agentic AI Communication System**: A system comprising one or more
AI agents that communicate with each other, with users, and with
external tools or services to complete tasks. The communication
interfaces between these entities are the subject of protocol
standardization in this document.

**Orchestrator Agent**: An agent that acts as a controller,
coordinating the activity of other agents by decomposing goals into
sub-tasks and delegating those sub-tasks to appropriate agents.

**A2A (Agent-to-Agent) Communication**: Direct or brokered
communication between two or more AI agents, where brokered
communication involves an intermediary agent or coordination service,
as distinguished from communication between an agent and a user or
between an agent and a tool.

**Tool**: An external service invoked by an agent to retrieve data or
perform operations.

**Tool Agent**: An agent that serves as a proxy or mediator for
external tools, APIs, databases, or other resources that other agents
require but cannot directly access.

**Session**: A logical communication context shared between two or more
agents over a period of time, which may persist across multiple
individual message exchanges and network connections.

**Task**: A unit of work submitted by a user to an agent, or
delegated by one agent to another.

**Initiating Agent**: An agent that receives an initial request and
delegates subtasks to peer agents. Any peer agent may itself delegate
further to other agents without routing through the initiating agent.

**Peer Agent**: An agent that receives delegated subtasks from another
agent and may itself delegate further to other agents.

# Common Requirements {#common-requirements}

The following baseline requirements apply to both agent-to-agent and agent-to-tool protocol interactions across all use cases and are not repeated per use case.

Each per-use-case requirement is tagged with one or more of the following protocol area tags to allow cross-use-case navigation:

- **Discovery**: Requirements related to locating, advertising, or selecting agents, tools, or capabilities.
- **Transport**: Requirements related to message delivery, streaming, cancellation, session management, and data transfer.
- **Security**: Requirements related to confidentiality, integrity, and policy enforcement.
- **Authentication**: Requirements related to identity verification and credential delegation.

| REQ-ID | Description | Tag |
|--------|-------------|-----|
| CMN-1  | Mutual authentication is required between all communicating parties. | Authentication |
| CMN-2  | All protocol traffic is required to be encrypted and integrity-protected in transit. | Security |
| CMN-3  | Structured error responses are required, distinguishing at minimum: authentication failure, authorization failure, timeout, and internal error. | Transport |
| CMN-4  | Structured error responses are required to include a policy violation type, reported by the orchestrator or mediator when an agent performs an action that exceeds or contradicts the scope delegated to it. | Security |

# Use Cases {#usecases}

## Simple Single-Agent Task {#simple-single-agent}

### Description

A user submits a task to an AI agent via a client application. The
agent executes the task by invoking one or more tools and returns
results to the user. The tools invoked by the agent may reside in
the same or a different administrative domain. The agent protocol
is required to support multiple input and output modalities, and
the client and agent are required to be able to negotiate which
modalities are active for the session.

This use case covers the protocol interface between the client
application and the agent. The interaction between the user and
the client application is out of scope. This use case assumes that
the user communicates with the agent via a client application;
direct communication between a user and an agent without an
intermediary client application is not covered in this use case.

This interaction pattern is described in [ROSENBERG] and [SCRM].

### Actors

- User: the entity that initiates and directs the task via a client
  application.

- Agent: an autonomous software service that receives the task,
  invokes tools, and returns results.

- Tool(s): external services invoked by the agent to retrieve data
  or perform operations.

### Interaction Flow

~~~
+--------------- +                       +-----------+
| App/agent      |<--------------------> |   Agent   |
+------------ ---+        Protocol       +-----------+
                                               |
                                     Protocol  |
                                               v
                                          +---------+
                                          | Tool(s) |
                                          +---------+
~~~

### Protocol Requirements {#a1-protocol-requirements}

| REQ-ID | Description | Tag |
|--------|-------------|-----|
| A1-1  | The protocol is required to allow any client to communicate with any agent service. | Discovery, Authentication |
| A1-2  | The protocol is required to support incremental streaming of agent output, allowing partial results to be delivered to the client before the agent has completed processing. | Transport |
| A1-3  | The protocol is required to define a task cancellation message that the client can issue at any point during task execution. | Transport |
| A1-4  | The protocol is required to define structured error message types that distinguish at minimum: transport failure, tool invocation failure, and agent processing failure. | Transport |
| A1-5  | The protocol is required to support multiple input and output modalities. | Transport |
| A1-6  | The protocol is required to support modality negotiation at session setup, allowing the client and agent to agree on which modalities are active for the session. | Discovery, Transport |
| A1-7  | The protocol is required to support agent-initiated notifications to the client during task execution. | Transport |
| A1-8  | The protocol is required to support concurrent invocation of multiple tools within a single agent task, where tools may be operated by distinct providers across different administrative domains, each with independent authentication and authorization requirements. | Discovery, Transport, Security |
| A1-9  | The protocol is required to support bulk transfer of large data between communicating parties, applicable to both agent-to-tool and agent-to-agent interactions. | Transport |
| A1-10 | A delegation mechanism is required to be defined by which an agent presents to a tool provider a credential attesting the authorization for the requested tool access, without exposing the client's primary credentials. This mechanism may be based on or extend an existing authorization framework such as OAuth 2.0 {{RFC6749}} or GNAP {{RFC9635}}. | Authentication |

## Orchestrator and agent Collaboration {#orchestrator-agent}

### Description

An orchestrator agent acts as a controller, decomposing a task into
subtasks and delegating them asynchronously to one or more other agents.
The orchestrator decides which other agents to invoke, sequences the
delegation, and aggregates results to continue task execution. Each
agent executes the respective subtask independently and reports results back
to the orchestrator.

It should be noted that AI models are stateless by nature — each inference
call processes only what is explicitly provided with a particular context,
with no persistent memory between calls. The application
layer is responsible for maintaining the context across the calls by
carrying conversation history, intermediate results, and task
state. Session continuity is therefore required to preserve this
accumulated context across network interruptions, ensuring that a
reconnecting agent can restore the prior task context without
having to reconstruct it from scratch.

This pattern is described in [ROSENBERG] and reflected in [A2A],
and is implemented in deployed multi-agent frameworks including
[AUTOGEN], [LANGCHAIN], and [OPENAI-AGENTS].

### Actors

- Orchestrator: an agent that acts as a controller, decomposes a
  task, delegates subtasks to agents, and aggregates results.

### Interaction Flow

~~~
+---------------------+                        +------------+
| Orchestrator Agent  |---Task Delegation----->|   Agent-1  |
|                     |<--Result Reporting-----|            |
|                     |                        +------------+
|                     |
|                     |                        +------------+
|                     |---Task Delegation----->|   Agent-2  |
|                     |<--Result Reporting-----|            |
+---------------------+                        +------------+
~~~

### Protocol Requirements {#b1-protocol-requirements}

| REQ-ID | Description | Tag |
|--------|-------------|-----|
| B1-1  | A protocol is required to be defined for an orchestrator to delegate a subtask to a agent, including task delegation and acknowledgement message types. | Transport |
| B1-2  | The protocol is required to support asynchronous delegation, allowing the orchestrator to delegate to multiple agents without waiting for each to complete before proceeding. | Transport |
| B1-3  | The protocol is required to define a result reporting message by which a agent returns its completed output to the orchestrator. | Transport |
| B1-4  | The protocol is required to support streaming of intermediate results from the agent to the orchestrator during task execution. | Transport |
| B1-5  | The protocol is required to define a task cancellation message that the orchestrator can send to a agent to abort a delegated subtask. | Transport |
| B1-6  | The protocol is required to support persistent session identifiers that survive network interruption, and is required to define a session resumption message by which an agent re-attaches to an interrupted session restoring the prior task context. | Transport |

## Long-Running Delegated Task with Authorization Checkpoint {#authz-checkpoint}

### Description

An orchestrator agent delegates a long-running task to other agents.
The delegated agents executes the task autonomously and sends progress
notifications to the orchestrator. At any certain point one or more delegated
agents pause and request explicit authorization from the orchestrator
before proceeding further. The orchestrator may relay this authorization
request to the invoker (user or agent) or resolve it autonomously based on policy.

This pattern is reflected in the In-Task Authorization mechanism
defined in [A2A].

### Interaction Flow

~~~
+---------------------+                        +------------+
| Orchestrator Agent  |---Task Delegation----->|            |
|                     |<--Progress Notif.------|            |
|                     |<--Authz Checkpoint-----|  Agent(s)  |
|                     |---Authz Response------>|            |
|                     |<--Result Reporting-----|            |
+---------------------+                        +------------+
~~~

### Additional Protocol Requirements {#b2-protocol-requirements}

| REQ-ID | Description | Tag |
|--------|-------------|-----|
| B2-1  | The protocol is required to support agent-initiated progress notifications to the delegating agent during task execution. | Transport |
| B2-2  | The protocol is required to define an authorization checkpoint message by which a agent pauses task execution and requests explicit authorization from the orchestrator before proceeding. The message is required to include sufficient context for the authorizing party to make an informed decision, including the action to be taken and its potential consequences. | Transport, Security |
| B2-3  | The protocol is required to define the valid responses to an authorization checkpoint, including at minimum: approve, deny, and approve with modified parameters. The protocol is required to support a response timeout, after which the agent treats the request as denied and halts the affected subtask. | Transport, Security |

## Peer Collaborative Multi-Agent Problem Solving {#peer-collaborative}

### Description

A task requires coordinated problem solving across multiple agents,
where no single agent has full authority or capability to complete
the task alone. The agent that receives the initial request
dynamically delegates subtasks to peer agents based on their
advertised capabilities. Any agent may itself delegate further to
other agents, forming a dynamic collaboration graph. Each agent
remains opaque to others, collaborating only through the protocol
interface.

This use case introduces multi-hop delegation chains that are not
present in {{orchestrator-agent}}. Each agent in the chain may
delegate further to other agents, and authorization scope is required
to be progressively constrained at each hop.

This use case is described in [A2A] and [ROSENBERG].

### Actors

- Initiating Agent: as defined in {{terminology}}.

- Peer Agents: AI agents that receive delegated subtasks and may
  themselves delegate further to other agents.

### Interaction Flow

~~~
+------------------+
| Initiating Agent |
+------------------+
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
defined for {{orchestrator-agent}}. No additional protocol
requirements are introduced.

### Additional Protocol Requirements {#b3-protocol-requirements}

| REQ-ID | Description | Tag |
|--------|-------------|-----|
| B3-1  | The protocol is required to support multi-hop delegation chains, where an agent that receives a delegated subtask may itself delegate further to other agents. At each hop, the delegating agent is required to present a credential that does not exceed the authorization scope of the credential it received. | Authentication, Security |
| B3-2  | The protocol is required to preserve the identity of the originating entity across all hops in the delegation chain, such that any agent in the chain can determine the identity of the entity that originally authorized the task. | Authentication, Security |
| B3-3  | The protocol is required to support transferable credentials that carry the original authorization constraints across all hops in the delegation chain. Each receiving agent is required to be able to cryptographically verify that the credential presented to it was issued by the delegating agent and that the chain of delegation traces back to the original authorization. | Authentication, Security |

## Cooperative Reasoning and Consensus Formation {#cooperative-reasoning}

### Description

A set of peer agents is tasked with analyzing a shared problem
independently and exchanging intermediate reasoning outputs to
converge on a collective conclusion. A coordinator agent distributes
the problem to all participating agents, collects their reasoning
outputs, and drives the convergence process across multiple rounds
until a consensus conclusion is reached. Unlike
{{peer-collaborative}}, all agents work on the same problem rather
than different subtasks.

Two communication topologies are possible. In the first, agents
communicate only through the coordinator, which acts as the central
hub for all message exchange. In the second, agents may also
communicate directly with each other to exchange intermediate
reasoning outputs without routing through the coordinator. The
second topology introduces the same multi-hop authorization
requirements defined in {{peer-collaborative}}.

### Interaction Flow

The coordinator-mediated topology:

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

The direct agent-to-agent topology:

~~~
                 +--------------+
                 | Coordinator  |
                 +--------------+
                 /       |        \
                v        v         v
           +-------+   +-------+   +-------+
           |Agent-1|<->|Agent-2|<->|Agent-3|
           +-------+   +-------+   +-------+
~~~

### Protocol Requirements

The protocol requirements for this use case are the same as those
defined for {{orchestrator-agent}}. No additional protocol
requirements are introduced.

## Tool, Data, and API Mediation Between Agents {#tool-mediation}

### Description

In many multi-agent deployments, access to external resources —
APIs, databases, enterprise systems, or hardware interfaces — is
intentionally mediated through a designated tool agent. Other agents
request the tool agent to perform actions or retrieve data on their
behalf, rather than directly invoking external systems. This
architecture allows access control, auditing, rate limiting, and
schema normalization to be applied uniformly at the mediation layer.

The mediator agent may also serve as an adapter between the agent
protocol and non-agent systems or other services that do not natively
support agent communication protocols, or between different agent
communication protocols such as translating between the agentic
protocol suite being developed at the IETF and existing protocols
such as [MCP] and [A2A]. In this role, the mediator is responsible
for protocol translation and for presenting the appropriate
credentials to the target system on behalf of the requesting agent.

The mediator may additionally act as a router, dispatching requests
to appropriate agents or tools based on the content and context of
the request, without requiring the requesting agent to have prior
knowledge of which agent or tool is most appropriate.

The mediator may also validate agent requests before invocation,
checking whether the action being requested matches the
authorization granted to the agent and whether execution would
cause unintended or irreversible side effects.

This pattern is reflected in the MCP server architecture defined
in [MCP] and the agent routing patterns discussed in [A2A].

### Interaction Flow

~~~
       +--------------+
       |    Agent     |
       +--------------+
              |
              v
       +--------------+
       |   Mediator   |
       +--------------+
       /       |       \
      v        v        v
+--------+ +--------+ +--------+
|Agent-1 | |Tool-1  | |Agent-2 |
+--------+ +--------+ +--------+
~~~

### Additional Protocol Requirements {#b5-protocol-requirements}

| REQ-ID | Description | Tag |
|--------|-------------|-----|
| B5-1  | The protocol is required to define error response types for request validation failure and protocol translation failure, distinct from authorization failure. A request validation failure is returned when a request is rejected due to potential unintended or irreversible side effects. | Transport, Security |

# Security Considerations {#security}

Security considerations are addressed throughout this document via
the Identity, Authentication, and Delegation requirements defined
for each use case. Agent identity and authentication mechanisms are
further discussed in [KLRC].

# IANA Considerations {#iana}

This document has no IANA actions.

# Acknowledgements
{:numbered="false"}

Thanks to Julien Maisonneuve, Parisa Foroughi, Borislava Gajic and
Sina Khatibi for the discussion and comments.
