---
title: Arquiteturas de soluções usando ficheiros Azure NetApp / Microsoft Docs
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
ms.date: 08/13/2020
ms.author: b-juche
ms.openlocfilehash: 79ad95179f4ad6d332a848e59ca341b8a9f90b1f
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258225"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Arquiteturas de solução com o Azure NetApp Files
Este artigo fornece referências às melhores práticas que podem ajudá-lo a entender as arquiteturas de solução para a utilização de Ficheiros Azure NetApp.  

O seguinte diagrama resume as categorias de arquiteturas de soluções que o Azure NetApp Files oferece:

![Categorias de arquitetura de soluções](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>Aplicativos e soluções de base de dados linux OSS

Esta secção fornece referências para soluções para aplicações e bases de dados Linux OSS. 

### <a name="oracle"></a>Oracle

* [Oracle no guia de boas práticas de implementação do Azure usando ficheiros Azure NetApp](https://www.netapp.com/us/media/tr-4780.pdf)
* [Imagens Do Oracle VM e a sua implementação no Microsoft Azure: Opções de configuração de armazenamento partilhada](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-vm-solutions#shared-storage-configuration-options)
* [Benefícios da utilização do Azure NetApp Files com o Oracle Database](solutions-benefits-azure-netapp-files-oracle-database.md)

## <a name="windows-apps-and-sql-server-solutions"></a>Apps Windows e soluções SQL Server

Esta secção fornece referências para aplicações Windows e soluções SQL Server.

### <a name="file-sharing-and-global-file-caching"></a>Partilha de ficheiros e Caching global de ficheiros

* [Construir o seu próprio Azure NFS? Wrestling Linux File Shares into Cloud](https://cloud.netapp.com/blog/ma-anf-blg-build-your-own-linux-nfs-file-shares)
* [Implementação de ficheiros Global File Cache / Azure NetApp](https://youtu.be/91LKb1qsLIM)

### <a name="sql-server"></a>SQL Server

* [Implementar servidor SQL sobre SMB com ficheiros Azure NetApp](https://www.youtube.com/watch?v=x7udfcYbibs)
* [Implementar o Cluster de Falha de Falha do Servidor SQL sobre sMB com ficheiros Azure NetApp](https://www.youtube.com/watch?v=zuNJ5E07e8Q)
* [Implementar grupos de disponibilidade sempre com ficheiros Azure NetApp](https://www.youtube.com/watch?v=y3VQmzzeyvc)

## <a name="sap-on-azure-solutions"></a>SOLUÇÕES SAP em Azure

Esta secção fornece referências à SAP sobre soluções Azure. 

### <a name="generic-sap-and-sap-netweaver"></a>Sap e SAP Netweaver genéricos 

* [Aplicações SAP no Microsoft Azure utilizando ficheiros Azure NetApp](https://www.netapp.com/us/media/tr-4746.pdf)
* [Alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server com Ficheiros Azure NetApp para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
* [Alta disponibilidade para SAP NetWeaver em VMs Azure em Red Hat Enterprise Linux com Ficheiros Azure NetApp para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* [Alta disponibilidade para SAP NetWeaver em VMs Azure no Windows com Ficheiros Azure NetApp (SMB) para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
* [Alta disponibilidade para SAP NetWeaver em VMs Azure em Red Hat Enterprise Linux para aplicações SAP multi-SID guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)

### <a name="sap-hana"></a>SAP HANA 

* [Configurações de armazenamento da máquina virtual do Azure do SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
* [Escala SAP HANA com nó de espera em VMs Azure com Ficheiros Azure NetApp no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
* [Escala SAP HANA com nó de espera em VMs Azure com Ficheiros Azure NetApp no Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)

### <a name="sap-tech-community-and-blog-posts"></a>Sap tech community e blog posts 

* [Ficheiros Azure NetApp - backup SAP HANA em segundos](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Ficheiros Azure NetApp – Restaurar a sua base de dados HANA a partir de uma cópia de segurança instantânea](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Ficheiros Azure NetApp – SAP HANA descarregando backup com Cloud Sync](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [Acelere as suas cópias do sistema SAP HANA utilizando ficheiros Azure NetApp](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [Volumes de nuvem ONTAP e Azure NetApp Ficheiros: Migração do sistema SAP HANA facilitada](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)

## <a name="virtual-desktop-infrastructure-solutions"></a>Soluções de Infraestrutura de Ambiente de Trabalho Virtual

Esta secção fornece referências para soluções de infraestrutura virtual do Ambiente de Trabalho.

### <a name="windows-virtual-desktop"></a>Windows Virtual Desktop

* [Benefícios da utilização de ficheiros Azure NetApp com desktop virtual do Windows](solutions-windows-virtual-desktop.md)
* [Opções de armazenamento para recipientes de perfil FSLogix no Windows Virtual Desktop](https://docs.microsoft.com/azure/virtual-desktop/store-fslogix-profile#azure-platform-details)
* [Crie um recipiente de perfil FSLogix para uma piscina hospedeira utilizando ficheiros Azure NetApp](https://docs.microsoft.com/azure/virtual-desktop/create-fslogix-profile-container)
* [Windows Virtual Desktop na escala da empresa](https://docs.microsoft.com/azure/architecture/example-scenario/wvd/windows-virtual-desktop)

## <a name="hpc-solutions"></a>Soluções HPC

Esta secção fornece referências para soluções de Computação de Alto Desempenho (HPC). 

### <a name="generic-hpc"></a>HPC genérico

* [Ficheiros Azure NetApp: Aproveitar ao máximo o seu armazenamento em nuvem](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [Executar cargas de carga de MPI com ficheiros Azure Batch e Azure NetApp](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Nuvem de ciclo Azure: ambientes HPC cicloCloud em ficheiros Azure NetApp](https://docs.microsoft.com/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>Petróleo e gás

* [Computação de alto desempenho (HPC): Petróleo e gás em Azure](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [Executar o software de simulação do reservatório em Azure](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>Automação de design eletrónico (EDA)

* [Benefícios da utilização do Azure NetApp Files para a automatização de design eletrónico](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [Azure CycleCloud: EDA HPC Lab com ficheiros Azure NetApp](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)

### <a name="analytics"></a>Análise

* [Ficheiros Azure NetApp: um novo sistema de ficheiros partilhados para utilizar com a GRELHA SAS no Microsoft Azure](https://communities.sas.com/t5/Architecture/Azure-NetApp-Files-A-new-shared-file-system-to-use-with-SAS-Grid/m-p/606978)

## <a name="azure-platform-services-solutions"></a>Soluções de serviços da plataforma Azure

Esta secção fornece soluções para os serviços da plataforma Azure. 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Serviços Azure Kubernetes e Kubernetes

* [Integrar ficheiros Azure NetApp com o Serviço Azure Kubernetes](https://docs.microsoft.com/azure/aks/azure-netapp-files)
* [Desempenho fora deste mundo kubernetes no Azure com ficheiros Azure NetApp](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Tridente - Orquestrador de Armazenamento para Contentores](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)

### <a name="azure-batch"></a>Azure Batch

* [Executar cargas de carga de MPI com ficheiros Azure Batch e Azure NetApp](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
 
