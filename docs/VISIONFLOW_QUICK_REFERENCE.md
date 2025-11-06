# VisionFlow Quick Reference Guide

## One-Page Overview

**VisionFlow = 3D Knowledge Graphs + AI Agents + Semantic Reasoning**

A self-hosted platform that deploys autonomous AI agents to continuously analyze your data, visualizes knowledge as interactive 3D spaces, and applies semantic reasoning to uncover hidden insights.

---

## Installation (5 minutes)

```bash
git clone https://github.com/DreamLab-AI/VisionFlow.git
cd VisionFlow
cp .env.example .env
docker-compose up -d
# Wait 2-3 minutes, then visit http://localhost:3030
```

**Verify:**
```bash
curl http://localhost:4000/health
```

---

## Core Concepts

| Concept | Meaning | Example |
|---------|---------|---------|
| **Knowledge Graph** | Visual nodes & relationships | Person → WorksAt → Company |
| **Ontology** | Formal knowledge structure | Class hierarchies (Person ⊂ Agent) |
| **Semantic Reasoning** | Automatic inference | SubClassOf creates clustering force |
| **Agent** | Autonomous AI with role | Researcher analyzing documents |
| **CQRS** | Separate reads & writes | Queries cached, commands mutate state |
| **Hexagonal Arch** | Clean separation | Domain logic isolated from infrastructure |

---

## Key Features at a Glance

| Feature | Capability | Performance |
|---------|-----------|-------------|
| **Multi-Agent** | 50+ concurrent agents | <50ms spawn, <10ms coordination |
| **3D Visualization** | 100,000+ nodes rendered | 60 FPS at <16ms/frame |
| **Reasoning** | OWL 2 EL inference | 50ms cold, <1ms cached |
| **GPU Physics** | 39 CUDA kernels | 100x speedup over CPU |
| **Network** | Binary WebSocket | 36 bytes/node, <10ms latency |
| **Security** | Self-hosted, JWT auth | Role-based access control |
| **Voice** | STT/TTS via WebRTC | <10ms latency |
| **XR/AR** | Meta Quest 3, Vircadia | Full immersive support |

---

## Architecture in 60 Seconds

```
User (Web UI)
    ↓
Vue.js + Three.js (Frontend)
    ↓
REST/WebSocket API
    ↓
Actix Actors (Backend Concurrency)
    ↓
CQRS Layer (Commands & Queries)
    ↓
Services (Business Logic)
    ↓
Repositories (Data Access)
    ↓
SQLite (Unified Database)
    +
    → AI Agents (Analysis)
    → GPU Kernels (Physics)
    → Reasoning Engine (Inference)
```

---

## Technology Stack Summary

### Backend
- **Rust** + Actix-Web 4.11
- **SQLite** unified database (WAL mode)
- **CUDA 12.4** for GPU acceleration
- **OWL** reasoning via Horned-OWL + Whelk-rs

### Frontend
- **Vue.js** UI framework
- **Three.js** / **Babylon.js** for 3D
- **WebRTC** real-time communication
- **WebXR** for immersive experiences

### Infrastructure
- **Docker** / **Docker Compose**
- **Nginx** reverse proxy
- **Supervisord** process management

---

## Database Schema (Key Tables)

```sql
-- Knowledge Graph
graph_nodes(id, name, description, type)
graph_edges(id, source_id, target_id, relationship_type, strength)

-- Ontology
owl_classes(id, uri, name, parent_class_id)
owl_axioms(id, axiom_type, source_id, target_id, inference_result)

-- Configuration
file_metadata(id, file_path, source, last_synced)
settings(key, value, scope)
inference_cache(source_hash, result_json, timestamp)
```

---

## Agent Types Available

| Agent | Role | Output |
|-------|------|--------|
| **Researcher** | Data analysis, synthesis | Reports, insights |
| **Coder** | Code analysis, generation | Source code, docs |
| **Architect** | System design | Design docs, recommendations |
| **Tester** | QA, validation | Test suites, reports |
| **Reviewer** | Audit, compliance | Review findings |
| **Coordinator** | Orchestration | Logs, metrics |

**Swarm Topologies:** Hierarchical, Mesh, Ring, Star

---

