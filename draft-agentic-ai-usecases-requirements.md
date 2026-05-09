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

  I-D.ietf-oauth-identity-chaining:
    title: "OAuth Identity and Authorization Chaining Across Domains"
    target: https://datatracker.ietf.org/doc/draft-ietf-oauth-identity-chaining

  I-D.ietf-oauth-transaction-tokens:
    title: "Transaction Tokens"
    target: https://datatracker.ietf.org/doc/draft-ietf-oauth-transaction-tokens

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

The use cases in this document cover interaction
patterns for agentic AI communication systems. This document takes
into account related use case and problem statement documents
including [SCRM], [YAO], [SONG], and [ROSENBERG], and existing
protocol work including [A2A] and [MCP].

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
sub-tasks and delegating those sub-tasks to appropriate subagents.

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
| A1-SP-5 | The agent protocol is required to support multiple input and output modalities. |
| A1-SP-6 | The agent protocol is required to support modality negotiation at session setup, allowing the client and agent to agree on which modalities are active for the session. |
| A1-SP-7 | The protocol is required to support agent-initiated notifications to the client during task execution. |

### Tool Invocation Protocol Requirements

| REQ-ID  | Description |
|---------|-------------|
| A1-TI-1 | A protocol is required to be defined for agent-to-tool invocation, including request, response, and error message types. |
| A1-TI-2 | The protocol is required to support concurrent invocation of multiple tools within a single agent task, where tools may be operated by distinct providers across different administrative domains, each with independent authentication and authorization requirements. |
| A1-TI-3 | The protocol is required to define error message types for tool invocations that distinguish at minimum: tool unavailability, authorization failure, timeout, and tool-internal error. |
| A1-TI-4 | The tool invocation protocol is required to support bulk transfer of large results from a tool to the agent. |

### Identity, Authentication, and Delegation Requirements

| REQ-ID   | Description |
|----------|-------------|
| A1-IAD-1 | The agent protocol is required to support mutual authentication between the client and the agent. |
| A1-IAD-2 | The tool invocation protocol is required to support mutual authentication between the agent and the tool provider, ensuring the agent authenticates to the tool provider and the tool provider authenticates to the agent. |
| A1-IAD-3 | A delegation mechanism is required to be defined by which an agent presents to a tool provider a credential attesting the authorization for the requested tool access, without exposing the client's primary credentials. This mechanism may be based on or extend an existing authorization framework such as OAuth 2.0 {{RFC6749}} or GNAP {{RFC9635}}. |
| A1-IAD-4 | All agent and tool invocation protocol traffic is required to be encrypted and integrity-protected in transit. |

## Orchestrator and Subagent Collaboration {#orchestrator-subagent}

### Description

An orchestrator agent acts as a controller, decomposing a task into
subtasks and delegating them asynchronously to one or more subagents.
The orchestrator decides which subagents to invoke, sequences the
delegation, and aggregates results to continue task execution. Each
subagent executes its subtask independently and reports results back
to the orchestrator. The session between the orchestrator and each
subagent is required to support persistent session identifiers and
session resumption in the event of network interruption.

This pattern is described in [ROSENBERG] and reflected in [A2A],
and is implemented in deployed multi-agent frameworks including
[AUTOGEN], [LANGCHAIN], and [OPENAI-AGENTS].

### Actors

- Orchestrator: an agent that acts as a controller, decomposes a
  task, delegates subtasks to subagents, and aggregates results.

- Subagent: an agent that receives a delegated subtask, executes it,
  and returns results to the orchestrator.

### Interaction Flow

~~~
+---------------------+                        +------------+
| Orchestrator Agent  |---Task Delegation----->| Subagent-1 |
|                     |<--Result Reporting-----|            |
|                     |                        +------------+
|                     |
|                     |                        +------------+
|                     |---Task Delegation----->| Subagent-2 |
|                     |<--Result Reporting-----|            |
+---------------------+                        +------------+
~~~

### Agent-to-Agent Protocol Requirements

| REQ-ID   | Description |
|----------|-------------|
| B1-AA-1  | A protocol is required to be defined for an orchestrator to delegate a subtask to a subagent, including task delegation and acknowledgement message types. |
| B1-AA-2  | The protocol is required to support asynchronous delegation, allowing the orchestrator to delegate to multiple subagents without waiting for each to complete before proceeding. |
| B1-AA-3  | The protocol is required to define a result reporting message by which a subagent returns its completed output to the orchestrator. |
| B1-AA-4  | The protocol is required to support streaming of intermediate results from the subagent to the orchestrator during task execution. |
| B1-AA-5  | The protocol is required to define a task cancellation message that the orchestrator can send to a subagent to abort a delegated subtask. |
| B1-AA-6  | The protocol is required to support persistent session identifiers that survive network interruption, and is required to define a session resumption message by which an agent re-attaches to an interrupted session restoring the prior task context. |

