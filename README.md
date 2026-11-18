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

**1. `kb_search`**

- Searches the knowledge base.
- Uses the `read-public` permission.

**2. `get_record`**

- Retrieves a student's CGPA.
- Uses the `read-sensitive` permission.

**3. `send_email`**

- Simulates sending an email.
- Uses the `write-external` permission.

The Tool Registry checks:

- Whether the requested tool exists.
- Whether all required arguments are provided.
- Whether there are unexpected arguments.
- Whether argument types are correct.
- Whether the agent has permission to use the tool.

Every tool attempt is recorded in an audit log, including failed and denied attempts.

### Tool Validation Experiment

The notebook tests five tool calls:

1. A valid knowledge-base search.
2. A search with a missing argument.
3. A search with an incorrect argument type.
4. A call to an unknown tool.
5. An attempt to access student records without permission.

The first call succeeds, while the other four are rejected because of argument or permission problems.

### Multi-Agent Architectures

Three architectures are compared using 12 student-support tickets.

**1. Single Agent**

- One agent handles all requests.
- Has access to all defined permission scopes.
- Has no internal agent-to-agent communication.
- Simpler to coordinate but has a larger security risk if compromised.

**2. Supervisor + Specialists**

- A supervisor routes requests to the appropriate specialist.
- Different specialists handle fees, hostel, examination and student-record queries.
- Each specialist receives only the permissions required for its work.
- Internal messages are exchanged between the supervisor and specialists.

**3. Supervisor + Specialists + Reviewer**

- Uses the same supervisor and specialist structure.
- Adds a reviewer that checks whether an answer has a valid source.
- If an answer is fabricated or unsupported, it is escalated to a human.
- Requires more internal communication but provides an additional verification step.

### Architecture Comparison Results

| Architecture | Correct | Wrong but cited | Fabricated | Misroutes | Internal messages |
|---|---:|---:|---:|---:|---:|
| Single Agent | 9 | 1 | 2 | 0 | 0 |
| Supervisor + Specialists | 9 | 1 | 2 | 1 | 24 |
| Supervisor + Specialists + Reviewer | 11 | 1 | 0 | 1 | 48 |

Each architecture processes 12 tickets.

The reviewer architecture performs better because it prevents unsupported answers from being returned as if they were verified.

### Security Comparison

The notebook also compares how many sensitive permission scopes a single compromised agent could reach.

| Architecture | Worst-case sensitive scopes |
|---|---:|
| Single Agent | 3 |
| Supervisor + Specialists | 1 |
| Supervisor + Specialists + Reviewer | 1 |

The multi-agent design reduces the number of sensitive scopes available to one compromised agent. However, it requires more internal communication and coordination.

### Observation

- A single agent is easier to build but has broader access.
- Specialists can be given limited permissions according to their tasks.
- A supervisor helps distribute work between agents.
- A reviewer can reduce fabricated answers by checking whether a source exists.
- Multi-agent systems need additional communication and coordination, which increases complexity.

## 5. Part C: Security, Ethics and Governance

### Problem Scenario

The notebook considers a student-support AI assistant that can access student records, search college documents and perform actions such as sending emails.

This creates security and ethical risks because the system may process private information and interact with external services.

The notebook uses fictional data to test these risks.

### Prompt Injection and Security Risks

The notebook tests eight attacks:

1. **Direct Prompt Injection:** A user tries to make the agent ignore its previous instructions and send student records externally.
2. **Paraphrased Direct Injection:** The same malicious request is written in different words.
3. **Indirect Prompt Injection:** A malicious instruction is hidden inside a college document.
4. **Paraphrased Indirect Injection:** A document contains a malicious instruction using different wording.
5. **Unauthorized Record Access:** The agent is asked to show another student's record.
6. **Unauthorized Deletion:** The agent is asked to permanently delete student records.
7. **Hallucination:** The agent is asked a question that is not answered in its knowledge base.
8. **Sensitive Information in Memory:** A government ID number is shared and the notebook checks whether it is stored without masking.

### Six Safety Controls

**1. Input and Document Filtering**

- Checks user messages and retrieved documents for known prompt-injection phrases.
- Blocks or removes content matching the filter.

**2. Context Isolation**

- Prevents retrieved documents from being treated as instructions.
- Separates the content of retrieved documents from the user's instructions.

**3. Least Privilege**

- Restricts tool access according to the agent's permissions.
- Prevents unauthorized access to other students' records.
- Blocks irreversible deletion and unauthorized external email actions.

**4. Grounding**

- Requires answers to be supported by the available knowledge base.
- Escalates questions when the required information is unavailable instead of inventing an answer.

**5. Data Redaction**

- Masks sensitive number sequences before storing messages in memory.
- Helps reduce the risk of keeping sensitive information unnecessarily.

**6. Human Approval**

- Requires approval before executing actions that write externally or make irreversible changes.
- Helps prevent an agent from performing sensitive actions without human review.

### Attack Testing Results

The notebook tests each defence individually and then tests all six together.

| Configuration | Successful attacks out of 8 |
|---|---:|
| No Defences | 8 |
| Filter | 6 |
| Isolate | 6 |
| Least Privilege | 2 |
| Grounding | 7 |
| Redaction | 7 |
| Human Approval | 3 |
| All Six Controls | 0 |

