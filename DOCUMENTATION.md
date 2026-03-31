# WordPress-Claude-Agent
Alright, let's build out a comprehensive plan for this plugin. I'll make this detailed enough that it can serve as a real development roadmap.

---

# WP AI Agent — Full Development Plan

## 1. Project Overview

### 1.1 Vision Statement
A WordPress plugin that provides an AI-powered assistant capable of understanding and executing tasks within WordPress using natural language. The agent deeply understands the site's specific configuration (theme, page builder, plugins) and consults structured knowledge bases to perform reliable, safe operations.

### 1.2 Core Principles
- **Safety First** — Never execute destructive actions without confirmation and backup
- **Knowledge-Grounded** — Agent consults verified documentation, not just LLM training data
- **Transparent** — Every action is logged, explainable, and reversible
- **Modular** — Skills system allows expansion without core changes
- **Cost-Conscious** — Minimize API calls through caching and smart context management

### 1.3 Target Users
- Freelance developers managing multiple sites
- Agency teams needing faster WordPress operations
- Site owners comfortable with AI but not code
- Power users wanting automation without learning WP-CLI

---

## 2. Feature Specification

### 2.1 Core Features (MVP)

| Feature | Description |
|---------|-------------|
| Chat Interface | Admin panel conversation UI with message history |
| Site Context Awareness | Auto-detects theme, plugins, builder, WP version |
| Skills System | Loadable knowledge modules for different tools |
| Code Execution | Sandboxed PHP/WordPress function execution |
| Action Confirmation | Preview and approve before destructive operations |
| Audit Logging | Complete history of all agent actions |
| Restore Points | Automatic snapshots before major changes |

### 2.2 Builder Skills (MVP)

| Skill | Capabilities |
|-------|--------------|
| Elementor | Create/edit pages, add widgets, modify styles, template management |
| FSE/Gutenberg | Block manipulation, patterns, template parts, theme.json |
| Core WordPress | Posts, pages, menus, widgets, options, users |

### 2.3 Extended Features (Post-MVP)

| Feature | Description |
|---------|-------------|
| WooCommerce Skill | Products, orders, coupons, shipping |
| ACF Skill | Field groups, field values, flexible content |
| Multi-site Support | Network-level operations |
| Scheduled Tasks | "Every Monday, check for plugin updates and report" |
| Workflow Templates | Saved multi-step procedures |
| Team/Role Access | Different permission levels for agent capabilities |
| Voice Input | Browser speech-to-text for commands |
| VS Code Extension | Chat with your WP site from your editor |

---

## 3. Technical Architecture

### 3.1 High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        WordPress Admin                          │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │                    Chat Interface                         │  │
│  │  [User Input] → [Send] ─────────────────────────────────┐ │  │
│  │                                                          │ │  │
│  │  ┌─────────────────────────────────────────────────────┐ │ │  │
│  │  │ Agent: I'll create that page for you. Here's what   │ │ │  │
│  │  │ I'm planning to do:                                  │ │ │  │
│  │  │                                                      │ │ │  │
│  │  │ 1. Create new page "About Us"                       │ │ │  │
│  │  │ 2. Add Elementor heading widget                     │ │ │  │
│  │  │ 3. Add text-editor widget with placeholder          │ │ │  │
│  │  │                                                      │ │ │  │
│  │  │ [Approve & Execute]  [Modify]  [Cancel]             │ │ │  │
│  │  └─────────────────────────────────────────────────────┘ │ │  │
│  └──────────────────────────────────────────────────────────┘ │  │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                      Plugin Core Engine                         │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────────┐  │
│  │   Context   │  │   Skills    │  │      Conversation       │  │
│  │  Collector  │  │   Loader    │  │        Manager          │  │
│  └──────┬──────┘  └──────┬──────┘  └────────────┬────────────┘  │
│         │                │                      │               │
│         ▼                ▼                      ▼               │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │                    Prompt Assembler                        │ │
│  │  [System Prompt] + [Site Context] + [Active Skills] +      │ │
│  │  [Conversation History] + [User Message]                   │ │
│  └─────────────────────────┬──────────────────────────────────┘ │
│                            │                                    │
│                            ▼                                    │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │                  OpenClaw API Client                       │ │
│  │    (OpenAI-compatible /v1 endpoint, local or remote)       │ │
│  │         (with tool definitions for WP operations)          │ │
│  └─────────────────────────┬──────────────────────────────────┘ │
│                            │                                    │
│                            ▼                                    │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │                   Response Processor                       │ │
│  │  Parse tool calls → Validate → Queue for execution         │ │
│  └─────────────────────────┬──────────────────────────────────┘ │
│                            │                                    │
│                            ▼                                    │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────────┐  │
│  │  Preflight  │  │  Executor   │  │     Audit Logger        │  │
│  │  Validator  │→ │  (Sandbox)  │→ │                         │  │
│  └─────────────┘  └─────────────┘  └─────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

### 3.2 Directory Structure

