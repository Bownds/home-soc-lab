```mermaid. 
flowchart TD

    subgraph HOME["🏠 Home Network — 192.168.1.0/24"]
        ISP["📡 ISP Modem / Router\nWAN Gateway"]
        MESH["📶 Google Mesh\nWi-Fi + DHCP"]
        MBA["💻 MacBook Admin\n192.168.1.x — Wi-Fi"]
        MAC2["💻 MacBook\n192.168.1.x — Wi-Fi"]

        ISP -->|WAN| MESH
        MESH -.->|Wi-Fi| MBA
        MESH -.->|Wi-Fi| MAC2
    end

    subgraph OFFICE["🏢 Office Uplink"]
        MESH_UP["📶 Google Mesh Node\nUpstairs — Wi-Fi Backhaul"]
        BRIDGE["🔁 Wireless Bridge\nWi-Fi → Ethernet"]
        SWITCH["🔀 Office Switch\nEthernet"]

        MESH_UP -->|Ethernet backhaul| BRIDGE
        BRIDGE -->|Ethernet| SWITCH
    end

    subgraph PERIMETER["🛡️ Lab Perimeter"]
        FW["🔥 Optiplex 790\nOPNsense Firewall\nNAT + DHCP\nWAN: 192.168.1.x\nLAN: 10.10.10.1"]
    end

    subgraph LAB["⚙️ Lab Compute — 10.10.10.0/24"]
        PROX["🖥️ Optiplex 990\nProxmox VE\n10.10.10.10"]

        subgraph VMS["Virtual Machines"]
            KALI["🗡️ Kali Linux\nAttacker\n10.10.10.20"]
            UBUNTU["🐧 Ubuntu Server\nTarget\n10.10.10.30"]
            WIN["🪟 Windows VM\nEndpoint\n10.10.10.40"]
            SONION["🧅 Security Onion\nSIEM · IDS · NSM\n10.10.10.50"]
        end

        PROX --> KALI
        PROX --> UBUNTU
        PROX --> WIN
        PROX --> SONION
    end

    subgraph SOC["📺 SOC Visibility"]
        SMIL["🖥️ Smilodin Display\nKibana · Elastic\nSOC Dashboard"]
    end

    MESH -->|Ethernet — 192.168.1.0/24| MESH_UP
    SWITCH -->|WAN — 192.168.1.x| FW
    FW -->|LAN — 10.10.10.0/24| PROX
    SONION -->|Dashboards| SMIL

    style HOME     fill:#0d1f33,stroke:#38bdf8,color:#e2e8f0
    style OFFICE   fill:#0d1a0d,stroke:#34d399,color:#e2e8f0
    style PERIMETER fill:#1f0d0d,stroke:#fb923c,color:#e2e8f0
    style LAB      fill:#0d0d1f,stroke:#a78bfa,color:#e2e8f0
    style VMS      fill:#13133a,stroke:#7c6fcd,stroke-dasharray:4 4,color:#e2e8f0
    style SOC      fill:#1f1a00,stroke:#fbbf24,color:#e2e8f0

    style ISP    fill:#0a1628,stroke:#38bdf8,color:#cbd5e1
    style MESH   fill:#0a1628,stroke:#38bdf8,color:#cbd5e1
    style MBA    fill:#0a1628,stroke:#60a5fa,color:#cbd5e1
    style MAC2   fill:#0a1628,stroke:#60a5fa,color:#cbd5e1

    style MESH_UP fill:#0a1a0a,stroke:#34d399,color:#cbd5e1
    style BRIDGE  fill:#0a1a0a,stroke:#34d399,color:#cbd5e1
    style SWITCH  fill:#0a1a0a,stroke:#34d399,color:#cbd5e1

    style FW     fill:#2a0a00,stroke:#fb923c,color:#fed7aa

    style PROX   fill:#0a0a2a,stroke:#a78bfa,color:#cbd5e1
    style KALI   fill:#2a0a0a,stroke:#f87171,color:#fecaca
    style UBUNTU fill:#0a2a0a,stroke:#34d399,color:#bbf7d0
    style WIN    fill:#0a1a2a,stroke:#60a5fa,color:#bfdbfe
    style SONION fill:#1a0a2a,stroke:#a78bfa,color:#e9d5ff

    style SMIL   fill:#2a1a00,stroke:#fbbf24,color:#fef08a
  ```mermaid. 
