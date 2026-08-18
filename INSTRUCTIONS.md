# Assignment 29: Connecting VMW Workstation Nets to Physical Nets

## Learning Goals

Originally assignment 19.

The student can:

- Draw a HLD, High Level Network Design.
- Create a LLD, Low Level Network Design.
- Use Wireshark and Junos monitor traffic to monitor relevant network traffic.
- Interconnect virtualised networks via a bridge and physical Ethernet adapters and cables.
- Explain and document all of the above.

## Reference Images

- [Network topology](images/network-topology.png)
- [Example LLD table](images/device-interface-table.png)

## Tasks Part 1

1. By means of VMware Workstations on Host Computer 1 and physical routers, build the network complete with PCs on the subnets. Ultimately, all user hosts, PCs, should be able to ping each other. Use Raspberries or laptops on the physical network. Use bridging to interconnect the network on Host Computer 1 with the physical world router(s) as shown in the diagram. Use a cabled Ethernet adapter port for the interconnection.

   Hint: Download the needed software, such as Wireshark and TCPdump, to the virtual PCs before connecting them to the routers. For example, use VMnet8 with NAT initially to have internet access on the PCs.

2. Create a working GitHub link to well-organised router configurations.
3. Create a HLD with a brief explanation.
4. Create a LLD with a brief explanation.
5. Show and describe how Wireshark and Junos monitor traffic was used for problem solving.
6. Demonstrate how Wireshark and Junos monitor traffic is used to monitor relevant network traffic.
7. Show and describe the configuration of the interconnected virtualised network via bridge, physical Ethernet adapters, and cable.
8. Show that pings work between all subnets.
9. Show one virtual router and one physical router routing table, and explain very briefly the interesting parts.
10. Show one or more PC routing tables with `route -n`, `ip route`, and/or `netstat -rn`, and explain the relevant parts.

## Tasks Part 2

Optional.

1. Provide internet access for all PCs from/via one of the physical routers.
2. Provide a DHCP service for all PCs from one or more of the routers.

## Configurations

Please find the base configurations for this assignment below.

### vSRX_1

```text
## Last changed: 2020-11-23 18:00:10 UTC
/* Assignment 29. Outset for four routers. */
/* By Per Dahlstroem */
version 12.1X47-D15.4;
system {
    host-name vSRX_1;
    /* User: root Password: Rootpass */
    root-authentication {
        encrypted-password "$1$4TkbZDtp$6E8C6Bg7K6gnHR31XnJjl0";
    }
}
interfaces {
    ge-0/0/1 {
        unit 0 {
            family inet {
                address 192.168.10.1/24;
            }
        }
    }
    ge-0/0/2 {
        unit 0 {
            family inet {
                address 192.168.11.1/24;
            }
        }
    }
    ge-0/0/3 {
        unit 0 {
            family inet {
                address 10.10.10.1/30;
            }
        }
    }
}
routing-options {
    static {
        route 192.168.12.0/24 next-hop 10.10.10.2;
        route 192.168.13.0/24 next-hop 10.10.10.2;
    }
}
security {
    policies {
        from-zone myTrust_1 to-zone myTrust_1 {
            policy default-permit {
                match {
                    source-address any;
                    destination-address any;
                    application any;
                }
                then {
                    permit;
                }
            }
        }
    }
    zones {
        security-zone myTrust_1 {
            interfaces {
                ge-0/0/1.0;
                ge-0/0/2.0;
                ge-0/0/3.0;
            }
            host-inbound-traffic {
                system-services {
                    ping;
                    traceroute;
                }
            }
        }
    }
}
```

### vSRX_2

```text
## Last changed: 2020-11-23 18:00:10 UTC
/* Assignment 29. Outset for four routers. */
/* By Per Dahlstroem */
version 12.1X47-D15.4;
system {
    host-name vSRX_2;
    /* User: root Password: Rootpass */
    root-authentication {
        encrypted-password "$1$4TkbZDtp$6E8C6Bg7K6gnHR31XnJjl0";
    }
    services {
        ssh;
    }
}
interfaces {
    ge-0/0/1 {
        unit 0 {
            family inet {
                address 192.168.12.1/24;
            }
        }
    }
    ge-0/0/2 {
        unit 0 {
            family inet {
                address 192.168.13.1/24;
            }
        }
    }
    ge-0/0/4 {
        unit 0 {
            family inet {
                address 10.10.10.2/30;
            }
        }
    }
}
routing-options {
    static {
        route 192.168.10.0/24 next-hop 10.10.10.1;
        route 192.168.11.0/24 next-hop 10.10.10.1;
    }
}
security {
    policies {
        from-zone myTrust_2 to-zone myTrust_2 {
            policy default-permit {
                match {
                    source-address any;
                    destination-address any;
                    application any;
                }
                then {
                    permit;
                }
            }
        }
    }
    zones {
        security-zone myTrust_2 {
            interfaces {
                ge-0/0/1.0 {
                    host-inbound-traffic {
                        system-services {
                            ping;
                        }
                    }
                }
                ge-0/0/2.0 {
                    host-inbound-traffic {
                        system-services {
                            ping;
                        }
                    }
                }
                ge-0/0/4.0 {
                    host-inbound-traffic {
                        system-services {
                            ping;
                        }
                    }
                }
            }
        }
    }
}
```

## Documentation to Hand In

- Document the tasks.
- Optional: Document the Part 2 tasks.
