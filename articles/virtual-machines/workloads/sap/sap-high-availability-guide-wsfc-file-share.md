---
title: Cluster SAP ASCS/SCS na WSFC utilizando a quota de ficheiros no Azure [ Microsoft Docs
description: Aprenda a agrupar uma instância SAP ASCS/SCS num cluster de falhas do Windows utilizando uma quota de ficheiro sintetizá-lo no Azure.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/24/2019
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 545bcd1fa521b945d822b7eb69945cf381bf480a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77918670"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[kb4025334]:https://support.microsoft.com/help/4025334/windows-10-update-kb4025334

[dv2-series]:../../dv2-dsv2-series.md
[ds-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-volumes-s2d-choosing-filesystem]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-filesystem
[choosing-the-size-of-volumes-s2d]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-size-of-volumes
[deploy-sofs-s2d-in-azure]:https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment
[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[deep-dive-volumes-in-s2d]:https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Configuração de alta disponibilidade sap multi-SID)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

[1869038]:https://launchpad.support.sap.com/#/notes/1869038 

# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-file-share-in-azure"></a>Cluster uma instância SAP ASCS/SCS num cluster de falhas do Windows utilizando uma quota de ficheiro no Azure

> ![Windows][Logo_Windows] Windows
>

O clusterde failover do Windows Server é a base de uma instalação SAP ASCS/SCS de alta disponibilidade e DBMS no Windows.

Um cluster failover é um grupo de servidores independentes de 1+n (nós) que trabalham em conjunto para aumentar a disponibilidade de aplicações e serviços. Se ocorrer uma falha no nó, o clusterde failover do Windows Server calcula o número de falhas que podem ocorrer e ainda mantém um cluster saudável para fornecer aplicações e serviços. Pode escolher entre diferentes modos de quórum para obter o agrupamento failover.

## <a name="prerequisites"></a>Pré-requisitos
Antes de iniciar as tarefas descritas neste artigo, reveja este artigo:

* [Arquitetura e cenários de alta disponibilidade das Máquinas Virtuais Azure para SAP NetWeaver][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
> Clustering SAP ASCS/SCS, utilizando uma parte de ficheiro, é suportado para SAP NetWeaver 7.40 (e mais tarde), com SAP Kernel 7.49 (e mais tarde).
>


## <a name="windows-server-failover-clustering-in-azure"></a>Cluster de falha do Windows Server em Azure

Em comparação com as implementações em nuvem de metal nu ou privadas, as Máquinas Virtuais Azure requerem passos adicionais para configurar o clusterde failover do Windows Server. Quando constrói um cluster, precisa de definir vários endereços IP e nomes de anfitriões virtuais para a instância SAP ASCS/SCS.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Resolução de nomes em Azure e o nome de anfitrião virtual do cluster

A plataforma cloud Azure não oferece a opção de configurar endereços IP virtuais, como endereços IP flutuantes. Precisa de uma solução alternativa para configurar um endereço IP virtual para chegar ao recurso do cluster na nuvem. 

O serviço Azure Load Balancer fornece um *equilibrador de carga interno* para o Azure. Com o equilibrador de carga interna, os clientes chegam ao cluster sobre o endereço IP virtual cluster. 

Coloque o equilibrador de carga interno no grupo de recursos que contém os nós do cluster. Em seguida, configure todas as regras necessárias de reencaminhamento da porta utilizando as portas de sonda do equilibrante de carga interna. Os clientes podem ligar-se através do nome de anfitrião virtual. O servidor DNS resolve o endereço IP do cluster. O equilibrador de carga interno manuseia a porta encaminhando-se para o nó ativo do cluster.

![Figura 1: Configuração de clustering de falha do servidor do Windows em Azure sem um disco partilhado][sap-ha-guide-figure-1001]

_**Figura 1:** Configuração de clusterde falha do Windows Server em Azure sem um disco partilhado_

## <a name="sap-ascsscs-ha-with-file-share"></a>SAP ASCS/SCS HA com partilha de ficheiros

A SAP desenvolveu uma nova abordagem, e uma alternativa aos discos partilhados do cluster, para agrupar uma instância SAP ASCS/SCS num cluster de falhas do Windows. Em vez de utilizar discos partilhados em cluster, pode utilizar uma partilha de ficheiroS SMB para implementar ficheiros anfitriões globais SAP.

> [!NOTE]
> Uma partilha de ficheiroS SMB é uma alternativa à utilização de discos partilhados de cluster para agrupar instâncias SAP ASCS/SCS.  
>

Esta arquitetura é específica das seguintes formas:

* Os serviços centrais SAP (com a sua própria estrutura de ficheiros e processos de mensagem e enfila) estão separados dos ficheiros de acolhimento globais da SAP.
* Os serviços centrais SAP funcionam sob uma instância SAP ASCS/SCS.
* A instância SAP ASCS/SCS está agrupada e é acessível utilizando o \<nome\> de anfitrião virtual ASCS/SCS.
* Os ficheiros globais da SAP são colocados na \<partilha de\> ficheiros \\ \\ &lt;SMB e&gt;são acedidos utilizando o nome de anfitrião global SAP: SAP global host \sapmnt\\&lt;SID&gt;\SYS\...
* A instância SAP ASCS/SCS está instalada num disco local em ambos os nós do cluster.
* O \<nome de rede de nome\> virtual ascs/scs é diferente do &lt;hospedeiro&gt;global SAP .

![Figura 2: Arquitetura SAP ASCS/SCS HA com partilha de ficheiros SMB][sap-ha-guide-figure-8004]

_**Figura 2:** Nova arquitetura SAP ASCS/SCS HA com uma partilha de ficheiroS SMB_

Pré-requisitos para uma partilha de ficheiroS SMB:

* Protocolo SMB 3.0 (ou posterior).
* Capacidade de definir listas de controlo de acesso ao Diretório `computer$` Ativo (ACLs) para grupos de utilizadores de Diretório Ativo e o objeto de computador.
* A parte do ficheiro deve ser ativada por HA:
    * Os discos utilizados para armazenar ficheiros não devem ser um único ponto de falha.
    * O tempo de inatividade do servidor ou do VM não causa tempo de inatividade na partilha de ficheiros.

A função de cluster SAP \<SID\> não contém discos partilhados de cluster ou um recurso genérico de partilha de ficheiros.


![Figura 3: \<Recursos\> de cluster SAP SID para a utilização de uma parte de ficheiro][sap-ha-guide-figure-8005]

_**Figura 3:** Recursos &lt;de&gt; papel de cluster SAP SID para usar uma parte de arquivo_


## <a name="scale-out-file-shares-with-storage-spaces-direct-in-azure-as-an-sapmnt-file-share"></a>Partilhas de ficheiros scale-out com Espaços de Armazenamento Direto em Azure como uma partilha de ficheiros SAPMNT

Pode utilizar uma partilha de ficheiros scale-out para hospedar e proteger ficheiros anfitriões globais da SAP. Uma quota de ficheiro sive-out também oferece um serviço de partilha de ficheiros SAPMNT altamente disponível.

![Figura 4: Partilha de ficheiros scale-out utilizada para proteger ficheiros anfitriões globais da SAP][sap-ha-guide-figure-8006]

_**Figura 4:** Uma partilha de ficheiros de escala usada para proteger ficheiros anfitriões globais da SAP_

> [!IMPORTANT]
> As partilhas de ficheiros scale-out são totalmente suportadas na nuvem do Microsoft Azure e em ambientes no local.
>

Uma quota de ficheiro salonante oferece uma partilha de ficheiros SAPMNT altamente disponível e horizontalmente escalável.

Espaços de Armazenamento Direct é usado como um disco partilhado para uma partilha de ficheiros scale-out. Pode utilizar espaços de armazenamento direto para construir armazenamento altamente disponível e escalável utilizando servidores com armazenamento local. O armazenamento partilhado que é usado para uma partilha de ficheiros à escala, como para ficheiros anfitriões globais da SAP, não é um único ponto de falha.

Ao escolher espaços de armazenamento direto, considere estes casos de utilização:

- As máquinas virtuais utilizadas para construir o cluster De armazenamento Spaces Direct precisam de ser implantadas num conjunto de disponibilidade azure.
- Para recuperação de desastres de um Cluster Direto espaços de armazenamento, pode utilizar os Serviços de Recuperação do [Site Azure.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#replicated-machines---storage)
- Não é suportado para esticar o Cluster Direto espaço de armazenamento em diferentes Zonas de Disponibilidade Azure.

### <a name="sap-prerequisites-for-scale-out-file-shares-in-azure"></a>Pré-requisitos do SAP para ações de ficheiros em escala no Azure

Para utilizar uma partilha de ficheiros scale-out, o seu sistema deve satisfazer os seguintes requisitos:

* Pelo menos dois nós de cluster para uma parte de ficheiro supérbio.
* Cada nó deve ter pelo menos dois discos locais.
* Por razões de desempenho, deve utilizar *a resiliência espelhada:*
    * Espelhamento bidirecional para uma partilha de ficheiros com dois nós de cluster.
    * Espelhamento a três para uma partilha de ficheiros em escala com três (ou mais) nós de cluster.
* Recomendamos três (ou mais) nós de cluster para uma partilha de ficheiros em escala, com espelhamento de três vias.
    Esta configuração oferece mais escalabilidade e mais resiliência de armazenamento do que a configuração de partilha de ficheiros em escala com dois nós de cluster e espelhamento bidirecional.
* Deve utilizar discos Azure Premium.
* Recomendamos que utilize discos geridos azure.
* Recomendamos que forme volumes utilizando o Sistema de Ficheiros Resiliente (ReFS).
    * Para mais informações, consulte [O Suporte SAP Nota 1869038 - Suporte SAP para sistema de ficheiros ReFs][1869038] e a Escolha do capítulo do sistema de [ficheiros][planning-volumes-s2d-choosing-filesystem] do artigo Planeamento de volumes em Espaços de Armazenamento Direto.
    * Certifique-se de que instala a [atualização cumulativa do Microsoft KB4025334][kb4025334].
* Pode utilizar tamanhos De DS-Series ou DSv2-Series Azure VM.
* Para um bom desempenho de rede entre VMs, que é necessário para a sincronização do disco De armazenamento Spaces Direct, utilize um tipo VM que tenha pelo menos uma largura de banda de rede "alta".
    Para mais informações, consulte as especificações [dSv2-Series][dv2-series] e [DS-Series.][ds-series]
* Recomendamos que reserve alguma capacidade não atribuída no depósito. Deixar alguma capacidade não atribuída na piscina de armazenamento dá espaço aos volumes para reparar "no lugar" se uma unidade falhar. Isto melhora a segurança e o desempenho dos dados.  Para mais informações, consulte [Escolher o tamanho do volume][choosing-the-size-of-volumes-s2d].
* Não é necessário configurar o equilíbrio interno de carga azure para o nome \<da rede\>de partilha de ficheiros à escala, como para o anfitrião global SAP . Isto é feito \<para o nome\> de hospedeiro virtual ASCS/SCS da instância SAP ASCS/SCS ou para o DBMS. Uma partilha de ficheiros scale-out escala a carga em todos os nós do cluster. \<O anfitrião\> global da SAP usa o endereço IP local para todos os nós do cluster.


> [!IMPORTANT]
> Não é possível renomear a parte de \<ficheiro SAPMNT, que aponta para o anfitrião\>global da SAP. A SAP apoia apenas o nome de partilha "sapmnt".
>
> Para mais informações, consulte [SAP Nota 2492395 - O nome da partilha pode ser alterado?][2492395]

### <a name="configure-sap-ascsscs-instances-and-a-scale-out-file-share-in-two-clusters"></a>Configure as instâncias SAP ASCS/SCS e uma quota de ficheiro supérbio em dois clusters

Pode implantar instâncias SAP ASCS/SCS num só cluster, com a sua própria função de cluster SAP \<SID.\> Neste caso, configura a quota de ficheiros scale-out noutro cluster, com outra função de cluster.

> [!IMPORTANT]
>Neste cenário, a instância SAP ASCS/SCS está configurada para aceder ao \\ \\ &lt;hospedeiro global&gt;SAP utilizando\\&lt;&gt;o caminho cNU SAP global host \sapmnt SID \SYS\.
>

![Figura 5: Instância SAP ASCS/SCS e uma quota de ficheiro sitia implantada em dois clusters][sap-ha-guide-figure-8007]

_**Figura 5:** Uma instância SAP ASCS/SCS e uma quota de ficheiro sitia implantada em dois clusters_

> [!IMPORTANT]
> Na nuvem Azure, cada cluster utilizado para SAP e partilhas de ficheiros em escala deve ser implantado no seu próprio conjunto de disponibilidade Azure ou em zonas de disponibilidade Azure. Isto garante a colocação distribuída dos VMs do cluster através da infraestrutura Azure subjacente. As implementações da Zona de Disponibilidade são suportadas com esta tecnologia.
>

## <a name="generic-file-share-with-sios-datakeeper-as-cluster-shared-disks"></a>Partilha genérica de ficheiros com o SIOS DataKeeper como discos partilhados de cluster


Uma partilha genérica de ficheiros é outra opção para alcançar uma partilha de ficheiros altamente disponível.

Neste caso, pode utilizar uma solução SIOS de terceiros como um disco partilhado em cluster.

## <a name="next-steps"></a>Passos seguintes

* [Prepare a infraestrutura Azure para SAP HA utilizando um cluster de falhas do Windows e partilha de ficheiros para uma instância SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-file-share]
* [Instale o SAP NetWeaver HA num cluster de falhas do Windows e partilhe ficheiros para uma instância SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]
* [Implemente um servidor de ficheiros de dois nós espaços de armazenamento direto para armazenamento UPD em Azure][deploy-sofs-s2d-in-azure]
* [Espaços de Armazenamento Direto no Windows Server 2016][s2d-in-win-2016]
* [Mergulho profundo: Volumes em Espaços de Armazenamento Direto][deep-dive-volumes-in-s2d]
