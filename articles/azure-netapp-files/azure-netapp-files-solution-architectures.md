---
title: Arquiteturas de soluções usando arquivos Azure NetApp | Microsoft Docs
description: Fornece referências às melhores práticas para arquiteturas de soluções usando arquivos Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: b-juche
ms.openlocfilehash: cccfd6b74acf26aeed3ad06ac6dcdb39c2ae6bef
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802238"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Arquiteturas de solução com o Azure NetApp Files
Este artigo fornece referências às melhores práticas que podem ajudá-lo a entender as arquiteturas de solução para a utilização de Ficheiros Azure NetApp.  

O seguinte diagrama resume as categorias de arquiteturas de soluções que o Azure NetApp Files oferece:

![Categorias de arquitetura de soluções](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>Aplicativos e soluções de base de dados linux OSS

Esta secção fornece referências para soluções para aplicações e bases de dados Linux OSS. 

### <a name="oracle"></a>Oracle

* [Oracle no guia de boas práticas de implementação do Azure usando ficheiros Azure NetApp](https://www.netapp.com/us/media/tr-4780.pdf)
* [Imagens Do Oracle VM e a sua implementação no Microsoft Azure: Opções de configuração de armazenamento partilhada](../virtual-machines/workloads/oracle/oracle-vm-solutions.md#shared-storage-configuration-options)
* [Desempenho da base de dados Oracle em volumes individuais do Azure NetApp Files](performance-oracle-single-volumes.md)
* [Benefícios da utilização do Azure NetApp Files com o Oracle Database](solutions-benefits-azure-netapp-files-oracle-database.md)

### <a name="machine-learning"></a>Machine Learning
*   [Aprendizagem automática cloudera](https://docs.cloudera.com/machine-learning/cloud/requirements-azure/topics/ml-requirements-azure.html)

## <a name="windows-apps-and-sql-server-solutions"></a>Apps Windows e soluções SQL Server

Esta secção fornece referências para aplicações Windows e soluções SQL Server.

### <a name="file-sharing-and-global-file-caching"></a>Partilha de ficheiros e Caching global de ficheiros

* [Construir o seu próprio Azure NFS? Wrestling Linux File Shares into Cloud](https://cloud.netapp.com/blog/ma-anf-blg-build-your-own-linux-nfs-file-shares)
* [Implementação de ficheiros Global File Cache / Azure NetApp](https://youtu.be/91LKb1qsLIM)
* [Conformidade em nuvem para ficheiros Azure NetApp](https://cloud.netapp.com/hubfs/Cloud%20Compliance%20for%20Azure%20NetApp%20Files%20-%20November%202020.pdf)

### <a name="sql-server"></a>SQL Server

* [Implementar servidor SQL sobre SMB com ficheiros Azure NetApp](https://www.youtube.com/watch?v=x7udfcYbibs)
* [Implementar o sql server Always-On Failover Cluster sobre SMB com ficheiros Azure NetApp](https://www.youtube.com/watch?v=zuNJ5E07e8Q) 
* [Implementar grupos de disponibilidade Always-On com ficheiros Azure NetApp](https://www.youtube.com/watch?v=y3VQmzzeyvc) 
* [Benefícios da utilização de ficheiros Azure NetApp para implementação de servidor SQL](solutions-benefits-azure-netapp-files-sql-server.md)

## <a name="sap-on-azure-solutions"></a>SOLUÇÕES SAP em Azure

Esta secção fornece referências à SAP sobre soluções Azure. 

### <a name="generic-sap-and-sap-netweaver"></a>Sap e SAP Netweaver genéricos 

* [Aplicações SAP no Microsoft Azure utilizando ficheiros Azure NetApp](https://www.netapp.com/us/media/tr-4746.pdf)
* [Alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server com Ficheiros Azure NetApp para aplicações SAP](../virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files.md)
* [Alta disponibilidade para SAP NetWeaver em VMs Azure em Red Hat Enterprise Linux com Ficheiros Azure NetApp para aplicações SAP](../virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files.md)
* [Alta disponibilidade para SAP NetWeaver em VMs Azure no Windows com Ficheiros Azure NetApp (SMB) para aplicações SAP](../virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb.md)
* [Alta disponibilidade para SAP NetWeaver em VMs Azure em Red Hat Enterprise Linux para aplicações SAP multi-SID guide](../virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid.md)

### <a name="sap-hana"></a>SAP HANA 

* [Configurações de armazenamento da máquina virtual do Azure do SAP HANA](../virtual-machines/workloads/sap/hana-vm-operations-storage.md)
* [Volumes NFS v4.1 no Azure NetApp Files para SAP HANA](../virtual-machines/workloads/sap/hana-vm-operations-netapp.md)
* [Alta disponibilidade de ESCALA SAP HANA com ficheiros Azure NetApp no Red Hat Enterprise Linux](../virtual-machines/workloads/sap/sap-hana-high-availability-netapp-files-red-hat.md)
* [Escala SAP HANA com nó de espera em VMs Azure com Ficheiros Azure NetApp no SUSE Linux Enterprise Server](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse.md)
* [Escala SAP HANA com nó de espera em VMs Azure com Ficheiros Azure NetApp no Red Hat Enterprise Linux](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel.md)
* [Escala SAP HANA com HSR e Pacemaker em RHEL - Azure Virtual Machines](../virtual-machines/workloads/sap/sap-hana-high-availability-scale-out-hsr-rhel.md)
* [Ferramenta Azure Application Consistent Snapshot (AzAcSnap)](azacsnap-introduction.md)

### <a name="sap-anydb"></a>SAP AnyDB

* [Oracle Azure Virtual Machines DBMS implantação para carga de trabalho SAP - Azure Virtual Machines](../virtual-machines/workloads/sap/dbms_guide_oracle.md#oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux)
* [Implementar SAP AnyDB (Oracle 19c) com ficheiros Azure NetApp](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-anydb-oracle-19c-with-azure-netapp-files/ba-p/2064043)

### <a name="sap-iq-nls"></a>SAP IQ-NLS

*   [Implementar solução SAP IQ-NLS HA utilizando ficheiros Azure NetApp no SUSE Linux Enterprise Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172#.X2tDfpNzBh4.linkedin)
* [Como gerir a Licença DE QI SAP em cenário HA](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/how-to-manage-sap-iq-license-in-ha-scenario/ba-p/2052583)

### <a name="sap-tech-community-and-blog-posts"></a>Sap tech community e blog posts 

* [Ficheiros Azure NetApp - backup SAP HANA em segundos](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Ficheiros Azure NetApp – Restaurar a sua base de dados HANA a partir de uma cópia de segurança instantânea](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Ficheiros Azure NetApp – SAP HANA descarregando backup com Cloud Sync](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [Acelere as suas cópias do sistema SAP HANA utilizando ficheiros Azure NetApp](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [Volumes de nuvem ONTAP e Azure NetApp Ficheiros: Migração do sistema SAP HANA facilitada](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)
* [Decisões arquitetónicas para maximizar o investimento da ANF na HANA N+M Scale-Out Architecture - Parte 1](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2078737)
* [Decisões arquitetónicas para maximizar o investimento da ANF na HANA N+M Scale-Out Architecture - Parte 2](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2117130)

## <a name="azure-vmware-solutions"></a>Soluções Azure VMware

* [Ficheiros Azure NetApp com Solução VMware Azure - Suportes de OS Convidados](../azure-vmware/netapp-files-with-azure-vmware-solution.md)

## <a name="virtual-desktop-infrastructure-solutions"></a>Soluções de Infraestrutura de Ambiente de Trabalho Virtual

Esta secção fornece referências para soluções de infraestrutura virtual do Ambiente de Trabalho.

### <a name="windows-virtual-desktop"></a>Windows Virtual Desktop

* [Benefícios da utilização do Azure NetApp Files com o Windows Virtual Desktop](solutions-windows-virtual-desktop.md)
* [Opções de armazenamento para recipientes de perfil FSLogix no Windows Virtual Desktop](../virtual-desktop/store-fslogix-profile.md#azure-platform-details)
* [Crie um recipiente de perfil FSLogix para uma piscina hospedeira utilizando ficheiros Azure NetApp](../virtual-desktop/create-fslogix-profile-container.md)
* [Windows Virtual Desktop à escala empresarial](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)
* [Microsoft FSLogix para a empresa - Azure NetApp Files as melhores práticas](/azure/architecture/example-scenario/wvd/windows-virtual-desktop-fslogix#azure-netapp-files-best-practices)
* [Criação de ficheiros Azure NetApp para anexação de aplicações MSIX](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/setting-up-azure-netapp-files-for-msix-app-attach-step-by-step/m-p/1990021)

## <a name="hpc-solutions"></a>Soluções HPC

Esta secção fornece referências para soluções de Computação de Alto Desempenho (HPC). 

### <a name="generic-hpc"></a>HPC genérico

* [Ficheiros Azure NetApp: Aproveitar ao máximo o seu armazenamento em nuvem](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [Executar cargas de carga de MPI com ficheiros Azure Batch e Azure NetApp](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Nuvem de ciclo Azure: ambientes HPC cicloCloud em ficheiros Azure NetApp](/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>Petróleo e gás

* [Computação de alto desempenho (HPC): Petróleo e gás em Azure](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [Executar o software de simulação do reservatório em Azure](/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>Automação de design eletrónico (EDA)

* [Benefícios da utilização do Azure NetApp Files para a automatização de design eletrónico](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [Azure CycleCloud: EDA HPC Lab com ficheiros Azure NetApp](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)
* [Azure para a indústria de semicondutores](https://azurecomcdn.azureedge.net/cvt-f40f39cd9de2d875ab0c198a8d7b186350cf0bca161e80d7896941389685d012/mediahandler/files/resourcefiles/azure-for-the-semiconductor-industry/Azure_for_the_Semiconductor_Industry.pdf)

### <a name="analytics"></a>Análise

* [Ficheiros Azure NetApp: um sistema de ficheiros partilhado para utilizar com a GRELHA SAS no Microsoft Azure](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/705192)
* [Ficheiros Azure NetApp: um sistema de ficheiros partilhado para utilizar com a GRELHA SAS em MS Azure – RHEL8.3/nconnect UPDATE](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/722261#M21648)
* [Boas Práticas para utilizar o Microsoft Azure com SAS®](https://communities.sas.com/t5/Administration-and-Deployment/Best-Practices-for-Using-Microsoft-Azure-with-SAS/m-p/676833#M19680)

## <a name="azure-platform-services-solutions"></a>Soluções de serviços da plataforma Azure

Esta secção fornece soluções para os serviços da plataforma Azure. 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Serviços Azure Kubernetes e Kubernetes

* [Integrar ficheiros Azure NetApp com o Serviço Azure Kubernetes](../aks/azure-netapp-files.md)
* [Desempenho fora deste mundo kubernetes no Azure com ficheiros Azure NetApp](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Ficheiros Azure NetApp + Tridente = Armazenamento Dinâmico e Persistente para Kubernetes](https://anfcommunity.com/2021/02/16/azure-netapp-files-trident-dynamic-and-persistent-storage-for-kubernetes/)
* [Tridente - Orquestrador de Armazenamento para Contentores](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)
* [Plataforma de e-commerce Magento no Serviço Azure Kubernetes (AKS)](/azure/architecture/example-scenario/magento/magento-azure)

### <a name="azure-batch"></a>Azure Batch

* [Executar cargas de carga de MPI com ficheiros Azure Batch e Azure NetApp](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
