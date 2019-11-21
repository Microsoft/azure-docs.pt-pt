---
title: Support matrix for VMware/physical disaster recovery in Azure Site Recovery
description: Summarizes support for disaster recovery of VMware VMs and physical server to Azure using Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: b75d9e9fc88cabacab912efa17938708eb061b99
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232438"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Support matrix for disaster recovery  of VMware VMs and physical servers to Azure

This article summarizes supported components and settings for disaster recovery of VMware VMs and physical servers to Azure using [Azure Site Recovery](site-recovery-overview.md).

- [Learn more](vmware-azure-architecture.md) about VMware VM/physical server disaster recovery architecture.
- Follow our [tutorials](tutorial-prepare-azure.md) to try out disaster recovery.

## <a name="deployment-scenarios"></a>Cenários de implementação

**Cenário** | **Detalhes**
--- | ---
Disaster recovery of VMware VMs | Replication of on-premises VMware VMs to Azure. You can deploy this scenario in the Azure portal or by using [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Disaster recovery of physical servers | Replication of on-premises Windows/Linux physical servers to Azure. You can deploy this scenario in the Azure portal.

## <a name="on-premises-virtualization-servers"></a>On-premises virtualization servers

**Servidor** | **Requisitos** | **Detalhes**
--- | --- | ---
vCenter Server | Version 6.7, 6.5, 6.0, or 5.5 | We recommend that you use a vCenter server in your disaster recovery deployment.
vSphere hosts | Version 6.7, 6.5, 6.0, or 5.5 | We recommend that vSphere hosts and vCenter servers are located in the same network as the process server. By default the process server runs on the configuration server. [Saiba mais](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Site Recovery configuration server

The configuration server is an on-premises machine that runs Site Recovery components, including the configuration server, process server, and master target server.

- For VMware VMs you set the configuration server by downloading an OVF template to create a VMware VM.
- For physical servers, you set up the configuration server machine manually.

**Componente** | **Requisitos**
--- |---
Núcleos de CPU | 8
RAM | 16 GB
Número de discos | 3 disks<br/><br/> Disks include the OS disk, process server cache disk, and retention drive for failback.
Disk free space | 600 GB of space for the process server cache.
Disk free space | 600 GB  of space for the retention drive.
Sistema operativo  | Windows Server 2012 R2, or Windows Server 2016 with Desktop experience <br/><br> If you plan to use the in-built Master Target of this appliance for failback, ensure that the OS version is same or higher than the replicated items.|
Região do sistema operativo | Inglês (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Not needed for configuration server version [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) or later. 
Funções do Windows Server | Don't enable Active Directory Domain Services; Internet Information Services (IIS) or Hyper-V. 
Group policies| - Prevent access to the command prompt. <br/> - Prevent access to registry editing tools. <br/> - Trust logic for file attachments. <br/> - Turn on Script Execution. <br/> - [Learn more](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Make sure you:<br/><br/> - Don't have a preexisting default website <br/> - Enable  [anonymous authentication](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> - Enable [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) setting  <br/> - Don't have preexisting website/app listening on port 443<br/>
NIC type | VMXNET3 (when deployed as a VMware VM)
Tipo de endereço IP | Estático
Portas | 443 used for control channel orchestration<br/>9443 for data transport

## <a name="replicated-machines"></a>Replicated machines

Site Recovery supports replication of any workload running on a supported machine.

> [!Note]
> The following table lists the support for machines with BIOS boot. Please refer to [Storage](#storage) section for support on UEFI based machines.

**Componente** | **Detalhes**
--- | ---
Machine settings | Machines that replicate to Azure must meet [Azure requirements](#azure-vm-requirements).
Machine workload | Site Recovery supports replication of any workload running on a supported machine. [Saiba mais](https://aka.ms/asr_workload).
Windows Server 2019 | Supported from [Update rollup 34](https://support.microsoft.com/help/4490016) (version 9.22 of the Mobility service) onwards.
Windows Server 2016 64-bit | Supported for Server Core, Server with Desktop Experience.
Windows Server 2012 R2 / Windows Server 2012 | Suportado.
Windows Server 2008 R2 with SP1 onwards. | Suportado.<br/><br/> From version 9.30.x.x (expected release beginning of November 2019) of the Mobility service agent, you need [servicing stack update (SSU)](https://support.microsoft.com/help/4490628) and [SHA-2 update](https://support.microsoft.com/help/4474419) installed on machines running Windows 2008 R2 with SP1 or later. SHA-1 isn't supported from September 2019, and if SHA-2 code signing isn't enabled the agent extension won't install/upgrade as expected. Learn more about [SHA-2 upgrade and requirements](https://aka.ms/SHA-2KB).
Windows Server 2008 with SP2 or later (64-bit/32-bit) |  Supported for migration only. [Saiba mais](migrate-tutorial-windows-server-2008.md).<br/><br/> From version 9.30.x.x (expected release beginning of November 2019) of the Mobility service agent, you need [servicing stack update (SSU)](https://support.microsoft.com/help/4493730) and [SHA-2 update](h https://support.microsoft.com/help/4474419) installed on Windows 2008 SP2 machines. ISHA-1 isn't supported from September 2019, and if SHA-2 code signing isn't enabled the agent extension won't install/upgrade as expected. Learn more about [SHA-2 upgrade and requirements](https://aka.ms/SHA-2KB).
Windows 10, Windows 8.1, Windows 8 | Suportado.
Windows 7 with SP1 64-bit | Supported from [Update rollup 36](https://support.microsoft.com/help/4503156) (version 9.22 of the Mobility service) onwards. </br></br> From version 9.30.x.x (expected release beginning of November 2019) of the Mobility service agent, you need [servicing stack update (SSU)](https://support.microsoft.com/help/4490628) and [SHA-2 update](https://support.microsoft.com/help/4474419) installed on Windows 7 SP1 machines.  SHA-1 isn't supported from September 2019, and if SHA-2 code signing isn't enabled the agent extension won't install/upgrade as expected. Learn more about [SHA-2 upgrade and requirements](https://aka.ms/SHA-2KB).
Linux | Only 64-bit system is supported. 32-bit system isn't supported.<br/><br/>Every Linux server should have [Linux Integration Services (LIS) components](https://www.microsoft.com/download/details.aspx?id=55106) installed. It is required to boot the server in Azure after test failover/failover. If LIS components are missing, ensure to install the [components](https://www.microsoft.com/download/details.aspx?id=55106) before enabling replication for the machines to boot in Azure. <br/><br/> Site Recovery orchestrates failover to run Linux servers in Azure. However Linux vendors might limit support to only distribution versions that haven't reached end-of-life.<br/><br/> On Linux distributions, only the stock kernels that are part of the distribution minor version release/update are supported.<br/><br/> Upgrading protected machines across major Linux distribution versions isn't supported. To upgrade, disable replication, upgrade the operating system, and then enable replication again.<br/><br/> [Learn more](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) about support for Linux and open-source technology in Azure.
Linux Red Hat Enterprise | 5.2 to 5.11</b><br/> 6.1 to 6.10</b> </br> 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) <br/> Servers running Red Hat Enterprise Linux 5.2-5.11 & 6.1-6.10 do not have [Linux Integration Services (LIS) components](https://www.microsoft.com/download/details.aspx?id=55106) pre-installed. Ensure to install the [components](https://www.microsoft.com/download/details.aspx?id=55106) before enabling replication for the machines to boot in Azure.
Linux: CentOS | 5.2 to 5.11</b><br/> 6.1 to 6.10</b><br/> 7.0 to 7.6<br/> <br/> Servers running  CentOS 5.2-5.11 & 6.1-6.10 do not have  [Linux Integration Services (LIS) components](https://www.microsoft.com/download/details.aspx?id=55106) pre-installed. Ensure to install the [components](https://www.microsoft.com/download/details.aspx?id=55106) before enabling replication for the machines to boot in Azure.
Ubuntu | Ubuntu 14.04 LTS server [(review supported kernel versions)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS server [(review supported kernel versions)](#ubuntu-kernel-versions) </br> Ubuntu 18.04 LTS server [(review supported kernel versions)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(review supported kernel versions)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(review supported kernel versions)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> Upgrading replicated machines from SUSE Linux Enterprise Server 11 SP3 to SP4 isn't supported. To upgrade, disable replication and re-enable after the upgrade.
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)<br/><br/> Running the Red Hat compatible kernel or Unbreakable Enterprise Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5) 


### <a name="ubuntu-kernel-versions"></a>Ubuntu kernel versions

**Supported release** | **Mobility service version** | **Versão de kernel** |
--- | --- | --- |
14.04 LTS | [9.28][9.28 UR]| 3.13.0-24-generic to 3.13.0-170-generic,<br/>3.16.0-25-generic to 3.16.0-77-generic,<br/>3.19.0-18-generic to 3.19.0-80-generic,<br/>4.2.0-18-generic to 4.2.0-42-generic,<br/>4.4.0-21-generic to 4.4.0-148-generic,<br/>4.15.0-1023-azure to 4.15.0-1045-azure |
14.04 LTS | [9.27][9.27 UR]| 3.13.0-24-generic to 3.13.0-170-generic,<br/>3.16.0-25-generic to 3.16.0-77-generic,<br/>3.19.0-18-generic to 3.19.0-80-generic,<br/>4.2.0-18-generic to 4.2.0-42-generic,<br/>4.4.0-21-generic to 4.4.0-148-generic,<br/>4.15.0-1023-azure to 4.15.0-1045-azure |
14.04 LTS | [9.26][9.26 UR]| 3.13.0-24-generic to 3.13.0-170-generic,<br/>3.16.0-25-generic to 3.16.0-77-generic,<br/>3.19.0-18-generic to 3.19.0-80-generic,<br/>4.2.0-18-generic to 4.2.0-42-generic,<br/>4.4.0-21-generic to 4.4.0-148-generic,<br/>4.15.0-1023-azure to 4.15.0-1045-azure |
14.04 LTS | [9.25][9.25 UR]  | 3.13.0-24-generic to 3.13.0-169-generic,<br/>3.16.0-25-generic to 3.16.0-77-generic,<br/>3.19.0-18-generic to 3.19.0-80-generic,<br/>4.2.0-18-generic to 4.2.0-42-generic,<br/>4.4.0-21-generic to 4.4.0-146-generic,<br/>4.15.0-1023-azure to 4.15.0-1042-azure |
|||
16.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.4.0-21-generic to 4.4.0-166-generic,<br/>4.8.0-34-generic to 4.8.0-58-generic,<br/>4.10.0-14-generic to 4.10.0-42-generic,<br/>4.11.0-13-generic to 4.11.0-14-generic,<br/>4.13.0-16-generic to 4.13.0-45-generic,<br/>4.15.0-13-generic to 4.15.0-66-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure to 4.13.0-1018-azure <br/>4.15.0-1012-azure to 4.15.0-1061-azure|
16.04 LTS | [9.28][9.28 UR] | 4.4.0-21-generic to 4.4.0-159-generic,<br/>4.8.0-34-generic to 4.8.0-58-generic,<br/>4.10.0-14-generic to 4.10.0-42-generic,<br/>4.11.0-13-generic to 4.11.0-14-generic,<br/>4.13.0-16-generic to 4.13.0-45-generic,<br/>4.15.0-13-generic to 4.15.0-58-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure to 4.13.0-1018-azure <br/>4.15.0-1012-azure to 4.15.0-1055-azure|
16.04 LTS | [9.27][9.27 UR] | 4.4.0-21-generic to 4.4.0-154-generic,<br/>4.8.0-34-generic to 4.8.0-58-generic,<br/>4.10.0-14-generic to 4.10.0-42-generic,<br/>4.11.0-13-generic to 4.11.0-14-generic,<br/>4.13.0-16-generic to 4.13.0-45-generic,<br/>4.15.0-13-generic to 4.15.0-54-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure to 4.13.0-1018-azure <br/>4.15.0-1012-azure to 4.15.0-1050-azure|
16.04 LTS | [9.26][9.26 UR] | 4.4.0-21-generic to 4.4.0-148-generic,<br/>4.8.0-34-generic to 4.8.0-58-generic,<br/>4.10.0-14-generic to 4.10.0-42-generic,<br/>4.11.0-13-generic to 4.11.0-14-generic,<br/>4.13.0-16-generic to 4.13.0-45-generic,<br/>4.15.0-13-generic to 4.15.0-50-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure to 4.13.0-1018-azure <br/>4.15.0-1012-azure to 4.15.0-1045-azure|
16.04 LTS | [9.25][9.25 UR] | 4.4.0-21-generic to 4.4.0-146-generic,<br/>4.8.0-34-generic to 4.8.0-58-generic,<br/>4.10.0-14-generic to 4.10.0-42-generic,<br/>4.11.0-13-generic to 4.11.0-14-generic,<br/>4.13.0-16-generic to 4.13.0-45-generic,<br/>4.15.0-13-generic to 4.15.0-48-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure to 4.13.0-1018-azure <br/>4.15.0-1012-azure to 4.15.0-1042-azure|
16.04 LTS | [9.24][9.24 UR] | 4.4.0-21-generic to 4.4.0-143-generic,<br/>4.8.0-34-generic to 4.8.0-58-generic,<br/>4.10.0-14-generic to 4.10.0-42-generic,<br/>4.11.0-13-generic to 4.11.0-14-generic,<br/>4.13.0-16-generic to 4.13.0-45-generic,<br/>4.15.0-13-generic to 4.15.0-46-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure to 4.13.0-1018-azure <br/>4.15.0-1012-azure to 4.15.0-1040-azure|
|||
18.04 LTS | [9.29](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) | 4.15.0-20-generic to 4.15.0-62-generic </br> 4.18.0-13-generic to 4.18.0-25-generic </br> 5.0.0-15-generic to 5.0.0-27-generic </br> 4.15.0-1009-azure to 4.15.0-1037-azure </br> 4.18.0-1006-azure to 4.18.0-1025-azure </br> 5.0.0-1012-azure to 5.0.0-1018-azure
18.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.15.0-20-generic to 4.15.0-66-generic </br> 4.18.0-13-generic to 4.18.0-25-generic </br> 5.0.0-15-generic to 5.0.0-32-generic </br> 4.15.0-1009-azure to 4.15.0-1037-azure </br> 4.18.0-1006-azure to 4.18.0-1025-azure </br> 5.0.0-1012-azure to 5.0.0-1023-azure


### <a name="debian-kernel-versions"></a>Debian kernel versions


**Supported release** | **Mobility service version** | **Versão de kernel** |
--- | --- | --- |
Debian 7 | [9.25][9.25 UR],[9.26][9.26 UR], [9.27][9.27 UR], [9.28][9.28 UR]| 3.2.0-4-amd64 to 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.28][9.28 UR] | 3.16.0-4-amd64 to 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.9-amd64 |
Debian 8 | [9.27][9.27 UR] | 3.16.0-4-amd64 to 3.16.0-9-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.9-amd64 |
Debian 8 | [9.25][9.25 UR], [9.26][9.26 UR] | 3.16.0-4-amd64 to 3.16.0-8-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.8-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 supported kernel versions

**Release** | **Mobility service version** | **Versão de kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.28][9.28 UR] | SP1 3.12.49-11-default to 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.118-default</br></br> SP2 4.4.21-69-default to 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default to 4.4.121-92.117-default</br></br>SP3 4.4.73-5-default to 4.4.180-94.100-default</br></br>SP3 4.4.138-4.7-azure to 4.4.180-4.31-azure</br></br>SP4 4.12.14-94.41-default to 4.12.14-95.29-default</br>SP4 4.12.14-6.3-azure to 4.12.14-6.23-azure |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.27][9.27 UR] | SP1 3.12.49-11-default to 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.115-default</br></br> SP2 4.4.21-69-default to 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default to 4.4.121-92.114-default</br></br>SP3 4.4.73-5-default to 4.4.180-94.97-default</br></br>SP3 4.4.138-4.7-azure to 4.4.180-4.31-azure</br></br>SP4 4.12.14-94.41-default to 4.12.14-95.19-default</br>SP4 4.12.14-6.3-azure to 4.12.14-6.15-azure |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.26][9.26 UR] | SP1 3.12.49-11-default to 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.110-default</br></br> SP2 4.4.21-69-default to 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default to 4.4.121-92.109-default</br></br>SP3 4.4.73-5-default to 4.4.178-94.91-default</br></br>SP3 4.4.138-4.7-azure to 4.4.178-4.28-azure</br></br>SP4 4.12.14-94.41-default to 4.12.14-95.16-default</br>SP4 4.12.14-6.3-azure to 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.25][9.25 UR] | SP1 3.12.49-11-default to 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default to 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default to 4.4.121-92.104-default</br></br>SP3 4.4.73-5-default to 4.4.176-94.88-default</br></br>SP3 4.4.138-4.7-azure to 4.4.176-4.25-azure</br></br>SP4 4.12.14-94.41-default to 4.12.14-95.13-default</br>SP4 4.12.14-6.3-azure to 4.12.14-6.9-azure |

## <a name="linux-file-systemsguest-storage"></a>Linux file systems/guest storage

**Componente** | **Suportado**
--- | ---
File systems | ext3, ext4, XFS
Volume manager | - LVM is supported.<br/> - /boot on LVM is supported from [Update Rollup 31](https://support.microsoft.com/help/4478871/) (version 9.20 of the Mobility service) onwards. It isn't supported in earlier Mobility service versions.<br/> - Multiple OS disks aren't supported.
Paravirtualized storage devices | Os dispositivos exportados por controladores paravirtualizados não são suportados.
Multi-queue block IO devices | Não suportado.
Physical servers with the HP CCISS storage controller | Não suportado.
Device/Mount point naming convention | Device name or mount point name should be unique.<br/> Ensure that no two devices/mount points have case-sensitive names. For example naming devices for the same VM as *device1* and *Device1* isn't supported.
Diretórios | If you're running a version of the Mobility service earlier than version 9.20 (released in [Update Rollup 31](https://support.microsoft.com/help/4478871/)), then these restrictions apply:<br/><br/> - These directories (if set up as separate partitions/file-systems) must be on the same OS disk on the source server: /(root), /boot, /usr, /usr/local, /var, /etc.</br> - The /boot directory should be on a disk partition and not be an LVM volume.<br/><br/> From version 9.20 onwards, these restrictions don't apply. 
Boot directory | - Boot disks mustn't be in GPT partition format. This is an Azure architecture limitation. GPT disks are supported as data disks.<br/><br/> Multiple boot disks on a VM aren't supported<br/><br/> - /boot on an LVM volume across more than one disk isn't supported.<br/> - A machine without a boot disk can't be replicated.
Free space requirements| 2 GB on the /root partition <br/><br/> 250 MB on the installation folder
XFSv5 | XFSv5 features on XFS file systems, such as metadata checksum, are supported (Mobility service version 9.10 onwards).<br/> Use the xfs_info utility to check the XFS superblock for the partition. If `ftype` is set to 1, then XFSv5 features are in use.
BTRFS | BTRFS is supported from [Update Rollup 34](https://support.microsoft.com/help/4490016) (version 9.22 of the Mobility service) onwards. BTRFS isn't supported if:<br/><br/> - The BTRFS file system subvolume is changed after enabling protection.</br> - The BTRFS file system is spread over multiple disks.</br> - The BTRFS file system supports RAID.

## <a name="vmdisk-management"></a>VM/Disk management

**Ação** | **Detalhes**
--- | ---
Resize disk on replicated VM | Suportado.
Add disk on replicated VM | Não suportado.<br/> Disable replication for the VM, add the disk, and then re-enable replication.

## <a name="network"></a>Rede

**Componente** | **Suportado**
--- | ---
Host network NIC Teaming | Supported for VMware VMs. <br/><br/>Not supported for physical machine replication.
Host network VLAN | Sim.
Host network IPv4 | Sim.
Host network IPv6 | Não.
Guest/server network NIC Teaming | Não.
Guest/server network IPv4 | Sim.
Guest/server network IPv6 | Não.
Guest/server network static IP (Windows) | Sim.
Guest/server network static IP (Linux) | Sim. <br/><br/>VMs are configured to use DHCP on failback.
Guest/server network multiple NICs | Sim.


## <a name="azure-vm-network-after-failover"></a>Azure VM network (after failover)

**Componente** | **Suportado**
--- | ---
Azure ExpressRoute | Sim
ILB | Sim
ELB | Sim
Traffic Manager do Azure | Sim
Multi-NIC | Sim
Endereço IP reservado | Sim
IPv4 | Sim
Retain source IP address | Sim
Azure virtual network service endpoints<br/> | Sim
Redes aceleradas | Não

## <a name="storage"></a>Armazenamento
**Componente** | **Suportado**
--- | ---
Dynamic disk | OS disk must be a basic disk. <br/><br/>Data disks can be dynamic disks
Docker disk configuration | Não
Host NFS | Yes for VMware<br/><br/> No for physical servers
Host SAN (iSCSI/FC) | Sim
Host vSAN | Yes for VMware<br/><br/> N/A for physical servers
Host multipath (MPIO) | Yes, tested with Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM for CLARiiON
Host Virtual Volumes (VVols) | Yes for VMware<br/><br/> N/A for physical servers
Guest/server VMDK | Sim
Guest/server shared cluster disk | Não
Guest/server encrypted disk | Não
Guest/server NFS | Não
Guest/server iSCSI | For Migration - Yes<br/>For Disaster Recovery - No, iSCSI will failback as an attached disk to the VM
Guest/server SMB 3.0 | Não
Guest/server RDM | Sim<br/><br/> N/A for physical servers
Guest/server disk > 1 TB | Yes, disk must be larger than 1024 MB<br/><br/>Up to 8,192 GB when replicating to managed disks (9.26 version onwards)<br></br> Up to 4,095 GB when replicating to storage accounts
Guest/server disk with 4K logical and 4k physical sector size | Não
Guest/server disk with 4K logical and 512-bytes physical sector size | Não
Guest/server volume with striped disk >4 TB <br/><br/>Logical volume management (LVM)| Sim
Guest/server - Storage Spaces | Não
Guest/server hot add/remove disk | Não
Guest/server - exclude disk | Sim
Guest/server multipath (MPIO) | Não
Guest/server GPT partitions | Five partitions are supported from [Update Rollup 37](https://support.microsoft.com/help/4508614/) (version 9.25 of the Mobility service) onwards. Anteriormente, eram suportadas quatro.
ReFS | Resilient File System is supported with Mobility service version 9.23 or higher
Guest/server EFI/UEFI boot | - Supported for Windows Server 2012 or later, SLES 12 SP4 and RHEL 8.0 with mobility agent version 9.30 onwards<br/> - Secure UEFI boot type is not supported. 

## <a name="replication-channels"></a>Replication channels

|**Type of replication**   |**Suportado**  |
|---------|---------|
|Offloaded Data Transfers  (ODX)    |       Não  |
|Seeding Offline        |   Não      |
| Azure Data Box | Não

## <a name="azure-storage"></a>Storage do Azure

**Componente** | **Suportado**
--- | ---
Armazenamento localmente redundante | Sim
Armazenamento georredundante | Sim
Read-access geo-redundant storage | Sim
Cool storage | Não
Hot storage| Não
Blobs de bloco | Não
Encryption-at-rest (SSE)| Sim
Encryption-at-rest (CMK)| Não
Armazenamento Premium | Sim
Import/export service | Não
Azure Storage firewalls for VNets | Sim.<br/> Configured on target storage/cache storage account (used to store replication data).
General-purpose v2 storage accounts (hot and cool tiers) | Yes (Transaction costs are substantially higher for V2 compared to V1)

## <a name="azure-compute"></a>Azure compute

**Funcionalidade** | **Suportado**
--- | ---
Conjuntos de disponibilidade | Sim
Zonas de disponibilidade | Não
HUB | Sim
Managed Disks | Sim

## <a name="azure-vm-requirements"></a>Azure VM requirements

On-premises VMs replicated to Azure must meet the Azure VM requirements summarized in this table. When Site Recovery runs a prerequisites check for replication, the check will fail if some of the requirements aren't met.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Guest operating system | Verify [supported operating systems](#replicated-machines) for replicated machines. | Check fails if unsupported.
Guest operating system architecture | 64-bit. | Check fails if unsupported.
Operating system disk size | Up to 2,048 GB. | Check fails if unsupported.
Operating system disk count | 1 | Check fails if unsupported.
Data disk count | 64 or less. | Check fails if unsupported.
Data disk size | Up to 8,192 GB when replicating to managed disk (9.26 version onwards)<br></br>Up to 4,095 GB when replicating to storage account| Check fails if unsupported.
Network adapters | Multiple adapters are supported. |
Shared VHD | Não suportado. | Check fails if unsupported.
FC disk | Não suportado. | Check fails if unsupported.
BitLocker | Não suportado. | BitLocker must be disabled before you enable replication for a machine. |
o nome da VM | From 1 to 63 characters.<br/><br/> Limitado a letras, números e hífenes.<br/><br/> The machine name must start and end with a letter or number. |  Update the value in the machine properties in Site Recovery.

## <a name="resource-group-limits"></a>Resource group limits

To understand the number of virtual machines that can be protected under a single resource group, refer to the article on [subscription limits and quotas](https://docs.microsoft.com/azure/azure-subscription-service-limits#resource-group-limits)

## <a name="churn-limits"></a>Churn limits

A tabela seguinte fornece os limites do Azure Site Recovery. 
- These limits are based on our tests, but don't cover all possible app I/O combinations.
- Os resultados reais podem variar consoante a combinação de E/S da sua aplicação.
- For best results, we strongly recommend that you run the [Deployment Planner tool](site-recovery-deployment-planner.md), and perform extensive application testing using test failovers to get the true performance picture for your app.

**Replication target** | **Tamanho médio de E/S do disco de origem** |**Média de alterações a dados do disco de origem** | **Total de alterações a dados do disco de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB  | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |  336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou superior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou superior |20 MB/s | 1684 GB per disk


**Alterações a dados de origem** | **Limite Máximo**
---|---
Alterações a dados de pico em todos os discos numa VM | 54 MB/s
Máximo de alterações a dados por dia suportadas por um Servidor de Processos | 2 TB

- Estes são números médios, que pressupõem uma sobreposição de 30 por cento de E/S.
- O Site Recovery é capaz de processar um débito superior com base no rácio de sobreposição, em tamanhos de escrita maiores e no comportamento real de E/S da carga de trabalho.
- These numbers assume a typical backlog of approximately five minutes. Ou seja, depois de os dados serem carregados, são processados e é criado um ponto de recuperação ao fim de cinco minutos.

## <a name="vault-tasks"></a>Vault tasks

**Ação** | **Suportado**
--- | ---
Move vault across resource groups | Não
Move vault within and across subscriptions | Não
Move storage, network, Azure VMs across resource groups | Não
Move storage, network, Azure VMs within and across subscriptions. | Não


## <a name="obtain-latest-components"></a>Obtain latest components

**Nome** | **Descrição** | **Detalhes**
--- | --- | ---
Servidor de configuração | Installed on-premises.<br/> Coordinates communications between on-premises VMware servers or physical machines, and Azure. | - [Learn about](vmware-physical-azure-config-process-server-overview.md) the configuration server.<br/> - [Learn about](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) upgrading to the latest version.<br/> - [Learn about](vmware-azure-deploy-configuration-server.md) setting up the configuration server. 
Process server | Instalado por predefinição no servidor de configuração.<br/> Receives replication data, optimizes it with caching, compression, and encryption, and sends it to Azure.<br/> As your deployment grows, you can add additional process servers to handle larger volumes of replication traffic. | - [Learn about](vmware-physical-azure-config-process-server-overview.md) the process server.<br/> - [Learn about](vmware-azure-manage-process-server.md#upgrade-a-process-server) upgrading to the latest version.<br/> - [Learn about](vmware-physical-large-deployment.md#set-up-a-process-server) setting up scale-out process servers.
Mobility Service | Installed on VMware VM or physical servers you want to replicate.<br/> Coordinates replication between on-premises VMware servers/physical servers and Azure.| - [Learn about](vmware-physical-mobility-service-overview.md) the Mobility service.<br/> - [Learn about](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) upgrading to the latest version.<br/> 



## <a name="next-steps"></a>Passos seguintes
[Learn how](tutorial-prepare-azure.md) to prepare Azure for disaster recovery of VMware VMs.

[9.28 UR]: https://support.microsoft.com/en-in/help/4521530/update-rollup-40-for-azure-site-recovery
[9.27 UR]: https://support.microsoft.com/en-in/help/4517283/update-rollup-39-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