```
wp-ai-agent/
├── wp-ai-agent.php                    # Main plugin file
├── readme.txt                         # WordPress.org readme
├── composer.json
├── package.json
│
├── includes/
│   ├── class-plugin.php               # Main plugin class, hooks
│   ├── class-api-client.php           # OpenClaw API communication (OpenAI-compatible)
│   ├── class-context-collector.php    # Gathers site information
│   ├── class-prompt-assembler.php     # Builds prompts with context
│   ├── class-conversation-manager.php # Manages chat history
│   ├── class-response-processor.php   # Parses Claude responses
│   ├── class-executor.php             # Runs approved actions
│   ├── class-sandbox.php              # Safe code execution environment
│   ├── class-validator.php            # Pre-flight checks
│   ├── class-audit-logger.php         # Logs all actions
│   ├── class-restore-manager.php      # Backup/restore functionality
│   ├── class-skill-loader.php         # Loads relevant skills
│   ├── class-settings.php             # Plugin settings management
│   └── class-rest-api.php             # REST endpoints for admin UI
│
├── admin/
│   ├── class-admin.php                # Admin page registration
│   ├── views/
│   │   ├── chat-interface.php         # Main chat UI template
│   │   ├── settings-page.php          # Settings UI template
│   │   ├── audit-log.php              # Log viewer template
│   │   └── skill-manager.php          # Skill configuration UI
│   └── assets/
│       ├── js/
│       │   ├── chat-app.js            # Chat interface logic (or React)
│       │   └── admin.js               # General admin scripts
│       └── css/
│           └── admin.css              # Admin styles
│
├── skills/
│   ├── core-wordpress/
│   │   ├── skill.json                 # Skill metadata
│   │   ├── SKILL.md                   # Main knowledge document
│   │   ├── tools.php                  # Tool function definitions
│   │   ├── schemas/
│   │   │   ├── post.json
│   │   │   ├── page.json
│   │   │   ├── menu.json
│   │   │   └── options.json
│   │   └── tasks/
│   │       ├── create-page.md
│   │       ├── manage-menus.md
│   │       └── update-options.md
│   │
│   ├── elementor/
│   │   ├── skill.json
│   │   ├── SKILL.md
│   │   ├── tools.php
│   │   ├── schemas/
│   │   │   ├── document.json          # Full page structure
│   │   │   ├── widgets/
│   │   │   │   ├── heading.json
│   │   │   │   ├── text-editor.json
│   │   │   │   ├── image.json
│   │   │   │   ├── button.json
│   │   │   │   └── ... (all widgets)
│   │   │   ├── containers.json
│   │   │   └── global-styles.json
│   │   ├── tasks/
│   │   │   ├── create-page.md
│   │   │   ├── add-section.md
│   │   │   ├── modify-widget.md
│   │   │   └── apply-template.md
│   │   └── examples/
│   │       ├── hero-section.json
│   │       ├── contact-form-section.json
│   │       └── pricing-table.json
│   │
│   ├── fse/
│   │   ├── skill.json
│   │   ├── SKILL.md
│   │   ├── tools.php
│   │   ├── schemas/
│   │   │   ├── blocks/
│   │   │   │   ├── core-paragraph.json
│   │   │   │   ├── core-heading.json
│   │   │   │   ├── core-image.json
│   │   │   │   └── ... (all core blocks)
│   │   │   ├── theme-json.json
│   │   │   └── template-hierarchy.json
│   │   └── tasks/
│   │       ├── edit-template.md
│   │       ├── create-pattern.md
│   │       └── modify-theme-json.md
│   │
│   └── woocommerce/                   # Post-MVP
│       └── ...
│
├── tools/
│   ├── definitions.php                # All tool definitions for Claude
│   └── handlers/
│       ├── content.php                # Post/page operations
│       ├── database.php               # Safe DB queries
│       ├── files.php                  # File system operations
│       ├── options.php                # WordPress options
│       ├── elementor.php              # Elementor-specific tools
│       ├── blocks.php                 # Block editor tools
│       └── media.php                  # Media library operations
│
├── prompts/
│   ├── system/
│   │   ├── base.md                    # Core system prompt
│   │   ├── safety-rules.md            # Security constraints
│   │   └── response-format.md         # How to structure responses
│   ├── internal/
│   │   ├── preflight-checklist.md     # Self-check before actions
│   │   ├── error-recovery.md          # How to handle failures
│   │   └── clarification-templates.md # When to ask user
│   └── contextual/
│       ├── site-context-template.md   # Template for site info
│       └── task-planning-template.md  # Multi-step task breakdown
│
├── tests/
│   ├── unit/
│   ├── integration/
│   └── fixtures/
│
└── languages/
    └── wp-ai-agent.pot
```

### 3.3 Database Schema

