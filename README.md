# Invoice Manager Agent - Complete Deployment Package

## Overview

This repository contains the complete **Invoice Manager Agent** (formerly EVCO Quote Processing Manager) system for watsonx Orchestrate. This is a production-ready, multi-agent system that automates invoice/quote processing workflows with customer verification, part validation, pricing analysis, and sales order processing.

## What's Included

This package contains everything needed to deploy the complete agent system:

### 🤖 Agents (5 Total)
1. **Invoice Manager Agent** (`invoice_manager_agent`) - Main orchestrator
2. **Customer Identity Agent** - Verifies customer information
3. **Quote Validation Agent** - Validates part numbers
4. **Pricing Agent** - Analyzes pricing and MOQ compliance
5. **Sales Order Agent** - Processes orders and handles exceptions

### 🛠️ Tools (9 Total)
1. **Agentic_workflow_3334K2** - Document extraction workflow
2. **erp_lookup_customer** - Customer lookup and verification
3. **erp_get_item_master** - Item master record retrieval
4. **erp_get_aka_crossref** - Part number cross-reference
5. **erp_get_pricing_tiers** - Pricing tier retrieval
6. **erp_check_moq** - Minimum order quantity validation
7. **erp_update_price_tier** - Price tier updates
8. **erp_approve_sales_order** - Order approval
9. **erp_place_order_on_hold** - Order hold management
10. **notify_human_escalation** - Human escalation notifications

## Architecture

```
Invoice Manager Agent (Orchestrator)
├── Customer Identity Agent
│   └── erp_lookup_customer
├── Quote Validation Agent
│   ├── erp_get_item_master
│   └── erp_get_aka_crossref
├── Pricing Agent
│   ├── erp_get_pricing_tiers
│   └── erp_check_moq
└── Sales Order Agent
    ├── erp_update_price_tier
    ├── erp_approve_sales_order
    ├── erp_place_order_on_hold
    └── notify_human_escalation
```

## Quick Start

### Prerequisites
- Access to watsonx Orchestrate instance
- IBM watsonx Orchestrate ADK Python SDK installed:
  ```bash
  pip install ibm-watsonx-orchestrate
  ```
- Python 3.8 or higher

### Installation Steps

1. **Extract the package**
   ```bash
   cd invoice_manager_agent_full_export
   ```

2. **Import all tools first**
   ```bash
   # Import each tool directory
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

3. **Import collaborator agents**
   ```bash
   # Import in dependency order
   ibm-watsonx-orchestrate agent import --path agents/native/customer_identity_agent.yaml
   ibm-watsonx-orchestrate agent import --path agents/native/quote_validation_agent.yaml
   ibm-watsonx-orchestrate agent import --path agents/native/pricing_agent.yaml
   ibm-watsonx-orchestrate agent import --path agents/native/sales_order_agent.yaml
   ```

4. **Import the main orchestrator agent**
   ```bash
   ibm-watsonx-orchestrate agent import --path agents/native/invoice_manager_agent.yaml
   ```

### Using Python SDK

```python
from ibm_watsonx_orchestrate import APIClient, AgentRuntime

# Initialize client
client = APIClient()

# Import tools
for tool_dir in [
    "tools/erp_lookup_customer",
    "tools/erp_get_item_master",
    # ... add all tool directories
]:
    client.tools.import_tool(kind="python", path=tool_dir)

# Import agents
for agent_file in [
    "agents/native/customer_identity_agent.yaml",
    "agents/native/quote_validation_agent.yaml",
    "agents/native/pricing_agent.yaml",
    "agents/native/sales_order_agent.yaml",
    "agents/native/invoice_manager_agent.yaml"
]:
    client.agents.import_agent(path=agent_file)

print("✅ All agents and tools imported successfully!")
```

## How It Works

### 4-Phase Processing Workflow

1. **Phase 1: Customer Verification**
   - Extracts customer information from quote
   - Verifies against ERP customer master
   - Confidence scoring (≥70% to proceed)

2. **Phase 2: Part Validation**
   - Validates part numbers (ACME or customer PNs)
   - Checks item master records
   - Verifies active status and lead times

3. **Phase 3: Pricing Analysis**
   - Retrieves ERP pricing tiers
   - Compares quote prices (±2% tolerance)
   - Validates MOQ compliance

4. **Phase 4: Order Processing**
   - Approves valid orders
   - Updates pricing if needed
   - Places holds for exceptions
   - Escalates to human review when required

### Decision Matrix

| Customer | Parts | Pricing | MOQ | Action |
|----------|-------|---------|-----|--------|
| ✓ (≥70%) | ✓ Valid | ✓ (±2%) | ✓ | Approve Order |
| ✓ (≥70%) | ✓ Valid | ✗ (>2%) | ✓ | Update Price → Approve |
| ✓ (≥70%) | ✓ Valid | ✓/✗ | ✗ | Hold + Escalate |
| ✗ (<70%) | Any | Any | Any | Hold + Escalate |
| Any | ✗ Invalid | Any | Any | Hold + Escalate |

## Usage Examples

### Processing a Quote

```python
from ibm_watsonx_orchestrate import AgentRuntime

