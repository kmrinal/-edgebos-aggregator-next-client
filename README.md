# EdgeBOS Aggregator Next Client

A next-generation multi-link aggregator **bonding client** that implements link aggregation at a lower level instead of relying on MPTCP (Multipath TCP). This client component runs on edge devices with multiple network interfaces and works in tandem with the **EdgeBOS Aggregator Next Server** to provide true multi-link bonding capabilities.

## Overview

EdgeBOS Aggregator Next Client is the client-side component of a client-server bonding architecture. The client bonds multiple network interfaces (4G/5G, WiFi, Ethernet, etc.) on edge devices and intelligently distributes traffic across them to the EdgeBOS Aggregator Next Server. This approach provides more granular control over packet routing, load balancing, and failover mechanisms compared to traditional MPTCP.

### How It Works

1. **Client Side**: This client component runs on edge devices with multiple network interfaces
2. **Bonding**: Traffic is intelligently distributed across multiple links using custom algorithms
3. **Server Side**: EdgeBOS Aggregator Next Server receives and reassembles the bonded traffic
4. **Routing**: Server forwards traffic to final destinations (internet/internal network)

This architecture allows for:

- **Direct packet-level control**: Manage packet routing and distribution across multiple links
- **Custom load balancing algorithms**: Implement sophisticated strategies beyond what MPTCP offers
- **Enhanced failover mechanisms**: Faster detection and recovery from link failures
- **Protocol-agnostic aggregation**: Not limited to TCP connections
- **Fine-grained QoS**: Per-packet or per-flow quality of service controls

## Key Differences from Previous Version

The original EdgeBOS Aggregator focused on system monitoring and heartbeat services. This next-generation version is fundamentally different:

| Feature | Previous Version | Next Generation |
|---------|-----------------|-----------------|
| **Primary Purpose** | System monitoring & heartbeat | Multi-link network aggregation |
| **Aggregation Method** | N/A | Custom lower-level implementation |
| **Network Layer** | Application layer | Network/Transport layer |
| **Use Case** | Server health monitoring | Bandwidth aggregation & redundancy |

## Architecture

### Client-Server Model

```
┌─────────────────────────────────────┐
│   EdgeBOS Aggregator Next Client   │
│   (Edge Device with Multiple Links) │
│                                     │
│   ┌──────┐ ┌──────┐ ┌──────┐      │
│   │ 4G/5G│ │ WiFi │ │ LTE  │      │
│   └───┬──┘ └───┬──┘ └───┬──┘      │
│       └────────┴────────┘          │
│            Bonding                  │
└───────────────┬─────────────────────┘
                │ Aggregated Traffic
                ▼
┌─────────────────────────────────────┐
│ EdgeBOS Aggregator Next Server      │
│ (Ubuntu 24/25)                      │
│                                     │
│  • Receives bonded traffic          │
│  • Reassembles packets              │
│  • Routes to destination            │
│  • Monitors link health             │
└─────────────────────────────────────┘
```

### Client Responsibilities

The client operates at a lower network stack level to:

1. **Detect and manage** multiple network interfaces (4G/5G, WiFi, Ethernet, etc.)
2. **Establish connections** to EdgeBOS Aggregator Next Server via all available links
3. **Distribute packets** across bonded links using intelligent algorithms
4. **Monitor link quality** (latency, bandwidth, packet loss) in real-time
5. **Handle failover** automatically when links go down
6. **Maintain session state** and reconnection logic
7. **Report metrics** to the server for coordinated load balancing

## Project Status

🚧 **This project is currently in active development** 🚧

The next-generation aggregator client is being built from the ground up with a focus on performance, reliability, and flexibility.

## Planned Features

### Core Client Features
- [ ] Multi-interface detection and management
- [ ] Packet distribution across bonded links
- [ ] Custom load balancing algorithms (round-robin, weighted, latency-based, adaptive)
- [ ] Real-time link quality monitoring
- [ ] Automatic failover and recovery
- [ ] Dynamic link addition/removal (hot-plug support)
- [ ] Traffic shaping and QoS per interface
- [ ] Protocol tunneling support (GRE, VXLAN, custom)
- [ ] Local routing and NAT traversal

