# qTest MCP Server - Design Specification

## 🎯 Overview

A Model Context Protocol (MCP) server for qTest integration, enabling AI-native interactions with qTest test management system.

**Goal:** Replace scattered API scripts with unified, AI-friendly tooling for test case management and synchronization.

---

## 📁 Project Structure

```
qtest-mcp-server/
├── package.json
├── tsconfig.json
├── README.md
├── .env.example
├── src/
│   ├── index.ts                 # MCP server entry point
│   ├── config.ts                # Configuration & credentials
│   ├── qtest-client.ts          # qTest API wrapper
│   ├── tools/
│   │   ├── get-module.ts        # Fetch module & test cases
│   │   ├── sync-results.ts      # Sync test execution results
│   │   ├── check-connection.ts  # Health check
│   │   ├── create-module.ts     # Create new qTest module
│   │   ├── get-test-case.ts     # Get individual test case
│   │   ├── update-test-case.ts  # Update test case details
│   │   └── search-tests.ts      # Search across modules
│   ├── utils/
│   │   ├── validators.ts        # Input validation
│   │   ├── formatters.ts        # Response formatting
│   │   ├── cache.ts             # Optional caching layer
│   │   └── logger.ts            # Structured logging
│   └── types/
│       ├── qtest.ts             # qTest API types
│       └── mcp.ts               # MCP tool types
├── tests/
│   ├── tools/                   # Tool tests
│   └── integration/             # Integration tests
└── examples/
    └── usage.md                 # Example queries
```

---

## 🔧 Core MCP Tools

### 1. **qtest_check_connection**

Verify qTest API credentials and connectivity.

```typescript
{
  name: "qtest_check_connection",
  description: "Test qTest API connection and validate credentials",
  inputSchema: {
    type: "object",
    properties: {
      projectId: {
        type: "string",
        description: "qTest project ID (optional, uses default if not provided)"
      }
    }
  }
}
```

**Returns:**

```json
{
  "status": "connected",
  "projectId": "123456",
  "projectName": "Como Automation",
  "apiVersion": "v3",
  "permissions": ["read", "write", "execute"]
}
```

---

### 2. **qtest_get_module**

Fetch test module with all test cases.

```typescript
{
  name: "qtest_get_module",
  description: "Retrieve qTest module with test cases, optionally filtered by type",
  inputSchema: {
    type: "object",
    properties: {
      moduleId: {
        type: "string",
        description: "qTest module ID",
        required: true
      },
      typeFilter: {
        type: "string",
        description: "Filter by test type: 'automation' (702), 'manual' (701), or 'all'",
        enum: ["automation", "manual", "all"],
        default: "all"
      },
      includeSteps: {
        type: "boolean",
        description: "Include detailed test steps",
        default: true
      },
      outputFormat: {
        type: "string",
        description: "Output format: 'json', 'markdown', 'std'",
        enum: ["json", "markdown", "std"],
        default: "json"
      }
    },
    required: ["moduleId"]
  }
}
```

**Returns:**

```json
{
  "module": {
    "id": "67420552",
    "name": "Generic Wallet",
    "parentId": "12345",
    "path": "Test Design/Hub/Generic Wallet"
  },
  "testCases": [
    {
      "id": "134717060",
      "pid": "TC-GW-001",
      "name": "View Wallet Activity",
      "type": "automation",
      "priority": "High",
      "status": "Approved",
      "steps": [...],
      "precondition": "User is logged in",
      "expectedResult": "Wallet activity displayed"
    }
  ],
  "summary": {
    "total": 10,
    "automation": 2,
    "manual": 8
  }
}
```

---

### 3. **qtest_sync_results**

Sync test execution results to qTest.

```typescript
{
  name: "qtest_sync_results",
  description: "Sync test execution results to qTest with smart duplicate detection",
  inputSchema: {
    type: "object",
    properties: {
      moduleId: {
        type: "string",
        description: "qTest module ID",
        required: true
      },
      results: {
        type: "array",
        description: "Array of test results",
        items: {
          type: "object",
          properties: {
            testCaseId: { type: "string" },
            testCasePid: { type: "string" },
            status: {
              type: "string",
              enum: ["passed", "failed", "skipped", "blocked"]
            },
            executionTime: { type: "number" },
            errorMessage: { type: "string" },
            attachments: { type: "array" }
          }
        },
        required: true
      },
      createCycle: {
        type: "boolean",
        description: "Create new test cycle for results",
        default: false
      },
      cycleName: {
        type: "string",
        description: "Test cycle name (required if createCycle=true)"
      },
      dryRun: {
        type: "boolean",
        description: "Validate without syncing (pre-sync check)",
        default: false
      }
    },
    required: ["moduleId", "results"]
  }
}
```