# Initialize the agent
agent = AgentRuntime("invoice_manager_agent")

# Process a quote
quote_data = """
Quote #: Q-2024-001
Customer: Acme Corp
Address: 123 Main St, Springfield
Line Items:
1. Part: 8381000, Qty: 750, Unit Price: $7,125.00
2. Part: 8381001, Qty: 50, Unit Price: $500.00
"""

response = agent.chat(quote_data)
print(response)
```

### Expected Output

```
Quote processed successfully!

✅ Line 1 (Part 8381000): APPROVED
   - Customer verified (95% confidence)
   - Part validated: Active, 14-day lead time
   - Pricing correct (0% variance)
   - MOQ compliant

⚠️ Line 2 (Part 8381001): ON HOLD
   - MOQ violation: Ordered 50, Required 100
   - Order placed on hold
   - Operations team notified for review
```

## File Structure

```
invoice_manager_agent_full_export/
├── agents/
│   └── native/
│       ├── invoice_manager_agent.yaml             # Main orchestrator
│       ├── customer_identity_agent.yaml          # Customer verification
│       ├── quote_validation_agent.yaml           # Part validation
│       ├── pricing_agent.yaml                    # Pricing analysis
│       └── sales_order_agent.yaml                # Order processing
├── tools/
│   ├── Agentic_workflow_3334K2/                  # Document extraction
│   ├── erp_lookup_customer/                      # Customer lookup
│   ├── erp_get_item_master/                      # Item master
│   ├── erp_get_aka_crossref/                     # Part cross-ref
│   ├── erp_get_pricing_tiers/                    # Pricing tiers
│   ├── erp_check_moq/                            # MOQ validation
│   ├── erp_update_price_tier/                    # Price updates
│   ├── erp_approve_sales_order/                  # Order approval
│   ├── erp_place_order_on_hold/                  # Order holds
│   └── notify_human_escalation/                  # Escalations
└── README.md                                      # This file
```

Each tool directory contains:
- Python implementation files
- Agent specifications
- Mock ERP data for testing
- Documentation (README, QUICKSTART, ARCHITECTURE)
- Deployment scripts
- Test cases and guides

## Testing

Each tool directory includes:
- `tests/QUICK_TEST_GUIDE.md` - Quick testing instructions
- `tests/TEST_USE_CASES.md` - Comprehensive test scenarios
- `data/mock_erp.py` - Mock ERP data for testing

Run tests before deployment:
```bash
cd tools/erp_lookup_customer
python -m pytest tests/
```

## Customization

### Modifying ERP Integration

Each tool uses mock ERP data in `data/mock_erp.py`. To integrate with your actual ERP:

1. Replace mock data with actual ERP API calls
2. Update connection credentials in tool configurations
3. Test thoroughly with real data

### Adjusting Business Rules

Key parameters to customize:
- **Customer confidence threshold**: Default 70% (in Customer Identity Agent)
- **Price variance tolerance**: Default ±2% (in Pricing Agent)
- **MOQ rules**: Defined in ERP data (in Pricing Agent)

## Troubleshooting

### Common Issues

1. **Tool import fails**
   - Ensure all dependencies are installed
   - Check Python version (3.8+)
   - Verify file permissions

2. **Agent import fails**
   - Import tools before agents
   - Import collaborator agents before main agent
   - Check for naming conflicts

3. **Runtime errors**
   - Verify all tools are imported
   - Check agent configurations
   - Review error logs in watsonx Orchestrate

## Support & Documentation

- **Tool Documentation**: Each tool has its own README and QUICKSTART guide
- **Architecture Details**: See `docs/ARCHITECTURE.md` in each tool directory
- **Deployment Guide**: See `docs/DEPLOYMENT_GUIDE.md` in each tool directory

## Version Information

- **Package Version**: 1.0.0
- **Export Date**: June 11, 2026
- **watsonx Orchestrate Version**: Compatible with latest ADK
- **Python Version**: 3.8+

## License

This package is provided as-is for deployment in watsonx Orchestrate environments.

## Next Steps

1. ✅ Extract and review the package contents
2. ✅ Install prerequisites (Python SDK, dependencies)
3. ✅ Import tools in the correct order
4. ✅ Import agents (collaborators first, then orchestrator)
5. ✅ Test with sample quotes
6. ✅ Customize for your ERP system
7. ✅ Deploy to production

---

**Ready to deploy?** Follow the Quick Start guide above to get started in minutes!