## Environment Variables (Key Ones)

```bash
# Core
NODE_ENV=development
BIND_ADDRESS=0.0.0.0
DOMAIN=localhost

# Database
DATABASE_PATH=/app/data/unified.db

# GitHub Integration
GITHUB_OWNER=your-owner
GITHUB_REPO=your-repo
GITHUB_TOKEN=your-token

# APIs
RAGFLOW_API_KEY=xxx
RAGFLOW_BASE_URL=http://localhost:9380

# GPU (optional)
CUDA_ENABLED=true
CUDA_COMPUTE_CAPABILITY=80

# Security
JWT_SECRET=generate-strong-random-value
```

See `.env.example` for complete list.

---

## Quick Start Projects

### 1. Personal Knowledge Graph (4 hours)
1. Open http://localhost:3030
2. Create 15 nodes (topics)
3. Connect with edges
4. Customize colors and physics
5. Deploy 1-2 researcher agents
6. Export results

### 2. Simple Ontology (6 hours)
1. Define domain (e.g., "Restaurant Types")
2. Create class hierarchy
3. Add properties
4. Run reasoning
5. Visualize inferred relationships

### 3. Multi-Agent Research (8 hours)
1. Define research question
2. Configure 3-5 agents
3. Monitor execution
4. Analyze insights
5. Create visualization

### 4. Code Analysis (10 hours)
1. Point to GitHub repo
2. Deploy architect agents
3. Create architecture ontology
4. Generate visualizations
5. Find refactoring opportunities

---

## API Endpoints (Key Ones)

### REST API (Port 4000)

```bash
# Health
GET /health

# Workspaces
GET    /api/workspaces
POST   /api/workspaces
GET    /api/workspaces/{id}

# Graphs
GET    /api/graphs
POST   /api/graphs
GET    /api/graphs/{id}/nodes
POST   /api/graphs/{id}/nodes

# Agents
GET    /api/agents
POST   /api/agents/{id}/start
GET    /api/agents/{id}/status

# Ontology
GET    /api/ontology/classes
GET    /api/ontology/infer
POST   /api/ontology/constraints
```

### WebSocket Endpoints

```
/wss - Main graph updates
/ws/speech - Voice commands
/ws/mcp-relay - MCP protocol relay
```

---

## Performance Metrics

| Metric | Value |
|--------|-------|
| Node rendering (100k) | 60 FPS |
| Frame time | <16ms |
| Agent spawn time | <50ms |
| WebSocket latency | <10ms |
| Reasoning (cold) | ~50ms |
| Reasoning (cached) | <1ms |
| Network protocol | 36 bytes/node |
| Bandwidth reduction | 80% vs JSON |
| GPU speedup (physics) | 100x |
| GPU speedup (clustering) | 67x |

---

## File Organization

```
VisionFlow/
├── src/                    # Rust backend (30+ modules)
│   ├── main.rs            # Entry point
│   ├── services/          # Business logic
│   ├── repositories/      # Data access
│   ├── cqrs/              # Commands & queries
│   ├── ontology/          # Reasoning pipeline
│   ├── gpu/               # CUDA integration
│   └── actors/            # Actor definitions
├── client/src/            # Vue.js frontend
│   ├── components/        # UI components
│   ├── rendering/         # 3D engine
│   ├── immersive/         # XR/VR
│   └── services/          # API clients
├── multi-agent-docker/    # Agent orchestration
│   ├── agents/            # 54+ templates
│   ├── skills/            # Capabilities
│   └── coordination/       # Cooperation
├── docs/                  # 311+ docs
├── examples/              # 5 examples
├── schema/                # DB schemas
└── Cargo.toml             # Rust config
```

---

## Typical Workflow

```
1. SETUP
   └─ Install, configure .env, start services

2. EXPLORE
   └─ Visit UI, create first graph with demo data

3. CREATE
   └─ Add nodes, edges, customize visualization

4. INTEGRATE
   └─ Point to data source (GitHub, RAGFlow, etc.)

5. REASON
   └─ Upload ontology, run semantic reasoning

6. DEPLOY AGENTS
   └─ Configure team, set task, monitor execution

7. ANALYZE
   └─ Review agent insights, visualize findings

8. EXPORT
   └─ Export graph, reports, metrics
```