**Returns:**

```json
{
  "status": "success",
  "synced": 48,
  "failed": 2,
  "skipped": 0,
  "cycleId": "TC-12345",
  "details": [
    {
      "testCaseId": "134717060",
      "status": "synced",
      "qTestExecutionId": "EX-98765"
    }
  ],
  "warnings": ["Test case TC-GW-003 not found in qTest, created automatically"]
}
```

---

### 4. **qtest_create_module**

Create new qTest module with test cases.

```typescript
{
  name: "qtest_create_module",
  description: "Create new qTest module and populate with test cases from STD",
  inputSchema: {
    type: "object",
    properties: {
      parentId: {
        type: "string",
        description: "Parent module/folder ID",
        required: true
      },
      moduleName: {
        type: "string",
        description: "Module name",
        required: true
      },
      testCases: {
        type: "array",
        description: "Test cases to create",
        items: {
          type: "object",
          properties: {
            name: { type: "string" },
            description: { type: "string" },
            type: { type: "string", enum: ["automation", "manual"] },
            priority: { type: "string" },
            steps: { type: "array" },
            precondition: { type: "string" },
            expectedResult: { type: "string" }
          }
        },
        required: true
      },
      source: {
        type: "string",
        description: "Source document (e.g., 'payment-feature-std.md')"
      }
    },
    required: ["parentId", "moduleName", "testCases"]
  }
}
```

**Returns:**

```json
{
  "moduleId": "67891234",
  "moduleName": "Payment Feature",
  "testCasesCreated": 12,
  "automationTests": 8,
  "manualTests": 4,
  "testCaseIds": ["TC-PAY-001", "TC-PAY-002", ...]
}
```

---

### 5. **qtest_search_tests**

Search for test cases across modules.

```typescript
{
  name: "qtest_search_tests",
  description: "Search for test cases by name, tags, or content",
  inputSchema: {
    type: "object",
    properties: {
      query: {
        type: "string",
        description: "Search query",
        required: true
      },
      moduleId: {
        type: "string",
        description: "Limit search to specific module (optional)"
      },
      type: {
        type: "string",
        description: "Filter by test type",
        enum: ["automation", "manual", "all"],
        default: "all"
      },
      maxResults: {
        type: "number",
        description: "Maximum results to return",
        default: 50
      }
    },
    required: ["query"]
  }
}
```

---

### 6. **qtest_get_test_case**

Get detailed information for a specific test case.

```typescript
{
  name: "qtest_get_test_case",
  description: "Retrieve detailed information for a specific test case",
  inputSchema: {
    type: "object",
    properties: {
      testCaseId: {
        type: "string",
        description: "qTest test case ID or PID",
        required: true
      },
      includeHistory: {
        type: "boolean",
        description: "Include execution history",
        default: false
      }
    },
    required: ["testCaseId"]
  }
}
```

---

### 7. **qtest_update_test_case**

Update existing test case details.

```typescript
{
  name: "qtest_update_test_case",
  description: "Update test case details (for enhancements/modifications)",
  inputSchema: {
    type: "object",
    properties: {
      testCaseId: {
        type: "string",
        description: "qTest test case ID",
        required: true
      },
      updates: {
        type: "object",
        description: "Fields to update",
        properties: {
          name: { type: "string" },
          description: { type: "string" },
          steps: { type: "array" },
          priority: { type: "string" },
          status: { type: "string" }
        }
      },
      reason: {
        type: "string",
        description: "Reason for update (for audit trail)"
      }
    },
    required: ["testCaseId", "updates"]
  }
}
```

---

## 🔐 Configuration

### Environment Variables (.env)

