# Invoice Manager Agent - Package Contents

## 📦 Package Overview

This package contains a complete, production-ready multi-agent system for automated invoice/quote processing in watsonx Orchestrate.

**Package Name**: Invoice Manager Agent  
**Version**: 1.0.0  
**Export Date**: June 11, 2026  
**Total Size**: ~28 MB

## 📁 Directory Structure

```
Invoice_Manager_Agent_Export/
├── README.md                           # Main documentation
├── QUICKSTART.md                       # Quick deployment guide
├── PACKAGE_CONTENTS.md                 # This file
├── DEPLOY.sh                           # Bash deployment script
├── deploy.py                           # Python deployment script
├── invoice_manager_agent_full_export.zip  # Original export archive
└── invoice_manager_agent_full_export/  # Extracted contents
    ├── agents/
    │   └── native/
    │       ├── invoice_manager_agent.yaml        # Main orchestrator
    │       ├── customer_identity_agent.yaml      # Customer verification
    │       ├── quote_validation_agent.yaml       # Part validation
    │       ├── pricing_agent.yaml                # Pricing analysis
    │       └── sales_order_agent.yaml            # Order processing
    └── tools/
        ├── Agentic_workflow_3334K2/              # Document extraction
        ├── erp_lookup_customer/                  # Customer lookup
        ├── erp_get_item_master/                  # Item master records
        ├── erp_get_aka_crossref/                 # Part cross-reference
        ├── erp_get_pricing_tiers/                # Pricing tiers
        ├── erp_check_moq/                        # MOQ validation
        ├── erp_update_price_tier/                # Price updates
        ├── erp_approve_sales_order/              # Order approval
        ├── erp_place_order_on_hold/              # Order holds
        └── notify_human_escalation/              # Escalations
```

## 🤖 Agents (5 Total)

### 1. Invoice Manager Agent (`invoice_manager_agent`)
- **Type**: Native Agent
- **Role**: Main orchestrator
- **LLM**: groq/openai/gpt-oss-120b
- **Style**: React
- **Collaborators**: 4 (customer_identity, quote_validation, pricing, sales_order)
- **Tools**: 1 (Agentic_workflow_3334K2)
- **Description**: Coordinates the complete quote processing workflow

### 2. Customer Identity Agent (`customer_identity_agent`)
- **Type**: Native Agent
- **Role**: Customer verification
- **LLM**: groq/openai/gpt-oss-120b
- **Tools**: 1 (erp_lookup_customer)
- **Description**: Verifies customer identity with fuzzy matching

### 3. Quote Validation Agent (`quote_validation_agent`)
- **Type**: Native Agent
- **Role**: Part number validation
- **LLM**: groq/openai/gpt-oss-120b
- **Tools**: 2 (erp_get_item_master, erp_get_aka_crossref)
- **Description**: Validates part numbers and checks item master

### 4. Pricing Agent (`pricing_agent`)
- **Type**: Native Agent
- **Role**: Pricing analysis
- **LLM**: groq/openai/gpt-oss-120b
- **Tools**: 2 (erp_get_pricing_tiers, erp_check_moq)
- **Description**: Analyzes pricing and validates MOQ compliance

### 5. Sales Order Agent (`sales_order_agent`)
- **Type**: Native Agent
- **Role**: Order processing
- **LLM**: groq/openai/gpt-oss-120b
- **Tools**: 4 (erp_update_price_tier, erp_approve_sales_order, erp_place_order_on_hold, notify_human_escalation)
- **Description**: Processes orders and handles exceptions

## 🛠️ Tools (10 Total)

### Document Processing
1. **Agentic_workflow_3334K2**
   - Type: Flow/Workflow
   - Purpose: Document extraction and processing
   - Used by: Invoice Manager Agent

### Customer Management
2. **erp_lookup_customer**
   - Type: Python Tool
   - Purpose: Customer lookup with fuzzy matching
   - Used by: Customer Identity Agent
   - Features: Confidence scoring, address matching

### Part Validation
3. **erp_get_item_master**
   - Type: Python Tool
   - Purpose: Retrieve item master records
   - Used by: Quote Validation Agent
   - Returns: Description, UOM, status, lead time

4. **erp_get_aka_crossref**
   - Type: Python Tool
   - Purpose: Part number cross-reference
   - Used by: Quote Validation Agent
   - Features: Customer PN to ACME PN mapping