---

## Common Commands

### Docker
```bash
docker-compose ps              # Check status
docker-compose logs -f         # View logs
docker-compose up -d           # Start
docker-compose down            # Stop
docker-compose restart         # Restart
```

### Rust Development
```bash
cargo build --release          # Build
cargo run --release            # Run
cargo test                     # Test
cargo test --lib              # Unit tests only
cargo fmt                      # Format code
cargo clippy                   # Lint
```

### Frontend Development
```bash
cd client
npm install                    # Install
npm run dev                    # Dev server (5173)
npm run build                  # Build
npm run test                   # Test
```

---

## Access Points

| Service | URL | Purpose |
|---------|-----|---------|
| Web UI | http://localhost:3030 | Main interface |
| REST API | http://localhost:4000 | API endpoint |
| Health | http://localhost:4000/health | Status check |
| Code Server | http://localhost:8080 | IDE (dev container) |
| VNC | vnc://localhost:5901 | Remote desktop |
| SSH | ssh://localhost:2222 | Terminal access |

---

## Documentation Files (Priority Order)

**ESSENTIAL (Read First)**
1. README.md - Overview
2. docs/getting-started/01-installation.md - Setup
3. docs/getting-started/02-first-graph-and-agents.md - Tutorial

**IMPORTANT (Next)**
4. docs/PHASE-5-EXECUTIVE-SUMMARY.md - Capabilities
5. docs/concepts/ontology-reasoning.md - How reasoning works
6. docs/concepts/ontology-pipeline-integration.md - Full pipeline

**ADVANCED (Later)**
7. src/main.rs - Server code
8. Cargo.toml - Dependencies
9. Architecture docs in docs/

---

## Troubleshooting Quick Tips

| Issue | Solution |
|-------|----------|
| Ports in use | Change ports in docker-compose.yml |
| GPU not detected | Verify NVIDIA driver, CUDA 12.4 installed |
| Slow rendering | Reduce node count, enable LOD |
| High memory | Reduce agent count, clear cache |
| Ontology not loading | Check file format (OWL/RDF), validate syntax |
| Agents not spawning | Check logs with `docker-compose logs` |

---

## Learning Path (4 Weeks)

**Week 1:** Install → Explore UI → Create first graph → Read architecture docs
**Week 2:** Build 2-3 personal projects → Understand CQRS → Study reasoning
**Week 3:** Deploy agents → Create ontology → Run reasoning pipeline
**Week 4:** Advanced: GPU tuning → Custom agents → Production deployment

---

## Key Design Decisions

| Decision | Why | Tradeoff |
|----------|-----|----------|
| Unified SQLite DB | Simplicity, ACID guarantees | Not for massive scale |
| Server-authoritative state | Data consistency | More server load |
| CQRS pattern | Scalability | More complexity |
| Hexagonal architecture | Testability, separation | More boilerplate |
| GPU via CUDA | Performance (100x) | NVIDIA hardware only |
| Binary WebSocket | Bandwidth (80% reduction) | Less human-readable |

---

## Production Checklist

- [ ] Generate strong JWT secret
- [ ] Set up proper environment variables
- [ ] Enable database backups
- [ ] Configure HTTPS/TLS
- [ ] Set up monitoring and logging
- [ ] Configure rate limiting
- [ ] Enable audit trails
- [ ] Test disaster recovery
- [ ] Set up automated deployments
- [ ] Document security policies

---

## Next Steps

1. **Install Now:** 5 minutes with Docker
2. **Explore UI:** 15 minutes with demo data
3. **Read Docs:** 1-2 hours on architecture
4. **Build Project:** 4-10 hours depending on scope
5. **Deploy:** 2-4 hours for production setup

---

## Links

- **Repository:** https://github.com/DreamLab-AI/VisionFlow
- **Issues:** https://github.com/DreamLab-AI/VisionFlow/issues
- **License:** Mozilla Public License 2.0
- **Created:** July 2024
- **Current:** v1.0.0 (October 2025)

---

**Last Updated:** November 5, 2025  
**For Complete Analysis:** See VISIONFLOW_COMPREHENSIVE_ANALYSIS.md

