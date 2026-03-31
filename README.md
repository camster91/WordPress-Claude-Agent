# WordPress Pi Agent (GlowOS Plugin)

**Bring the power of the GlowOS Agent ecosystem directly into your WordPress dashboard.**

As part of the **Mainstream Pi Coding Agent** evolution, the WordPress Pi Agent is being developed from a concept into a fully public, production-ready WordPress plugin. This tool bridges the gap between raw AI execution and the world's most popular CMS, allowing users to manage, debug, and build WordPress sites using natural language.

## 🚀 The Vision: A Public-Ready AI Plugin

We are transitioning this project from internal planning to a **WordPress.org Public Plugin Release**. 
- **Sustainable Scaling:** Incorporates our 24-hour LLM token reset model to ensure high-end AI capabilities are accessible without burning through API costs.
- **Agentic Power:** The plugin securely hooks into WordPress APIs, allowing the AI to read site context, manage plugins, adjust settings, and write safe, sandboxed code.
- **Mainstream Accessible:** No terminal required. Just a clean, chat-based UI inside the WP Admin dashboard.

## 📄 Documentation & Planning

The full architectural roadmap, feature specifications, and development plan are documented in [`DOCUMENTATION.md`](DOCUMENTATION.md).

## 🛠 Tech Stack (Planned)

- **Backend:** PHP 8+ / WordPress REST API
- **Frontend:** React / Tailwind CSS (Admin UI)
- **AI Brain:** GlowOS Engine / Pi CLI (via API integration)
- **Security:** Strict nonce verification, user capability checks, and sandboxed PHP execution.

## 📈 Roadmap to WordPress.org

- [ ] Refactor the monolithic planning document into structured PHP skeleton files.
- [ ] Implement the secure API bridge to the `glowos-broker`.
- [ ] Build the React-based Chat UI for the WordPress admin area.
- [ ] Pass the rigorous WordPress.org plugin review guidelines.
- [ ] Launch with a freemium model utilizing the daily usage reset strategy.

---
*Developed by Cameron Ashley / Nexus AI.*