```bash
# qTest Configuration
QTEST_API_URL=https://heartland.qtestnet.com
QTEST_API_TOKEN=your_api_token_here
QTEST_PROJECT_ID=123456

# MCP Server Configuration
MCP_SERVER_NAME=qtest-server
MCP_SERVER_VERSION=1.0.0
LOG_LEVEL=info

# Optional Features
ENABLE_CACHE=true
CACHE_TTL_SECONDS=300
ENABLE_RATE_LIMITING=true
MAX_REQUESTS_PER_MINUTE=60

# Pre-sync Safety
REQUIRE_DRY_RUN=true
BLOCK_FAILING_SYNC=true
```

---

## 🚀 Implementation Guide

### Phase 1: Setup (Day 1, 4 hours)

**1. Initialize Project**

```bash
mkdir qtest-mcp-server
cd qtest-mcp-server
npm init -y
npm install @modelcontextprotocol/sdk axios dotenv
npm install -D typescript @types/node ts-node
```

**2. Basic Structure**

```typescript
// src/index.ts
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import {
  CallToolRequestSchema,
  ListToolsRequestSchema,
} from "@modelcontextprotocol/sdk/types.js";

const server = new Server(
  {
    name: "qtest-server",
    version: "1.0.0",
  },
  {
    capabilities: {
      tools: {},
    },
  },
);

// Register tools
server.setRequestHandler(ListToolsRequestSchema, async () => ({
  tools: [
    // Tool definitions
  ],
}));

server.setRequestHandler(CallToolRequestSchema, async (request) => {
  // Tool implementation
});

// Start server
const transport = new StdioServerTransport();
await server.connect(transport);
```

**3. qTest Client Wrapper**

```typescript
// src/qtest-client.ts
import axios, { AxiosInstance } from "axios";

export class QTestClient {
  private client: AxiosInstance;

  constructor(apiUrl: string, apiToken: string, projectId: string) {
    this.client = axios.create({
      baseURL: `${apiUrl}/api/v3/projects/${projectId}`,
      headers: {
        Authorization: `Bearer ${apiToken}`,
        "Content-Type": "application/json",
      },
    });
  }

  async getModule(moduleId: string, typeFilter?: string) {
    // Implementation
  }

  async syncResults(moduleId: string, results: any[]) {
    // Implementation
  }

  async checkConnection() {
    // Implementation
  }

  async createModule(parentId: string, data: any) {
    // Implementation
  }
}
```

---

### Phase 2: Core Tools (Day 1-2, 8 hours)

**Priority Order:**

1. ✅ `qtest_check_connection` - Essential for debugging
2. ✅ `qtest_get_module` - Most frequently used
3. ✅ `qtest_sync_results` - Core workflow

**Implementation Pattern:**

```typescript
// src/tools/get-module.ts
import { QTestClient } from "../qtest-client.js";

export async function getModule(client: QTestClient, args: any) {
  const { moduleId, typeFilter, includeSteps, outputFormat } = args;

  // 1. Validate inputs
  if (!moduleId) {
    throw new Error("moduleId is required");
  }

  // 2. Fetch from qTest
  const moduleData = await client.getModule(moduleId);

  // 3. Filter by type if requested
  let testCases = moduleData.testCases;
  if (typeFilter === "automation") {
    testCases = testCases.filter((tc) => tc.typeId === 702);
  } else if (typeFilter === "manual") {
    testCases = testCases.filter((tc) => tc.typeId === 701);
  }

  // 4. Format output
  if (outputFormat === "markdown") {
    return formatAsMarkdown(moduleData, testCases);
  } else if (outputFormat === "std") {
    return formatAsSTD(moduleData, testCases);
  }

  // 5. Return JSON
  return {
    module: {
      id: moduleData.id,
      name: moduleData.name,
      path: moduleData.path,
    },
    testCases: testCases.map((tc) => ({
      id: tc.id,
      pid: tc.pid,
      name: tc.name,
      type: tc.typeId === 702 ? "automation" : "manual",
      priority: tc.priority,
      status: tc.status,
      steps: includeSteps ? tc.steps : undefined,
    })),
    summary: {
      total: testCases.length,
      automation: testCases.filter((tc) => tc.typeId === 702).length,
      manual: testCases.filter((tc) => tc.typeId === 701).length,
    },
  };
}
```

---

### Phase 3: Safety Features (Day 2, 4 hours)

**Pre-Sync Validation:**

