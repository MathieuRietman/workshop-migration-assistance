---
name: fabric-data-agent-setup
description: "Generate Data Agent artifacts for Microsoft Fabric Gold lakehouse to enable natural language querying by business users."
metadata:
  owner: migration-helper
  added: "2026-03-18"
---

# Fabric Data Agent Setup

Use this skill to create Data Agent artifacts that enable business users to query Fabric Gold lakehouse data using natural language.

## Use this skill when

- User requests a Data Agent for their Fabric migration scenario
- User wants to enable natural language querying of Gold tier data
- User asks for business user-friendly data access layer
- Planning phase includes Data Agent as optional component

## Data Agent Purpose

The Data Agent is a specialized AI assistant that:
- Helps business users query Gold tier data without writing SQL/PySpark
- Provides schema understanding and query guidance
- Suggests optimal query patterns and aggregations
- Explains data relationships and business metrics
- References available dashboards and semantic models

## Required Artifacts

Create the following files in `result/data_agent/`:

### 1. `agent_instructions.md`
Master prompt instructions for the Data Agent containing:
- **Overview**: Agent purpose and expertise scope
- **Background**: Solution architecture context (medallion, domains)
- **Architecture Components**: Bronze/Silver/Gold layers, Power BI integration
- **Data Domains**: List of domains (Shared, Finance, Sales, etc.)
- **Data Schema Knowledge**: Detailed table descriptions per domain with fields, data types, sample values
- **Core Capabilities**: Query assistance, BI support, data model navigation
- **Query Construction Guidelines**: PySpark and SQL patterns
- **Response Style**: How to format answers, handle limitations
- **Special Guidelines**: Sample data limitations, what NOT to offer (e.g., complex statistical analysis)

**Template structure**:
```markdown
# Microsoft Fabric Data Agent - Master Prompt Instructions

## Overview
[Agent purpose, expertise, goal]

## Background and Special Guide
[Solution context, data limitations, user guidance]

## Solution Architecture Context
[Medallion architecture, domains, Power BI integration]

## Data Schema Knowledge
### [Domain] Domain Tables:
- **[Table]**: [Purpose]
  - Fields: [Field descriptions with data types and samples]
  - Business Rules: [Key rules and constraints]

## Core Capabilities
1. Data Query Assistance
2. Business Intelligence Support
3. Data Model Navigation
4. Data Lake Navigation

## Query Construction Guidelines
[PySpark and SQL patterns]

## Response Style
[Formatting, tone, structure]
```

### 2. `data_source_description.md`
Comprehensive data source descriptions including:
- **Domain Structure**: Overview of each domain (Shared, Finance, Sales)
- **Table Descriptions**: Per table:
  - Purpose and business context
  - Source systems
  - Update frequency
  - Record counts (if known)
  - Field-level descriptions with data types and sample values
  - Business rules and constraints
  - Relationships to other tables

**Template structure**:
```markdown
# Data Source Descriptions - Microsoft Fabric Gold Tier Data Lake

## Overview
[Gold tier data lake purpose and scope]

## Domain Structure

### [Domain] Domain (`[schema]` schema)
[Domain purpose and business context]

#### [Table] (`[schema].[table]`)
**Purpose**: [What this table stores]
**Source**: [Source systems]
**Update Frequency**: [Batch daily/real-time/weekly]
**Record Count**: [Approximate count if known]

| Field Name | Data Type | Description | Sample Values |
|------------|-----------|-------------|---------------|
| [Field] | [Type] | [Description] | [Examples] |

**Business Rules**:
- [Rule 1]
- [Rule 2]

**Relationships**:
- [Foreign key relationships to other tables]
```

### 3. `data_source_instructions.md`
Query patterns and best practices including:
- **General Query Guidelines**: Schema naming, performance optimization, data type handling
- **Domain-Specific Query Instructions**: Sample queries per domain
- **Common Query Patterns**: Aggregations, joins, filters
- **Performance Tips**: Partitioning, indexing, query optimization
- **Data Quality Checks**: Validation patterns

**Template structure**:
```markdown
# Data Source Instructions - Microsoft Fabric Gold Tier Data Lake

## Overview
[Query instructions purpose]

## General Query Guidelines

### 1. Schema and Table Naming Conventions
[Fully qualified table names, best practices]

### 2. Performance Optimization Principles
[Query optimization tips]

### 3. Data Type Handling
[Type-specific guidance]

## Domain-Specific Query Instructions

### [Domain] Domain Queries

#### [Use Case] Analysis Patterns
```sql
-- [Query description]
[Sample SQL query]
```

## Data Quality and Validation
[Validation patterns and checks]
```

### 4. `query_examples.json`
JSON file with natural language questions mapped to SQL queries:
- Business user questions as keys
- Complete SQL queries as values
- Cover common use cases per domain
- Include complex multi-table joins
- Show aggregation and calculation patterns

**Template structure**:
```json
{
  "User question in natural language": "SQL query with comments explaining logic",
  "Another business question": "Corresponding SQL query"
}
```

## Content Generation Strategy

### Step 1: Extract Schema Information
From `analysis/metadata-extraction-summary.md` and Gold schema notebooks:
- Extract all Gold tier tables with their schemas
- Identify domain groupings (Shared, Finance, Sales, etc.)
- List relationships and foreign keys
- Document business rules from source metadata