### Identity and Authentication Requirements

| REQ-ID   | Description |
|----------|-------------|
| B1-IAD-1 | The protocol is required to support mutual authentication between the orchestrator and each subagent. |
| B1-IAD-2 | All agent-to-agent protocol traffic is required to be encrypted and integrity-protected in transit. |

## Long-Running Delegated Task with Authorization Checkpoint {#authz-checkpoint}

### Description

An orchestrator agent delegates a long-running task to a subagent.
The subagent executes the task autonomously and sends progress
notifications to the orchestrator. At certain points the subagent
pauses and requests explicit authorization from the orchestrator
before proceeding. The orchestrator may relay this authorization
request or resolve it autonomously based on policy.

This pattern is reflected in the In-Task Authorization mechanism
defined in [A2A].

### Interaction Flow

~~~
+---------------------+                        +------------+
| Orchestrator Agent  |---Task Delegation----->| Subagent   |
|                     |<--Progress Notif.------|            |
|                     |<--Authz Checkpoint-----|            |
|                     |---Authz Response------>|            |
|                     |<--Result Reporting-----|            |
+---------------------+                        +------------+
~~~

### Additional Agent-to-Agent Protocol Requirements

| REQ-ID   | Description |
|----------|-------------|
| B2-AA-1  | The protocol is required to support agent-initiated progress notifications to the delegating agent during task execution. |
| B2-AA-2  | The protocol is required to define an authorization checkpoint message by which a subagent pauses task execution and requests explicit authorization from the orchestrator before proceeding. The message is required to include sufficient context for the authorizing party to make an informed decision, including the action to be taken and its potential consequences. |
| B2-AA-3  | The protocol is required to define the valid responses to an authorization checkpoint, including at minimum: approve, deny, and approve with modified parameters. The protocol is required to support a response timeout, after which the subagent treats the request as denied and halts the affected subtask. |

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
present in {{orchestrator-subagent}}. Each agent in the chain may
delegate further to other agents, and authorization scope is required
to be progressively constrained at each hop.
{{I-D.ietf-oauth-identity-chaining}} and
{{I-D.ietf-oauth-transaction-tokens}} are relevant in-progress work
in the OAuth WG.

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
defined for {{orchestrator-subagent}}. No additional protocol
requirements are introduced.

### Additional Identity and Authorization Requirements

| REQ-ID   | Description |
|----------|-------------|
| B3-IAD-1 | The protocol is required to support multi-hop delegation chains, where an agent that receives a delegated subtask may itself delegate further to other agents. At each hop, the delegating agent is required to present a credential that does not exceed the authorization scope of the credential it received. |
| B3-IAD-2 | The protocol is required to preserve the identity of the originating entity across all hops in the delegation chain, such that any agent in the chain can determine the identity of the entity that originally authorized the task. |

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
                  /      |      \
                 v       v       v
           +--------+ +--------+ +--------+
           |Agent-1 |<->|Agent-2|<->|Agent-3|
           +--------+ +--------+ +--------+
~~~

### Protocol Requirements

The protocol requirements for this use case are the same as those
defined for {{orchestrator-subagent}}. No additional protocol
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

The mediator agent may also serve as an adapter
between the agent protocol and non-agent systems or other services
that do not natively support agent communication protocols, or
between different agent communication protocols such as translating
between the agentic protocol suite being developed at the IETF and
existing protocols such as [MCP] and [A2A]. In this role, the
mediator is responsible for protocol translation and for presenting
the appropriate credentials to the target system on behalf of the
requesting agent.

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
 /      |      \
v       v       v
+--------+ +--------+ +--------+
|Agent-1 | |Tool-1  | |Agent-2 |
+--------+ +--------+ +--------+
~~~

### Additional Protocol Requirements

| REQ-ID   | Description |
|----------|-------------|
| B5-AA-1  | The protocol is required to define error response types
for request validation failure and protocol translation failure,
distinct from authorization failure. A request validation failure
is returned when a request is rejected due to potential unintended
or irreversible side effects. |

# Security Considerations {#security}

Security considerations are addressed throughout this document via
the Identity, Authentication, and Delegation requirements defined
for each use case. Agent identity and authentication mechanisms are
further discussed in [KLRC].

# IANA Considerations {#iana}

This document has no IANA actions.

# Acknowledgements
{:numbered="false"}

Thanks to Julien Maisonneuve, Parisa Foroughi, Borislava Gajic and Sina Khatibi for
the discussion and comments.
