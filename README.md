# EdgeBOS Aggregator Next Client

A next-generation multi-link aggregator **bonding client** designed for **OpenWrt 24** that implements link aggregation at a lower level instead of relying on MPTCP (Multipath TCP). This client component runs on OpenWrt-based routers and edge devices with multiple network interfaces and works in tandem with the **EdgeBOS Aggregator Next Server** to provide true multi-link bonding capabilities.

## Overview

EdgeBOS Aggregator Next Client is the client-side component of a client-server bonding architecture, specifically designed for **OpenWrt 24** routers and edge devices. The client bonds multiple network interfaces (4G/5G, WiFi, Ethernet, etc.) and intelligently distributes traffic across them to the EdgeBOS Aggregator Next Server. This approach provides more granular control over packet routing, load balancing, and failover mechanisms compared to traditional MPTCP.

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
│        (OpenWrt 24 Router)          │
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

The next-generation aggregator client is being built from the ground up with a focus on performance, reliability, and flexibility, optimized specifically for OpenWrt 24's embedded Linux environment.

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
- [ ] OpenWrt 24.x (primary target)
- [ ] OpenWrt 23.x (compatibility mode)

## System Requirements

### Client Requirements
- **OS**: OpenWrt 24.x (OpenWrt 23.x supported in compatibility mode)
- **Kernel**: Linux kernel 6.1+ (included in OpenWrt 24)
- **Architecture**: 
  - ARM (armv7, armv8/aarch64)
  - MIPS (mips, mipsel)
  - x86_64
  - Other architectures supported by OpenWrt 24
- **Flash Storage**: Minimum 16MB free space (32MB+ recommended)
- **RAM**: 
  - Minimum: 128MB RAM
  - Recommended: 256MB+ RAM for optimal performance
- **Network**: Multiple network interfaces (minimum 2 for bonding)
  - USB modems (4G/5G)
  - WiFi interfaces
  - Ethernet ports
  - PPP connections
- **OpenWrt Packages**: 
  - kmod-tun (for tunneling)
  - ip-full or ip-tiny
  - Additional kernel modules as needed

### Server Requirements
- See EdgeBOS Aggregator Next Server documentation

## Quick Start (Coming Soon)

### Installation on OpenWrt 24

#### Method 1: Using opkg (Recommended)

```bash
# Add the EdgeBOS package repository
echo "src/gz edgebos https://packages.benlycos.com/openwrt/24" >> /etc/opkg/customfeeds.conf

# Update package lists
opkg update

# Install EdgeBOS Aggregator Next Client
opkg install edgebos-aggregator-next-client

# The service will be installed but not started automatically
```

#### Method 2: Manual Installation

```bash
# Download the .ipk package for your architecture
# Example for ARM64:
wget https://packages.benlycos.com/openwrt/24/edgebos-aggregator-next-client_1.0.0_aarch64.ipk

# Install the package
opkg install edgebos-aggregator-next-client_1.0.0_aarch64.ipk

# Install dependencies if prompted
opkg install kmod-tun ip-full
```

#### Method 3: From Source (Advanced)

```bash
# On your OpenWrt build system
git clone https://git.benlycos.com/edgebos-aggregator-next-client.git
cd edgebos-aggregator-next-client

# Build for your target
./scripts/build-openwrt.sh --target=aarch64

# Transfer the .ipk to your router and install
scp bin/edgebos-aggregator-next-client_*.ipk root@192.168.1.1:/tmp/
ssh root@192.168.1.1 'opkg install /tmp/edgebos-aggregator-next-client_*.ipk'
```

### Configuration

#### Using LuCI Web Interface (Recommended)

1. Navigate to **Services → EdgeBOS Aggregator** in LuCI
2. Configure server connection settings
3. Select interfaces to bond
4. Choose load balancing algorithm
5. Save and apply changes

#### Using UCI Command Line

