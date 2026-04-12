# AtliQ HR Assist

An MCP (Model Context Protocol) server built with [FastMCP](https://github.com/jlowin/fastmcp) that exposes HR management tools to AI assistants. It manages employees, leaves, meetings, and IT tickets — all backed by in-memory storage seeded with dummy data on startup.

## Features

- **Employee Management** — Add employees, look up details, fuzzy name search
- **Leave Management** — Check balances, apply for leave, view history
- **Meeting Scheduling** — Schedule, list, and cancel meetings with conflict detection
- **IT Ticketing** — Create and track IT/procurement tickets (laptop, ID card, etc.)
- **Email Notifications** — Send emails via SMTP (Gmail supported)
- **Onboarding Prompt** — Built-in prompt to walk through full employee onboarding

## Project Structure

```
server.py              — FastMCP server entrypoint; all @mcp.tool() definitions
utils.py               — seed_services() seeds dummy data on startup
emails.py              — EmailSender helper (SMTP)
hrms/
  __init__.py          — re-exports all managers and schemas
  schemas.py           — Pydantic models (Employee, Leave, Meeting, Ticket)
  employee_manager.py  — In-memory employee store with fuzzy name search
  leave_manager.py     — Per-employee leave balance + history
  meeting_manager.py   — Per-employee meeting list with conflict detection
  ticket_manager.py    — Flat ticket list with auto-incrementing IDs
```

## Setup

### Prerequisites

- Python 3.11+
- A Gmail account (or other SMTP provider) for email functionality

### Installation

```bash
# Clone the repository
git clone <repo-url>
cd AtliQ-HR-Assist

# Create and activate virtual environment
python -m venv hrvenv
source hrvenv/bin/activate

# Install dependencies
pip install -e .
```

### Environment Variables

Create a `.env` file in the project root:

```env
email=your_gmail_address@gmail.com
password=your_app_password
```

> For Gmail, use an [App Password](https://support.google.com/accounts/answer/185833) rather than your account password.

## Running the Server

```bash
# Activate virtual environment
source hrvenv/bin/activate

# Run as MCP server over stdio (for Claude Desktop / MCP clients)
python server.py

# Run with FastMCP's dev/inspector tool
fastmcp dev server.py

# Install into Claude Desktop
fastmcp install server.py
```

## Available Tools

| Tool | Description |
|------|-------------|
| `add_employee` | Add a new employee with name, manager ID, and email |
| `get_employee_details` | Look up employee details by name (fuzzy search) |
| `send_email` | Send an email to one or more recipients |
| `create_ticket` | Raise an IT/procurement ticket for an employee |
| `update_ticket_status` | Update the status of an existing ticket |
| `list_tickets` | List tickets for an employee, optionally filtered by status |
| `schedule_meeting` | Schedule a meeting for an employee |
| `get_meetings` | List all scheduled meetings for an employee |
| `cancel_meeting` | Cancel a scheduled meeting |
| `get_employee_leave_balance` | Check remaining leave balance |
| `apply_leave` | Apply for leave on specified dates |
| `get_leave_history` | View leave history for an employee |

## Prompts

### `onboard_new_employee`

A guided prompt that walks through the full onboarding flow:
1. Adds the employee to HRMS
2. Sends a welcome email
3. Notifies the manager
4. Raises tickets for laptop, ID card, and equipment
5. Schedules an intro meeting with the manager

## Seed Data

On startup, `seed_services()` pre-populates **8 employees** across two org hierarchies:

- **Engineering** — managed by Sarah Johnson (`E001`)
- **Product** — managed by Michael Chen (`E002`)

All state is **in-memory only** — data resets on server restart.

## ID Formats

- Employee IDs: `E001`, `E002`, ...
- Ticket IDs: `T0001`, `T0002`, ...

## Dependencies

Managed via `pyproject.toml`. Runtime dependency: `fastmcp >= 2.14.4`.