# NeurWall

> An open-source, AI-native firewall operating system that thinks, adapts, and protects — without you touching a rule.

![Status](https://img.shields.io/badge/status-concept%20%2F%20early%20design-orange)
![License](https://img.shields.io/badge/license-MIT-blue)
![Contributions](https://img.shields.io/badge/contributions-welcome-brightgreen)

---

## What Is NeurWall?

NeurWall is a firewall OS built from the ground up with machine learning and LLM intelligence at its core. Instead of a human writing static rules, NeurWall observes your network traffic, learns what is normal, and rewrites its own firewall rules automatically — then explains every decision in plain English.

Think: **pfSense meets a local AI brain.**

No subscriptions. No cloud dependency. No PhD required to manage it. Just flash it onto your hardware and let it learn.

---

## The Problem With Existing Firewalls

Every firewall today — pfSense, OPNsense, iptables, even enterprise solutions — shares the same fundamental flaw:

- Rules are written by humans
- Humans are slow, make mistakes, and cannot read millions of packets per second
- Threats evolve faster than rule updates
- No existing open-source firewall OS has native AI/ML built in

NeurWall fixes this at the architecture level.

---

## How It Works

```
[ Network Traffic ]
        │
        ▼
[ Packet Capture Layer ]  ←── libpcap / eBPF
        │
        ▼
[ Traffic Classifier ]    ←── ML model (benign / scan / DDoS / exfil / etc.)
        │
        ▼
[ Rule Engine ]           ←── RL agent rewrites nftables rules dynamically
        │
        ▼
[ LLM Reasoning Layer ]   ←── Local model explains decisions in plain English
        │
        ▼
[ Web Dashboard ]         ←── See what's happening and why
```

All of this runs **on-device**. No cloud. No telemetry sent anywhere.

---

## Goals

### Short Term
- [ ] Minimal bootable OS image (Alpine Linux base)
- [ ] Packet capture and flow telemetry pipeline
- [ ] Traffic classification model (ML)
- [ ] Dynamic nftables rule rewriting engine

### Medium Term
- [ ] Reinforcement learning agent for adaptive routing
- [ ] On-device LLM integration (Phi-3 mini / Gemma 2B via llama.cpp)
- [ ] Natural language interface: *"Why did you block 192.168.1.5?"*
- [ ] Web dashboard with real-time traffic visualization

### Long Term
- [ ] Flashable image for x86 mini PCs (Protectli, APU2)
- [ ] ARM support (Raspberry Pi 4, NanoPi R5S)
- [ ] OpenWrt-compatible build for consumer routers
- [ ] Community threat model sharing

---

## Planned Architecture

| Layer | Technology |
|---|---|
| Base OS | Alpine Linux / Buildroot |
| Packet Inspection | nftables, libpcap, eBPF |
| Telemetry Storage | InfluxDB / SQLite (on-device) |
| Traffic Classifier | scikit-learn / ONNX (quantized) |
| RL Agent | Python, stable-baselines3 |
| On-device LLM | llama.cpp (Phi-3 mini or Gemma 2B) |
| Management API | Python (FastAPI) |
| Dashboard | React + lightweight web server |

---

## Target Hardware

NeurWall is designed to run on affordable, commodity hardware:

| Device | Status |
|---|---|
| x86 mini PC (Protectli Vault, PC Engines APU) | Primary target |
| Raspberry Pi 4 (4GB+) | Secondary target |
| NanoPi R5S / R4S | Planned |
| Consumer routers (OpenWrt-compatible) | Long-term goal |

---

## Who We Need

This project is community-built. We need contributors across every layer:

| Role | What You'll Work On |
|---|---|
| **Linux / Kernel** | Base OS image, netfilter, eBPF hooks |
| **ML Engineers** | Traffic classifier, anomaly detection models |
| **LLM / NLP** | Model quantization, llama.cpp integration, prompt engineering |
| **Backend Developers** | FastAPI management layer, rule compiler |
| **Frontend Developers** | React dashboard, real-time visualisation |
| **Security Researchers** | Threat modelling, red team testing |
| **Embedded / Hardware** | OpenWrt porting, ARM builds |
| **Technical Writers** | Documentation, architecture diagrams |

No experience in networking? That is fine. If you write good Python, Go, or JavaScript — there is a place for you here.

---

## Community

This project is being coordinated via a WhatsApp developer group.

> **[Join the WhatsApp Group →](#)**  
> *(https://chat.whatsapp.com/Le8mor1A5lK0UEAqdxJjXp?mode=gi_t)*

We use GitHub Issues for task tracking and GitHub Discussions for design decisions. The WhatsApp group is for real-time coordination and brainstorming.

---

## Design Principles

1. **Local-first** — everything runs on the device, no cloud required
2. **Explainable** — every block or allow decision can be explained in plain English
3. **Open** — MIT licensed, no commercial lock-in
4. **Lean** — must boot and run on a $60 SBC with 4GB RAM
5. **Incremental** — works as a basic firewall on day one, gets smarter over time

---

## Contributing

We do not have code to contribute to yet — that is the point. Right now the most valuable contribution is:

- Joining the discussion and sharing expertise
- Proposing architecture decisions via GitHub Discussions
- Helping define the ML model training data strategy
- Sharing research papers or prior art

See [CONTRIBUTING.md](CONTRIBUTING.md) *(coming soon)* for guidelines.

---

## License

MIT — free to use, modify, and distribute.

---

## Inspiration & Related Work

- [pfSense](https://www.pfsense.org/) / [OPNsense](https://opnsense.org/) — the firewall OS baseline we are building beyond
- [Suricata](https://suricata.io/) — open-source IDS/IPS engine
- [OpenWrt](https://openwrt.org/) — open Linux for routers
- [llama.cpp](https://github.com/ggerganov/llama.cpp) — on-device LLM inference
- [NetML](https://github.com/NetML) — ML for network traffic research

---

*NeurWall is in early concept/design phase. Stars and watchers help signal momentum to potential contributors.*