```bash
# Configure server connection
uci set edgebos.@client[0].server_host='your-server.example.com'
uci set edgebos.@client[0].server_port='8443'

# Enable auto-detection of interfaces
uci set edgebos.@client[0].auto_detect='1'

# Or manually configure interfaces
uci set edgebos.@client[0].auto_detect='0'
uci add_list edgebos.@client[0].interfaces='wwan0'
uci add_list edgebos.@client[0].interfaces='wlan0'
uci add_list edgebos.@client[0].interfaces='eth1'

# Set load balancing algorithm
uci set edgebos.@client[0].algorithm='adaptive'

# Configure security (if using certificates)
uci set edgebos.@client[0].certificate='/etc/edgebos/client-cert.pem'
uci set edgebos.@client[0].key='/etc/edgebos/client-key.pem'

# Commit changes
uci commit edgebos

# Restart the service
/etc/init.d/edgebos restart
```

#### Manual Configuration File

Edit `/etc/config/edgebos`:

```
config client 'client'
	option server_host 'your-server.example.com'
	option server_port '8443'
	option auto_detect '1'
	option algorithm 'adaptive'
	option enabled '1'
	
	# Manual interface configuration (if auto_detect is 0)
	# list interfaces 'wwan0'
	# list interfaces 'wlan0'
	# list interfaces 'eth1'
	
	# Security settings
	option certificate '/etc/edgebos/client-cert.pem'
	option key '/etc/edgebos/client-key.pem'
```

## Service Management

### Starting/Stopping the Service

```bash
# Start the service
/etc/init.d/edgebos start

# Stop the service
/etc/init.d/edgebos stop

# Restart the service
/etc/init.d/edgebos restart

# Check service status
/etc/init.d/edgebos status

# Enable service at boot
/etc/init.d/edgebos enable

# Disable service at boot
/etc/init.d/edgebos disable
```

### Monitoring and Logs

```bash
# View real-time logs
logread -f | grep edgebos

# View service status
edgebos-client status

# View interface statistics
edgebos-client stats

# View bonded connections
edgebos-client connections
```

## Documentation

Detailed documentation will be added as the project develops:

- OpenWrt-specific architecture and design
- Installation and deployment guide for OpenWrt 24
- UCI configuration reference
- LuCI interface guide
- Client-server protocol specification
- API reference
- Performance tuning for embedded devices
- Troubleshooting guide
- Hardware compatibility list

## Use Cases

Perfect for OpenWrt-based deployments:

- **Mobile Routers**: Aggregate multiple 4G/5G USB modems with WiFi for increased bandwidth
- **Remote Site Connectivity**: Bond multiple cellular links for reliable connectivity in remote locations
- **Failover Routers**: Automatic failover between primary and backup WAN connections
- **RV/Marine Internet**: Combine cellular, satellite, and WiFi connections for mobile connectivity
- **Industrial IoT Gateways**: Aggregate multiple low-bandwidth connections for reliable data transmission
- **Emergency Response Vehicles**: Maintain connectivity while moving with bonded cellular links
- **Live Event Broadcasting**: Ensure reliable uplink for live video streaming from temporary locations
- **Branch Office Connectivity**: Cost-effective multi-WAN bonding for small offices
- **Temporary Installations**: Quick deployment of bonded connections for events, construction sites, etc.
- **Home Power Users**: Advanced multi-WAN setup for remote work and content creation

## Supported Network Interfaces

The client can bond various types of network interfaces commonly found in OpenWrt routers:

- **Cellular Modems**: 
  - USB 4G/5G modems (QMI, MBIM, NCM protocols)
  - Built-in cellular modules
  - 3G/LTE dongles
  - Compatible with most Qualcomm, Huawei, Sierra Wireless, Quectel modems
- **WiFi**: 
  - 802.11a/b/g/n/ac/ax (WiFi 6)
  - Client mode connections
  - Mesh networks
- **Ethernet**: 
  - WAN ports
  - LAN ports (configured as WAN)
  - USB Ethernet adapters
- **Satellite**: 
  - Starlink (via Ethernet)
  - Traditional satellite modems
- **USB Tethering**: 
  - Android devices
  - iOS devices (via USB)
- **PPP/PPPoE**: 
  - Point-to-point connections
  - DSL connections
- **VPN Interfaces**: 
  - Existing VPN connections (nested bonding)
  - WireGuard, OpenVPN, etc.

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
- Better compatibility with OpenWrt's network stack

### How is this different from mwan3?

mwan3 is a multi-WAN load balancer that works at the routing level. EdgeBOS Aggregator Next provides true bonding:
- **mwan3**: Routes different connections through different WANs (policy-based routing)
- **EdgeBOS**: Bonds WANs together, splitting individual connections across multiple links for true bandwidth aggregation