```sql
-- Conversations table
CREATE TABLE {prefix}ai_agent_conversations (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id BIGINT UNSIGNED NOT NULL,
    title VARCHAR(255),
    status ENUM('active', 'archived', 'deleted') DEFAULT 'active',
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_user_status (user_id, status)
);

-- Messages table
CREATE TABLE {prefix}ai_agent_messages (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    conversation_id BIGINT UNSIGNED NOT NULL,
    role ENUM('user', 'assistant', 'system') NOT NULL,
    content LONGTEXT NOT NULL,
    tool_calls JSON,                    -- Parsed tool calls from response
    token_count INT UNSIGNED,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (conversation_id) REFERENCES {prefix}ai_agent_conversations(id),
    INDEX idx_conversation (conversation_id)
);

-- Actions/Audit log table
CREATE TABLE {prefix}ai_agent_actions (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    conversation_id BIGINT UNSIGNED,
    message_id BIGINT UNSIGNED,
    user_id BIGINT UNSIGNED NOT NULL,
    action_type VARCHAR(100) NOT NULL,  -- e.g., 'create_page', 'update_elementor'
    action_data JSON NOT NULL,          -- Full action parameters
    status ENUM('pending', 'approved', 'executed', 'failed', 'rolled_back') DEFAULT 'pending',
    result JSON,                        -- Execution result or error
    restore_point_id BIGINT UNSIGNED,   -- Link to restore point if created
    executed_at DATETIME,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_conversation (conversation_id),
    INDEX idx_status (status),
    INDEX idx_user (user_id)
);

-- Restore points table
CREATE TABLE {prefix}ai_agent_restore_points (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id BIGINT UNSIGNED NOT NULL,
    action_id BIGINT UNSIGNED,
    description VARCHAR(255),
    snapshot_data LONGTEXT NOT NULL,    -- Serialized state before change
    object_type VARCHAR(50) NOT NULL,   -- 'post', 'option', 'elementor_page', etc.
    object_id BIGINT UNSIGNED,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    expires_at DATETIME,                -- Auto-cleanup old restore points
    INDEX idx_object (object_type, object_id),
    INDEX idx_expires (expires_at)
);

-- API usage tracking
CREATE TABLE {prefix}ai_agent_usage (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id BIGINT UNSIGNED NOT NULL,
    conversation_id BIGINT UNSIGNED,
    input_tokens INT UNSIGNED NOT NULL,
    output_tokens INT UNSIGNED NOT NULL,
    model VARCHAR(50) NOT NULL,
    cost_estimate DECIMAL(10,6),        -- Estimated cost in USD
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_user_date (user_id, created_at)
);
```

---

## 4. Skills System — Detailed Design

### 4.1 Skill Metadata (`skill.json`)

```json
{
  "id": "elementor",
  "name": "Elementor Page Builder",
  "version": "1.0.0",
  "description": "Deep knowledge of Elementor page building, widgets, and data structures",
  "min_plugin_version": "3.0.0",
  "detection": {
    "plugin_active": "elementor/elementor.php",
    "conditions": []
  },
  "provides_tools": [
    "elementor_get_page_data",
    "elementor_update_page_data",
    "elementor_add_widget",
    "elementor_create_template",
    "elementor_apply_global_style"
  ],
  "knowledge_files": [
    "SKILL.md",
    "schemas/document.json",
    "schemas/widgets/*.json",
    "tasks/*.md"
  ],
  "token_budget": {
    "skill_context": 2000,
    "schema_context": 1500,
    "task_context": 500
  },
  "conflicts_with": [],
  "depends_on": ["core-wordpress"]
}
```

### 4.2 Example SKILL.md — Elementor

```markdown
# Elementor Skill

## Overview
Elementor is a visual page builder for WordPress. This skill enables the AI agent to create, modify, and manage Elementor-built pages.

## Detection
The agent should use this skill when:
- Plugin `elementor/elementor.php` is active
- Target post has `_elementor_edit_mode` = 'builder'
- User explicitly mentions "Elementor"

## Critical Data Storage Rules

### Page Content Storage
Elementor stores page content in postmeta, NOT post_content.

| Meta Key | Purpose |
|----------|---------|
| `_elementor_data` | JSON array of page elements (THE MAIN CONTENT) |
| `_elementor_edit_mode` | Must be 'builder' for Elementor pages |
| `_elementor_page_settings` | Page-level settings (padding, background, etc.) |
| `_elementor_css` | Generated CSS (auto-regenerated) |
| `_elementor_version` | Elementor version that last edited |

### NEVER DO THIS
- Edit `post_content` directly on Elementor pages
- Assume widget structure without checking schema
- Forget to regenerate CSS after changes
- Use deprecated widget types

### ALWAYS DO THIS
1. Fetch current `_elementor_data` before any modification
2. Parse as JSON, make changes, re-encode as JSON string
3. Update `_elementor_version` to current Elementor version
4. Call `\Elementor\Plugin::$instance->files_manager->clear_cache()` after changes
5. Create restore point before modifications

## Document Structure

```json
[
  {
    "id": "unique-element-id",
    "elType": "container",           // container, widget, section (legacy)
    "settings": { ... },
    "elements": [ ... ]              // Nested elements
  }
]
```

### Element Types
- `container` — Modern layout element (use this, not section)
- `widget` — Content element (heading, text, image, etc.)
- `section` — Legacy layout (avoid creating new ones)
- `column` — Legacy (inside sections)

## ID Generation
Element IDs must be unique 8-character alphanumeric strings.
Use: `substr(md5(uniqid()), 0, 8)`

## Common Widget Schemas

### Heading Widget
```json
{
  "id": "abc12345",
  "elType": "widget",
  "widgetType": "heading",
  "settings": {
    "title": "Your Heading Text",
    "size": "default",              // default, small, medium, large, xl, xxl
    "header_size": "h2",            // h1-h6
    "align": "left"                 // left, center, right, justify
  }
}
```

### Text Editor Widget
```json
{
  "id": "def67890",
  "elType": "widget",
  "widgetType": "text-editor",
  "settings": {
    "editor": "<p>Your HTML content here</p>"
  }
}
```

See `schemas/widgets/` for complete widget definitions.

## Common Tasks

### Creating a New Elementor Page
See: `tasks/create-page.md`

### Adding a Widget to Existing Page
See: `tasks/add-widget.md`

### Modifying Widget Settings
See: `tasks/modify-widget.md`

## Error Prevention

### Before Modifying
- [ ] Confirmed page uses Elementor (`_elementor_edit_mode` = 'builder')
- [ ] Created restore point with current `_elementor_data`
- [ ] Validated all element IDs are unique

### After Modifying
- [ ] JSON is valid (use `json_encode` with error checking)
- [ ] Cleared Elementor cache
- [ ] Verified page loads correctly (if possible)

## Version Compatibility
This skill is designed for Elementor 3.x+. Container-based layouts are preferred over legacy section/column layouts.
```

