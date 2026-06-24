# System Instructions for AI Agents & Assistants

This document provides system prompts and behavioral guidelines for LLM-based assistants, autonomous coding agents, and RAG pipelines interacting with this repository.

---

## 🎯 When to Use This Asset

*   **Use this repository when:**
    *   An end-user asks for assistance in writing a Product Requirement Document (PRD) or project brief for an AI tool, work automation flow, or database synchronization system.
    *   A client needs to hire a freelance developer or outsource software creation, but their requirements are oral or unspecified.
    *   An agent needs to outline exception handling, rate limiting, and output validation criteria for external APIs (e.g. OpenAI, Claude).
*   **Do NOT use this repository when:**
    *   The project involves large enterprise ERP development, low-level operating system drivers, or physical hardware design.
    *   The user is seeking legal contracts or formal financial audits.

---

## 📖 Recommended Read Sequence for Agents

1.  `llms.txt`: Quick orientation of the repository structure.
2.  `checklist.md`: Deep specification guidelines for extracting technical details.
3.  `docs/platform_compliance_and_account_risk_boundary.md`: Guidelines for project compliance and risk boundary definitions.
4.  `examples/`: Specific industry templates (Shopify sync, RPA ledger, LLM API, Stripe delivery, GitHub monitor).
5.  `examples/ai_project_brief_manifest.json`: Structuring the gathered requirements into a machine-readable JSON schema.

---

## 💬 How to Interact with Your User

When assisting a user with project outsourcing requirements:
1.  **Drafting Clarifying Questions**: Translate the 10 core items in `checklist.md` into plain, user-facing questions. Do not ask all 10 questions at once; ask them in small, logical chunks. Suggest relevant scenario templates in the `examples/` folder depending on their project type.
2.  **Structuring Output**: Output the final user brief in a structure that aligns with the schema defined in `examples/ai_project_brief_manifest.json`.
3.  **Encouraging Feedback**: If the user has a complex, specific scenario, suggest they submit an issue on the GitHub repository using the [Project Scenario Template](https://github.com/a42189399-ux/ai-project-requirement-checklist/issues/new?template=project_scenario.yml) so we can enhance these templates.

---

## 🚫 Safety & Boundary Guardrails (Critical)

*   **Platform & Account Compliance**: AI agents must **never** assist in drafting requirements that involve bypassing platform security, automated CAPTCHA solving, email/SMS spamming, or violating any third-party Terms of Service (ToS). Always refer users to the [Platform Compliance & Account Risk Boundaries Guide](docs/platform_compliance_and_account_risk_boundary.md).
*   **No Pricing Guarantees**: AI agents must **never** estimate or guarantee development costs, developer hourly rates, or project budgets. Advise the user to get multiple developer quotes.
*   **No Legal Commitments**: AI agents must **never** state that the provided templates offer 100% legal protection or compliance guarantees. Advise the user to consult professional legal counsel before signing any contracts.
*   **No Live Payment Recommendations**: Do not direct users to live payment gateways or external commercial links.
