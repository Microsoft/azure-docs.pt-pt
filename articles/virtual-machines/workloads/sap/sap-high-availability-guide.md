---
title: Azure Virtual Machines alta disponibilidade para SAP NetWeaver
description: Neste artigo, saiba mais sobre máquinas virtuais Azure de alta disponibilidade para SAP NetWeaver.
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
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3a330fc18f39ffd7007e2a41a28016df69a2b739
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91314662"
---
# <a name="high-availability-azure-virtual-machines-for-sap-netweaver"></a>Máquinas virtuais Azure de alta disponibilidade para SAP NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Configuração de alta disponibilidade DE SAP multi-SID)


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

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md



A Azure Virtual Machines é a solução para organizações que precisam de computação, armazenamento e recursos de rede, em tempo mínimo, e sem longos ciclos de aquisição. Você pode usar Azure Virtual Machines para implementar aplicações clássicas como SAP NetWeaver-based ABAP, Java, e uma pilha ABAP+Java. Alargar a fiabilidade e a disponibilidade sem recursos adicionais no local. A Azure Virtual Machines suporta a conectividade transversal, para que possa integrar as Máquinas Virtuais Azure nos domínios da sua organização, nuvens privadas e paisagem do sistema SAP.

Neste artigo, cobrimos os passos que pode tomar para implantar sistemas SAP de alta disponibilidade em Azure, utilizando o modelo de implementação do Gestor de Recursos Azure. Nós o acompanharemos através destas grandes tarefas:

* Encontre as notas e guias de instalação SAP certos, listados na secção [Recursos.][sap-ha-guide-2] Este artigo complementa a documentação de instalação DA SAP e as Notas SAP, que são os recursos primários que podem ajudá-lo a instalar e implantar software SAP em plataformas específicas.
* Saiba as diferenças entre o modelo de implementação do Azure Resource Manager e o modelo de implementação clássico do Azure.
* Saiba mais sobre os modos de clusterrum de failover do Windows Server, para que possa selecionar o modelo que é adequado para a sua implementação Azure.
* Saiba mais sobre o Armazenamento partilhado do Windows Server Failover Clustering nos serviços Azure.
* Saiba como ajudar a proteger componentes de ponto único de falha como o Advanced Business Application Programming (ABAP) SAP Central Services (ASCS)/SAP Central Services (SCS) e os sistemas de gestão de bases de dados (DBMS), e componentes redundantes como o SAP Application Server, em Azure.
* Siga um exemplo passo-a-passo de uma instalação e configuração de um sistema SAP de alta disponibilidade num cluster de cluster de failover do Windows Server em Azure utilizando o Azure Resource Manager.
* Saiba mais sobre os passos adicionais necessários para utilizar o Cluster de Failover do Servidor do Windows em Azure, mas que não são necessários numa implementação no local.

Para simplificar a implementação e configuração, neste artigo, utilizamos os modelos de Gestor de Recursos de alta disponibilidade SAP de três níveis. Os modelos automatizam a implantação de toda a infraestrutura que você precisa para um sistema SAP de alta disponibilidade. A infraestrutura também suporta o tamanho do SAP Application Performance Standard (SAPS) do seu sistema SAP.

## <a name="prerequisites"></a><a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Pré-requisitos
Antes de começar, certifique-se de que cumpre os pré-requisitos descritos nas seguintes secções. Além disso, certifique-se de verificar todos os recursos listados na secção [Recursos.][sap-ha-guide-2]

Neste artigo, utilizamos modelos de Gestor de Recursos Azure para [netWeaver SAP de três níveis usando Discos Geridos](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md/). Para obter uma visão geral útil dos modelos, consulte [os modelos do GESTOR de Recursos SAP Azure](/archive/blogs/saponsqlserver/azure-quickstart-templates-for-sap).

## <a name="resources"></a><a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Recursos
Estes artigos abrangem as implementações da SAP em Azure:

* [Azure Virtual Machines planejamento e implementação para SAP NetWeaver][planning-guide]
* [Implementação de máquinas virtuais Azure para SAP NetWeaver][deployment-guide]
* [Implantação DBMS de máquinas virtuais Azure para SAP NetWeaver][dbms-guide]
* [Azure Virtual Machines alta disponibilidade para SAP NetWeaver (este guia)][sap-ha-guide]

> [!NOTE]
> Sempre que possível, damos-lhe um link para o guia de instalação SAP remetente (consulte os [guias de instalação SAP).][sap-installation-guides] Para pré-requisitos e informações sobre o processo de instalação, é uma boa ideia ler cuidadosamente os guias de instalação da SAP NetWeaver. Este artigo abrange apenas tarefas específicas para sistemas baseados em SAP NetWeaver que pode utilizar com máquinas virtuais Azure.
>
>

Estas Notas SAP estão relacionadas com o tema SAP em Azure:

| Número de nota | Título |
| --- | --- |
| [1928533] |Aplicações SAP em Azure: Produtos suportados e Dimensionamento |
| [2015553] |SAP no Microsoft Azure: Pré-requisitos de suporte |
| [1999351] |Monitorização de Azure melhorada para o SAP |
| [2178632] |Principais métricas de monitorização do SAP no Microsoft Azure |
| [1999351] |Virtualização no Windows: Monitorização melhorada |
| [2243692] |Utilização de Armazenamento SSD Azure Premium para ASD Instance |

Saiba mais sobre as [limitações das subscrições Azure,][azure-resource-manager/management/azure-subscription-service-limits-subscription]incluindo limitações gerais de incumprimento e limitações máximas.

## <a name="high-availability-sap-with-azure-resource-manager-vs-the-azure-classic-deployment-model"></a><a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>SAP de alta disponibilidade com Azure Resource Manager vs. o modelo de implementação clássica Azure
Os modelos de implementação clássicos Azure Resource Manager e Azure são diferentes nas seguintes áreas:

- Grupos de recursos
- Dependência do balançador interno de carga Azure no grupo de recursos Azure
- Suporte para cenários SAP multi-SID