### Step 2: Generate agent_instructions.md
- Start with overview of agent purpose
- List solution architecture components (layers, domains)
- Document each domain and its tables with field descriptions
- Include query capabilities and guidelines
- Add special instructions (limitations, what to avoid)
- Reference Power BI dashboards if available

### Step 3: Generate data_source_description.md
- Create detailed table catalog per domain
- For each table include:
  - Business purpose from source metadata
  - Field-level descriptions with data types
  - Sample values (if available from source)
  - Business rules from SQL comments
  - Relationships to other tables
  - Update frequency and source systems

### Step 4: Generate data_source_instructions.md
- Create query guidelines (naming, performance, types)
- Generate 3-5 sample queries per domain showing:
  - Simple selections
  - Aggregations and grouping
  - Multi-table joins
  - Time-based analysis
  - Customer/product segmentation
- Include query optimization tips
- Add data quality check patterns

### Step 5: Generate query_examples.json
- Create 5-10 realistic business questions
- Map each question to a complete SQL query
- Include queries that:
  - Join across domains (e.g., Customer + Sales + Product)
  - Calculate business metrics (revenue, growth, segments)
  - Show time-based trends
  - Demonstrate aggregation patterns
- Add inline SQL comments explaining logic

## Implementation Checklist

When generating Data Agent artifacts:

- [ ] Extract Gold schema from `schema/model_*_gold.ipynb` notebooks
- [ ] Review `analysis/metadata-extraction-summary.md` for business context
- [ ] Identify all domains and their relationships
- [ ] Generate `agent_instructions.md` with complete schema knowledge
- [ ] Generate `data_source_description.md` with field-level details
- [ ] Generate `data_source_instructions.md` with query patterns
- [ ] Generate `query_examples.json` with realistic business questions
- [ ] Verify all table names match Gold schema exactly
- [ ] Include bronze/silver/gold lineage context where relevant
- [ ] Document data limitations (sample data, synthetic data)
- [ ] Add references to Power BI dashboards if available

## Quality Criteria

A complete Data Agent setup must:
1. **Accuracy**: All table/column names match actual Gold schema
2. **Completeness**: All Gold tables documented across all domains
3. **Usability**: Business users can understand without technical knowledge
4. **Traceability**: References to source metadata and business rules
5. **Queryable**: Sample queries are executable and demonstrate key patterns
6. **Limitations**: Clearly states what the agent CAN and CANNOT do

## Integration with Migration Workflow

### During Intake (migratie.assistant)
- Add question: "Wilt u een Data Agent voor business gebruikers? (Ja/Nee, default: Nee)"
- If Yes: Mark Data Agent as in-scope for planning and implementation
- If No: Skip Data Agent artifacts entirely

### During Planning (migratie.planning)
- If Data Agent in scope: Include Data Agent artifacts in deliverables section
- Plan Data Agent content scope based on Gold schema complexity
- Estimate effort for Data Agent creation (typically low, template-driven)

### During Implementation (migratie.implementation)
- If Data Agent in scope: Generate all 4 Data Agent files in `result/data_agent/`
- Use `.github/skills/fabric-data-agent-setup/SKILL.md` guidance
- Populate content from Gold schema notebooks and metadata extraction
- Validate query examples against actual Gold tables
- Test queries work on sample data

### During Documentation (migratie.documentation)
- If Data Agent exists: Document Data Agent usage in production documentation
- Include instructions for business users to access Data Agent
- Reference example queries and capabilities

## Example Content Snippets

### agent_instructions.md snippet:
```markdown
## Data Schema Knowledge

### Shared Domain Tables:

- **Customer**: Customer master data with demographics
  - Fields: CustomerId (STRING), FirstName (STRING), LastName (STRING), Gender (STRING), 
    DateOfBirth (DATE), CustomerRelationshipTypeId (STRING: Standard/Premium/VIP), IsActive (BOOLEAN)
  - Business Rules: Each customer has unique CustomerId; relationship type determines pricing tier

- **Product**: Product catalog with pricing
  - Fields: ProductID (INTEGER), Name (STRING), StandardCost (DECIMAL), ListPrice (DECIMAL), 
    CategoryName (STRING)
  - Business Rules: ListPrice must be > StandardCost
```

### data_source_description.md snippet:
```markdown
#### Customer (`shared.Customer`)
**Purpose**: Central customer master data with demographics and relationship management
**Source**: Customer management systems and CRM platforms
**Update Frequency**: Daily batch processing
**Record Count**: ~515 customers

| Field Name | Data Type | Description | Sample Values |
|------------|-----------|-------------|---------------|
| CustomerId | STRING | Primary key, unique customer identifier | CID-001, CID-002 |
| CustomerRelationshipTypeId | STRING | Customer tier/relationship level | Standard, Premium, VIP |
```

### query_examples.json snippet:
```json
{
  "Show me top 10 customers by total revenue": "SELECT TOP 10 c.CustomerId, c.FirstName, c.LastName, SUM(o.OrderTotal) AS TotalRevenue FROM shared.Customer c INNER JOIN salesfabric.[Order] o ON c.CustomerId = o.CustomerId WHERE o.OrderStatus = 'Completed' GROUP BY c.CustomerId, c.FirstName, c.LastName ORDER BY TotalRevenue DESC;"
}
```

## Output Expectations

When this skill is invoked, generate:
- Complete `result/data_agent/` folder with all 4 required files
- Content derived from Gold schema and metadata extraction
- Executable sample queries validated against Gold schema
- Clear documentation of data limitations
- Business-user-friendly language throughout