```typescript
// src/utils/validators.ts

export async function validateSync(
  client: QTestClient,
  moduleId: string,
  results: any[],
) {
  const issues: string[] = [];

  // 1. Check connection
  const connected = await client.checkConnection();
  if (!connected) {
    issues.push("❌ qTest API connection failed");
  }

  // 2. Fetch existing tests
  const existingTests = await client.getModule(moduleId);

  // 3. Check for duplicates
  const resultPids = results.map((r) => r.testCasePid);
  const duplicates = resultPids.filter(
    (pid, idx) => resultPids.indexOf(pid) !== idx,
  );
  if (duplicates.length > 0) {
    issues.push(`⚠️ Duplicate test PIDs: ${duplicates.join(", ")}`);
  }

  // 4. Check for missing test cases
  const existingPids = existingTests.testCases.map((tc) => tc.pid);
  const missing = resultPids.filter((pid) => !existingPids.includes(pid));
  if (missing.length > 0) {
    issues.push(`⚠️ Test cases not in qTest: ${missing.join(", ")}`);
  }

  // 5. Validate all tests passed (if BLOCK_FAILING_SYNC=true)
  if (process.env.BLOCK_FAILING_SYNC === "true") {
    const failing = results.filter((r) => r.status !== "passed");
    if (failing.length > 0) {
      issues.push(`❌ Cannot sync: ${failing.length} tests not passing`);
    }
  }

  return {
    valid: issues.filter((i) => i.startsWith("❌")).length === 0,
    issues,
    warnings: issues.filter((i) => i.startsWith("⚠️")),
  };
}
```

---

### Phase 4: Testing & Integration (Day 3, 4 hours)

**Unit Tests:**

```typescript
// tests/tools/get-module.test.ts
import { describe, it, expect } from "vitest";
import { getModule } from "../../src/tools/get-module";

describe("qtest_get_module", () => {
  it("should fetch module with automation filter", async () => {
    const result = await getModule(mockClient, {
      moduleId: "67420552",
      typeFilter: "automation",
    });

    expect(result.testCases).toHaveLength(2);
    expect(result.testCases.every((tc) => tc.type === "automation")).toBe(true);
  });
});
```

**Integration with AI-STLC Workflows:**

```bash
# Add to .copilot-mcp.json or MCP config
{
  "mcpServers": {
    "qtest": {
      "command": "node",
      "args": ["./qtest-mcp-server/dist/index.js"],
      "env": {
        "QTEST_API_URL": "https://heartland.qtestnet.com",
        "QTEST_API_TOKEN": "${QTEST_API_TOKEN}",
        "QTEST_PROJECT_ID": "123456"
      }
    }
  }
}
```

---

## 🎨 Usage Examples

### Example 1: Workflow A (qTest-First)

**AI Query:**

```
"Fetch automation tests from qTest module 67420552 and create JSON file"
```

**MCP Call:**

```json
{
  "tool": "qtest_get_module",
  "arguments": {
    "moduleId": "67420552",
    "typeFilter": "automation",
    "outputFormat": "json"
  }
}
```

**AI Then:**

- Receives JSON with 2 automation tests
- Creates `.qtest/test-cases/hub/GenericWallet.json`
- Generates code skeleton

---

### Example 2: Workflow B (Code-First with Sync)

**AI Query:**

```
"Sync test results for payment module after verification"
```

**MCP Calls:**

```json
// 1. Pre-sync check
{
  "tool": "qtest_sync_results",
  "arguments": {
    "moduleId": "67891234",
    "results": [...],
    "dryRun": true
  }
}

// 2. Actual sync (if dry run passes)
{
  "tool": "qtest_sync_results",
  "arguments": {
    "moduleId": "67891234",
    "results": [...],
    "createCycle": true,
    "cycleName": "Sprint 5 - Payment Feature"
  }
}
```

---

### Example 3: Enhancement Workflow

**AI Query:**

```
"Update test case TC-P2C-005 with new Redis caching steps"
```

**MCP Call:**

```json
{
  "tool": "qtest_update_test_case",
  "arguments": {
    "testCaseId": "TC-P2C-005",
    "updates": {
      "steps": [
        "Step 1: Configure Redis cache",
        "Step 2: Load merchant config from cache",
        "Step 3: Verify cache hit",
        "Step 4: Validate config data"
      ],
      "description": "Updated to include Redis caching validation"
    },
    "reason": "Enhancement: JIRA-1234 - Add Redis caching support"
  }
}
```