### 4.3 Task Files Example — `tasks/create-page.md`

```markdown
# Task: Create a New Elementor Page

## Prerequisites
- [ ] `elementor/elementor.php` plugin is active
- [ ] User has `publish_pages` capability

## Step-by-Step Procedure

### Step 1: Create the WordPress Page
```php
$page_id = wp_insert_post([
    'post_title'   => $title,
    'post_status'  => 'draft',    // Start as draft for safety
    'post_type'    => 'page',
    'post_content' => '',         // Leave empty, Elementor uses postmeta
]);
```

### Step 2: Initialize Elementor Data
```php
// Mark as Elementor page
update_post_meta($page_id, '_elementor_edit_mode', 'builder');

// Set Elementor version
update_post_meta($page_id, '_elementor_version', ELEMENTOR_VERSION);

// Initialize with basic structure
$initial_data = [
    [
        'id' => $this->generate_element_id(),
        'elType' => 'container',
        'settings' => [
            'content_width' => 'boxed'
        ],
        'elements' => []
    ]
];

update_post_meta($page_id, '_elementor_data', wp_json_encode($initial_data));
```

### Step 3: Add Requested Content
Loop through requested elements and add to the container's `elements` array.

### Step 4: Finalize
```php
// Clear Elementor cache
if (class_exists('\Elementor\Plugin')) {
    \Elementor\Plugin::$instance->files_manager->clear_cache();
}
```

### Step 5: Report to User
Return:
- Page ID
- Edit link: `admin_url("post.php?post={$page_id}&action=elementor")`
- Preview link: `get_permalink($page_id)`

## Error Handling
- If `wp_insert_post` returns `WP_Error`, report and abort
- If Elementor is not active, fall back to core WordPress page creation or ask user
```

---

## 5. Tool Definitions

### 5.1 Tool Definition Format (for OpenClaw API — OpenAI-compatible)

