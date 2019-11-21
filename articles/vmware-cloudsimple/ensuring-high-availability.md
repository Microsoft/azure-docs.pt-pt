---
title: Ensure application high availability when running in VMware on Azure
description: Describes CloudSimple high availability features to address common application failure scenarios for applications running in a CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a3eed033ba6a1a6f9237116a53ec7751ae906fe4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74206533"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>Ensure application high availability when running in VMware on Azure

The CloudSimple solution provides high availability for your applications running on VMware in the Azure environment. The following table lists failure scenarios and the associated high availability features.

| Failure scenario | Application protected? | Platform HA feature | VMware HA feature | Azure HA feature |
------------ | ------------- | ------------ | ------------ | ------------- |
| Disk Failure | YES | Fast replacement of failed node | [About the vSAN Default Storage Policy](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| Fan Failure | YES | Redundant fans, fast replacement of failed node |  |  |
| NIC Failure | YES | Redundant NIC, fast replacement of failed node
| Host Power Failure | YES | Redundant power supply |  |  |
| ESXi Host Failure | YES | fast replacement of failed node | [VMware vSphere High Availability](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| VM Failure | YES | [Balanceadores de carga](load-balancers.md)  | [VMware vSphere High Availability](https://www.vmware.com/products/vsphere/high-availability.html) | Azure Load Balancer for stateless VMware VMs |
| Leaf Switch Port Failure | YES | Redundant NIC |  |  |
| Leaf Switch Failure | YES | Redundant leaf switches |  |  |
| Rack Failure | YES | Grupos de colocação |  |  |
| Network Connectivity to on-premises DC | YES  | Redundant networking services |  | Redundant ER circuits |
| Network Connectivity to Azure | YES | |  | Redundant ER circuits |
| Datacenter Failure | YES |  |  | Zonas de disponibilidade |
| Regional Failure | YES  |  |  | Regiões do Azure |

Azure VMware Solution by CloudSimple provides the following high availability features.

## <a name="fast-replacement-of-failed-node"></a>Fast replacement of failed node

The CloudSimple control plane software continuously monitors the health of VMware clusters and detects when an ESXi node fails. It then automatically adds a new ESXi host to the affected VMware cluster from its pool of readily available nodes and takes the failed node out of the cluster. This functionality ensures that the spare capacity in the VMware cluster is restored quickly so that the cluster’s resiliency provided by vSAN and VMware HA is restored.

## <a name="placement-groups"></a>Grupos de colocação

A user who creates a Private Cloud can select an Azure region and a placement group within the selected region. A placement group is a set of nodes spread across multiple racks but within the same spine network segment. Nodes within the same placement group can reach each other with a maximum of two extra switch hops. A placement group is always within a single Azure availability zone and spans multiple racks. The CloudSimple control plane distributes nodes of a Private Cloud across multiple racks based on best effort. Nodes in different placement groups are guaranteed to be placed in different racks.

## <a name="availability-zones"></a>Zonas de disponibilidade

Availability zones are a high-availability offering that protects your applications and data from datacenter failures. Availability zones are special physical locations within an Azure region. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Each region has one availability zone. For more information, see [What are Availability Zones in Azure?](../availability-zones/az-overview.md).

## <a name="redundant-azure-expressroute-circuits"></a>Redundant Azure ExpressRoute circuits

Data center connectivity to Azure vNet using ExpressRoute has redundant circuits to provide highly available network connectivity link.

## <a name="redundant-networking-services"></a>Redundant networking services

All the CloudSimple networking services for the Private Cloud (including VLAN, firewall, public IP addresses, Internet, and VPN) are designed to be highly available and able to support the service SLA.

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>Azure Layer 7 Load Balancer for stateless VMware VMs

Users can put an Azure Layer 7 Load Balancer in front of the stateless web tier VMs running in the VMware environment to achieve high availability for the web tier.

## <a name="azure-regions"></a>Regiões do Azure

An Azure region is a set of data centers deployed within a latency-defined perimeter and connected through a dedicated regional low-latency network. For details, see [Azure Regions](https://azure.microsoft.com/global-infrastructure/regions).
