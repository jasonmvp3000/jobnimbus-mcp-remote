# JobNimbus MCP Client
**Connect Claude Desktop to your remote JobNimbus MCP server from any computer**
This package provides an MCP (Model Context Protocol) client that connects Claude Desktop to a remote JobNimbus MCP server. No local installation or repository cloning required!
## ✨ Features
- 🔒 **Zero Storage Security**: API keys are NEVER stored on the server
- 🌐 **Multi-Client**: Supports multiple simultaneous clients
- ⚡ **Stateless**: Each request is independent
- 🚦 **Rate Limiting**: Automatic protection per client
- 🛠️ **Extensible**: Add new tools in ~10 minutes
- 🔄 **Automatic CI/CD**: Deploy with `git push`
- 📊 **Monitoring**: Health checks and sanitized logs
## 🎯 Architecture
```
Claude Desktop (API Key) → Render.com Server → JobNimbus API
         ↓                         ↓
   Local Config            Validates temporarily
   (Never leaves)          (Never stores)
```
**Key principle**: The server is a stateless proxy. API keys come from the client in each request and are cleared from memory immediately after use.
## 📦 Quick Installation
### 1. Clone and Install
```bash
cd jobnimbus-mcp-remote
npm install
```
### 2. Configure Local Environment
```bash
cp .env.example .env
```
### 3. Run Locally
```bash
npm run dev
```
Visit: http://localhost:3000/health
### 4. Deploy to Render.com
See: [docs/SETUP.md](docs/SETUP.md) for the complete deployment guide.
## 🔧 Available Tools (88 TOTAL - Optimized)
### Core CRUD (27 tools)
- **Validation**: `validate_api_key` - Validate API key
- **Jobs (7)**: `get_jobs`, `search_jobs`, `search_jobs_enhanced`, `get_job`, `search_job_notes`, `get_job_tasks`
- **Status Search (13)**: `search_jobs_by_status`, `get_leads`, `get_pending_approval`, `get_lost_jobs`, `get_in_progress`, `get_completed`, `get_paid_closed`, `get_estimating`, `get_signed_contracts`, `get_scheduled`, `get_appointments`, `get_invoiced`, `get_deposits`
- **Contacts (3)**: `get_contacts`, `search_contacts`, `create_contact`
- **Other (3)**: `get_estimates`, `get_activities`, `create_activity`, `get_calendar_activities`, `get_timeline_data`
### Analytics (35 tools)
- **Insurance & Retail (3)**: `analyze_insurance_pipeline`, `analyze_retail_pipeline`, `analyze_services_repair_pipeline`
- **Financial (6)**: `get_sales_rep_performance`, `get_performance_metrics`, `get_automated_followup`, `get_revenue_report`, `get_margin_analysis`, `analyze_revenue_leakage`, `get_profitability_dashboard`
- **Performance (2)**: `get_seasonal_trends`, `get_pipeline_forecasting`
- **Territory (5)**: `get_job_summary`, `get_optimal_door_routes`, `get_territory_heat_maps`, `get_jobs_distribution`, `get_door_knocking_scripts_by_area`, `get_seasonal_door_timing`, `get_estimates_with_addresses`
- **Productivity (9)**: `get_activities_analytics`, `get_task_management_analytics`, `get_user_productivity_analytics`, `get_lead_scoring_analytics`, `get_communication_analytics`, `get_conversion_funnel_analytics`, `get_resource_allocation_analytics`, `get_customer_satisfaction_analytics`, `get_time_tracking_analytics`
- **Business (8)**: `get_project_management_analytics`, `get_marketing_campaign_analytics`, `get_financial_forecasting_analytics`, `get_customer_segmentation_analytics`, `get_operational_efficiency_analytics`, `get_sales_velocity_analytics`, `get_competitive_analysis_analytics`
### Materials (11 tools)
- **Tracking**: `get_estimate_materials`, `analyze_material_costs`, `get_material_usage_report`, `get_material_inventory_insights`
- **Calculations**: `calculate_roofing_materials`, `calculate_siding_materials`, `estimate_materials_from_job`, `calculate_waste_factors`, `optimize_material_orders`, `get_material_specifications`, `compare_material_alternatives`
### Attachments & Business Intelligence (6 tools)
- **Attachments (4)**: `get_attachments`, `get_file_by_id`, `analyze_job_attachments`, `get_job_attachments_distribution`
- **Business (1)**: `search_insurance_jobs`
- **Invoices (1)**: `get_invoices`
### System (2 tools)
- `get_tasks`, `get_users`
### 📦 Archived/Experimental Tools
14 obsolete or non-functional tools were removed to optimize performance:
- See `/src/tools/archived/` - 11 tools with no operational value
- See `/src/tools/experimental/` - 7 tools with unverified endpoints
**Benefits**: ~40% reduction in token usage, faster tool discovery.
**Adding more**: See [docs/ADDING_TOOLS.md](docs/ADDING_TOOLS.md)
## 💻 MCP Configuration
### Option 1: Claude Code (Recommended)
1. **Configure environment variables**:
```bash
cp .env.mcp.example .env.mcp
# Edit .env.mcp with your API keys
```
2. **Load variables** (PowerShell):
```powershell
Get-Content .env.mcp | ForEach-Object {
    if ($_ -match '^([^=]+)=(.+)$') {
        [Environment]::SetEnvironmentVariable($matches[1], $matches[2], 'Process')
    }
}
```
3. **Verify**:
```
/mcp
```
See full guide: [MCP_SETUP.md](MCP_SETUP.md)
### Option 2: Claude Desktop
Location: `%APPDATA%/Claude/claude_desktop_config.json`
```json
{
  "mcpServers": {
    "jobnimbus-stamford": {
      "command": "node",
      "args": ["C:/path/to/examples/mcp-client.js"],
      "env": {
        "MCP_SERVER_URL": "https://your-server.onrender.com",
        "JOBNIMBUS_API_KEY": "your_api_key_stamford",
        "JOBNIMBUS_INSTANCE": "stamford"
      }
    }
  }
}
```
See full example: [examples/claude-desktop-config.json](examples/claude-desktop-config.json)
## 📚 Documentation
- [📖 Setup Guide](docs/SETUP.md) - Installation and deployment
- [🔌 MCP Setup](MCP_SETUP.md) - MCP configuration for Claude Code
- [🏗️ Architecture](docs/ARCHITECTURE.md) - Complete technical design
- [🛠️ Adding Tools](docs/ADDING_TOOLS.md) - How to create new tools
## 🔐 Security
### ✅ What the server DOES:
- Extracts API key from the `X-JobNimbus-Api-Key` header
- Validates API key format
- Uses the API key to call JobNimbus
- Clears the API key from memory immediately
### ❌ What the server does NOT do:
- Store API keys in a database
- Save API keys to files
- Log API keys
- Cache API keys
- Share API keys between clients
### Security Verification
```bash
# Check for hardcoded API keys (should return empty)
grep -r "api[_-]key.*=" src/
# Dependency audit
npm audit
# Security tests in CI
npm run lint
```
## 🚀 Deployment
### Automatic (Recommended)
```bash
git add .
git commit -m "feat: new feature"
git push origin main
```
GitHub Actions deploys automatically to Render.com.
### Manual
```bash
npm run build
npm run start:prod
```
## 🧪 Testing
### Health Check
```bash
curl https://your-server.onrender.com/health
```
### List Tools
```bash
curl -X POST https://your-server.onrender.com/mcp/tools/list \
  -H "X-JobNimbus-Api-Key: your_api_key" \
  -H "Content-Type: application/json"
```
### Execute Tool
```bash
curl -X POST https://your-server.onrender.com/mcp/tools/call \
  -H "X-JobNimbus-Api-Key: your_api_key" \
  -H "Content-Type: application/json" \
  -d '{"name":"get_jobs","arguments":{"size":10}}'
```
## 📊 Monitoring
### Health Check Endpoint
- `GET /health` - Server status
### Rate Limit Headers
```
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 45
X-RateLimit-Reset: 1234567890
```
### Logs
All logs are sanitized — they do not include API keys or sensitive information.
## 🛠️ Development
### Project Structure
```
jobnimbus-mcp-remote/
├── src/
│   ├── server/         # Express server
│   ├── middleware/     # Auth, rate limiting
│   ├── tools/          # MCP tools
│   ├── services/       # JobNimbus client
│   ├── config/         # Configuration
│   ├── types/          # TypeScript types
│   └── utils/          # Logger, errors
├── .github/workflows/  # CI/CD
├── docs/               # Documentation
├── scripts/            # Utility scripts
└── examples/           # Usage examples
```
### Available Commands
```bash
npm run dev          # Development with hot reload
npm run build        # Compile TypeScript
npm run start        # Run production
npm run lint         # Linter
npm run type-check   # Type checking
npm test             # Tests
```
## 🤝 Contributing
1. Fork the project
2. Create a branch: `git checkout -b feature/new-feature`
3. Commit: `git commit -m 'feat: add new feature'`
4. Push: `git push origin feature/new-feature`
5. Open a Pull Request
## 📄 License
MIT
## 🆘 Support
- **Documentation**: See the `docs/` folder
- **Issues**: GitHub Issues
- **Logs**: Render.com dashboard
## ✅ Project Status
- [x] Base structure
- [x] Express server with MCP
- [x] Security middleware
- [x] JobNimbus API client
- [x] Extensible tools system
- [x] **88 CONSOLIDATED tools** ✅ (14 archived/experimental)
- [x] GitHub Actions CI/CD
- [x] Render.com config
- [x] Complete documentation
- [x] Claude Desktop client
- [x] Factory pattern for extensibility
- [x] Redis cache integration for optimization
- [x] Tool consolidation (January 2025)
- [ ] Unit tests (next)
- [ ] Advanced metrics and monitoring (optional)
---
**Made with ❤️ for secure remote access to JobNimbus from Claude Desktop**