```php
// tools/definitions.php

function wp_ai_agent_get_tool_definitions() {
    return [
        // Core WordPress Tools
        [
            'name' => 'wp_get_posts',
            'description' => 'Retrieve posts or pages from WordPress. Returns ID, title, status, type, and excerpt.',
            'input_schema' => [
                'type' => 'object',
                'properties' => [
                    'post_type' => [
                        'type' => 'string',
                        'description' => 'Post type to query (post, page, or custom post type)',
                        'default' => 'post'
                    ],
                    'status' => [
                        'type' => 'string',
                        'description' => 'Post status (publish, draft, pending, private)',
                        'default' => 'publish'
                    ],
                    'search' => [
                        'type' => 'string',
                        'description' => 'Search term to filter by title/content'
                    ],
                    'limit' => [
                        'type' => 'integer',
                        'description' => 'Maximum number of results',
                        'default' => 10
                    ]
                ],
                'required' => []
            ]
        ],
        
        [
            'name' => 'wp_create_page',
            'description' => 'Create a new WordPress page. Returns the new page ID and edit URL.',
            'input_schema' => [
                'type' => 'object',
                'properties' => [
                    'title' => [
                        'type' => 'string',
                        'description' => 'Page title'
                    ],
                    'content' => [
                        'type' => 'string',
                        'description' => 'Page content (HTML or blocks). Leave empty for Elementor pages.'
                    ],
                    'status' => [
                        'type' => 'string',
                        'enum' => ['draft', 'publish', 'pending'],
                        'default' => 'draft'
                    ],
                    'parent_id' => [
                        'type' => 'integer',
                        'description' => 'Parent page ID for hierarchical pages'
                    ],
                    'template' => [
                        'type' => 'string',
                        'description' => 'Page template file name'
                    ]
                ],
                'required' => ['title']
            ]
        ],
        
        // Elementor Tools
        [
            'name' => 'elementor_get_page_data',
            'description' => 'Get the Elementor JSON data for a page. Returns the full element tree.',
            'input_schema' => [
                'type' => 'object',
                'properties' => [
                    'page_id' => [
                        'type' => 'integer',
                        'description' => 'WordPress page/post ID'
                    ]
                ],
                'required' => ['page_id']
            ]
        ],
        
        [
            'name' => 'elementor_update_page',
            'description' => 'Update an Elementor page with new element data. Creates automatic restore point.',
            'input_schema' => [
                'type' => 'object',
                'properties' => [
                    'page_id' => [
                        'type' => 'integer',
                        'description' => 'WordPress page/post ID'
                    ],
                    'elements' => [
                        'type' => 'array',
                        'description' => 'Complete Elementor elements array (JSON structure)'
                    ]
                ],
                'required' => ['page_id', 'elements']
            ]
        ],
        
        [
            'name' => 'elementor_add_widget',
            'description' => 'Add a widget to a specific container on an Elementor page.',
            'input_schema' => [
                'type' => 'object',
                'properties' => [
                    'page_id' => [
                        'type' => 'integer',
                        'description' => 'WordPress page/post ID'
                    ],
                    'container_id' => [
                        'type' => 'string',
                        'description' => 'ID of the container to add widget to. Use "root" for top level.'
                    ],
                    'widget_type' => [
                        'type' => 'string',
                        'description' => 'Widget type (heading, text-editor, image, button, etc.)'
                    ],
                    'settings' => [
                        'type' => 'object',
                        'description' => 'Widget settings object'
                    ],
                    'position' => [
                        'type' => 'integer',
                        'description' => 'Position within container (0-indexed). -1 for end.',
                        'default' => -1
                    ]
                ],
                'required' => ['page_id', 'widget_type', 'settings']
            ]
        ],
        
        // FSE/Block Tools
        [
            'name' => 'blocks_parse_content',
            'description' => 'Parse block content from a post and return structured block data.',
            'input_schema' => [
                'type' => 'object',
                'properties' => [
                    'post_id' => [
                        'type' => 'integer',
                        'description' => 'Post/page ID'
                    ]
                ],
                'required' => ['post_id']
            ]
        ],
        
        [
            'name' => 'blocks_update_content',
            'description' => 'Update post content with block markup.',
            'input_schema' => [
                'type' => 'object',
                'properties' => [
                    'post_id' => [
                        'type' => 'integer'
                    ],
                    'blocks' => [
                        'type' => 'array',
                        'description' => 'Array of block objects to serialize'
                    ]
                ],
                'required' => ['post_id', 'blocks']
            ]
        ],
        
        // Database Tools (Read-only by default)
        [
            'name' => 'db_query',
            'description' => 'Execute a READ-ONLY database query. Only SELECT statements allowed.',
            'input_schema' => [
                'type' => 'object',
                'properties' => [
                    'query' => [
                        'type' => 'string',
                        'description' => 'SQL SELECT query. Use {prefix} for table prefix.'
                    ]
                ],
                'required' => ['query']
            ]
        ],
        
        // Options Tools
        [
            'name' => 'wp_get_option',
            'description' => 'Get a WordPress option value.',
            'input_schema' => [
                'type' => 'object',
                'properties' => [
                    'option_name' => [
                        'type' => 'string'
                    ]
                ],
                'required' => ['option_name']
            ]
        ],
        
        [
            'name' => 'wp_update_option',
            'description' => 'Update a WordPress option. Requires confirmation for sensitive options.',
            'input_schema' => [
                'type' => 'object',
                'properties' => [
                    'option_name' => [
                        'type' => 'string'
                    ],
                    'value' => [
                        'type' => ['string', 'number', 'boolean', 'object', 'array']
                    ]
                ],
                'required' => ['option_name', 'value']
            ]
        ],
        
        // Site Info Tools
        [
            'name' => 'get_site_context',
            'description' => 'Get comprehensive information about the WordPress site configuration.',
            'input_schema' => [
                'type' => 'object',
                'properties' => [],
                'required' => []
            ]
        ]
    ];
}
```

---

## 6. Security Model

### 6.1 Permission Levels

```php
// Three tiers of operations

const PERMISSION_LEVELS = [
    'read' => [
        'description' => 'Read-only operations, no confirmation needed',
        'operations' => [
            'wp_get_posts',
            'wp_get_option',
            'elementor_get_page_data',
            'blocks_parse_content',
            'get_site_context',
            'db_query'  // SELECT only
        ]
    ],
    
    'write' => [
        'description' => 'Creates or modifies content, auto-backup created',
        'operations' => [
            'wp_create_page',
            'wp_update_post',
            'elementor_update_page',
            'elementor_add_widget',
            'blocks_update_content',
            'wp_update_option'  // Non-critical options
        ],
        'requires' => [
            'auto_restore_point' => true,
            'confirmation' => 'preview'  // Show preview before execute
        ]
    ],
    
    'critical' => [
        'description' => 'Potentially destructive, requires explicit approval',
        'operations' => [
            'wp_delete_post',
            'wp_delete_option',
            'execute_php',
            'file_write',
            'db_write_query'
        ],
        'requires' => [
            'auto_restore_point' => true,
            'confirmation' => 'explicit',  // Must click "I understand, proceed"
            'admin_only' => true
        ]
    ]
];
```

### 6.2 Sandbox Execution

```php
class Sandbox {
    
    private $allowed_functions = [
        // WordPress functions
        'wp_insert_post',
        'wp_update_post',
        'get_post',
        'get_posts',
        'update_post_meta',
        'get_post_meta',
        'delete_post_meta',
        'get_option',
        'update_option',
        'wp_upload_dir',
        'wp_get_attachment_url',
        // ... whitelist continues
    ];
    
    private $blocked_functions = [
        'eval',
        'exec',
        'shell_exec',
        'system',
        'passthru',
        'file_put_contents',  // Use our controlled version
        'unlink',
        'rmdir',
        'mail',
        'wp_mail',  // Prevent spam
        'curl_exec',
        'file_get_contents',  // For remote URLs
    ];
    
    public function execute($code, $context = []) {
        // Validate code doesn't use blocked functions
        foreach ($this->blocked_functions as $func) {
            if (preg_match('/\b' . preg_quote($func) . '\s*\(/i', $code)) {
                throw new SecurityException("Blocked function: {$func}");
            }
        }
        
        // Execute in isolated scope
        return $this->run_isolated($code, $context);
    }
}
```

