# Invoice Manager Agent - Quick Start Guide

## 🚀 Get Started in 5 Minutes

This guide will help you deploy the Invoice Manager Agent system to your watsonx Orchestrate instance quickly.

## Prerequisites

Before you begin, ensure you have:

- ✅ Access to a watsonx Orchestrate instance
- ✅ IBM watsonx Orchestrate ADK Python SDK installed (`pip install ibm-watsonx-orchestrate`)
- ✅ Python 3.8 or higher
- ✅ Appropriate permissions to import agents and tools

## Option 1: Automated Deployment (Recommended)

### Using Bash Script

```bash
cd Invoice_Manager_Agent_Export
chmod +x DEPLOY.sh
./DEPLOY.sh
```

### Using Python Script

```bash
cd Invoice_Manager_Agent_Export
python deploy.py
```

That's it! The scripts will automatically import all tools and agents in the correct order.

## Option 2: Manual Deployment

If you prefer to deploy manually or need more control:

### Step 1: Navigate to the export directory

```bash
cd Invoice_Manager_Agent_Export/invoice_manager_agent_full_export
```

### Step 2: Import Tools (in any order)

```bash
ibm-watsonx-orchestrate tool import --path tools/erp_lookup_customer
ibm-watsonx-orchestrate tool import --path tools/erp_get_item_master
ibm-watsonx-orchestrate tool import --path tools/erp_get_aka_crossref
ibm-watsonx-orchestrate tool import --path tools/erp_get_pricing_tiers
ibm-watsonx-orchestrate tool import --path tools/erp_check_moq
ibm-watsonx-orchestrate tool import --path tools/erp_update_price_tier
ibm-watsonx-orchestrate tool import --path tools/erp_approve_sales_order
ibm-watsonx-orchestrate tool import --path tools/erp_place_order_on_hold
ibm-watsonx-orchestrate tool import --path tools/notify_human_escalation
ibm-watsonx-orchestrate tool import --path tools/Agentic_workflow_3334K2
```

### Step 3: Import Collaborator Agents (must be in this order)

```bash
ibm-watsonx-orchestrate agent import --path agents/native/customer_identity_agent.yaml
ibm-watsonx-orchestrate agent import --path agents/native/quote_validation_agent.yaml
ibm-watsonx-orchestrate agent import --path agents/native/pricing_agent.yaml
ibm-watsonx-orchestrate agent import --path agents/native/sales_order_agent.yaml
```

### Step 4: Import Main Agent

```bash
ibm-watsonx-orchestrate agent import --path agents/native/invoice_manager_agent.yaml
```

## Verify Deployment

After deployment, verify everything is working:

### Using CLI

```bash
# List all agents
ibm-watsonx-orchestrate agent list

# List all tools
ibm-watsonx-orchestrate tool list
```

### Using Python

```python
from ibm_watsonx_orchestrate import APIClient

client = APIClient()

# List agents
agents = client.agents.list_agents()
print(f"Found {len(agents.native)} native agents")

# List tools
tools = client.tools.list_tools()
print(f"Found {len(tools)} tools")
```

## Test the Agent

### Quick Test via CLI

```bash
ibm-watsonx-orchestrate agent chat invoice_manager_agent "Show me the format to paste a quote"
```

### Test via Python

```python
from ibm_watsonx_orchestrate import AgentRuntime

# Initialize the agent
agent = AgentRuntime("invoice_manager_agent")

# Test with a sample quote
quote = """
Quote #: Q-2024-001
Customer: Acme Corp
Address: 123 Main St, Springfield
Line Items:
1. Part: 8381000, Qty: 750, Unit Price: $7,125.00
"""

response = agent.chat(quote)
print(response)
```

### Test via watsonx Orchestrate UI

1. Log in to your watsonx Orchestrate instance
2. Navigate to the Agents section
3. Find "Invoice - Manager Agent"
4. Click to open the chat interface
5. Try one of the starter prompts or paste a quote

## What's Next?

After successful deployment:

1. **Customize ERP Integration**
   - Each tool uses mock ERP data in `data/mock_erp.py`
   - Replace with your actual ERP API calls
   - Update connection credentials

2. **Adjust Business Rules**
   - Customer confidence threshold (default: 70%)
   - Price variance tolerance (default: ±2%)
   - MOQ rules (defined in ERP data)

3. **Test with Real Data**
   - Start with test quotes
   - Verify all 4 phases work correctly
   - Check escalation workflows

4. **Deploy to Production**
   - Test thoroughly in development
   - Update to production ERP endpoints
   - Configure monitoring and alerts

## Troubleshooting

### "Tool not found" error
- Ensure all tools are imported before importing agents
- Check tool names match exactly

### "Agent import failed"
- Import collaborator agents before the main agent
- Verify all dependencies are met

### "Connection error"
- Check your watsonx Orchestrate credentials
- Verify network connectivity
- Ensure you have proper permissions

## Getting Help

- **Documentation**: See [README.md](README.md) for detailed information
- **Tool Docs**: Each tool has its own README in `tools/[tool_name]/`
- **Architecture**: See `docs/ARCHITECTURE.md` in each tool directory
- **Test Cases**: See `tests/TEST_USE_CASES.md` in each tool directory

## Summary

You've successfully deployed the Invoice Manager Agent! 🎉

The system includes:
- ✅ 1 Main orchestrator agent
- ✅ 4 Collaborator agents
- ✅ 10 Tools
- ✅ Complete documentation
- ✅ Test cases and examples

Start processing quotes and let the automation begin!