### Management & Monitoring
- [ ] Command-line interface for configuration
- [ ] Web-based status dashboard
- [ ] RESTful API for local management
- [ ] Per-interface statistics and metrics
- [ ] Comprehensive logging
- [ ] Real-time link status visualization
- [ ] Alert system for link failures

### Security
- [ ] Encrypted tunnels to server
- [ ] Client certificate authentication
- [ ] Secure credential storage

### Platform Support
- [ ] Linux (Ubuntu, Debian, Fedora, etc.)
- [ ] Windows (Windows 10/11)
- [ ] macOS
- [ ] OpenWrt/LEDE (for routers)
- [ ] Android (future)
- [ ] iOS (future)

## System Requirements

### Client Requirements
- **OS**: 
  - Linux: Ubuntu 20.04+, Debian 11+, Fedora 35+, or similar
  - Windows: Windows 10/11
  - macOS: macOS 11 (Big Sur) or later
  - OpenWrt: 21.02 or later
- **Kernel**: Linux kernel 5.x or higher (for advanced networking features)
- **Privileges**: Root/Administrator privileges (for network interface manipulation)
- **Network**: Multiple network interfaces (minimum 2 for bonding)
- **Hardware**: 
  - Minimum: 1 CPU core, 512MB RAM
  - Recommended: 2+ CPU cores, 1GB+ RAM
- **Software**: Python 3.10+ or Go 1.20+ (implementation language TBD)

### Server Requirements
- See EdgeBOS Aggregator Next Server documentation

## Quick Start (Coming Soon)

### Installation on Linux

```bash
# Download and extract the client package
tar -xzf edgebos-aggregator-next-client.tar.gz
cd edgebos-aggregator-next-client

# Install dependencies
./install.sh

# Configure the client
cp config.example.yaml config.yaml
nano config.yaml

# Start the client
sudo ./edgebos-client start
```

### Installation on Windows

```powershell
# Download the installer
# Run as Administrator
.\edgebos-client-installer.exe

# Configure via GUI or edit config file
notepad C:\Program Files\EdgeBOS\config.yaml

# Start the service
Start-Service EdgeBOSClient
```

### Installation on macOS

```bash
# Download and install the macOS package
# Open the .pkg installer and follow the instructions

# Or install via command line
sudo installer -pkg edgebos-client.pkg -target /

# Configure the client
cp /usr/local/etc/edgebos/config.example.yaml /usr/local/etc/edgebos/config.yaml
nano /usr/local/etc/edgebos/config.yaml

# Start the client
sudo edgebos-client start
```

### Basic Configuration

Edit `config.yaml` to configure your client:

```yaml
server:
  host: "your-server.example.com"
  port: 8443
  
interfaces:
  # Auto-detect all interfaces
  auto_detect: true
  
  # Or manually specify interfaces
  # manual:
  #   - name: "eth0"
  #     priority: 1
  #   - name: "wlan0"
  #     priority: 2
  #   - name: "ppp0"
  #     priority: 3

bonding:
  algorithm: "adaptive"  # Options: round-robin, weighted, latency-based, adaptive
  
security:
  certificate: "/path/to/client-cert.pem"
  key: "/path/to/client-key.pem"
```

## Documentation

Detailed documentation will be added as the project develops:

- Client architecture and design
- Installation and deployment guide
- Configuration reference
- Client-server protocol specification
- API reference
- Performance tuning and optimization
- Troubleshooting guide
- Platform-specific guides

## Use Cases

- **Mobile/Edge Computing**: Aggregate 4G/5G connections with WiFi for increased bandwidth
- **Remote Locations**: Bond multiple satellite/cellular links for reliable connectivity
- **Failover Systems**: Automatic failover between primary and backup connections
- **Bandwidth Intensive Applications**: Video streaming, large file transfers, real-time data
- **IoT Gateways**: Aggregate multiple low-bandwidth connections into a single high-bandwidth pipe
- **Mobile Vehicles**: Maintain connectivity while moving (trains, buses, emergency vehicles)
- **Live Broadcasting**: Ensure reliable uplink for live video streaming
- **Remote Work**: Combine multiple connections for better home office connectivity