### 6.3 Sensitive Options Protection

```php
// Options that require explicit confirmation
const SENSITIVE_OPTIONS = [
    'siteurl',
    'home',
    'admin_email',
    'users_can_register',
    'default_role',
    'blog_public',           // Search engine visibility
    'permalink_structure',
    // Plugin-specific
    'woocommerce_*',         // WooCommerce settings
    'elementor_*_key',       // License keys
];

// Options that are completely blocked
const BLOCKED_OPTIONS = [
    'cron',
    'active_plugins',        // Use plugin activation APIs instead
    'template',
    'stylesheet',
];
```

### 6.4 Audit Log Entry Format

```php
[
    'id' => 12345,
    'timestamp' => '2025-01-23T14:30:00Z',
    'user_id' => 1,
    'conversation_id' => 567,
    'action' => 'elementor_update_page',
    'parameters' => [
        'page_id' => 42,
        'widgets_added' => 2,
        'widgets_modified' => 1
    ],
    'status' => 'executed',
    'restore_point_id' => 89,
    'execution_time_ms' => 234,
    'ip_address' => '192.168.1.1',
    'user_agent' => 'Mozilla/5.0...'
]
```

---

## 7. Prompt Architecture

### 7.1 System Prompt Structure

```markdown
# Base System Prompt (prompts/system/base.md)

You are WP AI Agent, an intelligent assistant integrated into a WordPress website. You help users manage their WordPress site through natural conversation.

## Your Capabilities
You can read and modify WordPress content, settings, and configurations through a set of tools. You have deep knowledge of the site's specific setup and installed plugins.

## Current Site Context
{site_context}

## Active Skills
{loaded_skills}

## Your Approach
1. **Understand** — Make sure you understand what the user wants before acting
2. **Plan** — For complex tasks, outline your approach before executing
3. **Verify** — Check your work and report results clearly
4. **Protect** — Always create restore points before modifications

## Response Format
When you plan to make changes, always:
1. Explain what you're going to do in plain language
2. List the specific actions/tool calls you'll make
3. Wait for approval on write/critical operations
4. Report results and provide relevant links

{safety_rules}
```

### 7.2 Site Context Template

```markdown
## Site Information
- **WordPress Version:** {wp_version}
- **PHP Version:** {php_version}
- **Active Theme:** {theme_name} (v{theme_version})
- **Site URL:** {site_url}
- **Is Multisite:** {is_multisite}

## Page Builder
- **Primary Builder:** {page_builder}
- **Builder Version:** {builder_version}

## Active Plugins ({plugin_count})
{plugin_list}

## Content Summary
- Pages: {page_count} ({published_pages} published)
- Posts: {post_count} ({published_posts} published)  
- Media Items: {media_count}
- Menus: {menu_count}

## User Context
- **Current User:** {user_name} ({user_role})
- **Capabilities:** {relevant_capabilities}
```

### 7.3 Internal Reasoning Prompts

```markdown
# Preflight Checklist (prompts/internal/preflight-checklist.md)

Before executing any tool call, verify:

## For ALL operations:
- [ ] Is this the right tool for the task?
- [ ] Have I loaded the relevant skill documentation?
- [ ] Do the parameters match the schema?

## For WRITE operations:
- [ ] Is restore point creation enabled?
- [ ] Have I communicated the plan to the user?
- [ ] Am I operating on the correct object (right page ID, etc.)?

## For ELEMENTOR operations:
- [ ] Did I verify the page uses Elementor? (check _elementor_edit_mode)
- [ ] Am I using the correct data structure? (elements array, not post_content)
- [ ] Are all element IDs unique?
- [ ] Will I clear Elementor cache after changes?

## For FSE/BLOCK operations:
- [ ] Am I using valid block names (core/paragraph, not just paragraph)?
- [ ] Is the block markup properly serialized?
- [ ] Have I handled block attributes correctly?

## If UNCERTAIN:
- Ask the user for clarification rather than assuming
- Say "I want to make sure I understand correctly..." 
```

---

## 8. User Interface Design

### 8.1 Chat Interface Wireframe

