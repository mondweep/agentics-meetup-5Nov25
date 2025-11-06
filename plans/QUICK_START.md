# VisionFlow Quick Start Guide
## Get Running in 15 Minutes

---

## 🚀 What You'll Build

A **Personal Knowledge Graph** with AI agents that:
- Visualizes your learning journey in 3D
- Automatically discovers connections
- Provides intelligent recommendations

---

## ⚡ Prerequisites

```bash
# Required
- Docker & Docker Compose
- 8GB+ RAM
- Modern browser (Chrome/Firefox/Edge)

# Optional (for GPU acceleration)
- NVIDIA GPU with CUDA 11.8+
- nvidia-docker2
```

---

## 📦 Installation (5 minutes)

### Option 1: Docker (Recommended)

```bash
# 1. Clone repository
git clone https://github.com/DreamLab-AI/VisionFlow.git
cd VisionFlow

# 2. Start VisionFlow
docker-compose up -d

# 3. Verify it's running
curl http://localhost:8080/health

# Expected output:
# {"status":"healthy","version":"1.0.0"}
```

### Option 2: Local Development

```bash
# 1. Install Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# 2. Install dependencies
sudo apt-get install -y build-essential libssl-dev pkg-config

# 3. Clone and build
git clone https://github.com/DreamLab-AI/VisionFlow.git
cd VisionFlow
cargo build --release

# 4. Run
./target/release/visionflow serve
```

---

## 🎯 Your First Knowledge Graph (10 minutes)

### Step 1: Access UI

Open browser: **http://localhost:8080**

### Step 2: Create Nodes

Click **"Add Node"** and create:

```
1. Technology Nodes:
   - Name: "VisionFlow"
   - Type: Technology
   - Properties: { difficulty: "intermediate", progress: 0.1 }

   - Name: "Rust"
   - Type: Technology
   - Properties: { difficulty: "advanced", progress: 0.3 }

   - Name: "Three.js"
   - Type: Technology
   - Properties: { difficulty: "beginner", progress: 0.7 }

2. Project Node:
   - Name: "Knowledge Assistant"
   - Type: Project
   - Properties: { status: "active", priority: "high" }

3. Resource Nodes:
   - Name: "VisionFlow Docs"
   - Type: Resource
   - Properties: { type: "documentation", quality: 9 }

   - Name: "Rust Book"
   - Type: Resource
   - Properties: { type: "book", quality: 10 }
```

### Step 3: Create Relationships

Click on nodes and **"Add Relationship"**:

```
VisionFlow -> uses -> Rust
VisionFlow -> uses -> Three.js
Knowledge Assistant -> uses -> VisionFlow
VisionFlow Docs -> references -> VisionFlow
Rust Book -> references -> Rust
```

### Step 4: View 3D Graph

Click **"3D View"**:
- ✅ Use mouse to rotate
- ✅ Scroll to zoom
- ✅ Click nodes to see details
- ✅ Try different layouts (force-directed, hierarchical, radial)

---

## 🤖 Deploy Your First Agent (5 minutes)

### Create a Progress Tracker Agent

```rust
// save as: custom_agents/progress_tracker.rs

use visionflow::prelude::*;

#[derive(Agent)]
pub struct ProgressTracker {
    threshold: f64,
}

#[async_trait]
impl AgentBehavior for ProgressTracker {
    async fn execute(&mut self, ctx: &Context) -> Result<()> {
        // Find technologies with high progress
        let techs = ctx.query("SELECT * FROM nodes WHERE type = 'Technology'").await?;

        for tech in techs {
            let progress = tech.get_f64("progress").unwrap_or(0.0);

            if progress >= self.threshold {
                ctx.emit_event(Event::new(
                    "progress_milestone",
                    format!("🎉 You've mastered {}!", tech.name)
                )).await?;

                // Suggest next steps
                let related = ctx.traverse_from(tech.id, "requires").await?;
                for next_tech in related {
                    ctx.emit_event(Event::new(
                        "suggestion",
                        format!("💡 Consider learning {} next", next_tech.name)
                    )).await?;
                }
            }
        }

        Ok(())
    }

    fn interval(&self) -> Duration {
        Duration::from_secs(300) // Run every 5 minutes
    }
}

fn main() {
    let agent = ProgressTracker { threshold: 0.8 };
    visionflow::run_agent(agent);
}
```

### Deploy the Agent

```bash
# Build
cargo build --release --bin progress_tracker

# Deploy
./target/release/visionflow agent deploy \
  --name "Progress Tracker" \
  --binary ./target/release/progress_tracker \
  --enabled true

# Verify
./target/release/visionflow agent list
```

---

## 🧪 Test Semantic Reasoning (5 minutes)

### Create Simple Ontology

```bash
# Create ontology file
cat > learning_ontology.owl << 'EOF'
@prefix : <http://example.org/learning#> .
@prefix owl: <http://www.w3.org/2002/07/owl#> .
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .

# Classes
:Technology a owl:Class .
:Project a owl:Class .
:Resource a owl:Class .

# Properties
:requires a owl:ObjectProperty, owl:TransitiveProperty ;
    rdfs:domain :Technology ;
    rdfs:range :Technology .

:uses a owl:ObjectProperty ;
    rdfs:domain :Project ;
    rdfs:range :Technology .

:references a owl:ObjectProperty ;
    rdfs:domain :Resource ;
    rdfs:range :Technology .

# Inference Rule:
# If Project uses Tech A, and Tech A requires Tech B,
# then Project indirectly requires Tech B
:indirectlyRequires a owl:ObjectProperty ;
    owl:propertyChainAxiom ( :uses :requires ) .
EOF
```