## Supported Network Interfaces

The client can bond various types of network interfaces:

- **Cellular**: 4G LTE, 5G, 3G
- **WiFi**: 802.11a/b/g/n/ac/ax
- **Ethernet**: Wired connections
- **Satellite**: Starlink, traditional satellite
- **USB Tethering**: Mobile phone connections
- **PPP**: Point-to-point connections
- **VPN**: Existing VPN connections (nested bonding)

## Load Balancing Algorithms

### Round-Robin
Distributes packets evenly across all available links in sequence.

### Weighted
Distributes packets based on configured weights (e.g., prefer faster links).

### Latency-Based
Routes packets based on real-time latency measurements.

### Adaptive (Recommended)
Dynamically adjusts distribution based on:
- Link latency
- Available bandwidth
- Packet loss rate
- Link stability
- Historical performance

## About Benlycos

EdgeBOS Aggregator Next Client is developed and owned by **Benlycos Pvt Ltd**.

Visit us at: [benlycos.com](https://benlycos.com)

This is a proprietary commercial product. All rights reserved.

## License

Copyright © 2025-2026 Benlycos Pvt Ltd. All rights reserved.

This software is proprietary and confidential. Unauthorized copying, distribution, or use of this software, via any medium, is strictly prohibited.

## Related Products

- **EdgeBOS Aggregator Next Server** - The server component that receives and reassembles bonded traffic from this client
- EdgeBOS Aggregator (v1) - The original system monitoring service

All EdgeBOS products are developed by Benlycos Pvt Ltd.

## FAQ

### How is this different from MPTCP?

While MPTCP (Multipath TCP) operates at the TCP protocol level, EdgeBOS Aggregator Next works at a lower network level, providing:
- Support for non-TCP protocols (UDP, ICMP, etc.)
- More granular control over packet routing
- Custom load balancing algorithms
- Faster failover detection and recovery
- Protocol-agnostic aggregation

### Can I use this with a VPN?

Yes! The bonded connection can carry VPN traffic, or you can even bond multiple VPN connections together.

### How many interfaces can I bond?

There's no hard limit, but practical considerations (CPU, bandwidth) typically make 2-8 interfaces optimal.

### Does this work with metered connections?

Yes, you can configure per-interface bandwidth limits and priorities to control usage on metered connections.

### What's the latency overhead?

The bonding overhead is minimal (typically <5ms) as packet processing happens at a low level.

## Troubleshooting

### Client won't start
- Ensure you're running with root/administrator privileges
- Check that the server address is reachable
- Verify firewall settings

### Interface not detected
- Check interface status: `ip link show` (Linux) or `ipconfig` (Windows)
- Ensure interface is up and has an IP address
- Check interface permissions

### Poor performance
- Verify link quality with built-in diagnostics
- Check for packet loss on individual links
- Ensure server has adequate resources
- Try different load balancing algorithms

## Support

For technical support, sales inquiries, or more information:

- **Website**: [benlycos.com](https://benlycos.com)
- **Email**: support@benlycos.com
- **Sales**: sales@benlycos.com
- **Documentation**: Available to licensed customers

## Roadmap

### Phase 1: Core Functionality (Q1 2026)
- Basic multi-interface bonding
- Connection to server
- Round-robin load balancing
- Linux support

### Phase 2: Enhanced Features (Q2 2026)
- Advanced load balancing algorithms
- Windows and macOS support
- Web-based dashboard
- Comprehensive metrics

### Phase 3: Advanced Features (Q3 2026)
- Mobile platform support (Android/iOS)
- OpenWrt support
- Advanced QoS features
- AI-powered adaptive routing

### Phase 4: Enterprise Features (Q4 2026)
- Centralized management
- Advanced security features
- High availability configurations
- Enterprise support options