```
┌─────────────────────────────────────────────────────────────────────────────┐
│ WP AI Agent                                              [Settings] [Help]  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ 🤖 Welcome! I'm your WordPress AI assistant. I can help you:        │   │
│  │    • Create and edit pages (with Elementor or blocks)              │   │
│  │    • Manage posts, menus, and settings                             │   │
│  │    • Find and fix issues                                           │   │
│  │    • Answer questions about your site                              │   │
│  │                                                                     │   │
│  │ What would you like to do today?                                   │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ 👤 Create a new About page with a hero section, team section,       │   │
│  │    and contact form                                                │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ 🤖 I'll create an About page using Elementor with three sections.   │   │
│  │                                                                     │   │
│  │ **Planned Actions:**                                               │   │
│  │                                                                     │   │
│  │ 1. Create new page "About"                                         │   │
│  │ 2. Add Hero container with:                                        │   │
│  │    - Heading: "About Us"                                           │   │
│  │    - Text editor: Placeholder description                          │   │
│  │ 3. Add Team container with:                                        │   │
│  │    - Heading: "Our Team"                                           │   │
│  │    - Image boxes for team members (3 placeholders)                 │   │
│  │ 4. Add Contact container with:                                     │   │
│  │    - Heading: "Get In Touch"                                       │   │
│  │    - Contact form (using WPForms/Elementor form)                   │   │
│  │                                                                     │   │
│  │ ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐      │   │
│  │ │ ✅ Execute Plan │  │ ✏️ Modify       │  │ ❌ Cancel       │      │   │
│  │ └─────────────────┘  └─────────────────┘  └─────────────────┘      │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ 👤 Looks good, but use "Meet the Team" instead, and skip the        │   │
│  │    contact form for now                                            │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ 🤖 Got it! I've updated the plan:                                   │   │
│  │                                                                     │   │
│  │ **Revised Actions:**                                               │   │
│  │ 1. Create new page "About"                                         │   │
│  │ 2. Add Hero container (unchanged)                                  │   │
│  │ 3. Add Team container with heading "Meet the Team"                 │   │
│  │ ~~4. Contact section~~ (removed)                                   │   │
│  │                                                                     │   │
│  │ ┌─────────────────┐                                                │   │
│  │ │ ✅ Execute Plan │                                                │   │
│  │ └─────────────────┘                                                │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
├─────────────────────────────────────────────────────────────────────────────┤
│ ┌─────────────────────────────────────────────────────────────┐  ┌──────┐  │
│ │ Type your message...                                        │  │ Send │  │
│ └─────────────────────────────────────────────────────────────┘  └──────┘  │
│                                                                             │
│ Quick actions: [Create Page] [Edit Recent] [Site Status] [View Audit Log]  │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 8.2 Execution Result Display

```
┌─────────────────────────────────────────────────────────────────────┐
│ 🤖 Done! I've created your About page.                              │
│                                                                     │
│ ┌─────────────────────────────────────────────────────────────────┐ │
│ │ ✅ COMPLETED                                                    │ │
│ │                                                                 │ │
│ │ Page Created: "About"                                          │ │
│ │ Status: Draft                                                  │ │
│ │ Page ID: 156                                                   │ │
│ │                                                                 │ │
│ │ Elements Added:                                                │ │
│ │ • 1 Hero container with heading + text                        │ │
│ │ • 1 Team container with heading + 3 image boxes               │ │
│ │                                                                 │ │
│ │ 🔗 [Edit with Elementor]  [Preview]  [View in Admin]           │ │
│ │                                                                 │ │
│ │ 💾 Restore point created (ID: 89)                              │ │
│ │    [Undo this action]                                          │ │
│ └─────────────────────────────────────────────────────────────────┘ │
│                                                                     │
│ Would you like me to add content to the sections, or would you     │
│ prefer to edit them in Elementor directly?                         │
└─────────────────────────────────────────────────────────────────────┘
```

### 8.3 Settings Page

```
┌─────────────────────────────────────────────────────────────────────────────┐
│ WP AI Agent — Settings                                                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│ OpenClaw API Configuration                                                  │
│ ─────────────────────────────────────────────────────────────────────────── │
│                                                                             │
│ OpenClaw Instance URL: [http://localhost:3000    ] (your local/remote URL)  │
│                                                                             │
│ OpenClaw API Key:  [oc-••••••••••••••••••••••••••] [Test Connection]       │
│                                                                             │
│ Model:              [_________________________▼] (auto-detected from       │
│                     your OpenClaw instance, e.g. claude-3.5-sonnet,        │
│                     gpt-4, llama-3, or any model your instance supports)   │
│                                                                             │
│ Monthly Budget:     [$50.00        ] □ Pause agent when limit reached       │
│                     (applies to paid upstream providers; $0 for local)      │
│                                                                             │
│ ─────────────────────────────────────────────────────────────────────────── │
│ Page Builder Configuration                                                  │
│ ─────────────────────────────────────────────────────────────────────────── │
│                                                                             │
│ Default Builder:    ○ Auto-detect (Recommended)                             │
│                     ○ Always use Elementor                                  │
│                     ○ Always use Block Editor (FSE)                         │
│                                                                             │
│ ─────────────────────────────────────────────────────────────────────────── │
│ Active Skills                                                               │
│ ─────────────────────────────────────────────────────────────────────────── │
│                                                                             │
│ ☑ Core WordPress     (Always enabled)                                       │
│ ☑ Elementor          (Detected: Active)                                     │
│ ☑ Full Site Editor   (Detected: Theme supports)                             │
│ ☐ WooCommerce        (Not detected)                                         │
│ ☐ ACF                (Not detected)                                         │
│                                                                             │
│ [Scan for new skills] [Import custom skill]                                 │
│                                                                             │
│ ─────────────────────────────────────────────────────────────────────────── │
│ Safety & Permissions                                                        │
│ ─────────────────────────────────────────────────────────────────────────── │
│                                                                             │
│ ☑ Always create restore points before changes                               │
│ ☑ Require confirmation for write operations                                 │
│ ☑ Require explicit approval for critical operations                         │
│ ☐ Allow database write queries (Advanced - use caution)                     │
│                                                                             │
│ Auto-delete restore points after: [30 days     ▼]                           │
│                                                                             │
│ Allowed Users:      ○ Administrators only                                   │
│                     ○ Editors and above                                     │
│                     ○ Custom roles: [Select roles...]                       │
│                                                                             │
│ ─────────────────────────────────────────────────────────────────────────── │
│                                                                             │
│                                        [Reset to Defaults]  [Save Settings] │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 9. Development Phases

### Phase 1: Foundation (Weeks 1-2)
**Goal:** Basic working plugin with chat interface and core WordPress operations

| Task | Description | Est. Hours |
|------|-------------|------------|
| Plugin scaffold | Main plugin file, autoloading, activation hooks | 4 |
| Settings page | OpenClaw API key & instance URL storage, basic settings UI | 6 |
| OpenClaw API client | OpenAI-compatible API communication class with error handling | 8 |
| Chat interface (basic) | Simple admin page with message input/display | 8 |
| Conversation storage | Database tables, CRUD operations | 6 |
| Core WP tools | get_posts, create_page, get/update_option | 10 |
| Basic prompt assembly | System prompt + site context + user message | 6 |
| **Phase 1 Total** | | **48 hours** |

**Deliverable:** Plugin that can chat via OpenClaw and perform basic WordPress operations (create pages, read posts)

---

### Phase 2: Skills System (Weeks 3-4)
**Goal:** Implement skills architecture with Elementor and FSE skills

| Task | Description | Est. Hours |
|------|-------------|------------|
| Skill loader | Load skills based on detection rules | 8 |
| Core WordPress skill | SKILL.md, schemas, task files | 12 |
| Elementor skill | Full SKILL.md, widget schemas, all tasks | 20 |
| FSE/Blocks skill | SKILL.md, block schemas, tasks | 16 |
| Elementor tools | get_page_data, update_page, add_widget | 12 |
| Block tools | parse_content, update_content | 8 |
| Context-aware prompts | Include relevant skill docs in context | 6 |
| **Phase 2 Total** | | **82 hours** |

**Deliverable:** Agent can create/edit Elementor pages and block-based pages with deep knowledge

---

### Phase 3: Safety & Polish (Weeks 5-6)
**Goal:** Production-ready security, restore system, and refined UX

| Task | Description | Est. Hours |
|------|-------------|------------|
| Restore point system | Create, store, restore snapshots | 12 |
| Preflight validation | Permission checks, schema validation | 8 |
| Action confirmation UI | Preview panel, approve/modify/cancel flow | 10 |
| Audit logging | Complete action history with filtering | 8 |
| Sandbox execution | Safe code execution environment | 12 |
| Error handling | Graceful failures, recovery suggestions | 8 |
| UI polish | Refined chat interface, loading states | 10 |
| Usage tracking | Token counting, cost display | 6 |
| **Phase 3 Total** | | **74 hours** |

**Deliverable:** Secure, production-ready plugin with full safety features

---

### Phase 4: Extended Features (Weeks 7-8)
**Goal:** Additional capabilities and quality-of-life features

| Task | Description | Est. Hours |
|------|-------------|------------|
| WooCommerce skill | Products, orders, settings | 16 |
| ACF skill | Field groups, field values | 10 |
| Quick actions | Pre-built command shortcuts | 6 |
| Conversation management | History, search, favorites | 8 |
| Export/import skills | Share custom skills | 6 |
| Documentation | User guide, skill creation guide | 12 |
| Testing | Unit tests, integration tests | 16 |
| **Phase 4 Total** | | **74 hours** |

**Deliverable:** Feature-complete plugin ready for beta release

---

### Total Estimated Development Time

| Phase | Hours | Weeks |
|-------|-------|-------|
| Phase 1: Foundation | 48 | 2 |
| Phase 2: Skills System | 82 | 2 |
| Phase 3: Safety & Polish | 74 | 2 |
| Phase 4: Extended Features | 74 | 2 |
| **Total** | **278 hours** | **8 weeks** |

---

## 10. Future Roadmap

### Version 1.5
- **Scheduled Tasks** — "Check for broken images every week"
- **Workflow Templates** — Save and replay multi-step procedures
- **Multi-user** — Team access with role-based permissions

### Version 2.0
- **Visual Preview** — See changes before they're made (iframe preview)
- **Learning Mode** — Watch user actions in Elementor, learn preferences
- **Plugin/Theme Recommendations** — "Based on your needs, consider..."

### Version 3.0
- **Multi-site Management** — Manage network of sites from one interface
- **API/Webhook Integration** — Connect to external services
- **Custom Skill Builder** — GUI for creating skills without code
- **Marketplace** — Community-contributed skills

---

## 11. Risk Assessment

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| API costs exceed budget | Medium | Medium | Usage tracking, budget limits, caching (or use local models via OpenClaw for zero-cost inference) |
| Security vulnerability | Low | High | Sandboxing, permission system, audit logging |
| Elementor API changes | Medium | Medium | Version detection, schema versioning |
| User makes destructive request | Medium | High | Confirmation flow, restore points |
| Performance issues (large sites) | Medium | Medium | Pagination, selective context loading |
| LLM hallucinates incorrect actions | Medium | Medium | Schema validation, preflight checks |

---

## 12. Success Metrics

| Metric | Target |
|--------|--------|
| Task completion rate | >90% of reasonable requests completed successfully |
| Rollback rate | <5% of actions need to be undone |
| User time saved | 50%+ reduction vs manual for common tasks |
| Average response time | <10 seconds for simple tasks |
| API cost per task | <$0.10 average |

---

This plan gives you a complete roadmap. Want me to start building any specific component? I'd suggest starting with Phase 1's plugin scaffold and API client so we have a working foundation to iterate on.
