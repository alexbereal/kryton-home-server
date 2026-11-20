# Kryton | Home Plex Server and Hyper-V Lab

I built Kryton to run Plex Media Server at home and to practise Windows infrastructure administration. I run a Windows 11 VM in Hyper-V to download and share files I have permission to distribute via BitTorrent. I also use the lab to practise Active Directory and Windows client administration.

## Build and workloads

| Component | Configuration |
| --- | --- |
| Host OS | Windows 11 Pro (64-bit) with Hyper-V |
| Processor | Intel Core i7-8700 at 3.20 GHz |
| Motherboard | Gigabyte Z370 AORUS Gaming 7 (U3E1) |
| Chipset | Intel Z370 |
| BIOS | American Megatrends Inc., F15 (13 November 2021) |
| Memory | 32 GB (4 × 8 GB) TeamGroup TEAMGROUP-UD4-3200, dual-channel. Verified at DDR4-2133 (15-15-15-36). XMP profile rated for DDR4-3200 (14-14-14-31 at 1.35 V) |
| Graphics | Intel UHD Graphics 630 |
| Case and PSU | Fractal Design Define R4 Black and Corsair RM650 650 W Gold |
| CPU cooler | Cooler Master Hyper 212 EVO with upgraded fans |
| Case fans | Two front and one side Thermalright TL-C14C 140 mm PWM fans. One rear Noctua NF-A14 industrialPPC-3000 PWM |
| Storage controller | LSI SAS 9300-16i internal PCIe HBA |
| Front drive bays | Icy Dock MB994SK-1B and StarTech SATSAS425BK, four 2.5-inch hot-swap slots each |
| Remote power | SANT-SUC01 2.4 GHz Wi-Fi switch with eWeLink for remote power and reset |
| Media | Plex Media Server |
| File sharing | SMB shares for media and file transfers |
| Storage management | StableBit DrivePool and StableBit Scanner |
| Virtual networking | Hyper-V virtual switch and a separate internal virtual network |

## Storage and backups

I use three StableBit DrivePool volumes to separate media and personal files from downloads and torrents. The media pool uses two-copy file duplication to protect duplicated files against a single drive failure. EaseUS Todo Backup writes Windows and VM backups to a separate HDD.

| Pool | Total shown in DrivePool | Drives |
| --- | ---: | --- |
| D: Media and files | 36.8 TB | 2 × 250 GB Samsung SSD 840 and 4 × 10 TB WD Red Pro |
| E: Downloads | 5.88 TB | 2 × 240 GB OCZ Trion 100 and 2 × 3 TB WD Red |
| F: Torrents | 2.42 TB | 1 × 256 GB OCZ Vector and 6 × SAS HDDs |

### Drives reported through the HBA

The table uses Windows disk numbers from the captured configuration. The cable labels match the numbered positions on the front drive backplanes.

| Disk numbers | Model | Capacity each | Pool |
| --- | --- | ---: | --- |
| 5–6 | Samsung SSD 840 (SATA) | 250 GB | D: |
| 7 | HP EG0300FCSPH (SAS) | 300 GB | F: |
| 8 | HP EG0300FBDSP (SAS) | 300 GB | F: |
| 9–12 | WD Red Pro WD101KFBX (SATA) | 10 TB | D: |
| 13–16 | Hitachi Ultrastar C15K600 HUC156045CSS200 (SAS, 15,000 RPM) | 450 GB | F: |
| 17–18 | OCZ Trion 100 (SATA) | 240 GB | E: |
| 19–20 | WD Red WD30EFRX (SATA) | 3 TB | E: |

There are 16 drives on the SAS bus. The front hot-swap bays hold six SAS HDDs and two SSDs. The two SSDs are the 250 GB Samsung SSD 840 pair (Windows disks 5 and 6).

The LSI HBA connects to the StarTech SATSAS425BK backplane via an SFF-8643 to SFF-8087 Mini-SAS cable. The build also uses three 1 m YIWENTEC SFF-8643 to four-port SATA breakout cables. One of the three breakout cables connects the HBA to the four individual SATA-style connectors on the Icy Dock MB994SK-1B backplane.

The drives are distributed as follows.

| Front backplane | Installed drives | Windows disks |
| --- | --- | --- |
| StarTech SATSAS425BK | 2 × 250 GB Samsung SSD 840, 1 × 300 GB HP EG0300FCSPH and 1 × 300 GB HP EG0300FBDSP | 5–8 |
| Icy Dock MB994SK-1B | 4 × 450 GB Hitachi Ultrastar C15K600 SAS HDD | 13–16 |

### Other physical drives

| Drive | Role |
| --- | --- |
| 500 GB Samsung 970-series NVMe | Windows system drive (C:) |
| 250 GB Samsung 970 EVO NVMe | Storage for two Hyper-V VMs |
| 256 GB OCZ Vector SATA SSD (Disk 2) | SSD member of F: |
| ADATA SP550 SATA SSD | Plex file swap and backup |
| 3 TB WD WD30EZRX SATA HDD | Windows and VM backups using EaseUS Todo Backup |

The inventory totals 21 physical drives. Windows also reports three Covecube virtual disks.

## Networking

| Adapter | Configuration |
| --- | --- |
| GLOTRENDS ST7419, four Intel I226-V 2.5 GbE ports | VM 1A, Kryton 1A, Kryton 2A and Fast File Transfer |
| Intel PRO/1000 PT Dual Port, two 1 GbE ports | Kryton 1B and Kryton 2B |
| Onboard Intel I219-V | VM 1B |
| Onboard Killer E2500 | Unused |
| Hyper-V virtual adapters | Default Switch and a separate internal network |

I use a dedicated Ethernet link on the GLOTRENDS card to transfer large media files between Kryton and my DeepThought desktop. That link uses a separate subnet from the main LAN.

## Virtual machines

| Guest | Role |
| --- | --- |
| Windows Server 2025 | Active Directory domain controller |
| Windows 11 | Test client connected to both the main virtual switch and the internal virtual network |
| Ubuntu 22.04 LTS | Linux test guest |
| Windows 10 and Windows 7 | Additional Windows test guests |

## Troubleshooting a storage warning

StableBit Scanner flagged a file system issue on a 10 TB drive even though its surface scan passed. I took the drive out of the pool and compared Scanner’s result with Windows diagnostics. Repeated online CHKDSK scans found no errors, the volume dirty bit was clear, and the drive reported no reallocated, pending or uncorrectable sectors. The checks gave conflicting results, so I documented them and opened a support ticket with Covecube.

## Skills demonstrated

Hyper-V virtualisation, Windows Server and Active Directory, virtual networking, SMB file sharing, Plex administration and storage fault diagnosis.
