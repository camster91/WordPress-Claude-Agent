# Claude.md - WP AI Agent Development Guide

This file provides context and guidelines for AI assistants working on the WordPress Claude Agent plugin.

## Project Overview

**WP AI Agent** is a WordPress plugin that provides an AI-powered assistant capable of understanding and executing WordPress tasks through natural language. The agent deeply understands site-specific configurations (theme, page builder, plugins) and consults structured knowledge bases for reliable, safe operations.

## Core Principles

1. **Safety First** - Never execute destructive actions without confirmation and backup
2. **Knowledge-Grounded** - Consult verified documentation, not just LLM training data
3. **Transparent** - Every action is logged, explainable, and reversible
4. **Modular** - Skills system allows expansion without core changes
5. **Cost-Conscious** - Minimize API calls through caching and smart context management

## Project Structure

```
wp-ai-agent/
├── wp-ai-agent.php              # Main plugin file
├── readme.txt                   # WordPress.org readme
├── includes/                    # Core PHP classes
│   ├── class-plugin.php         # Main plugin class
│   ├── class-api-client.php     # OpenClaw API communication
│   ├── class-context-collector.php
│   ├── class-prompt-assembler.php
│   ├── class-conversation-manager.php
│   ├── class-executor.php
│   ├── class-sandbox.php
│   ├── class-validator.php
│   ├── class-audit-logger.php
│   ├── class-restore-manager.php
│   └── class-skill-loader.php
├── admin/                       # Admin interface
│   ├── class-admin.php
│   ├── views/
│   └── assets/
├── skills/                      # Modular knowledge modules
│   ├── core-wordpress/
│   ├── elementor/
│   └── fse/
├── tools/                       # Tool definitions and handlers
│   ├── definitions.php
│   └── handlers/
├── prompts/                     # System prompts and templates
│   ├── system/
│   ├── internal/
│   └── contextual/
└── tests/
```

## Development Phases

### Phase 1: Foundation (Current Priority)
- [ ] Plugin scaffold with main file, autoloading, activation hooks
- [ ] Settings page for OpenClaw API key & instance URL storage
- [ ] OpenClaw API client with error handling (OpenAI-compatible /v1 endpoint)
- [ ] Basic chat interface in admin
- [ ] Conversation storage (database tables)
- [ ] Core WordPress tools (get_posts, create_page, get/update_option)
- [ ] Basic prompt assembly

### Phase 2: Skills System
- [ ] Skill loader with detection rules
- [ ] Core WordPress skill (SKILL.md, schemas, tasks)
- [ ] Elementor skill (full implementation)
- [ ] FSE/Blocks skill
- [ ] Context-aware prompt inclusion

### Phase 3: Safety & Polish
- [ ] Restore point system
- [ ] Preflight validation
- [ ] Action confirmation UI
- [ ] Audit logging
- [ ] Sandbox execution
- [ ] Error handling and recovery

### Phase 4: Extended Features
- [ ] WooCommerce skill
- [ ] ACF skill
- [ ] Quick actions
- [ ] Conversation management
- [ ] Documentation

## Coding Standards

### PHP
- Follow WordPress Coding Standards
- Use PHP 7.4+ features (typed properties, arrow functions)
- Namespace: `WPAIAgent\`
- Prefix all functions/hooks with `wp_ai_agent_`

### JavaScript
- ES6+ syntax
- Use WordPress's built-in React for admin interfaces (optional)
- Or vanilla JS for simpler implementation

### Database
- Use `$wpdb` with prepared statements
- Table prefix: `{$wpdb->prefix}ai_agent_`
- Always use proper escaping

## Key Technical Decisions

### API Communication
- Use OpenClaw API (OpenAI-compatible /v1 endpoint)
- Supports local OpenClaw instances (e.g. http://localhost:3000) or remote deployments
- Model-agnostic: works with any LLM backend configured in OpenClaw (Claude, GPT-4, Llama, Ollama, etc.)
- Support for tool use (function calling) via OpenAI-compatible tool_calls format
- Implement retry logic with exponential backoff
- Cache responses where appropriate

### Skills System
Each skill contains:
- `skill.json` - Metadata and detection rules
- `SKILL.md` - Knowledge document for the AI
- `tools.php` - Tool function definitions
- `schemas/` - JSON schemas for data structures
- `tasks/` - Step-by-step task guides

### Security Model
Three permission levels:
1. **Read** - No confirmation needed (get_posts, get_option)
2. **Write** - Auto-backup, preview before execute (create_page, update_post)
3. **Critical** - Explicit approval required (delete_post, execute_php)

### Elementor Integration
- Data stored in `_elementor_data` postmeta (JSON)
- Never edit `post_content` directly on Elementor pages
- Always clear Elementor cache after modifications
- Use container-based layouts (not legacy sections)

## Important Files to Create First

1. `wp-ai-agent.php` - Plugin header and bootstrap
2. `includes/class-plugin.php` - Main plugin class
3. `includes/class-api-client.php` - OpenClaw API wrapper (OpenAI-compatible /v1 client)
4. `admin/class-admin.php` - Admin page registration
5. `admin/views/chat-interface.php` - Chat UI
6. `admin/views/settings-page.php` - OpenClaw instance URL + API key configuration

## Testing Approach

- Unit tests for core classes (PHPUnit)
- Integration tests for WordPress hooks
- Manual testing in WordPress environment
- Test against WordPress 6.0+

## Common Pitfalls to Avoid

1. **Don't store API keys in plain text** - Use WordPress options with encryption (OpenClaw API key + instance URL)
2. **Don't trust LLM output blindly** - Validate all tool call parameters
3. **Don't skip restore points** - Always backup before modifications
4. **Don't modify Elementor post_content** - Use `_elementor_data` meta
5. **Don't hardcode table names** - Use `$wpdb->prefix`

## Resources

- [WordPress Plugin Handbook](https://developer.wordpress.org/plugins/)
- [OpenClaw Documentation](https://docs.openclaw.ai/)
- [OpenClaw GitHub](https://github.com/openclaw/openclaw)
- [Elementor Developer Docs](https://developers.elementor.com/)
- [WordPress REST API Handbook](https://developer.wordpress.org/rest-api/)

## Getting Started

To begin development:

```bash
# Clone the repository
git clone https://github.com/camster91/WordPress-Claude-Agent.git

# Navigate to your WordPress plugins directory
cd /path/to/wordpress/wp-content/plugins/

# Symlink or copy the plugin
ln -s /path/to/WordPress-Claude-Agent ./wp-ai-agent

# Activate in WordPress admin or via WP-CLI
wp plugin activate wp-ai-agent
```

## Questions to Resolve

- [ ] Decide on React vs vanilla JS for admin UI
- [ ] Determine minimum WordPress version support
- [ ] Choose license (GPL-2.0+ recommended for WordPress.org)
- [ ] Decide on Composer autoloading vs manual includes
