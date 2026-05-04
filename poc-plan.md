# PoC Plan: craft-agents-oss

## Project Classification
- **Type:** api-service
- **Key Technologies:** Bun.js, TypeScript, Electron (UI), MCP protocol
- **ODH Relevance:** Provides a headless server component for agent workflows, aligns with Model Context Protocol (MCP) integration patterns in ODH

## PoC Objectives
What we want to prove:
1. The Craft Agents server component can be deployed as a Kubernetes service
2. The server responds to health checks and basic API operations
3. The server can create and manage agent sessions
4. The server can integrate with API sources and MCP servers

## Infrastructure Requirements
- **Inference Server:** none
- **Vector Database:** none
- **Embedding Model:** none
- **GPU Required:** no
- **Persistent Storage:** 1Gi PVC for session data and configuration
- **Resource Profile:** medium
- **Sidecar Containers:** none
- **Extra Environment Variables:** CRAFT_SERVER_TOKEN (required)

## Test Scenarios
### Scenario 1: health-check
- **Description:** Verify the server is running and healthy
- **Type:** http
- **Input:** none
- **Expected:** Returns 200 OK with status healthy
- **Timeout:** 60s

### Scenario 2: session-creation
- **Description:** Verify the server can create and manage agent sessions
- **Type:** http
- **Input:** `{"name": "test-session", "provider": "anthropic"}`
- **Expected:** Returns 201 Created with session metadata
- **Timeout:** 30s

### Scenario 3: api-source-connection
- **Description:** Verify the server can connect to an API source
- **Type:** http
- **Input:** `{"type": "openapi", "url": "https://api.example.com/spec"}`
- **Expected:** Returns 201 Created with source configuration
- **Timeout:** 60s

## Dockerfile Considerations
Use the existing `Dockerfile.server` which:
- Uses Bun runtime with Node.js dependencies
- Sets up non-root user (craftagents)
- Exposes port 9100
- Sets entrypoint to run the server
- Includes necessary system dependencies

## Deployment Considerations
- Deploy as Kubernetes Deployment with 1 replica
- Create Kubernetes Service on port 9100
- Mount 1Gi PVC for persistent storage
- Set environment variable CRAFT_SERVER_TOKEN
- Test via HTTP requests to /health and API endpoints
- Run as non-root user (UID/GID matching host)