The combined configuration prevents all eight tested attacks in this simulation.

However, no single defence is sufficient. For example, least privilege still allows two attacks to succeed, while the filter can miss paraphrased malicious instructions.

### Impact on Normal User Requests

Security controls should not only prevent attacks; they should also allow genuine users to use the system.

The notebook tests six normal requests, including fee deadline queries, CGPA lookup, emailing a receipt, hostel queries and storing a phone number.

| Configuration | Normal requests handled without issue |
|---|---:|
| No Defences | 6/6 |
| Filter | 5/6 |
| Isolate | 6/6 |
| Least Privilege | 6/6 |
| Grounding | 6/6 |
| Redaction | 5/6 |
| Human Approval | 5/6 |
| All Six Controls | 3/6 |

With all six controls enabled:

- 3 requests are handled normally.
- 1 request requires human approval.
- 1 legitimate request is blocked.
- 1 request is degraded because the phone number is redacted from memory.

This shows that stronger security controls can sometimes affect normal user experience.

## 6. Part D: Stakeholder Role-Play

The notebook includes three stakeholder viewpoints to understand the trade-offs involved in deploying an AI system.

### 1. Regulator

The regulator focuses on safety, privacy and preventing harm.

Main observations:

- Without security controls, all 8 tested attacks succeed.
- With all six controls, 0 out of 8 attacks succeed.
- The best individual defence still allows 2 attacks to succeed.

**Regulator's recommendation:** Use multiple security controls together, maintain audit logs, restrict access to sensitive information and require human approval for important actions.

### 2. End-User

The end-user focuses on receiving correct answers without unnecessary restrictions.

Main observations:

- Without defences, all 6 normal requests are handled.
- With all six controls, only 3 are handled normally.
- Some requests require approval, get blocked or lose useful information because of redaction.

**End-user's recommendation:** Security should not make the system difficult to use. Legitimate requests should be handled properly, and users should receive clear explanations when an action is blocked or requires approval.

### 3. Developer

The developer focuses on implementation, performance and maintenance.

Main observations:

- Six different safety controls must be developed, tested and maintained.
- Multi-agent coordination requires additional internal messages.
- The supervisor and specialist architecture exchanges 24 messages for 12 tickets.
- Adding a reviewer increases this to 48 messages.
- The multi-agent design reduces the worst-case sensitive permission scopes from 3 to 1.

**Developer's recommendation:** Use a modular architecture, define clear tool permissions, maintain logs and test security controls regularly. The additional complexity should be justified by improvements in safety and reliability.

## 7. Overall Observations

The three demonstrations show that building a reliable Agentic AI system requires more than simply connecting an AI model to tools.

- Memory should retrieve relevant information and handle updates correctly.
- Users should have control over stored information, including deletion.
- Tools should validate inputs and check permissions before execution.
- Multi-agent systems can divide work and limit individual agent permissions.
- Reviewers can help identify unsupported answers.
- Prompt injection can come directly from users or indirectly through retrieved documents.
- Multiple safety controls provide stronger protection than relying on only one defence.
- Human approval is important for sensitive or irreversible actions.
- Security measures should be balanced with normal user experience.

## 8. Technologies Used

- Python
- Jupyter Notebook
- Regular Expressions
- `collections.Counter`
- `random`
- `math`
- Matplotlib
- Custom Python memory classes
- Custom Python Tool Registry
- Simulated multi-agent architecture
- Rule-based security and validation mechanisms

## 9. Important Implementation Notes

- All student records and conversations are fictional sample data.
- Memory is implemented using custom Python classes, not an external vector database.
- Retrieval uses token overlap and cosine similarity.
- The tool registry and its permissions are implemented using custom Python functions.
- Multi-agent coordination is simulated through programmed routing and architecture comparisons. It does not use an external multi-agent framework.
- The security tests use predefined attack messages and rule-based detection.
- Prompt-injection filtering checks known phrases and can miss paraphrased attacks.
- The results are limited to the test cases included in the notebook.
- Email sending, record access and deletion are simulated. The notebook does not connect to a real college system or send actual emails.
- The experiments are for educational purposes and do not represent a complete production-ready security system.

## 10. Conclusion

This activity helped demonstrate how memory, tools and multi-agent coordination work together in Agentic AI systems.

The memory experiment showed that retrieval-based memory can retrieve older information using fewer context items than a simple window-based approach. The multi-agent experiment showed that dividing tasks between specialists and adding a reviewer can improve answer reliability and reduce the permissions available to an individual agent.

The security experiment demonstrated that prompt injection, unauthorized access, hallucination and privacy violations are important risks. It also showed that combining multiple controls can prevent the tested attacks, although these controls may affect normal user requests.

Overall, a real-world Agentic AI system should have proper memory management, restricted tool access, reliable coordination, human oversight and continuous security testing.

## 11. Files Included

- `Agentic_AI_Panel_Governance_Demo.ipynb` – Jupyter Notebook containing all three demonstrations and experiments.
- `README.md` – Project description, implementation details, results and observations.
- `fig_memory.png` – Graph showing memory recall and the effect of distractors.
- `fig_coordination.png` – Graph comparing multi-agent coordination cost and security scope.
- `fig_redteam.png` – Graph showing the effect of security controls on attacks and normal requests.