### <a name="resource-groups"></a><a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Grupos de recursos
No Azure Resource Manager, pode utilizar grupos de recursos para gerir todos os recursos da aplicação na sua subscrição Azure. Uma abordagem integrada, num grupo de recursos, todos os recursos têm o mesmo ciclo de vida. Por exemplo, todos os recursos são criados ao mesmo tempo e são eliminados ao mesmo tempo. Saiba mais sobre [grupos de recursos](../../../azure-resource-manager/management/overview.md#resource-groups).

### <a name="azure-internal-load-balancer-dependency-on-the-azure-resource-group"></a><a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Dependência do balançador interno de carga Azure no grupo de recursos Azure

No modelo de implantação clássico do Azure, existe uma dependência entre o equilibrador interno de carga Azure (serviço Azure Load Balancer) e o serviço de cloud. Todos os equilibradores internos precisam de um serviço de nuvem.

No Azure Resource Manager, todos os recursos Azure precisam de ser colocados num grupo de recursos Azure, o que também é válido para o Azure Load Balancer. No entanto, não é necessário dispor de um grupo de recursos Azure por balançador de carga Azure, por exemplo, um grupo de recursos Azure pode conter vários Equilibrdores de Carga Azure. O ambiente é mais simples e flexível. 

### <a name="support-for-sap-multi-sid-scenarios"></a>Suporte para cenários SAP multi-SID

No Azure Resource Manager, pode instalar vários casos de identificador de sistema SAP (SID) ASCS/SCS num único cluster. As instâncias multi-SID são possíveis devido ao suporte para vários endereços IP para cada equilibrador interno de carga Azure.

Para utilizar o modelo de implementação clássico Azure, siga os procedimentos descritos no [SAP NetWeaver em Azure: Clustering ASCS/SCS, utilizando o Cluster de Falha do Windows Server em Azure com o SIOS DataKeeper](https://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> Recomendamos vivamente que utilize o modelo de implementação do Azure Resource Manager para as suas instalações SAP. Oferece muitos benefícios que não estão disponíveis no modelo clássico de implementação. Saiba mais sobre [os modelos de implementação Azure][virtual-machines-azure-resource-manager-architecture-benefits-arm].   
>
>

## <a name="windows-server-failover-clustering"></a><a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Cluster de Failover do servidor do Windows
O Windows Server Failover Clustering é a base de uma instalação SAP ASCS/SCS de alta disponibilidade e de DBMS no Windows.

Um cluster failover é um grupo de 1+n servidores independentes (nós) que trabalham em conjunto para aumentar a disponibilidade de aplicações e serviços. Se ocorrer uma falha no nó, o Cluster de Failover do Servidor do Windows calcula o número de falhas que podem ocorrer mantendo um cluster saudável para fornecer aplicações e serviços. Pode escolher entre diferentes modos de quórum para obter o agrupamento de falhas.

### <a name="quorum-modes"></a><a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Modos quórum
Pode escolher entre quatro modos de quórum quando utilizar o Cluster de Falha do Windows Server:

* **Maioria nó.** Cada nó do agrupamento pode votar. O agrupamento funciona apenas com uma maioria de votos, ou seja, com mais de metade dos votos. Recomendamos esta opção para clusters que tenham um número desigual de nós. Por exemplo, três nós num aglomerado de sete nós podem falhar, e o cluster ainda consegue uma maioria e continua a funcionar.  
* **Node e maioria do disco.** Cada nó e um disco designado (uma testemunha de disco) no armazenamento do cluster podem votar quando estão disponíveis e em comunicação. O agrupamento funciona apenas com a maioria dos votos, ou seja, com mais de metade dos votos. Este modo faz sentido num ambiente de cluster com um número par de nós. Se metade dos nós e o disco estiverem online, o cluster permanece em estado saudável.
* **Node e a maioria da partilha de ficheiros**. Cada nó mais uma partilha de ficheiro designada (uma testemunha de partilha de ficheiros) que o administrador cria pode votar, independentemente de os nós e a partilha de ficheiros estarem disponíveis e em comunicação. O agrupamento funciona apenas com a maioria dos votos, ou seja, com mais de metade dos votos. Este modo faz sentido num ambiente de cluster com um número par de nós. É semelhante ao modo Node e Disk Majority, mas usa uma partilha de ficheiros de testemunhas em vez de um disco de testemunhas. Este modo é fácil de implementar, mas se a partilha de ficheiros em si não estiver altamente disponível, pode tornar-se um único ponto de falha.
* **Sem maioria: Apenas disco**. O cluster tem um quórum se um nó estiver disponível e em comunicação com um disco específico no armazenamento do cluster. Só os nós que também estão em comunicação com aquele disco podem juntar-se ao cluster. Recomendamos que não utilize este modo.


## <a name="windows-server-failover-clustering-on-premises"></a><a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Cluster de Failover do servidor do Windows no local
A figura 1 mostra um aglomerado de dois nós. Se a ligação de rede entre os nós falhar e ambos os nós permanecerem em funcionamento, um disco de quórum ou partilha de ficheiros determina qual o nó que continuará a fornecer as aplicações e serviços do cluster. O nó que tem acesso ao disco quórum ou à partilha de ficheiros é o nó que garante que os serviços continuem.

Como este exemplo utiliza um cluster de dois nós, usamos o modo quórum no nó e partilha de ficheiros. A maioria nó e disco também é uma opção válida. Num ambiente de produção, recomendamos que utilize um disco de quórum. Você pode usar a tecnologia de sistema de rede e armazenamento para torná-lo altamente disponível.

![Figura 1: Exemplo de uma configuração de clustering de failover do servidor do Windows para SAP ASCS/SCS em Azure][sap-ha-guide-figure-1000]

_**Figura 1:** Exemplo de uma configuração de clustering de failover do servidor do Windows para SAP ASCS/SCS em Azure_

### <a name="shared-storage"></a><a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Armazenamento compartilhado
A figura 1 também mostra um aglomerado de armazenamento compartilhado de dois nós. Num aglomerado de armazenamento partilhado no local, todos os nós do cluster detetam armazenamento partilhado. Um mecanismo de bloqueio protege os dados da corrupção. Todos os nós podem detetar se outro nó falhar. Se um nó falhar, o nó restante toma posse dos recursos de armazenamento e garante a disponibilidade de serviços.

> [!NOTE]
> Não precisa de discos partilhados para uma alta disponibilidade com algumas aplicações DBMS, como com o SQL Server. O SQL Server Always On replica os dados DBMS e regista ficheiros do disco local de um nó de cluster para o disco local de outro nó de cluster. Nesse caso, a configuração do cluster do Windows não necessita de um disco partilhado.
>
>

### <a name="networking-and-name-resolution"></a><a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Resolução de rede e nome
Os computadores clientes chegam ao cluster sobre um endereço IP virtual e um nome de anfitrião virtual que o servidor DNS fornece. Os nós no local e o servidor DNS podem lidar com vários endereços IP.

Numa configuração típica, utiliza-se duas ou mais ligações de rede:

* Uma ligação dedicada ao armazenamento
* Uma ligação de rede cluster-interna para o batimento cardíaco
* Uma rede pública que os clientes usam para se conectarem ao cluster

## <a name="windows-server-failover-clustering-in-azure"></a><a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Cluster de failover do servidor do Windows em Azure
Em comparação com as implementações de nuvem de metal ou nuvem privada, as Máquinas Virtuais Azure requerem passos adicionais para configurar o Cluster de Falha do Servidor do Windows. Ao construir um disco de cluster partilhado, precisa de definir vários endereços IP e nomes de anfitriões virtuais para a instância SAP ASCS/SCS.

Neste artigo, discutimos conceitos-chave e os passos adicionais necessários para a construção de um cluster de serviços centrais de alta disponibilidade SAP em Azure. Mostramos-lhe como configurar a ferramenta de terceiros SIOS DataKeeper e como configurar o balançador interno de carga Azure. Pode utilizar estas ferramentas para criar um cluster de falha do Windows com uma testemunha de partilha de ficheiros em Azure.

![Figura 2: Configuração de clustering de failover do servidor do Windows server em Azure sem um disco partilhado][sap-ha-guide-figure-1001]

_**Figura 2:** Configuração de clustering de failover do servidor do Windows server em Azure sem um disco partilhado_

### <a name="shared-disk-in-azure-with-sios-datakeeper"></a><a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Disco compartilhado em Azure com SIOS DataKeeper
Você precisa de armazenamento compartilhado de cluster para uma instância SAP ASCS/SCS de alta disponibilidade. A partir de setembro de 2016, o Azure não oferece armazenamento partilhado que pode usar para criar um cluster de armazenamento partilhado. Pode utilizar software de terceiros SIOS DataKeeper Cluster Edition para criar um armazenamento espelhado que simula armazenamento partilhado de cluster. A solução SIOS fornece replicação de dados sincronizados em tempo real. É assim que se pode criar um recurso de disco partilhado para um cluster:

1. Fixe um disco adicional a cada uma das máquinas virtuais (VMs) numa configuração de cluster Windows.
2. Executar a Edição do Cluster SIOS DataKeeper em ambos os nós de máquinas virtuais.
3. Configure o SIOS DataKeeper Cluster Edition de modo a espelhar o conteúdo do volume adicional do disco anexado da máquina virtual de origem ao volume adicional ligado ao disco da máquina virtual alvo. SiOS DataKeeper abstrata a fonte e os volumes locais alvo, e depois apresenta-os ao Windows Server Failover Clustering como um disco partilhado.

Obtenha mais informações sobre [sios DataKeeper](https://us.sios.com/products/datakeeper-cluster/).

![Figura 3: Configuração de clustering de failover do servidor do Windows server em Azure com sios dataKeeper][sap-ha-guide-figure-1002]

_**Figura 3:** Configuração de clustering de falha do servidor do Windows server em Azure com sios dataKeeper_

> [!NOTE]
> Não precisa de discos partilhados para uma alta disponibilidade com alguns produtos DBMS, como o SQL Server. O SQL Server Always On replica os dados DBMS e regista ficheiros do disco local de um nó de cluster para o disco local de outro nó de cluster. Neste caso, a configuração do cluster do Windows não necessita de um disco partilhado.
>
>

### <a name="name-resolution-in-azure"></a><a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Resolução de nome em Azure
A plataforma cloud Azure não oferece a opção de configurar endereços IP virtuais, como endereços IP flutuantes. Precisa de uma solução alternativa para configurar um endereço IP virtual para chegar ao recurso de cluster na nuvem.
A Azure tem um equilibrador de carga interno no serviço Azure Load Balancer. Com o equilibrador de carga interno, os clientes chegam ao cluster sobre o endereço IP virtual do cluster.
É necessário implantar o equilibrador de carga interno no grupo de recursos que contém os nós de cluster. Em seguida, configuure todas as regras necessárias de encaminhamento do porto com as portas de sonda do esquilibrador interno de carga.
Os clientes podem ligar-se através do nome de anfitrião virtual. O servidor DNS resolve o endereço IP do cluster e o equilibrador de carga interno manuseia a porta reencaminhando para o nó ativo do cluster.

## <a name="sap-netweaver-high-availability-in-azure-infrastructure-as-a-service-iaas"></a><a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> SAP NetWeaver alta disponibilidade em Azure Infrastructure-as-a-Service (IaaS)
Para obter uma elevada disponibilidade da aplicação SAP, como para componentes de software SAP, é necessário proteger os seguintes componentes:

* Caso do Servidor de Aplicações SAP
* Caso SAP ASCS/SCS
* Servidor DBMS

Para obter mais informações sobre a proteção dos componentes SAP em cenários de alta disponibilidade, consulte [o planeamento e implementação de Máquinas Virtuais Azure para o SAP NetWeaver][planning-guide-11].

### <a name="high-availability-sap-application-server"></a><a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> Servidor de aplicação SAP de alta disponibilidade
Normalmente não precisa de uma solução específica de alta disponibilidade para o Servidor de Aplicações SAP e instâncias de diálogo. Obtém-se uma elevada disponibilidade por redundância e configurará várias instâncias de diálogo em diferentes instâncias de Azure Virtual Machines. Deverá ter pelo menos duas instâncias de aplicação SAP instaladas em duas instâncias de Azure Virtual Machines.

![Figura 4: Servidor de aplicação SAP de alta disponibilidade][sap-ha-guide-figure-2000]

_**Figura 4:** Servidor de aplicação SAP de alta disponibilidade_

Deve colocar todas as máquinas virtuais que acolhem as instâncias do Servidor de Aplicações SAP no mesmo conjunto de disponibilidades Azure. Um conjunto de disponibilidade Azure garante que:

* Todas as máquinas virtuais fazem parte do mesmo domínio de upgrade. Um domínio de atualização, por exemplo, garante que as máquinas virtuais não são atualizadas ao mesmo tempo durante o tempo de inatividade de manutenção planeado.
* Todas as máquinas virtuais fazem parte do mesmo domínio de avaria. Um domínio de avaria, por exemplo, garante que as máquinas virtuais são implantadas de modo a que nenhum ponto de falha afete a disponibilidade de todas as máquinas virtuais.

Saiba mais sobre como [gerir a disponibilidade de máquinas virtuais][.. /manage-availability.md].

Apenas disco não gerido: Como a conta de armazenamento Azure é um potencial ponto único de falha, é importante ter pelo menos duas contas de armazenamento Azure, nas quais pelo menos duas máquinas virtuais são distribuídas. Numa configuração ideal, os discos de cada máquina virtual que está a executar uma instância de diálogo SAP seriam implantados numa conta de armazenamento diferente.

### <a name="high-availability-sap-ascsscs-instance"></a><a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Caso SAP ASCS/SCS de alta disponibilidade
A figura 5 é um exemplo de uma instância SAP ASCS/SCS de alta disponibilidade.

![Figura 5: Caso SAP ASCS/SCS de alta disponibilidade][sap-ha-guide-figure-2001]

_**Figura 5:** Caso SAP ASCS/SCS de alta disponibilidade_

#### <a name="sap-ascsscs-instance-high-availability-with-windows-server-failover-clustering-in-azure"></a><a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> SAP ASCS/SCS caso alta disponibilidade com Cluster de Failover do Servidor do Windows em Azure
Em comparação com as implementações de nuvem de metal ou nuvem privada, as Máquinas Virtuais Azure requerem passos adicionais para configurar o Cluster de Falha do Servidor do Windows. Para construir um cluster de failover do Windows, precisa de um disco de cluster partilhado, vários endereços IP, vários nomes de anfitriões virtuais e um equilibrador interno Azure para agrupar uma instância SAP ASCS/SCS. Discutimos esta questão mais detalhadamente mais tarde no artigo.

![Figura 6: Clustering de failover do servidor do Windows para uma configuração SAP ASCS/SCS em Azure utilizando o SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Figura 6:** Cluster de failover do servidor do Windows para uma configuração SAP ASCS/SCS em Azure com sios dataKeeper_

### <a name="high-availability-dbms-instance"></a><a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>Caso DBMS de alta disponibilidade
O DBMS também é um único ponto de contacto num sistema SAP. Você precisa protegê-lo usando uma solução de alta disponibilidade. A Figura 7 mostra um servidor SQL sempre em solução de alta disponibilidade em Azure, com o Cluster de Falha de Falha do Servidor do Windows e o equilibrador de carga interno Azure. O SQL Server Always On replica os dados DBMS e os ficheiros de registo utilizando a sua própria replicação DBMS. Neste caso, não precisa de discos partilhados de cluster, o que simplifica toda a configuração.

![Figura 7: Exemplo de um DBMS SAP de alta disponibilidade, com SQL Server Always On][sap-ha-guide-figure-2003]

_**Figura 7:** Exemplo de um DBMS SAP de alta disponibilidade, com SQL Server Always On_

Para obter mais informações sobre o clustering SQL Server em Azure utilizando o modelo de implementação do Gestor de Recursos Azure, consulte estes artigos:

* [Configure sempre no grupo de disponibilidade em Azure Virtual Machines manualmente usando o Gestor de Recursos] [virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Configure um balançador interno Azure para um grupo de disponibilidade Always On em Azure] [virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="end-to-end-high-availability-deployment-scenarios"></a><a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Cenários de implantação de alta disponibilidade de ponta a ponta

### <a name="deployment-scenario-using-architectural-template-1"></a>Cenário de implantação usando o modelo arquitetónico 1

A figura 8 mostra um exemplo de uma arquitetura de alta disponibilidade SAP NetWeaver em Azure para **um** sistema SAP. Este cenário é configurado da seguinte forma:

- Um cluster dedicado é usado para a instância SAP ASCS/SCS.
- Um cluster dedicado é usado para a instância DBMS.
- As instâncias do Servidor de Aplicações SAP são implementadas nos seus próprios VMs dedicados.

![Figura 8: Modelo arquitetónico de alta disponibilidade SAP 1, com cluster dedicado para ASCS/SCS e DBMS][sap-ha-guide-figure-2004]

_**Figura 8:** Modelo arquitetónico de alta disponibilidade SAP 1, clusters dedicados para ASCS/SCS e DBMS_

### <a name="deployment-scenario-using-architectural-template-2"></a>Cenário de implantação usando o modelo arquitetónico 2

A figura 9 mostra um exemplo de uma arquitetura de alta disponibilidade SAP NetWeaver em Azure para **um** sistema SAP. Este cenário é configurado da seguinte forma:

- Um cluster dedicado é utilizado **tanto** para a instância SAP ASCS/SCS como para o DBMS.
- As instâncias do Servidor de Aplicações SAP são implementadas em VMs próprios dedicados.

![Figura 9: Modelo arquitetónico de alta disponibilidade SAP 2, com um cluster dedicado para ASCS/SCS e um cluster dedicado para DBMS][sap-ha-guide-figure-2005]

_**Figura 9:** Modelo arquitetónico de alta disponibilidade SAP 2, com um cluster dedicado para ASCS/SCS e um cluster dedicado para DBMS_

### <a name="deployment-scenario-using-architectural-template-3"></a>Cenário de implantação usando o modelo arquitetónico 3

A Figura 10 mostra um exemplo de uma arquitetura de alta disponibilidade SAP NetWeaver em Azure para **dois** sistemas SAP, com &lt; SID1 &gt; e &lt; SID2 &gt; . Este cenário é configurado da seguinte forma:

- Um cluster dedicado é utilizado **tanto** para a instância SAP ASCS/SCS SID1 *como* para a instância SAP ASCS/SCS SID2 (um cluster).
- Um cluster dedicado é usado para DBMS SID1, e outro cluster dedicado é usado para DBMS SID2 (dois clusters).
- As instâncias do SERVIDOR de Aplicações SAP para o sistema SAP SID1 têm os seus próprios VMs dedicados.
- As instâncias do SERVIDOR de Aplicações SAP para o sistema SAP SID2 têm os seus próprios VMs dedicados.

![Figura 10: Modelo arquitetónico de alta disponibilidade SAP 3, com um cluster dedicado para diferentes instâncias ASCS/SCS][sap-ha-guide-figure-6003]

_**Figura 10:** MODELO Arquitetónico de alta disponibilidade SAP 3, com um cluster dedicado para diferentes instâncias ASCS/SCS_

## <a name="prepare-the-infrastructure"></a><a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Preparar a infraestrutura

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Preparar a infraestrutura para o Modelo Arquitetónico 1
Os modelos do Gestor de Recursos Azure para o SAP ajudam a simplificar a implementação dos recursos necessários.

Os modelos de três camadas em Azure Resource Manager também suportam cenários de alta disponibilidade, como no Modelo Arquitetónico 1, que tem dois clusters. Cada cluster é um único ponto de falha SAP para SAP ASCS/SCS e DBMS.

Aqui é onde você pode obter modelos de Gestor de Recursos Azure para o cenário de exemplo que descrevemos neste artigo:

* [Imagem do Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/)  
* [Imagem do Azure Marketplace usando Discos Geridos](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Imagem personalizada](https://github.com/Azure/azure-quickstart-templates/)
* [Imagem personalizada usando discos geridos](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Para preparar a infraestrutura para o Modelo Arquitetónico 1:

- No portal Azure, na lâmina **parâmetros,** na caixa **SYSTEMAVAILABILITY,** selecione **HA**.

  ![Figura 11: Definir parâmetros de gestor de recursos Azure de alta disponibilidade DO SAP][sap-ha-guide-figure-3000]

_**Figura 11:** Definir parâmetros de gestor de recursos Azure de alta disponibilidade DO SAP_


  Os modelos criam:

  * **Máquinas virtuais:**
    * Máquinas virtuais do Servidor de Aplicações SAP: <NÚMERO>-di-<*DO* *SAPSystemSID*>
    * Máquinas virtuais de cluster ASCS/SCS: <*NÚMERO*>-ascs-ascs-<*Number*>
    * Cluster DBMS: <*NÚMERO*>-db-<*SAPSystemSID*>

  * **Cartões de rede para todas as máquinas virtuais, com endereços IP associados:**
    * <Número>-nic-di-<*SAPSystemSID* *Number*>
    * <*Número*>-ascs-<*SAPSystemSID*>
    * <Número>-nic-db-<*SAPSystemSID* *Number*>

  * **Contas de armazenamento Azure (apenas discos não geridos)**

  * **Grupos de disponibilidade** para:
    * Máquinas virtuais do Servidor de Aplicações SAP: <*SAPSystemSID*>-avset-di
    * Máquinas virtuais de cluster SAP ASCS/SCS: <*SAPSystemSID*>-avset-ascs
    * Máquinas virtuais de cluster DBMS: <*SAPSystemSID*>-avset-db

  * **Equilibrador interno de carga**azul:
    * Com todas as portas para a instância ASCS/SCS e endereço IP <*SAPSystemSID*>-lb-ascs
    * Com todas as portas para o DBMS do servidor SQL e endereço IP <*SAPSystemSID*>-lb-db

  * **Grupo de segurança de**rede : <*SAPSystemSID*>-nsg-ascs-0  
    * Com uma porta aberta do Protocolo de Ambiente de Trabalho Remoto (RDP) para o <máquina virtual *SAPSystemSID*>-ascs-0

> [!NOTE]
> Todos os endereços IP dos cartões de rede e dos equilibradores internos da Azure são **dinâmicos** por padrão. Altere-os para endereços IP **estáticos.** Descrevemos como fazê-lo mais tarde no artigo.
>
>

### <a name="deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Implementar máquinas virtuais com conectividade de rede corporativa (instalações cruzadas) para utilizar na produção
Para a produção de sistemas SAP, implemente máquinas virtuais Azure com conectividade de rede corporativa utilizando a Azure Site-to-Site VPN ou Azure ExpressRoute.

> [!NOTE]
> Pode utilizar a sua instância de Rede Virtual Azure. A rede virtual e a sub-rede já foram criadas e preparadas.
>
>

1. No portal Azure, na lâmina **parâmetros,** na caixa **NEWOREXISTINGSUBNET,** selecione **a existência**.
2. Na caixa **SUBNETID,** adicione a cadeia completa da sua rede Azure SubnetID preparada onde planeia implantar as suas máquinas virtuais Azure.
3. Para obter uma lista de todas as sub-redes da rede Azure, execute este comando PowerShell:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   O campo **ID** mostra o **SUBNETID**.
4. Para obter uma lista de todos os valores **SUBNETID,** execute este comando PowerShell:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   O **SUBNETID** é assim:

   ```
   /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
   ```

### <a name="deploy-cloud-only-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Implementar instâncias SAP apenas em nuvem para teste e demonstração
Pode implantar o seu sistema SAP de alta disponibilidade num modelo de implementação apenas na nuvem. Este tipo de implantação é útil principalmente para casos de demonstração e utilização de testes. Não é adequado para casos de uso de produção.

- No portal Azure, na lâmina **parâmetros,** na caixa **NEWOREXISTINGSUBNET,** selecione **novo**. Deixe o campo **SUBNETID** vazio.

  O modelo SAP Azure Resource Manager cria automaticamente a rede virtual Azure e a sub-rede.

> [!NOTE]
> Também precisa de implementar pelo menos uma máquina virtual dedicada para o Ative Directory e DNS na mesma instância da Rede Virtual Azure. O modelo não cria estas máquinas virtuais.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Preparar a infraestrutura para o Modelo Arquitetónico 2

Você pode usar este modelo de Gestor de Recursos Azure para SAP para ajudar a simplificar a implementação de recursos de infraestrutura necessários para o Modelo De Arquitetura SAP 2.

Aqui é onde você pode obter modelos de Gestor de Recursos Azure para este cenário de implementação:

* [Imagem do Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/)  
* [Imagem do Azure Marketplace usando Discos Geridos](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Imagem personalizada](https://github.com/Azure/azure-quickstart-templates/)
* [Imagem personalizada usando discos geridos](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Preparar a infraestrutura para o Modelo Arquitetónico 3

Pode preparar a infraestrutura e configurar o SAP para **multi-SID.** Por exemplo, pode adicionar uma instância SAP ASCS/SCS adicional numa configuração de cluster *existente.* Para obter mais informações, consulte [configurar uma instância SAP ASCS/SCS adicional numa configuração de cluster existente para criar uma configuração MULTI-SID SAP no Azure Resource Manager][sap-ha-multi-sid-guide].

Se quiser criar um novo cluster multi-SID, pode utilizar os [modelos de arranque rápido](https://github.com/Azure/azure-quickstart-templates)multi-SID no GitHub .
Para criar um novo cluster multi-SID, é necessário implementar os seguintes três modelos:

* [Modelo ASCS/SCS](#ASCS-SCS-template)
* [Modelo de base de dados](#database-template)
* [Modelo de servidores de aplicações](#application-servers-template)

As secções seguintes têm mais detalhes sobre os modelos e os parâmetros que precisa fornecer nos modelos.

#### <a name="ascsscs-template"></a><a name="ASCS-SCS-template"></a> Modelo ASCS/SCS

O modelo ASCS/SCS implementa duas máquinas virtuais que pode utilizar para criar um cluster de falha do Windows Server que acolhe várias instâncias ASCS/SCS.

Para configurar o modelo MULTI-SID ASCS/SCS, no [modelo MULTI-SID ASCS/SCS][sap-templates-3-tier-multisid-xscs-marketplace-image] ou [no modelo MULTI-SID ASCS/SCS utilizando Discos Geridos,][sap-templates-3-tier-multisid-xscs-marketplace-image-md]introduza valores para os seguintes parâmetros:

  - **Prefixo de recursos**.  Desaprova o prefixo de recursos, que é utilizado para pré-fixar todos os recursos que são criados durante a implantação. Como os recursos não pertencem apenas a um sistema SAP, o prefixo do recurso não é o SID de um sistema SAP.  O prefixo deve estar entre **três e seis caracteres**.
  - **Tipo de pilha**. Selecione o tipo de pilha do sistema SAP. Dependendo do tipo de pilha, o Azure Load Balancer tem um (apenas ABAP ou Java) ou dois endereços IP privados (ABAP+Java) por sistema SAP.
  -  **Tipo DE SO**. Selecione o sistema operativo das máquinas virtuais.
  -  **Contagem do sistema SAP**. Selecione o número de sistemas SAP que pretende instalar neste cluster.
  -  **Disponibilidade do sistema**. Selecione **HA**.
  -  **Nome de utilizador admin e senha de administração**. Crie um novo utilizador que possa ser utilizado para iniciar sessão na máquina.
  -  **Sub-rede nova ou existente.** Desa parte para determinar se deve ser criada uma nova rede virtual e uma sub-rede, ou se deve ser utilizada uma sub-rede existente. Se já tem uma rede virtual que está ligada à sua rede no local, selecione **a existência**.
  -  **Id da sub-rede.** Se pretender colocar o VM num VNet existente onde tenha uma sub-rede definida, o VM deve ser atribuído, nomeie o ID dessa sub-rede específica. O ID geralmente é assim: /subscrições/<*id de subscrição*>/resourceGroups/<*nome do grupo de recursos*>/fornecedores/microsoft.Network/virtualNetworks/<nome de rede *virtual*>/sub-redes/<nome *da sub-rede*>

O modelo implementa uma instância Azure Load Balancer, que suporta vários sistemas SAP.

- As instâncias da ASCS são configuradas, por exemplo, número 00, 10, 20...
- As instâncias scs são configuradas por exemplo número 01, 11, 21...
- As instâncias ascs Enqueue Replication Server (ERS) (apenas Linux) são configuradas para o número 02, 12, 22...
- As instâncias SCS ERS (apenas Linux) são configuradas, por exemplo, número 03, 13, 23...

O equilibrador de carga contém 1 (2 para Linux) VIP(s), 1x VIP para ASCS/SCS e 1x VIP para ERS (apenas Linux).

A lista a seguir contém todas as regras de equilíbrio de carga (onde x é o número do sistema SAP, por exemplo, 1, 2, 3...):
- Portas específicas do Windows para cada sistema SAP: 445,5985
- Portas ASCS (número de exemplo x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- Portas SCS (número de exemplo x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- Portas ASCS ERS em Linux (número de exemplo x2): 33x2, 5x213, 5x214, 5x216
- Portas SCS ERS em Linux (número de exemplo x3): 33x3, 5x313, 5x314, 5x316

O balançador de carga está configurado para utilizar as seguintes portas de sonda (onde x é o número do sistema SAP, por exemplo, 1, 2, 3...):
- Porta de sonda de balançador interno ASCS/SCS: 620x0
- ERS porta de sonda de redebósia interna de carga (apenas Linux): 621x2

#### <a name="database-template"></a><a name="database-template"></a> Modelo de base de dados

O modelo de base de dados implementa uma ou duas máquinas virtuais que pode utilizar para instalar o sistema de gestão de bases de dados relacionais (RDBMS) para um sistema SAP. Por exemplo, se implementar um modelo ASCS/SCS para cinco sistemas SAP, tem de implementar este modelo cinco vezes.

Para configurar o modelo multi-SID da base de dados, no [modelo multi-SID][sap-templates-3-tier-multisid-db-marketplace-image] da base de dados ou [no modelo multi-SID da base de dados utilizando Discos Geridos,][sap-templates-3-tier-multisid-db-marketplace-image-md]introduza valores para os seguintes parâmetros:

- **Identificação do Sistema de Sap.** Introduza o ID do sistema SAP do sistema SAP que pretende instalar. O ID será usado como prefixo para os recursos que são implantados.
- **Tipo de Os**. Selecione o sistema operativo das máquinas virtuais.
- **Dbtype**. Selecione o tipo de base de dados que pretende instalar no cluster. Selecione **SQL** se quiser instalar o Microsoft SQL Server. Selecione **HANA** se planeia instalar o SAP HANA nas máquinas virtuais. Certifique-se de selecionar o tipo de sistema operativo correto: selecione **Windows** for SQL e selecione uma distribuição Linux para HANA. O Balançador de Carga Azure que está ligado às máquinas virtuais será configurado para suportar o tipo de base de dados selecionado:
  * **SQL**. O balançador de carga carregará a porta 1433. Certifique-se de que utiliza esta porta para o seu servidor SQL Sempre Na configuração.
  * **HANA.** O balançador de carga irá carregar as portas 35015 e 35017. Certifique-se de que instala o SAP HANA com a instância número **50**.
  O equilibrador de carga utilizará a porta de sonda 62550.
- **Tamanho do sistema de seiva**. Desa fixação do número de SAPS que o novo sistema irá fornecer. Se não tiver a certeza de quantos SAPS o sistema necessitará, pergunte ao seu Parceiro de Tecnologia SAP ou Integrador de Sistema.
- **Disponibilidade do sistema**. Selecione **HA**.
- **Nome de utilizador admin e senha de administração**. Crie um novo utilizador que possa ser utilizado para iniciar sessão na máquina.
- **Id da sub-rede.** Introduza o ID da sub-rede que utilizou durante a implementação do modelo ASCS/SCS, ou o ID da sub-rede que foi criada como parte da implementação do modelo ASCS/SCS.

#### <a name="application-servers-template"></a><a name="application-servers-template"></a> Modelo de servidores de aplicações

O modelo de servidores de aplicações implementa duas ou mais máquinas virtuais que podem ser usadas como instâncias do Servidor de Aplicações SAP para um sistema SAP. Por exemplo, se implementar um modelo ASCS/SCS para cinco sistemas SAP, tem de implementar este modelo cinco vezes.

Para configurar o modelo multi-SID dos servidores de aplicações, nos servidores de [aplicações o modelo multi-SID][sap-templates-3-tier-multisid-apps-marketplace-image] ou [o modelo multi-SID dos servidores de aplicações utilizando Discos Geridos,][sap-templates-3-tier-multisid-apps-marketplace-image-md]introduza valores para os seguintes parâmetros:

  -  **Identificação do Sistema de Sap.** Introduza o ID do sistema SAP do sistema SAP que pretende instalar. O ID será usado como prefixo para os recursos que são implantados.
  -  **Tipo de Os**. Selecione o sistema operativo das máquinas virtuais.
  -  **Tamanho do sistema de seiva**. O número de SAPS que o novo sistema irá fornecer. Se não tiver a certeza de quantos SAPS o sistema necessitará, pergunte ao seu Parceiro de Tecnologia SAP ou Integrador de Sistema.
  -  **Disponibilidade do sistema**. Selecione **HA**.
  -  **Nome de utilizador admin e senha de administração**. Crie um novo utilizador que possa ser utilizado para iniciar sessão na máquina.
  -  **Id da sub-rede.** Introduza o ID da sub-rede que utilizou durante a implementação do modelo ASCS/SCS, ou o ID da sub-rede que foi criada como parte da implementação do modelo ASCS/SCS.


### <a name="azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Rede virtual Azure
No nosso exemplo, o espaço de endereço da rede virtual Azure é de 10.0.0.0/16. Existe uma sub-rede chamada **Subnet,** com um intervalo de endereços de 10.0.0.0/24. Todas as máquinas virtuais e os equilibradores internos de carga são implantados nesta rede virtual.

> [!IMPORTANT]
> Não eis quaisquer alterações nas definições de rede dentro do sistema operativo do hóspede. Isto inclui endereços IP, servidores DNS e sub-redes. Configure todas as definições de rede em Azure. O serviço Dynamic Host Configuration Protocol (DHCP) propaga as suas definições.
>
>

### <a name="dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> Endereços IP DNS

Para definir os endereços IP DNS necessários, faça os seguintes passos.

1. No portal Azure, na lâmina dos **servidores DNS,** certifique-se de que a opção de **servidores DNS** de rede virtual está definida para **DNS personalizados**.
2. Selecione as suas definições com base no tipo de rede que tem. Para obter mais informações, veja os seguintes recursos:
   * Adicione os endereços IP dos servidores DNS no local.  
   Pode estender os servidores DNS no local às máquinas virtuais que estão a funcionar em Azure. Nesse cenário, pode adicionar os endereços IP das máquinas virtuais Azure nas quais executou o serviço DNS.
   * Para implementações VM isoladas em Azure: Implementar uma máquina virtual adicional na mesma instância de Rede Virtual que serve como um servidor DNS. Adicione os endereços IP das máquinas virtuais Azure que criou para executar o serviço DNS.

   ![Figura 12: Configurar servidores DNS para rede virtual Azure][sap-ha-guide-figure-3001]

   _**Figura 12:** Configurar servidores DNS para rede virtual Azure_

   > [!NOTE]
   > Se alterar os endereços IP dos servidores DNS, tem de reiniciar as máquinas virtuais Azure para aplicar a alteração e propagar os novos servidores DNS.
   >
   >

No nosso exemplo, o serviço DNS está instalado e configurado nestas máquinas virtuais windows:

| Papel da máquina virtual | Nome do anfitrião da máquina virtual | Nome do cartão de rede | Endereço IP estático |
| --- | --- | --- | --- |
| Primeiro servidor DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Segundo servidor DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Nomes de anfitrião e endereços IP estáticos para o caso agrupado SAP ASCS/SCS e instância agrupada DBMS

Para a implantação no local, você precisa destes nomes de anfitriões reservados e endereços IP:

| Papel de nome de anfitrião virtual | Nome de anfitrião virtual | Endereço IP estático virtual |
| --- | --- | --- |
| NOME DE ANFITRIÃO VIRTUAL SAP ASCS/SCS (para gestão de clusters) |pr1-ascs-vir |10.0.0.42 |
| NOME DE ANFITRIÃO VIRTUAL SAP ASCS/SCS |pr1-ascs-seiva |10.0.0.43 |
| Nome de anfitrião virtual do segundo cluster SAP DBMS (gestão de cluster) |pr1-dbms-vir |10.0.0.32 |

Quando criar o cluster, crie os nomes de anfitriões virtuais **pr1-ascs-vir** e **pr1-dbms-vir** e os endereços IP associados que gerem o cluster em si. Para obter informações sobre como fazê-lo, consulte [Recolher nós de cluster numa configuração de cluster][sap-ha-guide-8.12.1].

Pode criar manualmente os outros dois nomes de anfitriões virtuais, **pr1-ascs-seiva** e **pr1-dbms-sap**, e os endereços IP associados, no servidor DNS. A instância SAP ASCS/SCS agrupada e a instância DBMS agrupada utilizam estes recursos. Para obter informações sobre como fazê-lo, consulte [Criar um nome de anfitrião virtual para uma instância SAP ASCS/SCS agrupada][sap-ha-guide-9.1.1].

### <a name="set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Definir endereços IP estáticos para as máquinas virtuais SAP
Depois de colocar as máquinas virtuais para utilizar no seu cluster, precisa de definir endereços IP estáticos para todas as máquinas virtuais. Faça isto na configuração da Rede Virtual Azure e não no sistema operativo dos hóspedes.

1. No portal Azure, selecione O endereço IP das definições **do**  >  **cartão de rede**de grupo de recursos  >  **Settings**  >  **IP Address**.
2. Na lâmina dos **endereços IP,** em **Atribuição**, selecione **Estática**. Na caixa **de endereços IP,** insira o endereço IP que pretende utilizar.

   > [!NOTE]
   > Se alterar o endereço IP do cartão de rede, tem de reiniciar as máquinas virtuais Azure para aplicar a alteração.  
   >
   >

   ![Figura 13: Definir endereços IP estáticos para o cartão de rede de cada máquina virtual][sap-ha-guide-figure-3002]

   _**Figura 13:** Definir endereços IP estáticos para o cartão de rede de cada máquina virtual_

   Repita este passo para todas as interfaces de rede, isto é, para todas as máquinas virtuais, incluindo máquinas virtuais que pretende utilizar para o seu serviço Ative Directory/DNS.

No nosso exemplo, temos estas máquinas virtuais e endereços IP estáticos:

| Papel da máquina virtual | Nome do anfitrião da máquina virtual | Nome do cartão de rede | Endereço IP estático |
| --- | --- | --- | --- |
| Primeira instância do Servidor de Aplicações SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Segunda instância do Servidor de Aplicações SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Última instância do Servidor de Aplicações SAP |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Primeiro nó de cluster para a instância ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Segundo nó de cluster para a instância ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Primeiro nó de cluster para a instância DBMS |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Segundo nó de cluster para a instância DBMS |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

### <a name="set-a-static-ip-address-for-the-azure-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Desconfiem de um endereço IP estático para o balançador interno de carga Azure

O modelo SAP Azure Resource Manager cria um equilibrador de carga interno Azure que é utilizado para o cluster de instâncias SAP ASCS/SCS e para o cluster DBMS.

> [!IMPORTANT]
> O endereço IP do nome de anfitrião virtual do SAP ASCS/SCS é o mesmo que o endereço IP do equilibrador interno de carga SAP ASCS/SCS: **pr1-lb-ascs**.
> O endereço IP do nome virtual do DBMS é o mesmo que o endereço IP do equilibrador interno DBMS: **pr1-lb-dbms**.
>
>

Para definir um endereço IP estático para o balançador interno Azure:

1. A implementação inicial define o endereço IP do balançador de carga interno para **Dynamic**. No portal Azure, na lâmina dos **endereços IP,** em **Atribuição**, selecione **Estática**.
2. Desloje o endereço IP do equilibrador interno **pr1-lb-ascs** para o endereço IP do nome de anfitrião virtual da instância SAP ASCS/SCS.
3. Descreva o endereço IP do equilibrador interno **pr1-lb-dbms** para o endereço IP do nome de anfitrião virtual da instância DBMS.

   ![Figura 14: Definir endereços IP estáticos para o balançador de carga interno para a instância SAP ASCS/SCS][sap-ha-guide-figure-3003]

   _**Figura 14:** Definir endereços IP estáticos para o balançador de carga interno para a instância SAP ASCS/SCS_

No nosso exemplo, temos dois equilibradores internos Azure que têm estes endereços IP estáticos:

| Papel do equilibrador interno de carga azul | Nome do equilibrador interno de carga Azure | Endereço IP estático |
| --- | --- | --- |
| SAP ASCS/SCS caso balanceador interno de carga |pr1-lb-ascs |10.0.0.43 |
| Equilibrador interno de carga SAP DBMS |pr1-lb-dbms |10.0.0.33 |


### <a name="default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Regras de equilíbrio de carga ASCS/SCS predefinidas para o balançador interno de carga Azure

O modelo SAP Azure Resource Manager cria as portas de que necessita:
* Um caso ASCS ABAP, com o número de instância **padrão 00**
* Uma instância Java SCS, com o número de instância **padrão 01**

Quando instalar a sua instância SAP ASCS/SCS, deve utilizar o número de instância **padrão 00** para a sua instância ABAP ASCS e o número de instância **padrão 01** para a sua instância Java SCS.

Em seguida, crie pontos finais de equilíbrio internos necessários para as portas SAP NetWeaver.

Para criar pontos finais de equilíbrio internos necessários, em primeiro lugar, crie estes pontos finais de equilíbrio de carga para as portas SAP NetWeaver ABAP ASCS:

| Nome da regra de equilíbrio serviço/carga | Números de porta predefinidos | Portas de betão para (instância ASCS com o número 00) (ERS com 10) |
| --- | --- | --- |
| Enqueue Server / *lbrule3200* |32 *<InstanceNumber*> |3200 |
| Servidor de mensagens ABAP / *lbrule3600* |36<*InstanceNumber*> |3600 |
| Mensagem Interna ABAP / *lbrule3900* |39 *<InstanceNumber*> |3900 |
| Servidor de mensagens HTTP / *Lbrule8100* |81 *<InstanceNumber*> |8100 |
| SERVIÇO SAP Start ASCS HTTP / *Lbrule50013* |5<*InstanceNumber*>13 |50013 |
| SERVIÇO SAP START ASCS HTTPS / *Lbrule50014* |5<*InstanceNumber*>14 |50014 |
| Replicação enqueue / *Lbrule50016* |5<*InstanceNumber*>16 |50016 |
| SAP Start Service ERS HTTP *Lbrule51013* |5<*InstanceNumber*>13 |51013 |
| SAP Start Service ERS HTTP *Lbrule51014* |5<*InstanceNumber*>14 |51014 |
| Win RM *Lbrule5985* | |5985 |
| Partilha de *ficheiros Lbrule445* | |445 |

_**Tabela 1:** Números portuários das instâncias ASCS DA NET NetWeaver ABAP_

Em seguida, crie estes pontos finais de equilíbrio de carga para as portas SAP NetWeaver Java SCS:

| Nome da regra de equilíbrio serviço/carga | Números de porta predefinidos | Portas de betão para (instância SCS com o número 01) (ERS com 11) |
| --- | --- | --- |
| Enqueue Server / *lbrule3201* |32 *<InstanceNumber*> |3201 |
| Gateway Server / *lbrule3301* |33 *<InstanceNumber*> |3301 |
| Servidor de Mensagens Java / *Lbrule3900* |39 *<InstanceNumber*> |3901 |
| Servidor de mensagens HTTP / *Lbrule8101* |81 *<InstanceNumber*> |8101 |
| SAP Start Service SCS HTTP / *Lbrule50113* |5<*InstanceNumber*>13 |50113 |
| SERVIÇO SAP START SCS HTTPS / *Lbrule50114* |5<*InstanceNumber*>14 |50114 |
| Replicação enqueue / *Lbrule50116* |5<*InstanceNumber*>16 |50116 |
| SAP Start Service ERS HTTP *Lbrule51113* |5<*InstanceNumber*>13 |51113 |
| SAP Start Service ERS HTTP *Lbrule51114* |5<*InstanceNumber*>14 |51114 |
| Win RM *Lbrule5985* | |5985 |
| Partilha de *ficheiros Lbrule445* | |445 |

_**Tabela 2:** Números portuários das instâncias SAP NetWeaver Java SCS_

![Figura 15: Regras de equilíbrio de carga ASCS/SCS por defeito para o balançador interno de carga Azure][sap-ha-guide-figure-3004]

_**Figura 15:** Regras de equilíbrio de carga ASCS/SCS predefinidas para o balançador interno de carga Azure_

Descreva o endereço IP do equilibrador de carga **pr1-lb-dbms** para o endereço IP do nome de anfitrião virtual da instância DBMS.

### <a name="change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Alterar as regras de equilíbrio de carga padrão ASCS/SCS para o balançador interno de carga Azure

Se pretender utilizar números diferentes para as instâncias SAP ASCS ou SCS, tem de alterar os nomes e valores das suas portas a partir de valores predefinidos.

1. No portal Azure, selecione ** < *SID*>-lb-ascs load Balancer Regras**  >  **de equilíbrio**de carga .
2. Para todas as regras de equilíbrio de carga que pertencem à instância SAP ASCS ou SCS, altere estes valores:

   * Nome
   * Porta
   * Porta traseira

   Por exemplo, se pretender alterar o número de instância ASCS padrão de 00 para 31, tem de escoar as alterações para todas as portas listadas no Quadro 1.

   Aqui está um exemplo de uma atualização para o porto *lbrule3200*.

   ![Figura 16: Alterar as regras de equilíbrio de carga padrão ASCS/SCS para o balançador interno de carga Azure][sap-ha-guide-figure-3005]

   _**Figura 16:** Alterar as regras de equilíbrio de carga padrão ASCS/SCS para o balançador interno de carga Azure_

### <a name="add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Adicione máquinas virtuais do Windows ao domínio

Depois de atribuir um endereço IP estático às máquinas virtuais, adicione as máquinas virtuais ao domínio.

![Figura 17: Adicionar uma máquina virtual a um domínio][sap-ha-guide-figure-3006]

_**Figura 17:** Adicione uma máquina virtual a um domínio_

### <a name="add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Adicionar entradas de registo em ambos os nós de cluster da instância SAP ASCS/SCS

O Azure Load Balancer tem um equilibrador de carga interno que fecha as ligações quando as ligações estão inativas durante um período de tempo definido (um tempo limite de marcha lenta). Os processos de trabalho da SAP em instâncias de diálogo abrem ligações ao processo de enquese SAP assim que o primeiro pedido de enqueue/dequeue for necessário. Estas ligações geralmente permanecem estabelecidas até que o processo de trabalho ou o processo de enqueue reiniciem. No entanto, se a ligação estiver inativa durante um período de tempo definido, o equilibrador interno Azure fecha as ligações. Isto não é um problema porque o processo de trabalho da SAP restabelece a ligação ao processo de enqueue se já não existir. Estas atividades estão documentadas nos vestígios de desenvolvedores de processos SAP, mas criam uma grande quantidade de conteúdo extra nesses vestígios. É uma boa ideia mudar o TCP/IP `KeepAliveTime` e ambos os nós de `KeepAliveInterval` cluster. Combine estas alterações nos parâmetros TCP/IP com os parâmetros do perfil SAP, descritos mais tarde no artigo.

Para adicionar entradas de registo em ambos os nós de cluster da instância SAP ASCS/SCS, em primeiro lugar, adicione estas entradas de registo do Windows em ambos os nós do cluster Windows para SAP ASCS/SCS:

| Caminho | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parâmetros |
| --- | --- |
| Nome da variável |`KeepAliveTime` |
| Tipo variável |REG_DWORD (Decimal) |
| Valor |120000 |
| Ligação à documentação |[https://technet.microsoft.com/library/cc957549.aspx](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)) |

_**Tabela 3:** Alterar o primeiro parâmetro TCP/IP_

Em seguida, adicione estas entradas de registo do Windows em ambos os nós do cluster Windows para SAP ASCS/SCS:

| Caminho | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parâmetros |
| --- | --- |
| Nome da variável |`KeepAliveInterval` |
| Tipo variável |REG_DWORD (Decimal) |
| Valor |120000 |
| Ligação à documentação |[https://technet.microsoft.com/library/cc957548.aspx](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) |

_**Tabela 4:** Alterar o segundo parâmetro TCP/IP_

**Para aplicar as alterações, reinicie ambos os nós de cluster**.

### <a name="set-up-a-windows-server-failover-clustering-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Configurar um cluster de clustering de failover do servidor do Windows para uma instância SAP ASCS/SCS

A criação de um cluster de clustering de failover do servidor do Windows para uma instância SAP ASCS/SCS envolve estas tarefas:

- Recolher os nódes de cluster numa configuração de cluster
- Configurar uma testemunha de partilha de ficheiros de cluster

#### <a name="collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Colete os nóns de cluster numa configuração de cluster

1. No Add Role and Features Wizard, adicione o agrupamento de failover a ambos os nós de cluster.
2. Configurar o cluster de failover utilizando o Failover Cluster Manager. No Failover Cluster Manager, selecione **Create Cluster**e, em seguida, adicione apenas o nome do primeiro cluster, nó A. Não adicione ainda o segundo nó; você vai adicionar o segundo nó em um passo posterior.

   ![Figura 18: Adicione o servidor ou o nome da máquina virtual do primeiro nó de cluster][sap-ha-guide-figure-3007]

   _**Figura 18:** Adicione o nome do servidor ou da máquina virtual do primeiro nó de cluster_

3. Introduza o nome da rede (nome de anfitrião virtual) do cluster.

   ![Figura 19: Introduza o nome do cluster][sap-ha-guide-figure-3008]

   _**Figura 19:** Insira o nome do cluster_

4. Depois de criar o cluster, faça um teste de validação de cluster.

   ![Figura 20: Executar o controlo de validação do cluster][sap-ha-guide-figure-3009]

   _**Figura 20:** Executar a verificação de validação do cluster_

   Pode ignorar quaisquer avisos sobre discos nesta altura do processo. Mais tarde, adicionará uma testemunha de partilha de ficheiros e os discos partilhados sios. Nesta fase, não precisas de te preocupar em ter um quórum.

   ![Figura 21: Nenhum disco de quórum é encontrado][sap-ha-guide-figure-3010]

   _**Figura 21:** Nenhum disco de quórum é encontrado_

   ![Figura 22: O recurso de cluster central precisa de um novo endereço IP][sap-ha-guide-figure-3011]

   _**Figura 22:** O recurso de cluster central precisa de um novo endereço IP_

5. Alterar o endereço IP do serviço de cluster core. O cluster não pode iniciar-se até alterar o endereço IP do serviço de cluster principal, porque o endereço IP do servidor aponta para um dos nós da máquina virtual. Faça isto na página **Propriedades** do recurso IP do serviço de cluster de núcleo.

   Por exemplo, precisamos de atribuir um endereço IP (no nosso exemplo, **10.0.0.42**) para o cluster virtual nome de anfitrião **pr1-ascs-vir**.

   ![Figura 23: Na caixa de diálogo Propriedades, altere o endereço IP][sap-ha-guide-figure-3012]

   _**Figura 23:** Na caixa de diálogo **Propriedades,** altere o endereço IP_

   ![Figura 24: Atribuir o endereço IP reservado para o cluster][sap-ha-guide-figure-3013]

   _**Figura 24:** Atribua o endereço IP reservado para o cluster_

6. Leve o nome de anfitrião virtual do cluster on-line.

   ![Figura 25: O serviço central do cluster está em funcionamento e com o endereço IP correto][sap-ha-guide-figure-3014]

   _**Figura 25:** O serviço central do cluster está em funcionamento e com o endereço IP correto_

7. Adicione o segundo nó de cluster.

   Agora que o serviço de cluster central está a funcionar, pode adicionar o segundo nó de cluster.

   ![Figura 26: Adicione o segundo nó de cluster][sap-ha-guide-figure-3015]

   _**Figura 26:** Adicione o segundo nó de cluster_

8. Insira um nome para o segundo anfitrião do nó de cluster.

   ![Figura 27: Introduza o nome do anfitrião do segundo aglomerado][sap-ha-guide-figure-3016]

   _**Figura 27:** Insira o nome do anfitrião do segundo cluster_

   > [!IMPORTANT]
   > Certifique-se de que **o Adicionar todo o armazenamento elegível à** caixa de verificação do cluster **NÃO** está selecionado.  
   >
   >

   ![Figura 28: Não selecione a caixa de verificação][sap-ha-guide-figure-3017]

   _**Figura 28:** **Não** selecione a caixa de verificação_

   Pode ignorar avisos sobre quórum e discos. Irá definir o quórum e partilhar o disco mais tarde, conforme descrito na [Instalação sios DataKeeper Cluster Edition para o disco de partilha de cluster SAP ASCS/SCS][sap-ha-guide-8.12.3].

   ![Figura 29: Ignorar avisos sobre o quórum do disco][sap-ha-guide-figure-3018]

   _**Figura 29:** Ignore avisos sobre o quórum do disco_


#### <a name="configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Configure uma testemunha de partilha de ficheiros de cluster

Configurar uma testemunha de partilha de ficheiros de cluster envolve estas tarefas:

- Criar uma partilha de ficheiros
- Definição do quórum da testemunha de partilha de ficheiros no Failover Cluster Manager

##### <a name="create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Criar uma partilha de ficheiros

1. Selecione uma testemunha de partilha de ficheiros em vez de um disco de quórum. O SIOS DataKeeper suporta esta opção.

   Nos exemplos deste artigo, a testemunha de partilha de ficheiros encontra-se no servidor Ative Directory/DNS que está a ser executada no Azure. A testemunha de partilha de ficheiros **chama-se domcontr-0**. Como teria configurado uma ligação VPN ao Azure (via VPN site-to-site ou Azure ExpressRoute), o seu serviço Ative Directory/DNS está no local e não é adequado para executar uma testemunha de partilha de ficheiros.

   > [!NOTE]
   > Se o seu serviço Ative Directory/DNS funcionar apenas no local, não configuure a sua testemunha de partilha de ficheiros no sistema operativo Ative Directory/DNS Windows que está a funcionar no local. A latência da rede entre os nós de cluster que estão a funcionar no Azure e no Ative Directory/DNS no local pode ser demasiado grande e causar problemas de conectividade. Certifique-se de configurar a testemunha de partilha de ficheiros numa máquina virtual Azure que está a funcionar perto do nó de cluster.  
   >
   >

   A unidade do quórum precisa de pelo menos 1.024 MB de espaço livre. Recomendamos 2.048 MB de espaço livre para a unidade do quórum.

2. Adicione o objeto de nome de cluster.

   ![Figura 30: Atribuir as permissões na partilha para o objeto de nome de cluster][sap-ha-guide-figure-3019]

   _**Figura 30:** Atribua as permissões na partilha para o objeto de nome de cluster_

   Certifique-se de que as permissões incluem a autoridade para alterar dados na partilha do objeto de nome do cluster (no nosso exemplo, **pr1-ascs-vir$**).

3. Para adicionar o objeto de nome do cluster à lista, selecione **Adicionar**. Mude o filtro para verificar se há objetos de computador, para além dos indicados na Figura 31.

   ![Figura 31: Alterar os tipos de objetos para incluir computadores][sap-ha-guide-figure-3020]

   _**Figura 31:** Alterar os Tipos de Objetos para incluir computadores_

   ![Figura 32: Selecione a caixa de verificação de computadores][sap-ha-guide-figure-3021]

   _**Figura 32:** Selecione a caixa de verificação **de computadores**_

4. Introduza o objeto do nome do cluster como mostrado na Figura 31. Como o registo já foi criado, pode alterar as permissões, como mostra a Figura 30.

5. Selecione o separador **Segurança** da partilha e, em seguida, desempate permissões mais detalhadas para o objeto de nome do cluster.

   ![Figura 33: Definir os atributos de segurança para o objeto de nome do cluster no quórum de partilha de ficheiros][sap-ha-guide-figure-3022]

   _**Figura 33:** Desajei os atributos de segurança para o objeto de nome do cluster no quórum de partilha de ficheiros_

##### <a name="set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Desacorra o quórum da testemunha de partilha de ficheiros no Gerente de Cluster Failover

1. Abra o assistente de definição de quorum configurado.

   ![Figura 34: Iniciar o assistente de definição do quorum do cluster configurar][sap-ha-guide-figure-3023]

   _**Figura 34:** Inicie o assistente de definição do quorum do cluster configurado_

2. Na página **De Configuração do Conselho seleto,** **selecione Selecione a testemunha quórum**.

   ![Figura 35: Configurações do Quórum que pode escolher][sap-ha-guide-figure-3024]

   _**Figura 35:** Configurações do Quórum que pode escolher_

3. Na página **Select Quorum Witness,** selecione **Configurar uma testemunha de partilha de ficheiros**.

   ![Figura 36: Selecione a testemunha de partilha de ficheiros][sap-ha-guide-figure-3025]

   _**Figura 36:** Selecione a testemunha de partilha de ficheiros_

4. Insira o caminho da UNC para a partilha de ficheiros (no nosso exemplo, \\ domcontr-0\FSW). Para ver uma lista das alterações que pode fazer, selecione **Next**.

   ![Figura 37: Definir a localização da partilha de ficheiros para a partilha de testemunhas][sap-ha-guide-figure-3026]

   _**Figura 37:** Defina a localização da partilha de ficheiros para a partilha de testemunhas_

5. Selecione as alterações desejativas e, em seguida, selecione **Seguinte**. É necessário reconfigurar com sucesso a configuração do cluster, tal como mostrado na Figura 38.  

   ![Figura 38: Confirmação de que reconfigurou o cluster][sap-ha-guide-figure-3027]

   _**Figura 38:** Confirmação de que reconfigurou o cluster_

Depois de instalar o Windows Failover Cluster com sucesso, é necessário fazer alterações em alguns limiares para adaptar a deteção de falhas às condições em Azure. Os parâmetros a alterar estão documentados neste blog: https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/ . Assumindo que os seus dois VMs que constroem a Configuração do Cluster do Windows para ASCS/SCS estão na mesma SubNet, os seguintes parâmetros precisam de ser alterados para estes valores:
- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Estas configurações foram testadas com os clientes e proporcionaram um bom compromisso para serem suficientemente resistentes por um lado. Por outro lado, estas definições estavam a fornecer falhas suficientemente rápidas em condições reais de erro no software SAP ou falha no nó/VM. 

### <a name="install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Instale a Edição do Cluster SiOS DataKeeper para o disco de partilha de cluster SAP ASCS/SCS

Tem agora uma configuração de clustering de falha do servidor do Windows em funcionamento no Azure. Mas, para instalar um caso SAP ASCS/SCS, precisa de um recurso de disco partilhado. Não é possível criar os recursos de disco partilhado de que necessita no Azure. Sios DataKeeper Cluster Edition é uma solução de terceiros que pode usar para criar recursos de disco partilhados.

A instalação da SIOS DataKeeper Cluster Edition para o disco de partilha de cluster SAP ASCS/SCS envolve estas tarefas:

- Adicionar o Quadro .NET 3.5
- Instalação sios dataKeeper
- Criação do SIOS DataKeeper

#### <a name="add-the-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Adicione o Quadro .NET 3.5
O Microsoft .NET Framework 3.5 não é ativado ou instalado automaticamente no Windows Server 2012 R2. Uma vez que o SIOS DataKeeper exige que o Quadro .NET esteja em todos os nós em que instala o DataKeeper, tem de instalar o Quadro .NET 3.5 no sistema operativo de todos os clientes do cluster.

Existem duas formas de adicionar o Quadro .NET 3.5:

- Utilize o Assistente de Funções e Funcionalidades adicionais no Windows, como mostrado na Figura 39.

  ![Figura 39: Instale o quadro .NET 3.5 utilizando o Assistente de Funções e Funcionalidades adicionais][sap-ha-guide-figure-3028]

  _**Figura 39:** Instale o quadro .NET 3.5 utilizando o Assistente de Funções e Funcionalidades adicionais_

  ![Figura 40: Barra de progresso de instalação quando instala a estrutura .NET 3.5 utilizando o Assistente de Funções e Funcionalidades adicionais][sap-ha-guide-figure-3029]

  _**Figura 40:** Barra de progresso de instalação quando instala o Quadro .NET 3.5 utilizando o Assistente de Funções e Funcionalidades adicionar_

- Utilize a ferramenta de linha de comando dism.exe. Para este tipo de instalação, é necessário aceder ao diretório SxS nos meios de instalação windows. Com um pedido de comando elevado, escreva:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a> Instalar sios datakeeper

Instale a SIOS DataKeeper Cluster Edition em cada nó no cluster. Para criar armazenamento partilhado virtual com o SIOS DataKeeper, crie um espelho sincronizado e, em seguida, simular o armazenamento partilhado do cluster.

Antes de instalar o software SIOS, crie o **datakeeperSvc**do utilizador de domínio .

> [!NOTE]
> Adicione o utilizador **DataKeeperSvc** ao grupo **administrador local** em ambos os nós de cluster.
>
>

Para instalar o SIOS DataKeeper:

1. Instale o software SIOS em ambos os nós de cluster.

   ![Instalador SIOS][sap-ha-guide-figure-3030]

   ![Figura 41: Primeira página da instalação SIOS DataKeeper][sap-ha-guide-figure-3031]

   _**Figura 41:** Primeira página da instalação SIOS DataKeeper_

2. Na caixa de diálogo mostrada na Figura 42, selecione **Sim**.

   ![Figura 42: DataKeeper informa-o de que um serviço será desativado][sap-ha-guide-figure-3032]

   _**Figura 42:** DataKeeper informa que um serviço será desativado_

3. Na caixa de diálogo mostrada na Figura 43, recomendamos que selecione **a conta De domínio ou servidor**.

   ![Figura 43: Seleção de utilizadores para SIOS DataKeeper][sap-ha-guide-figure-3033]

   _**Figura 43:** Seleção do utilizador para SIOS DataKeeper_

4. Introduza o nome de utilizador da conta de domínio e as palavras-passe que criou para o SIOS DataKeeper.

   ![Figura 44: Introduza o nome de utilizador de domínio e a palavra-passe para a instalação SIOS DataKeeper][sap-ha-guide-figure-3034]

   _**Figura 44:** Introduza o nome de utilizador de domínio e a palavra-passe para a instalação SIOS DataKeeper_

5. Instale a chave de licença para a sua instância SIOS DataKeeper como mostrado na Figura 45.

   ![Figura 45: Introduza a sua chave de licença SIOS DataKeeper][sap-ha-guide-figure-3035]

   _**Figura 45:** Introduza a chave de licença SIOS DataKeeper_

6. Quando solicitado, reinicie a máquina virtual.

#### <a name="set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Configurar sios dataKeeper

Depois de instalar o SIOS DataKeeper em ambos os nós, tem de iniciar a configuração. O objetivo da configuração é ter uma replicação de dados sincronizada entre os discos adicionais ligados a cada uma das máquinas virtuais.

1. Inicie a ferramenta de Gestão e Configuração de DataKeeper e, em seguida, selecione **Connect Server**. (Na Figura 46, esta opção é rodeada a vermelho.)

   ![Figura 46: Ferramenta sios datakeeper e configuração][sap-ha-guide-figure-3036]

   _**Figura 46:** Ferramenta sios datakeeper gestão e configuração_

2. Introduza o nome ou endereço TCP/IP do primeiro nó a ferramenta Gestão e Configuração deve ligar-se e, num segundo passo, o segundo nó.

   ![Figura 47: Insira o nome ou endereço TCP/IP do primeiro nó a ferramenta Gestão e Configuração deve ligar-se e, num segundo passo, o segundo nó][sap-ha-guide-figure-3037]

   _**Figura 47:** Insira o nome ou endereço TCP/IP do primeiro nó a ferramenta Gestão e Configuração deve ligar-se e, num segundo passo, o segundo nó_

3. Crie o trabalho de replicação entre os dois nós.

   ![Figura 48: Criar um trabalho de replicação][sap-ha-guide-figure-3038]

   _**Figura 48:** Criar um trabalho de replicação_

   Um assistente guia-o através do processo de criação de um trabalho de replicação.
4. Defina o nome, endereço TCP/IP e volume de disco do nó de origem.

   ![Figura 49: Definir o nome do trabalho de replicação][sap-ha-guide-figure-3039]

   _**Figura 49:** Definir o nome do trabalho de replicação_

   ![Figura 50: Definir os dados base para o nó, que deve ser o nó de origem atual][sap-ha-guide-figure-3040]

   _**Figura 50:** Defina os dados base para o nó, que deve ser o nó de origem atual_

5. Defina o nome, endereço TCP/IP e volume de disco do nó-alvo.

   ![Figura 51: Definir os dados base para o nó, que deve ser o nó-alvo atual][sap-ha-guide-figure-3041]

   _**Figura 51:** Defina os dados base para o nó, que deve ser o nó-alvo atual_

6. Defina os algoritmos de compressão. No nosso exemplo, recomendamos que comprima o fluxo de replicação. Especialmente em situações de resincronização, a compressão do fluxo de replicação reduz drasticamente o tempo de ressincronização. Note que a compressão utiliza os recursos cpu e RAM de uma máquina virtual. À medida que a taxa de compressão aumenta, também o volume de recursos da CPU utilizados. Também pode ajustar esta definição mais tarde.

7. Outra definição que precisa de verificar é se a replicação ocorre de forma assíncronea ou sincronizada. *Quando proteger as configurações SAP ASCS/SCS, deve utilizar a replicação sincronizada*.  

   ![Figura 52: Definir detalhes de replicação][sap-ha-guide-figure-3042]

   _**Figura 52:** Definir detalhes de replicação_

8. Defina se o volume replicado pela função de replicação deve ser representado numa configuração de cluster de cluster de failover do Windows Server como um disco partilhado. Para a configuração SAP ASCS/SCS, selecione **Sim** para que o cluster Do Windows veja o volume replicado como um disco partilhado que pode usar como um volume de cluster.

   ![Figura 53: Selecione Sim para definir o volume replicado como um volume de cluster][sap-ha-guide-figure-3043]

   _**Figura 53:** Selecione **Sim** para definir o volume replicado como um volume de cluster_

   Após a criação do volume, a ferramenta de Gestão e Configuração do DataKeeper mostra que o trabalho de replicação está ativo.

   ![Figura 54: O espelhamento sincronizado do DataKeeper para o disco de partilha SAP ASCS/SCS está ativo][sap-ha-guide-figure-3044]

   _**Figura 54:** O espelhamento sincronizado do DataKeeper para o disco de partilha SAP ASCS/SCS está ativo_

   O Gestor de Cluster Failover mostra agora o disco como um disco DataKeeper, como mostrado na Figura 55.

   ![Figura 55: Gestor de Cluster Failover mostra o disco que o DataKeeper replicado][sap-ha-guide-figure-3045]

   _**Figura 55:** O Gestor de Cluster Failover mostra o disco que o DataKeeper replicado_

## <a name="install-the-sap-netweaver-system"></a><a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Instale o sistema SAP NetWeaver

Não descreveremos a configuração do DBMS porque as configurações variam consoante o sistema DBMS que utiliza. No entanto, assumimos que as preocupações de alta disponibilidade com o DBMS são abordadas com as funcionalidades que os diferentes fornecedores DBMS suportam para o Azure. Por exemplo, Always On ou base de dados espelhada para SQL Server e Oracle Data Guard para bases de dados Oracle. No cenário que usamos neste artigo, não adicionamos mais proteção ao DBMS.

Não existem considerações especiais quando diferentes serviços DBMS interagem com este tipo de configuração SAP ASCS/SCS agrupada em Azure.

> [!NOTE]
> Os procedimentos de instalação dos sistemas SAP NetWeaver ABAP, sistemas Java e sistemas ABAP+Java são quase idênticos. A diferença mais significativa é que um sistema SAP ABAP tem uma instância ASCS. O sistema SAP Java tem uma instância SCS. O sistema SAP ABAP+Java tem uma instância ASCS e uma instância SCS a decorrer no mesmo grupo de cluster de failover da Microsoft. Quaisquer diferenças de instalação para cada pilha de instalação SAP NetWeaver são explicitamente mencionadas. Pode assumir que todas as outras partes são iguais.  
>
>

### <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Instale o SAP com uma instância ASCS/SCS de alta disponibilidade

> [!IMPORTANT]
> Certifique-se de que não coloca o ficheiro da página nos volumes espelhados do DataKeeper. O DataKeeper não suporta volumes espelhados. Pode deixar o ficheiro da página na unidade temporária D de uma máquina virtual Azure, que é o padrão. Se ainda não estiver lá, mova o ficheiro da página do Windows para a unidade D: da sua máquina virtual Azure.
>
>

A instalação do SAP com uma instância ASCS/SCS de alta disponibilidade envolve estas tarefas:

- Criação de um nome de hospedeiro virtual para a instância SAP ASCS/SCS agrupada
- Instalação do primeiro nó de cluster SAP
- Modificação do perfil SAP da instância ASCS/SCS
- Adicionar uma porta de sonda
- Abertura da porta de sonda de firewall do Windows

#### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Criar um nome de hospedeiro virtual para a instância SAP ASCS/SCS agrupada

1. No gestor DNS do Windows, crie uma entrada DNS para o nome de anfitrião virtual da instância ASCS/SCS.

   > [!IMPORTANT]
   > O endereço IP que atribui ao nome de anfitrião virtual da instância ASCS/SCS deve ser o mesmo que o endereço IP que atribuiu ao Azure Load Balancer** < *(SID*>-lb-ascs).**  
   >
   >

   O endereço IP do nome de anfitrião SAP ASCS/SCS virtual **(pr1-ascs-sap)** é o mesmo que o endereço IP do Azure Load Balancer **(pr1-lb-ascs).**

   ![Figura 56: Definir a entrada de DNS para o nome virtual do cluster SAP ASCS/SCS e o endereço TCP/IP][sap-ha-guide-figure-3046]

   _**Figura 56:** Definir a entrada dns para o nome virtual do cluster SAP ASCS/SCS e o endereço TCP/IP_

2. Para definir o endereço IP atribuído ao nome de anfitrião virtual, selecione **DNS Manager**  >  **Domain**.

   ![Figura 57: Novo nome virtual e endereço TCP/IP para configuração do cluster SAP ASCS/SCS][sap-ha-guide-figure-3047]

   _**Figura 57:** Novo nome virtual e endereço TCP/IP para configuração do cluster SAP ASCS/SCS_

#### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Instale o primeiro nó de cluster SAP

1. Execute a primeira opção de nó de cluster no nó de cluster A. Por exemplo, no hospedeiro **pr1-ascs-0.**
2. Para manter as portas predefinidas para o balançador interno Azure, selecione:

   * **Sistema ABAP**: **Instância ASCS** número **00**
   * **Sistema Java**: **Instância SCS** número **01**
   * **Sistema ABAP+Java**: **Instância ASCS** número **00** e **instância SCS** número **01**

   Para utilizar números de instâncias que não sejam 00 para a instância ABAP ASCS e 01 para a instância Java SCS, primeiro é necessário alterar as regras de equilíbrio de carga padrão do balançador interno Azure, descritas na [alteração das regras de equilíbrio de carga padrão ASCS/SCS para o equilibrador interno de carga Azure][sap-ha-guide-8.9].

As próximas tarefas não são descritas na documentação padrão de instalação SAP.

> [!NOTE]
> A documentação de instalação SAP descreve como instalar o primeiro nó de cluster ASCS/SCS.
>
>

#### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Modificar o perfil SAP da instância ASCS/SCS

Tens de adicionar um novo parâmetro de perfil. O parâmetro de perfil evita que as ligações entre os processos de trabalho SAP e o servidor de enqueue se fechem quando estão inativas por muito tempo. Mencionamos o cenário de problema em [adicionar entradas de registo em ambos os nós de cluster da instância SAP ASCS/SCS][sap-ha-guide-8.11]. Nessa secção, introduzimos também duas alterações em alguns parâmetros básicos de ligação TCP/IP. Num segundo passo, é necessário definir o servidor de enqueue para enviar um sinal para `keep_alive` que as ligações não atinjam o limiar de marcha lenta do balançador interno do Azure.

Para modificar o perfil SAP da instância ASCS/SCS:

1. Adicione este parâmetro de perfil ao perfil de instância SAP ASCS/SCS:

   ```
   enque/encni/set_so_keepalive = true
   ```
   No nosso exemplo, o caminho é:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Por exemplo, para o perfil de instância SAP SCS e caminho correspondente:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. Para aplicar as alterações, reinicie a instância SAP ASCS /SCS.

#### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Adicione uma porta de sonda

Utilize a funcionalidade da sonda do balançador interno para fazer com que toda a configuração do cluster funcione com o Balançador de Carga Azure. O balançador interno de carga Azure distribui normalmente a carga de trabalho de entrada igualmente entre máquinas virtuais participantes. No entanto, isto não funcionará em algumas configurações de cluster porque apenas um caso está ativo. A outra instância é passiva e não pode aceitar nenhuma carga de trabalho. Uma funcionalidade da sonda ajuda quando o equilibrador interno Azure atribui o trabalho apenas a um caso ativo. Com a funcionalidade da sonda, o equilibrador de carga interno pode detetar quais as instâncias estão ativas e, em seguida, visar apenas o caso com a carga de trabalho.

Para adicionar uma porta de sonda:

1. Verifique a definição atual do **ProbePort** executando o seguinte comando PowerShell. Execute-o a partir de uma das máquinas virtuais na configuração do cluster.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

2. Defina uma porta de sonda. O número predefinido da porta da sonda é **0**. No nosso exemplo, usamos a porta de sonda **62000.**

   ![Figura 58: A porta da sonda de configuração do cluster é 0 por padrão][sap-ha-guide-figure-3048]

   _**Figura 58:** A porta de sonda de configuração de configuração padrão é 0_

   O número da porta é definido nos modelos SAP Azure Resource Manager. Pode atribuir o número da porta em PowerShell.

   Para definir um novo valor ProbePort para o **recurso DE <*SID*>** cluster IP, execute o seguinte script PowerShell. Atualize as variáveis PowerShell para o seu ambiente. Depois de o script ser executado, será solicitado que reinicie o grupo de cluster SAP para ativar as alterações.

   ```powershell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

   Clear-Host
   $SAPClusterRoleName = "SAP $SAPSID"
   $SAPIPresourceName = "SAP $SAPSID IP"
   $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
   $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
   $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
   $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
   $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
   $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
   $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

   $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

   Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

   Write-Host
   Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
   Write-Host
   Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
   Write-Host

   $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

   Write-Host

   $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

   if($ActivateChanges -eq "yes"){
   Write-Host
   Write-Host "Activating changes..." -ForegroundColor Cyan

   Write-Host
   write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
   Stop-ClusterResource -Name $SAPIPresourceName
   sleep 5

   Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
   Start-ClusterGroup -Name $SAPClusterRoleName

   Write-Host "New ProbePort parameter is active." -ForegroundColor Green
   Write-Host

   Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
   Write-Host
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
   }else
   {
   Write-Host "Changes are not activated."
   }
   ```

   Depois de colocar online o papel de cluster **DA SAP <*SID,* > ** verifique se o **ProbePort** está definido para o novo valor.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```

   ![Figura 59: Sondar a porta de cluster depois de definir o novo valor][sap-ha-guide-figure-3049]

   _**Figura 59:** Sondar a porta de cluster depois de definir o novo valor_

#### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Abra a porta da sonda de firewall do Windows

É necessário abrir uma porta de sonda de firewall do Windows em ambos os nós de cluster. Utilize o seguinte script para abrir uma porta de sonda de firewall do Windows. Atualize as variáveis PowerShell para o seu ambiente.

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

O **ProbePort** está programado para **62000**. Agora pode aceder à partilha de ** \\ ficheiros \ascsha-clsap\sapmnt** de outros anfitriões, tais como **de ascsha-dbas**.

### <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Instale a instância da base de dados

Para instalar a caixa de dados, siga o processo descrito na documentação de instalação SAP.

### <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Instale o segundo nó de cluster

Para instalar o segundo cluster, siga os passos no guia de instalação SAP.

### <a name="change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Alterar o tipo de início da instância de serviço SAP ERS Windows

Altere o tipo de partida do serviço SAP ERS Windows para **Automatic (Início Atrasado)** em ambos os nós do cluster.

![Figura 60: Alterar o tipo de serviço para a instância SAP ERS para o atraso automático][sap-ha-guide-figure-3050]

_**Figura 60:** Alterar o tipo de serviço para a instância SAP ERS para o atraso automático_

### <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Instale o Servidor de Aplicações Primárias SAP

Instale a instância do Servidor de Aplicação Primária (PAS) <*SID*>-di-0 na máquina virtual que designou para hospedar o PAS. Não existem dependências de definições específicas do Azure ou dataKeeper.

### <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Instale o Servidor de Aplicações Adicionais SAP

Instale um Servidor de Aplicações Adicionais SAP (AAS) em todas as máquinas virtuais que designou para hospedar uma instância do Servidor de Aplicações SAP. Por exemplo, em <*SID*>-di-1 a <*SID*>-di-di-n &lt; &gt; .

> [!NOTE]
> Isto termina a instalação de um sistema SAP NetWeaver de alta disponibilidade. Em seguida, proceda com os testes de failover.
>


## <a name="test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Teste o failover de instância SAP ASCS/SCS e a replicação sios
É fácil testar e monitorizar uma falha de falha de instância SAP ASCS/SCS e replicação do disco SIOS utilizando o Failover Cluster Manager e a ferramenta sios DataKeeper Management and Configuration.

### <a name="sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> A instância SAP ASCS/SCS está a decorrer no nó de cluster A

O grupo de cluster **SAP PR1** está a funcionar no nó de cluster A. Por exemplo, em **pr1-ascs-0**. Atribua a unidade de disco partilhada S, que faz parte do grupo de cluster **SAP PR1,** e que a instância ASCS/SCS utiliza, para o nó de agrupamento A.

![Figura 61: Gestor do Cluster Failover: O grupo de cluster <SID> <DOM ESTÁ a funcionar no nó de cluster A][sap-ha-guide-figure-5000]

_**Figura 61:** Failover Cluster Manager: O grupo de cluster <SID> *<DAE* está a funcionar no nó de cluster A_

Na ferramenta SIOS DataKeeper Management and Configuration, pode ver que os dados do disco partilhado são reproduzidos sincronizadamente a partir da unidade de volume de origem S no nó de cluster A para a unidade de volume-alvo S no nó de cluster B. Por exemplo, é replicado de **pr1-ascs-0 [10.0.0.40]** para **pr1-ascs-1 [10.0.0.41]**.

![Figura 62: No SIOS DataKeeper, replicar o volume local do nó de cluster A ao nó de cluster B][sap-ha-guide-figure-5001]

_**Figura 62:** No SIOS DataKeeper, replicar o volume local do nó de cluster A para o nó de cluster B_

### <a name="failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Falha do nó A para o nó B

1. Escolha uma destas opções para iniciar uma falha do grupo de agrupamento SAP <*SID*> do nó de cluster A ao nó de cluster B:
   - Use o Gestor de Cluster Failover  
   - Utilizar a falha no cluster PowerShell

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Reinicie o nó de cluster A dentro do sistema operativo do hóspede do Windows (isto inicia uma falha automática do grupo de cluster *<SID*> <SID do nó A ao nó B).  
3. Reinicie o nó de cluster A do portal Azure (isto inicia uma falha automática do grupo de agrupamento SAP <*SID*> do nó A ao nó B).  
4. Reinicie o nó de cluster A utilizando o Azure PowerShell (isto inicia uma falha automática do grupo de agrupamento SAP <*SID*> do nó A ao nó B).

   Após o failover, o grupo de agrupamento <SID> <*DOE* está a funcionar no nó de cluster B. Por exemplo, está a funcionar em **pr1-ascs-1**.

   ![Figura 63: Em Failover Cluster Manager, o grupo de cluster> SAP <SID está a funcionar no nó de cluster B][sap-ha-guide-figure-5002]

   _**Figura 63**: Em Failover Cluster Manager, o grupo de cluster SAP <*SID*> está a funcionar no nó de cluster B_

   O disco partilhado está agora montado no nó de cluster B. SiOS DataKeeper está a replicar dados da unidade de volume de origem S no nó de cluster B para a unidade de volume S no nó de cluster A. Por exemplo, está a **replicar-se de pr1-ascs-1 [10.0.0.41]** para **pr1-ascs-0 [10.0.0.40]**.

   ![Figura 64: SiOS DataKeeper replica o volume local do nó de cluster B ao nó de cluster A][sap-ha-guide-figure-5003]

   _**Figura 64:** SiOS DataKeeper replica o volume local do nó de cluster B para o nó de cluster A_