### Pricing & MOQ
5. **erp_get_pricing_tiers**
   - Type: Python Tool
   - Purpose: Retrieve tiered pricing
   - Used by: Pricing Agent
   - Returns: MOQ breakpoints and unit prices

6. **erp_check_moq**
   - Type: Python Tool
   - Purpose: Validate minimum order quantities
   - Used by: Pricing Agent
   - Returns: Compliance status and MOQ rules

### Order Processing
7. **erp_update_price_tier**
   - Type: Python Tool
   - Purpose: Update ERP pricing tiers
   - Used by: Sales Order Agent
   - Features: Price correction workflow

8. **erp_approve_sales_order**
   - Type: Python Tool
   - Purpose: Approve sales orders in ERP
   - Used by: Sales Order Agent
   - Features: Order approval workflow

9. **erp_place_order_on_hold**
   - Type: Python Tool
   - Purpose: Place orders on hold
   - Used by: Sales Order Agent
   - Features: Hold management with reasons

10. **notify_human_escalation**
    - Type: Python Tool
    - Purpose: Send escalation notifications
    - Used by: Sales Order Agent
    - Features: Exception handling workflow

## 📄 Documentation Files

Each tool directory contains:
- **README.md** - Tool overview and usage
- **QUICKSTART.md** - Quick start guide
- **PACKAGE_CONTENTS.md** - Package structure
- **docs/ARCHITECTURE.md** - Architecture details
- **docs/DEPLOYMENT_GUIDE.md** - Deployment instructions
- **tests/QUICK_TEST_GUIDE.md** - Testing guide
- **tests/TEST_USE_CASES.md** - Test scenarios

## 🔧 Configuration Files

- **tool_spec.json** - Tool specifications
- **requirements.txt** - Python dependencies
- **data/mock_erp.py** - Mock ERP data for testing
- **agent_specs/*.yaml** - Agent specifications

## 📊 Workflow Overview

```
User uploads quote/invoice
         ↓
Invoice Manager Agent
         ↓
    Phase 1: Customer Verification
    └── Customer Identity Agent
        └── erp_lookup_customer
         ↓
    Phase 2: Part Validation
    └── Quote Validation Agent
        ├── erp_get_item_master
        └── erp_get_aka_crossref
         ↓
    Phase 3: Pricing Analysis
    └── Pricing Agent
        ├── erp_get_pricing_tiers
        └── erp_check_moq
         ↓
    Phase 4: Order Processing
    └── Sales Order Agent
        ├── erp_update_price_tier (if needed)
        ├── erp_approve_sales_order (if valid)
        ├── erp_place_order_on_hold (if issues)
        └── notify_human_escalation (if needed)
         ↓
    Result: Approved / On Hold / Escalated
```

## 🎯 Key Features

- ✅ **Automated Processing**: 4-phase workflow automation
- ✅ **Multi-Agent Architecture**: Specialized agents for each task
- ✅ **Fuzzy Matching**: Customer verification with confidence scoring
- ✅ **Tiered Pricing**: Support for MOQ-based pricing
- ✅ **Exception Handling**: Automatic escalation workflows
- ✅ **Mock Data**: Complete test data for development
- ✅ **Comprehensive Docs**: Full documentation for all components
- ✅ **Easy Deployment**: Automated deployment scripts

## 📋 Deployment Checklist

- [ ] Extract package contents
- [ ] Review README.md
- [ ] Install prerequisites (Python SDK)
- [ ] Run deployment script (DEPLOY.sh or deploy.py)
- [ ] Verify all agents imported
- [ ] Verify all tools imported
- [ ] Test with sample quotes
- [ ] Customize ERP integrations
- [ ] Configure connections
- [ ] Deploy to production

## 🔐 Security Considerations

- Mock ERP data included for testing only
- Replace with secure ERP API calls in production
- Use watsonx Orchestrate connections for credentials
- Implement proper authentication and authorization
- Review and update security settings

## 📞 Support

For questions or issues:
1. Review the README.md
2. Check tool-specific documentation
3. Review test cases and examples
4. Consult watsonx Orchestrate documentation

## 📝 License

This package is provided for deployment in watsonx Orchestrate environments.

---

**Package Ready**: All components included and ready for deployment! 🚀