---

## 📊 Comparison: Before vs After

### Current Approach (APIs)

**Workflow A Example:**

```bash
# Step 1: Manual fetch
curl -H "Authorization: Bearer $TOKEN" \
  "https://heartland.qtestnet.com/api/v3/projects/123/modules/67420552"

# Step 2: Manual filtering
# ... process JSON manually or with jq

# Step 3: Create JSON file manually
# ... copy-paste into .qtest/test-cases/

# Step 4: Implement tests
# ... reference JSON

# Step 5: Sync results
python .qtest/simple_sync.py --module-id 67420552
```

**Lines of interaction:** ~30  
**Time:** 15-20 minutes  
**Error-prone steps:** 3-4

---

### With MCP Server

**Workflow A Example:**

```
AI: "Fetch automation tests from module 67420552, create JSON, and generate test skeleton"
```

**Behind the scenes:**

1. MCP calls `qtest_get_module`
2. AI creates JSON file automatically
3. AI generates test skeleton
4. Done

**Lines of interaction:** 1  
**Time:** 2-3 minutes  
**Error-prone steps:** 0

---

## 🎯 Success Metrics

### Track These KPIs:

1. **Time Savings:**
   - Current: 15-20 min for fetch + create JSON
   - Target: 2-3 min with MCP
   - **Goal: 80% reduction**

2. **Error Rate:**
   - Current: ~15% (duplicates, wrong format, sync issues)
   - Target: <2% (validation catches issues)
   - **Goal: 90% reduction**

3. **AI Accuracy:**
   - Current: AI guesses at qTest structure
   - Target: AI uses MCP tools correctly
   - **Goal: 95%+ correct tool usage**

4. **Developer Satisfaction:**
   - Survey team after 2 weeks
   - **Goal: 8+/10 rating**

---

## 🔄 Migration Strategy

### Week 1: Parallel Run

- ✅ Keep existing scripts working
- ✅ Introduce MCP server for 1-2 workflows
- ✅ Compare results side-by-side

### Week 2-3: Expand & Refine

- ✅ Add MCP to more workflows
- ✅ Fix issues discovered
- ✅ Update documentation

### Week 4: Decision Point

- ✅ Evaluate metrics
- ✅ Team feedback
- ✅ Decision: Full migration or hybrid

---

## 🚧 Potential Challenges

### 1. **Rate Limiting**

- **Solution:** Built-in rate limiter with queue
- **Code:** Use `bottleneck` package

### 2. **API Changes**

- **Solution:** Version-aware client with fallbacks
- **Monitoring:** Log API version mismatches

### 3. **Network Issues**

- **Solution:** Retry logic with exponential backoff
- **Timeout:** Configurable timeouts per operation

### 4. **Cache Staleness**

- **Solution:** TTL-based cache with manual invalidation
- **Tool:** Add `qtest_clear_cache` tool

---

## 📚 Next Steps

### Immediate (This Week):

1. ✅ Review this design doc
2. ✅ Create project structure
3. ✅ Implement `qtest_check_connection` tool
4. ✅ Test with one simple query

### Phase 1 (Next Week):

1. ✅ Implement `qtest_get_module`
2. ✅ Implement `qtest_sync_results`
3. ✅ Add pre-sync validation
4. ✅ Test with Generic Wallet example

### Phase 2 (Week 3):

1. ✅ Add remaining tools
2. ✅ Integration testing
3. ✅ Update AI-STLC docs
4. ✅ Team training

---

## 📖 References

- **MCP Specification:** https://modelcontextprotocol.io
- **qTest API Docs:** https://support.tricentis.com/qtest/api-docs/
- **Current Scripts:** `.qtest/simple_sync.py`, test-qtest-connection.sh
- **AI-STLC Guide:** `/Users/aliktitelman/AI-CoPilot/.github/prompts/ai-stlc/AI-STLC-Complete-Guide.md`

---

**Version:** 1.0  
**Created:** February 8, 2026  
**Status:** Design / Ready for Implementation  
**Estimated Effort:** 3 days POC, 2 weeks full implementation
