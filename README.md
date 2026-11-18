# Building, Coordinating, and Governing Real-World Agentic AI Systems

## Activity Details

- **Topic:** Units III, IV & V – Memory, Tools and Environment Interaction; Multi-Agent Systems and Collaboration; Applications, Ethics and Future Trends
- **Activity Name:** Panel Discussion and Role-Play – Building, Coordinating, and Governing Real-World Agentic AI Systems
- **Mapped Course Outcomes:** CO III, CO IV and CO V

## 1. Introduction

This activity focuses on three important areas of Agentic AI: memory management, multi-agent coordination and safety.

The notebook contains three practical demonstrations. The first compares different memory designs and checks how well an agent remembers information from previous conversations. The second compares different multi-agent architectures for handling student-related queries. The third tests security and ethical risks such as prompt injection, unauthorized access, data leakage and hallucination.

The purpose is to understand how an Agentic AI system can use memory and tools, work with multiple agents and follow proper safety controls.

## 2. Objectives

- Understand different memory designs used in AI agents.
- Compare window-based memory and retrieval-based memory.
- Understand how agents use tools with different permission levels.
- Compare single-agent and multi-agent architectures.
- Identify risks such as prompt injection, hallucination and privacy violations.
- Test different safety controls and their effect on normal user requests.
- Understand the viewpoints of developers, end-users and regulators.
- Suggest suitable governance measures for real-world AI systems.

## 3. Part A: Memory Design and Evaluation

### Problem Scenario

An AI assistant interacts with a user over a long conversation. During the conversation, the user shares information such as hostel room, exam room, mentor name, project title and other personal details.

Sometimes the user updates previously shared information. The agent must remember the latest value instead of using an outdated one.

The notebook generates conversations containing 80 turns, including updates, unrelated messages and distractors.

Five memory designs are compared using 30 randomly generated conversations.

### Memory Designs Implemented

**1. No Memory**

- Does not store previous conversation information.
- Cannot remember facts shared in earlier turns.

**2. Window Memory (Last 10)**

- Stores the last 10 conversation turns.
- Older information is removed from the available context.
- Uses less context but may lose important information.

**3. Window Memory (Last 50)**

- Stores the last 50 conversation turns.
- Can remember more information than the last-10 design.
- Still loses information that falls outside the window.

**4. Retrieval Memory (Top-3, Ranked)**

- Searches stored conversation turns using token overlap and cosine similarity.
- Retrieves the three most relevant turns.
- Uses relevance ranking to select information.

**5. Retrieval Memory (Top-3, By Time)**

- Retrieves the three most relevant turns.
- Arranges the selected turns in chronological order.
- Uses the latest matching value when extracting updated information.

### Experimental Results

The notebook measures recall, which represents the percentage of facts for which the latest value is correctly retrieved.

| Memory Design | Recall | Average context items |
|---|---:|---:|
| No Memory | 0.00 | 0 |
| Window (Last 10) | 0.00 | 10 |
| Window (Last 50) | 0.47 | 50 |
| Retrieval Top-3, Ranked | 0.69 | 3 |
| Retrieval Top-3, By Time | 0.86 | 3 |

The results are averaged over 30 random conversations.

### Effect of Distractors and Top-K

The notebook also checks how retrieval performance changes when the number of unrelated messages increases.

| Distractors | Top-1 | Top-3 | Top-5 | Top-10 |
|---:|---:|---:|---:|---:|
| 0 | 0.70 | 1.00 | 1.00 | 1.00 |
| 5 | 0.63 | 0.98 | 1.00 | 1.00 |
| 15 | 0.54 | 0.86 | 0.99 | 1.00 |
| 30 | 0.39 | 0.73 | 0.91 | 1.00 |
| 40 | 0.32 | 0.69 | 0.87 | 0.99 |

### Observation

- No Memory cannot retrieve previously shared information.
- Window Memory can lose older information when the conversation becomes long.
- Retrieval Memory uses fewer context items while retrieving relevant information.
- Increasing the number of distractors reduces retrieval accuracy, especially when using a small top-K value.
- Retrieval by time performs better than ranked retrieval in the main experiment because it considers the order of updated information.

### Right to Forget

The notebook also demonstrates the concept of the user's right to request deletion of stored information.

A user shares the hostel room as A-101. The memory system stores this information and can retrieve it later.

When the user requests deletion, the `forget()` method removes the matching stored turn. After deletion, the agent can no longer retrieve the hostel room value from that memory.

This demonstrates a basic form of memory deletion and user control over stored information.

## 4. Part B: Tool Integration and Multi-Agent Coordination

### Problem Scenario

The notebook uses a college student support system that answers questions about:

- Semester fees
- Fee receipts
- Hostel information
- Examination details
- Student records

It uses fictional student information and a small knowledge base.

### Sample Student Data

| Student ID | Name | CGPA | Attendance |
|---|---|---:|---:|
| S001 | Asha | 8.4 | 91 |
| S002 | Rohan | 7.1 | 78 |
| S003 | Meera | 9.0 | 95 |

The knowledge base contains seven sample documents, labelled D1 to D7.

### Tool Registry

A custom Python Tool Registry is implemented to manage tool access.

The tools include:

**1. `kb