### Can I use this with a VPN?

Yes! The bonded connection can carry VPN traffic, or you can even bond multiple VPN connections together.

### How many interfaces can I bond?

There's no hard limit, but practical considerations (CPU, RAM, bandwidth) on OpenWrt routers typically make 2-4 interfaces optimal. High-end routers can handle more.

### Does this work with metered connections?

Yes, you can configure per-interface bandwidth limits and priorities to control usage on metered cellular connections.

### What's the latency overhead?

The bonding overhead is minimal (typically <5ms) as packet processing happens at a low level. On embedded OpenWrt devices, expect 5-10ms overhead depending on CPU.

### Which routers are supported?

Any OpenWrt 24-compatible router with sufficient RAM (256MB+) and multiple network interfaces. Popular choices include:
- GL.iNet routers (GL-X3000, GL-XE3000)
- Raspberry Pi with OpenWrt
- x86-based routers
- Industrial routers with multiple modem slots

### Do I need a special server?

Yes, you need to run the EdgeBOS Aggregator Next Server (Ubuntu 24/25) to receive and reassemble the bonded traffic.

### Can I use this for gaming?

Yes! The low-latency bonding is suitable for gaming, though results depend on the quality of your individual links. The adaptive algorithm helps route latency-sensitive traffic optimally.

## Troubleshooting

### Service won't start
```bash
# Check service status
/etc/init.d/edgebos status

# Check logs for errors
logread | grep edgebos

# Verify configuration
uci show edgebos

# Check if server is reachable
ping your-server.example.com

# Verify firewall rules
iptables -L -n | grep 8443
```

### Interface not detected
```bash
# List all network interfaces
ip link show

# Check interface status in OpenWrt
uci show network

# Verify interface is up and has IP
ifconfig

# Check modem status (for cellular)
mmcli -L  # if using ModemManager
uqmi -d /dev/cdc-wdm0 --get-data-status  # for QMI modems

# Restart network
/etc/init.d/network restart
```

### Cellular modem issues
```bash
# Check USB devices
lsusb

# Check kernel modules
lsmod | grep usb

# Check modem logs
logread | grep modem

# Restart modem manager
/etc/init.d/modemmanager restart
```

### Poor performance
```bash
# Check interface statistics
edgebos-client stats

# Monitor real-time traffic
iftop

# Check for packet loss
ping -I wwan0 8.8.8.8

# Verify server has adequate resources
edgebos-client server-status

# Try different load balancing algorithm
uci set edgebos.@client[0].algorithm='round-robin'
uci commit edgebos
/etc/init.d/edgebos restart
```

### High memory usage
```bash
# Check memory usage
free -m

# Check EdgeBOS memory usage
ps | grep edgebos

# Reduce buffer sizes in configuration
uci set edgebos.@client[0].buffer_size='512'
uci commit edgebos
/etc/init.d/edgebos restart
```

## Support

For technical support, sales inquiries, or more information:

- **Website**: [benlycos.com](https://benlycos.com)
- **Email**: support@benlycos.com
- **Sales**: sales@benlycos.com
- **Documentation**: Available to licensed customers

## Roadmap

### Phase 1: Core Functionality (Q1 2026)
- Basic multi-interface bonding for OpenWrt 24
- Connection to server
- Round-robin and weighted load balancing
- LuCI web interface
- UCI configuration support
- Support for common USB modems

### Phase 2: Enhanced Features (Q2 2026)
- Advanced load balancing algorithms (latency-based, adaptive)
- Real-time monitoring dashboard in LuCI
- Comprehensive metrics and statistics
- Support for additional modem protocols
- Automatic modem detection and configuration
- Bandwidth usage tracking per interface

### Phase 3: Advanced Features (Q3 2026)
- OpenWrt 23.x compatibility
- Advanced QoS features
- AI-powered adaptive routing
- Failover prediction and prevention
- SMS/email alerts for link failures
- Integration with OpenWrt's mwan3

### Phase 4: Enterprise Features (Q4 2026)
- Centralized fleet management
- Remote configuration and monitoring
- Advanced security features
- High availability configurations
- Enterprise support options
- Custom hardware integration