### Upload Ontology

```bash
# Via CLI
./target/release/visionflow ontology upload learning_ontology.owl

# Or via API
curl -X POST http://localhost:8080/api/ontology \
  -H "Content-Type: application/rdf+xml" \
  --data-binary @learning_ontology.owl
```

### Test Inference

```bash
# Query: What does my project indirectly require?
curl http://localhost:8080/api/query \
  -H "Content-Type: application/json" \
  -d '{
    "query": "SELECT ?tech WHERE { ?project :indirectlyRequires ?tech }",
    "reasoning": true
  }'

# Expected: Should infer transitive dependencies!
```

---

## 🎨 Customize Visualization (5 minutes)

### Change Node Appearance

```javascript
// In browser console or custom script

// Get graph instance
const graph = window.visionflowGraph;

// Custom styling based on node type
graph.setNodeStyle('Technology', {
  color: '#4CAF50',
  size: 15,
  shape: 'sphere'
});

graph.setNodeStyle('Project', {
  color: '#2196F3',
  size: 20,
  shape: 'cube'
});

graph.setNodeStyle('Resource', {
  color: '#FF9800',
  size: 10,
  shape: 'pyramid'
});

// Custom edge styling
graph.setEdgeStyle('uses', {
  color: '#9C27B0',
  width: 3,
  dashed: false
});

graph.setEdgeStyle('requires', {
  color: '#F44336',
  width: 2,
  dashed: true
});
```

### Enable Physics Simulation

```javascript
graph.enablePhysics({
  repulsion: 1000,      // Nodes push apart
  springLength: 100,    // Edge length
  springStrength: 0.1,  // Edge pull
  damping: 0.9         // Movement decay
});
```

---

## 📊 Monitor Your System

### View Agent Status

```bash
# List all agents
curl http://localhost:8080/api/agents

# Get specific agent
curl http://localhost:8080/api/agents/progress_tracker

# View agent logs
curl http://localhost:8080/api/agents/progress_tracker/logs
```

### Check Performance

```bash
# System health
curl http://localhost:8080/health

# Graph statistics
curl http://localhost:8080/api/stats

# Example output:
# {
#   "nodes": 6,
#   "edges": 5,
#   "agents": 1,
#   "ontologies": 1,
#   "uptime": "2h 15m",
#   "memory": "145 MB",
#   "cpu": "3.2%"
# }
```

---

## 🎯 What's Next?

### Beginner Challenges

1. **Add 20+ nodes** about your learning goals
2. **Deploy 3 different agents** (tracker, recommender, analyzer)
3. **Create a domain ontology** (your field of study)
4. **Build a custom visualization** with different colors/shapes
5. **Set up automated backups** of your knowledge graph

### Intermediate Challenges

1. **Multi-agent collaboration** (3+ agents working together)
2. **External data integration** (import from APIs)
3. **Custom inference rules** (complex reasoning)
4. **Real-time collaboration** (multi-user graphs)
5. **Performance optimization** (10k+ nodes at 60 FPS)

### Advanced Challenges

1. **GPU-accelerated physics** (custom CUDA kernels)
2. **WebXR support** (VR/AR visualization)
3. **Distributed deployment** (multiple servers)
4. **Custom agent types** (domain-specific behaviors)
5. **Contribute to VisionFlow** (open source contributions)

---

## 🆘 Troubleshooting

### Docker Issues

```bash
# Container won't start
docker-compose logs visionflow

# Port already in use
sudo lsof -i :8080
kill -9 <PID>
docker-compose restart

# Reset everything
docker-compose down -v
docker-compose up -d
```

### Agent Issues

```bash
# Agent not spawning
./target/release/visionflow agent validate <agent-name>

# Agent crashing
tail -f logs/agents/<agent-name>.log

# Restart agent
./target/release/visionflow agent restart <agent-name>
```

### Performance Issues

```javascript
// Reduce node count shown
graph.setMaxVisibleNodes(1000);

// Enable Level of Detail
graph.enableLOD(true);

// Simplify distant nodes
graph.setSimplificationDistance(50);
```

---

## 📚 Resources

### Documentation
- [Full Implementation Plan](IMPLEMENTATION_PLAN.md)
- [Comprehensive Analysis](../docs/VISIONFLOW_COMPREHENSIVE_ANALYSIS.md)
- [Quick Reference](../docs/VISIONFLOW_QUICK_REFERENCE.md)
- [Official VisionFlow Docs](https://github.com/DreamLab-AI/VisionFlow/docs)

### Learning
- [Rust Book](https://doc.rust-lang.org/book/)
- [OWL 2 Primer](https://www.w3.org/TR/owl2-primer/)
- [Three.js Journey](https://threejs-journey.com/)
- [Actor Model](https://www.brianstorti.com/the-actor-model/)

### Community
- GitHub: https://github.com/DreamLab-AI/VisionFlow
- Discussions: https://github.com/DreamLab-AI/VisionFlow/discussions
- Issues: https://github.com/DreamLab-AI/VisionFlow/issues

---

## ✅ Checklist

- [ ] VisionFlow running (http://localhost:8080)
- [ ] Created 5+ nodes
- [ ] Added relationships
- [ ] Viewed 3D graph
- [ ] Deployed first agent
- [ ] Uploaded ontology
- [ ] Tested reasoning
- [ ] Customized visualization
- [ ] Checked system stats
- [ ] Ready for next phase!

---

**Congratulations! You're now a VisionFlow user! 🎉**

**Next:** Review the [Full Implementation Plan](IMPLEMENTATION_PLAN.md) to continue your journey to mastery!
