---
title: Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver | Microsoft Docs
description: Guia de alta disponibilidade para SAP NetWeaver em máquinas virtuais do Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0fbff3679004b8278b7634c2dc21253973cf34d0
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647667"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver"></a>Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver

[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2243692]: https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Configuração de alta disponibilidade do SAP multi-SID)


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



As máquinas virtuais do Azure são a solução para organizações que precisam de recursos de computação, armazenamento e rede, em tempo mínimo, e sem ciclos de aquisição longos. Você pode usar as máquinas virtuais do Azure para implantar aplicativos clássicos, como ABAP com base no SAP NetWeaver, Java e uma pilha ABAP + Java. Estenda a confiabilidade e a disponibilidade sem recursos locais adicionais. As máquinas virtuais do Azure dão suporte à conectividade entre locais, para que você possa integrar as máquinas virtuais do Azure nos domínios locais da sua organização, nuvens privadas e o cenário do sistema SAP.

Neste artigo, abordaremos as etapas que você pode executar para implantar sistemas SAP de alta disponibilidade no Azure usando o modelo de implantação Azure Resource Manager. Nós o orientaremos pelas principais tarefas:

* Encontre as notas e os guias de instalação corretos do SAP, listados na seção [recursos][sap-ha-guide-2] . Este artigo complementa a documentação de instalação do SAP e as notas SAP, que são os principais recursos que podem ajudá-lo a instalar e implantar o software SAP em plataformas específicas.
* Conheça as diferenças entre o modelo de implantação Azure Resource Manager e o modelo de implantação clássico do Azure.
* Saiba mais sobre os modos de quorum do Windows Server failover clustering, para que você possa selecionar o modelo certo para sua implantação do Azure.
* Saiba mais sobre o armazenamento compartilhado do Windows Server failover clustering nos serviços do Azure.
* Saiba como ajudar a proteger componentes de ponto único de falha, como ABAP (programação de aplicativos de negócios avançado), ASCS (serviços centrais do/SCS) e sistemas de gerenciamento de bancos de dados (DBMS) e componentes redundantes, como o servidor de aplicativos SAP, no Azure.
* Siga um exemplo passo a passo de uma instalação e configuração de um sistema SAP de alta disponibilidade em um cluster de clustering de failover do Windows Server no Azure usando Azure Resource Manager.
* Saiba mais sobre as etapas adicionais necessárias para usar o clustering de failover do Windows Server no Azure, mas que não são necessárias em uma implantação local.

Para simplificar a implantação e a configuração, neste artigo, usamos os modelos do Resource Manager de alta disponibilidade de três camadas SAP. Os modelos automatizam a implantação de toda a infraestrutura necessária para um sistema SAP de alta disponibilidade. A infraestrutura também dá suporte ao dimensionamento de SAPS (SAP Application Performance Standard) do seu sistema SAP.

## <a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Pré-requisitos
Antes de começar, verifique se você atende aos pré-requisitos descritos nas seções a seguir. Além disso, certifique-se de verificar todos os recursos listados na seção [recursos][sap-ha-guide-2] .

Neste artigo, usamos modelos de Azure Resource Manager para [SAP NetWeaver de três camadas usando Managed disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md/). Para obter uma visão geral útil dos modelos, consulte [modelos do SAP Azure Resource Manager](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).

## <a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a>Os
Estes artigos abordam as implantações do SAP no Azure:

* [Planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP NetWeaver][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP NetWeaver][dbms-guide]
* [Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver (este guia)][sap-ha-guide]

> [!NOTE]
> Sempre que possível, fornecemos um link para o guia de instalação do SAP de referência (consulte os [guias de instalação do SAP][sap-installation-guides]). Para obter os pré-requisitos e informações sobre o processo de instalação, é uma boa ideia ler os guias de instalação do SAP NetWeaver com cuidado. Este artigo aborda apenas tarefas específicas para sistemas baseados no SAP NetWeaver que você pode usar com as máquinas virtuais do Azure.
>
>

Essas notas SAP estão relacionadas ao tópico do SAP no Azure:

| Número da nota | Título |
| --- | --- |
| [1928533] |Aplicativos SAP no Azure: produtos e dimensionamento com suporte |
| [2015553] |SAP em Microsoft Azure: pré-requisitos de suporte |
| [1999351] |Monitoramento do Azure aprimorado para SAP |
| [2178632] |Principais métricas de monitoramento para SAP em Microsoft Azure |
| [1999351] |Virtualização no Windows: monitoramento avançado |
| [2243692] |Uso do armazenamento de SSD Premium do Azure para instância do SAP DBMS |

Saiba mais sobre as [limitações das assinaturas do Azure][azure-resource-manager/management/azure-subscription-service-limits-subscription], incluindo limitações padrão gerais e limites máximos.

## <a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>SAP de alta disponibilidade com Azure Resource Manager versus o modelo de implantação clássico do Azure
Os modelos de implantação do Azure Resource Manager e clássico do Azure são diferentes nas seguintes áreas:

- Grupos de recursos
- Dependência do balanceador de carga interno do Azure no grupo de recursos do Azure
- Suporte para cenários de vários SID do SAP

### <a name="f76af273-1993-4d83-b12d-65deeae23686"></a>Grupos de recursos
No Azure Resource Manager, você pode usar grupos de recursos para gerenciar todos os recursos do aplicativo em sua assinatura do Azure. Uma abordagem integrada, em um grupo de recursos, que todos os recursos têm o mesmo ciclo de vida. Por exemplo, todos os recursos são criados ao mesmo tempo e são excluídos ao mesmo tempo. Saiba mais sobre [grupos de recursos](../../../azure-resource-manager/management/overview.md#resource-groups).

### <a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a>Dependência do balanceador de carga interno do Azure no grupo de recursos do Azure

No modelo de implantação clássico do Azure, há uma dependência entre o Azure Load balancer interno (Azure Load Balancer serviço) e o serviço de nuvem. Cada balanceador de carga interno precisa de um serviço de nuvem.

Em Azure Resource Manager, todos os recursos do Azure precisam ser colocados em um grupo de recursos do Azure e isso é válido para Azure Load Balancer também. No entanto, não é necessário ter um grupo de recursos do Azure por balanceador de carga do Azure, por exemplo, um grupo de recursos do Azure pode conter vários balanceadores de carga do Azure. O ambiente é mais simples e mais flexível. 

### <a name="support-for-sap-multi-sid-scenarios"></a>Suporte para cenários de vários SID do SAP

No Azure Resource Manager, você pode instalar várias instâncias ASCS/SCS do SID (identificador do sistema SAP) em um cluster. Instâncias de vários SIDs são possíveis devido ao suporte para vários endereços IP para cada balanceador de carga interno do Azure.

Para usar o modelo de implantação clássico do Azure, siga os procedimentos descritos no [SAP NetWeaver no Azure: clustering de instâncias do SAP ASCS/SCS usando o clustering de failover do Windows Server no Azure com o sios Datakeeper](https://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> É altamente recomendável que você use o modelo de implantação Azure Resource Manager para suas instalações do SAP. Ele oferece muitos benefícios que não estão disponíveis no modelo de implantação clássico. Saiba mais sobre os [modelos de implementação][virtual-machines-azure-resource-manager-architecture-benefits-arm] do Azure.   
>
>

## <a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a>Clustering de failover do Windows Server
O Windows Server failover clustering é a base de uma instalação do SAP ASCS/SCS de alta disponibilidade e DBMS no Windows.

Um cluster de failover é um grupo de 1 + n servidores independentes (nós) que funcionam em conjunto para aumentar a disponibilidade de aplicativos e serviços. Se ocorrer uma falha de nó, o Windows Server failover clustering calculará o número de falhas que podem ocorrer enquanto mantém um cluster íntegro para fornecer aplicativos e serviços. Você pode escolher entre modos de quorum diferentes para obter o clustering de failover.

### <a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a>Modos de quorum
Você pode escolher entre quatro modos de quorum ao usar o clustering de failover do Windows Server:

* **Maioria de nós**. Cada nó do cluster pode votar. O cluster funciona apenas com a maioria dos votos, ou seja, com mais da metade dos votos. Recomendamos essa opção para clusters que têm um número desigual de nós. Por exemplo, três nós em um cluster de sete nós podem falhar e o cluster ainda atinge a maioria e continua a ser executado.  
* **Maioria de nós e discos**. Cada nó e um disco designado (uma testemunha de disco) no armazenamento de cluster podem votar quando estão disponíveis e em comunicação. O cluster funciona apenas com a maioria dos votos, ou seja, com mais da metade dos votos. Esse modo faz sentido em um ambiente de cluster com um número par de nós. Se metade dos nós e o disco estiverem online, o cluster permanecerá em um estado íntegro.
* **Maioria dos nós e compartilhamento de arquivos**. Cada nó, mais um compartilhamento de arquivo designado (uma testemunha de compartilhamento de arquivos) que o administrador cria pode votar, independentemente de os nós e o compartilhamento de arquivos estarem disponíveis e em comunicação. O cluster funciona apenas com a maioria dos votos, ou seja, com mais da metade dos votos. Esse modo faz sentido em um ambiente de cluster com um número par de nós. Ele é semelhante ao modo maioria dos nós e discos, mas usa um compartilhamento de arquivos testemunha em vez de um disco testemunha. Esse modo é fácil de implementar, mas se o compartilhamento de arquivos em si não estiver altamente disponível, ele poderá se tornar um ponto único de falha.
* **Sem maioria: somente disco**. O cluster terá um quorum se um nó estiver disponível e em comunicação com um disco específico no armazenamento de cluster. Somente os nós que também estão em comunicação com esse disco podem ingressar no cluster. Recomendamos que você não use esse modo.
 

## <a name="fdfee875-6e66-483a-a343-14bbaee33275"></a>Clustering de failover do Windows Server local
A Figura 1 mostra um cluster de dois nós. Se a conexão de rede entre os nós falhar e ambos os nós permanecerem funcionando, um disco de quorum ou compartilhamento de arquivos determinará qual nó continuará a fornecer os aplicativos e serviços do cluster. O nó que tem acesso ao disco de quorum ou compartilhamento de arquivos é o nó que garante que os serviços continuem.

Como este exemplo usa um cluster de dois nós, usamos o modo de quorum maioria dos nós e compartilhamentos de arquivos. A maioria dos nós e discos também é uma opção válida. Em um ambiente de produção, recomendamos que você use um disco de quorum. Você pode usar a tecnologia de rede e de sistema de armazenamento para torná-la altamente disponível.

![Figura 1: exemplo de uma configuração de clustering de failover do Windows Server para SAP ASCS/SCS no Azure][sap-ha-guide-figure-1000]

_**Figura 1:** Exemplo de uma configuração de clustering de failover do Windows Server para SAP ASCS/SCS no Azure_

### <a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a>Armazenamento compartilhado
A Figura 1 também mostra um cluster de armazenamento compartilhado de dois nós. Em um cluster de armazenamento compartilhado local, todos os nós no cluster detectam o armazenamento compartilhado. Um mecanismo de bloqueio protege os dados contra corrupção. Todos os nós podem detectar se outro nó falha. Se um nó falhar, o nó restante assumirá a propriedade dos recursos de armazenamento e garantirá a disponibilidade dos serviços.

> [!NOTE]
> Você não precisa de discos compartilhados para alta disponibilidade com alguns aplicativos DBMS, como com SQL Server. SQL Server Always On replica os dados do DBMS e os arquivos de log do disco local de um nó do cluster para o disco local de outro nó do cluster. Nesse caso, a configuração de cluster do Windows não precisa de um disco compartilhado.
>
>

### <a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a>Resolução de nomes e rede
Os computadores cliente atingem o cluster em um endereço IP virtual e um nome de host virtual que o servidor DNS fornece. Os nós locais e o servidor DNS podem lidar com vários endereços IP.

Em uma configuração típica, você usa duas ou mais conexões de rede:

* Uma conexão dedicada ao armazenamento
* Uma conexão de rede interna de cluster para a pulsação
* Uma rede pública que os clientes usam para se conectar ao cluster

## <a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a>Clustering de failover do Windows Server no Azure
Em comparação com as implantações bare-metal ou nuvem privada, as máquinas virtuais do Azure exigem etapas adicionais para configurar o clustering de failover do Windows Server. Ao criar um disco de cluster compartilhado, você precisa definir vários endereços IP e nomes de host virtual para a instância do SAP ASCS/SCS.

Neste artigo, discutiremos os principais conceitos e as etapas adicionais necessárias para criar um cluster de serviços centrais de alta disponibilidade do SAP no Azure. Mostramos como configurar a ferramenta de terceiros SIOS datakeeper e como configurar o balanceador de carga interno do Azure. Você pode usar essas ferramentas para criar um cluster de failover do Windows com uma testemunha de compartilhamento de arquivos no Azure.

![Figura 2: configuração do Windows Server failover clustering no Azure sem um disco compartilhado][sap-ha-guide-figure-1001]

_**Figura 2:** Configuração do Windows Server failover clustering no Azure sem um disco compartilhado_

### <a name="1a464091-922b-48d7-9d08-7cecf757f341"></a>Disco compartilhado no Azure com SIOS datakeeper
Você precisa de armazenamento compartilhado de cluster para uma instância do SAP ASCS/SCS de alta disponibilidade. A partir de setembro de 2016, o Azure não oferece armazenamento compartilhado que você pode usar para criar um cluster de armazenamento compartilhado. Você pode usar o software de terceiros SIOS datakeeper Cluster Edition para criar um armazenamento espelhado que simula o armazenamento compartilhado do cluster. A solução SIOS fornece replicação de dados síncrona em tempo real. É assim que você pode criar um recurso de disco compartilhado para um cluster:

1. Anexe um disco adicional a cada uma das máquinas virtuais (VMs) em uma configuração de cluster do Windows.
2. Execute o SIOS datakeeper Cluster Edition em ambos os nós da máquina virtual.
3. Configure o SIOS datakeeper Cluster Edition para que ele espelhe o conteúdo do volume anexado de disco adicional da máquina virtual de origem para o volume de disco anexado adicional da máquina virtual de destino. O SIOS datakeeper abstrai os volumes locais de origem e de destino e os apresenta ao clustering de failover do Windows Server como um disco compartilhado.

Obtenha mais informações sobre o [sios Datakeeper](https://us.sios.com/products/datakeeper-cluster/).

![Figura 3: configuração do Windows Server failover clustering no Azure com o SIOS datakeeper][sap-ha-guide-figure-1002]

_**Figura 3:** Configuração do Windows Server failover clustering no Azure com SIOS datakeeper_

> [!NOTE]
> Você não precisa de discos compartilhados para alta disponibilidade com alguns produtos DBMS, como SQL Server. SQL Server Always On replica os dados do DBMS e os arquivos de log do disco local de um nó do cluster para o disco local de outro nó do cluster. Nesse caso, a configuração de cluster do Windows não precisa de um disco compartilhado.
>
>

### <a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a>Resolução de nomes no Azure
A plataforma de nuvem do Azure não oferece a opção de configurar endereços IP virtuais, como endereços IP flutuantes. Você precisa de uma solução alternativa para configurar um endereço IP virtual para alcançar o recurso de cluster na nuvem.
O Azure tem um balanceador de carga interno no serviço de Azure Load Balancer. Com o balanceador de carga interno, os clientes acessam o cluster pelo endereço IP virtual do cluster.
Você precisa implantar o balanceador de carga interno no grupo de recursos que contém os nós de cluster. Em seguida, configure todas as regras de encaminhamento de porta necessárias com as portas de investigação do balanceador de carga interno.
Os clientes podem se conectar por meio do nome de host virtual. O servidor DNS resolve o endereço IP do cluster, e o balanceador de carga interno manipula o encaminhamento de porta para o nó ativo do cluster.

## <a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a>Alta disponibilidade do SAP NetWeaver na infraestrutura como serviço (IaaS) do Azure
Para obter alta disponibilidade de aplicativos SAP, como para componentes de software SAP, você precisa proteger os seguintes componentes:

* Instância do servidor de aplicativos SAP
* Instância do SAP ASCS/SCS
* Servidor DBMS

Para obter mais informações sobre como proteger os componentes do SAP em cenários de alta disponibilidade, consulte [planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver][planning-guide-11].

### <a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a>Servidor de aplicativos SAP de alta disponibilidade
Normalmente, você não precisa de uma solução específica de alta disponibilidade para o servidor de aplicativos SAP e as instâncias de caixa de diálogo. Você obtém alta disponibilidade por redundância e configurará várias instâncias de caixa de diálogo em diferentes instâncias de máquinas virtuais do Azure. Você deve ter pelo menos duas instâncias do aplicativo SAP instaladas em duas instâncias de máquinas virtuais do Azure.

![Figura 4: servidor de aplicativos SAP de alta disponibilidade][sap-ha-guide-figure-2000]

_**Figura 4:** Servidor de aplicativos SAP de alta disponibilidade_

Você deve posicionar todas as máquinas virtuais que hospedam instâncias do servidor de aplicativos SAP no mesmo conjunto de disponibilidade do Azure. Um conjunto de disponibilidade do Azure garante que:

* Todas as máquinas virtuais fazem parte do mesmo domínio de atualização. Um domínio de atualização, por exemplo, garante que as máquinas virtuais não sejam atualizadas ao mesmo tempo durante o período de inatividade da manutenção planejada.
* Todas as máquinas virtuais fazem parte do mesmo domínio de falha. Um domínio de falha, por exemplo, garante que as máquinas virtuais sejam implantadas para que nenhum ponto único de falha afete a disponibilidade de todas as máquinas virtuais.

Saiba mais sobre como [gerenciar a disponibilidade de máquinas virtuais][virtual-machines-manage-availability].

Somente disco não gerenciado: como a conta de armazenamento do Azure é um possível ponto único de falha, é importante ter pelo menos duas contas de armazenamento do Azure, nas quais pelo menos duas máquinas virtuais sejam distribuídas. Em uma configuração ideal, os discos de cada máquina virtual que está executando uma instância da caixa de diálogo do SAP seriam implantados em uma conta de armazenamento diferente.

### <a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a>Instância do SAP ASCS/SCS de alta disponibilidade
A Figura 5 é um exemplo de uma instância do SAP ASCS/SCS de alta disponibilidade.

![Figura 5: instância do SAP ASCS/SCS de alta disponibilidade][sap-ha-guide-figure-2001]

_**Figura 5:** Instância do SAP ASCS/SCS de alta disponibilidade_

#### <a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a>Alta disponibilidade da instância do SAP ASCS/SCS com o clustering de failover do Windows Server no Azure
Em comparação com as implantações bare-metal ou nuvem privada, as máquinas virtuais do Azure exigem etapas adicionais para configurar o clustering de failover do Windows Server. Para criar um cluster de failover do Windows, você precisa de um disco de cluster compartilhado, vários endereços IP, vários nomes de host virtual e um balanceador de carga interno do Azure para clustering de uma instância do SAP ASCS/SCS. Discutiremos isso em mais detalhes posteriormente neste artigo.

![Figura 6: clustering de failover do Windows Server para uma configuração do SAP ASCS/SCS no Azure usando o SIOS datakeeper][sap-ha-guide-figure-1002]

_**Figura 6:** Windows Server failover clustering para uma configuração do SAP ASCS/SCS no Azure com o SIOS datakeeper_

### <a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>Instância de DBMS de alta disponibilidade
O DBMS também é um ponto único de contato em um sistema SAP. Você precisa protegê-lo usando uma solução de alta disponibilidade. A Figura 7 mostra uma solução SQL Server Always On de alta disponibilidade no Azure, com o clustering de failover do Windows Server e o balanceador de carga interno do Azure. SQL Server Always On replica os dados do DBMS e os arquivos de log usando sua própria replicação de DBMS. Nesse caso, você não precisa de discos compartilhados de cluster, o que simplifica toda a configuração.

![Figura 7: exemplo de um DBMS SAP de alta disponibilidade, com SQL Server Always On][sap-ha-guide-figure-2003]

_**Figura 7:** Exemplo de um DBMS SAP de alta disponibilidade, com SQL Server Always On_

Para obter mais informações sobre o clustering SQL Server no Azure usando o modelo de implantação Azure Resource Manager, consulte estes artigos:

* [Configurar Always On grupo de disponibilidade em máquinas virtuais do Azure manualmente usando o Resource Manager] [Virtual-Machines-Windows-portal-SQL-AlwaysOn-Availability-groups-manual]
* [Configurar um balanceador de carga interno do Azure para um grupo de disponibilidade Always On no Azure] [Virtual-Machines-Windows-portal-SQL-AlwaysOn-int-Listener]

## <a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a>Cenários de implantação de alta disponibilidade de ponta a ponta

### <a name="deployment-scenario-using-architectural-template-1"></a>Cenário de implantação usando o modelo de arquitetura 1

A Figura 8 mostra um exemplo de uma arquitetura de alta disponibilidade do SAP NetWeaver no Azure para **um** sistema SAP. Esse cenário é configurado da seguinte maneira:

- Um cluster dedicado é usado para a instância do SAP ASCS/SCS.
- Um cluster dedicado é usado para a instância do DBMS.
- As instâncias do servidor de aplicativos SAP são implantadas em suas próprias VMs dedicadas.

![Figura 8: modelo de arquitetura de alta disponibilidade do SAP 1, com cluster dedicado para ASCS/SCS e DBMS][sap-ha-guide-figure-2004]

_**Figura 8:** Modelo de arquitetura 1 de alta disponibilidade do SAP, clusters dedicados para ASCS/SCS e DBMS_

### <a name="deployment-scenario-using-architectural-template-2"></a>Cenário de implantação usando o modelo de arquitetura 2

A Figura 9 mostra um exemplo de uma arquitetura de alta disponibilidade do SAP NetWeaver no Azure para **um** sistema SAP. Esse cenário é configurado da seguinte maneira:

- Um cluster dedicado é **usado para a instância do SAP** ASCS/SCS e para o DBMS.
- As instâncias do servidor de aplicativos SAP são implantadas nas próprias VMs dedicadas.

![Figura 9: modelo de arquitetura 2 de alta disponibilidade do SAP, com um cluster dedicado para ASCS/SCS e um cluster dedicado para DBMS][sap-ha-guide-figure-2005]

_**Figura 9:** Modelo de arquitetura 2 de alta disponibilidade do SAP, com um cluster dedicado para ASCS/SCS e um cluster dedicado para DBMS_

### <a name="deployment-scenario-using-architectural-template-3"></a>Cenário de implantação usando o modelo de arquitetura 3

A Figura 10 mostra um exemplo de uma arquitetura de alta disponibilidade do SAP NetWeaver no Azure para **dois** sistemas SAP, com &lt;SID1&gt; e &lt;&gt;de SID2. Esse cenário é configurado da seguinte maneira:

- Um cluster dedicado é **usado para a instância SID1 do SAP** ASCS/SCS *e* para a instância de SID2 do SAP ASCS/SCS (um cluster).
- Um cluster dedicado é usado para SID1 DBMS e outro cluster dedicado é usado para DBMS SID2 (dois clusters).
- As instâncias do servidor de aplicativos SAP para o sistema SAP SID1 têm suas próprias VMs dedicadas.
- As instâncias do servidor de aplicativos SAP para o sistema SAP SID2 têm suas próprias VMs dedicadas.

![Figura 10: modelo de arquitetura de alta disponibilidade do SAP 3, com um cluster dedicado para instâncias diferentes do ASCS/SCS][sap-ha-guide-figure-6003]

_**Figura 10:** Modelo de arquitetura de alta disponibilidade do SAP 3, com um cluster dedicado para instâncias diferentes do ASCS/SCS_

## <a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a>Preparar a infraestrutura

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Preparar a infraestrutura para o modelo de arquitetura 1
Azure Resource Manager modelos para SAP ajudam a simplificar a implantação de recursos necessários.

Os modelos de três camadas no Azure Resource Manager também oferecem suporte a cenários de alta disponibilidade, como no modelo de arquitetura 1, que tem dois clusters. Cada cluster é um ponto único de falha do SAP para SAP ASCS/SCS e DBMS.

Aqui está onde você pode obter Azure Resource Manager modelos para o cenário de exemplo que descrevemos neste artigo:

* [Imagem do Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Imagem do Azure Marketplace usando Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Imagem personalizada usando Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Para preparar a infraestrutura para o modelo de arquitetura 1:

- Na portal do Azure, na folha **parâmetros** , na caixa **SYSTEMAVAILABILITY** , selecione **ha**.

  ![Figura 11: definir parâmetros de Azure Resource Manager de alta disponibilidade do SAP][sap-ha-guide-figure-3000]

_**Figura 11:** Definir parâmetros de Azure Resource Manager de alta disponibilidade do SAP_


  Os modelos criam:

  * **Máquinas virtuais**:
    * Máquinas virtuais do servidor de aplicativos SAP: <*SAPSystemSID*>-di-<*número*>
    * Máquinas virtuais de cluster ASCS/SCS: <*SAPSystemSID*>-ASCS-*número* de <>
    * Cluster DBMS: <*SAPSystemSID*>-db-<*número*>

  * **Placas de rede para todas as máquinas virtuais, com endereços IP associados**:
    * <*SAPSystemSID*>-nic-di-<*Number*>
    * <*SAPSystemSID*>-nic-ascs-<*Number*>
    * <*SAPSystemSID*>-nic-db-<*Number*>

  * **Contas de armazenamento do Azure (somente discos não gerenciados)**

  * **Grupos de disponibilidade** para:
    * Máquinas virtuais do servidor de aplicativos SAP: <*SAPSystemSID*>-avset-di
    * Máquinas virtuais de cluster do SAP ASCS/SCS: <*SAPSystemSID*>-avset-ASCS
    * Máquinas virtuais do cluster DBMS: <*SAPSystemSID*>-avset-DB

  * **Balanceador de carga interno do Azure**:
    * Com todas as portas para a instância do ASCS/SCS e o endereço IP <*SAPSystemSID*>-lb-ASCS
    * Com todas as portas para o SQL Server DBMS e o endereço IP <*SAPSystemSID*>-lb-DB

  * **Grupo de segurança de rede**: <*SAPSystemSID*>-NSG-ASCs-0  
    * Com uma porta de RDP (Open external protocolo RDP) para a máquina virtual <*SAPSystemSID*>-ASCs-0

> [!NOTE]
> Todos os endereços IP das placas de rede e dos balanceadores de carga internos do Azure são **dinâmicos** por padrão. Altere-os para endereços IP **estáticos** . Descreveremos como fazer isso posteriormente neste artigo.
>
>

### <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Implantar máquinas virtuais com conectividade de rede corporativa (entre locais) para usar na produção
Para sistemas SAP de produção, implante máquinas virtuais do Azure com [conectividade de rede corporativa (entre instalações)][planning-guide-2.2] usando a VPN site a site do Azure ou o Azure ExpressRoute.

> [!NOTE]
> Você pode usar sua instância de rede virtual do Azure. A rede virtual e a sub-rede já foram criadas e preparadas.
>
>

1. Na portal do Azure, na folha **parâmetros** , na caixa **NEWOREXISTINGSUBNET** , selecione **existente**.
2. Na caixa **sub-rede** , adicione a cadeia de caracteres completa de sua sub-rede do Azure preparada na qual você planeja implantar suas máquinas virtuais do Azure.
3. Para obter uma lista de todas as sub-redes de rede do Azure, execute este comando do PowerShell:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   O campo **ID** mostra a **subnetid**.
4. Para obter uma lista de todos os valores de **subnetid** , execute este comando do PowerShell:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   A **subnetid** tem esta aparência:

   ```
   /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
   ```

### <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Implantar instâncias SAP somente na nuvem para teste e demonstração
Você pode implantar seu sistema SAP de alta disponibilidade em um modelo de implantação somente em nuvem. Esse tipo de implantação é principalmente útil para casos de uso de demonstração e teste. Não é adequado para casos de uso de produção.

- Na portal do Azure, na folha **parâmetros** , na caixa **NEWOREXISTINGSUBNET** , selecione **novo**. Deixe o campo **subnetid** vazio.

  O modelo SAP Azure Resource Manager cria automaticamente a rede virtual e a sub-rede do Azure.

> [!NOTE]
> Você também precisa implantar pelo menos uma máquina virtual dedicada para Active Directory e DNS na mesma instância de rede virtual do Azure. O modelo não cria essas máquinas virtuais.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Preparar a infraestrutura para o modelo arquitetônico 2

Você pode usar este modelo de Azure Resource Manager para SAP para ajudar a simplificar a implantação de recursos de infraestrutura necessários para o modelo 2 de arquitetura do SAP.

Aqui está onde você pode obter Azure Resource Manager modelos para este cenário de implantação:

* [Imagem do Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Imagem do Azure Marketplace usando Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Imagem personalizada usando Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Preparar a infraestrutura para o modelo arquitetônico 3

Você pode preparar a infraestrutura e configurar o SAP para **vários Sid**. Por exemplo, você pode adicionar uma instância do SAP ASCS/SCS adicional a uma configuração de cluster *existente* . Para obter mais informações, consulte [Configurar uma instância adicional do SAP ASCS/SCS em uma configuração de cluster existente para criar uma configuração de vários SID do SAP no Azure Resource Manager][sap-ha-multi-sid-guide].

Se você quiser criar um novo cluster de vários SID, poderá usar os [modelos de início rápido](https://github.com/Azure/azure-quickstart-templates)de vários Sid no github.
Para criar um novo cluster de vários SID, você precisa implantar os três modelos a seguir:

* [Modelo ASCS/SCS](#ASCS-SCS-template)
* [Modelo de banco de dados](#database-template)
* [Modelo de servidores de aplicativos](#application-servers-template)

As seções a seguir têm mais detalhes sobre os modelos e os parâmetros que você precisa fornecer nos modelos.

#### <a name="ASCS-SCS-template"></a>Modelo ASCS/SCS

O modelo ASCS/SCS implanta duas máquinas virtuais que você pode usar para criar um cluster de failover do Windows Server que hospeda várias instâncias ASCS/SCS.

Para configurar o modelo de vários SID do ASCS/SCS, no modelo de vários SID do [ASCS/SCS][sap-templates-3-tier-multisid-xscs-marketplace-image] ou no [modelo de vários SID do ASCS/SCS usando Managed disks][sap-templates-3-tier-multisid-xscs-marketplace-image-md], insira valores para os seguintes parâmetros:

  - **Prefixo de recurso**.  Defina o prefixo do recurso, que é usado para prefixar todos os recursos que são criados durante a implantação. Como os recursos não pertencem a apenas um sistema SAP, o prefixo do recurso não é o SID de um sistema SAP.  O prefixo deve ter entre **três e seis caracteres**.
  - **Tipo de pilha**. Selecione o tipo de pilha do sistema SAP. Dependendo do tipo de pilha, Azure Load Balancer tem apenas um (ABAP ou Java) ou dois (ABAP + Java) endereços IP privados por sistema SAP.
  -  **Tipo de so**. Selecione o sistema operacional das máquinas virtuais.
  -  **Contagem de sistema SAP**. Selecione o número de sistemas SAP que você deseja instalar neste cluster.
  -  **Disponibilidade do sistema**. Selecione **ha**.
  -  **Nome de usuário administrador e senha de administrador**. Crie um novo usuário que possa ser usado para entrar no computador.
  -  **Sub-rede nova ou existente**. Defina se uma nova rede virtual e sub-rede devem ser criadas ou se uma sub-rede existente deve ser usada. Se você já tiver uma rede virtual conectada à sua rede local, selecione **existente**.
  -  **ID da sub-rede**. Se você deseja implantar a VM em uma VNet existente em que você tem uma sub-rede definida, a VM deve ser atribuída, nomear a ID dessa sub-rede específica. A ID geralmente tem esta aparência:/subscriptions/<*ID da assinatura*>/resourceGroups/<*nome do grupo de recursos*>/Providers/Microsoft.Network/virtualNetworks/< nome da*rede virtual*>/Subnets/<*nome da sub-rede*>

O modelo implanta um Azure Load Balancer instância, que dá suporte a vários sistemas SAP.

- As instâncias ASCS são configuradas para o número de instância 00, 10, 20...
- As instâncias do SCS são configuradas para o número de instância 01, 11, 21...
- As instâncias do ASCS enqueue Replication Server (ERS) (somente Linux) são configuradas para o número de instância 02, 12, 22...
- As instâncias do SCS ERS (somente Linux) são configuradas para o número de instância 03, 13, 23...

O balanceador de carga contém 1 (2 para Linux) VIP (s), 1x VIP para ASCS/SCS e 1x VIP para ERS (somente Linux).

A lista a seguir contém todas as regras de balanceamento de carga (em que x é o número do sistema SAP, por exemplo, 1, 2, 3...):
- Portas específicas do Windows para cada sistema SAP: 445, 5985
- Portas ASCS (número de instância x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- Portas SCS (número de instância X1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- Portas ASCS ERS no Linux (número de instância x2): 33X2, 5x213, 5x214, 5x216
- Portas SCS ERS no Linux (número da instância X3): 33x3, 5x313, 5x314, 5x316

O balanceador de carga está configurado para usar as seguintes portas de investigação (em que x é o número do sistema SAP, por exemplo, 1, 2, 3...):
- Porta de investigação do balanceador de carga interno do ASCS/SCS: 620x0
- Porta de investigação do balanceador de carga interno do ERS (somente Linux): 621x2

#### <a name="database-template"></a>Modelo de banco de dados

O modelo de banco de dados implanta uma ou duas máquinas virtuais que você pode usar para instalar o RDBMS (sistema de gerenciamento de banco de dados relacional) para um sistema SAP. Por exemplo, se você implantar um modelo ASCS/SCS para cinco sistemas SAP, será necessário implantar esse modelo cinco vezes.

Para configurar o modelo de vários Sid de banco de dados, no modelo de vários SID [de banco de dados ou][sap-templates-3-tier-multisid-db-marketplace-image] [modelo de vários Sid][sap-templates-3-tier-multisid-db-marketplace-image-md]de banco de dados usando Managed disks, insira valores para os seguintes parâmetros:

- **ID do sistema SAP**. Insira a ID do sistema SAP do sistema SAP que você deseja instalar. A ID será usada como um prefixo para os recursos que são implantados.
- **Tipo de so**. Selecione o sistema operacional das máquinas virtuais.
- **DbType**. Selecione o tipo de banco de dados que você deseja instalar no cluster. Selecione **SQL** se você deseja instalar Microsoft SQL Server. Selecione **Hana** se você planeja instalar SAP Hana nas máquinas virtuais. Certifique-se de selecionar o tipo de sistema operacional correto: selecione **Windows** para SQL e selecione uma distribuição do Linux para Hana. O Azure Load Balancer que está conectado às máquinas virtuais será configurado para dar suporte ao tipo de banco de dados selecionado:
  * **SQL**. O balanceador de carga balanceará a carga da porta 1433. Certifique-se de usar essa porta para a instalação do SQL Server Always On.
  * **HANA**. O balanceador de carga balanceará a carga das portas 35015 e 35017. Certifique-se de instalar SAP HANA com o número de instância **50**.
  O balanceador de carga usará a porta de investigação 62550.
- **Tamanho do sistema SAP**. Defina o número de SAPS que o novo sistema fornecerá. Se você não tiver certeza de quantos SAPS o sistema precisará, pergunte ao seu parceiro de tecnologia SAP ou ao integrador de sistemas.
- **Disponibilidade do sistema**. Selecione **ha**.
- **Nome de usuário administrador e senha de administrador**. Crie um novo usuário que possa ser usado para entrar no computador.
- **ID da sub-rede**. Insira a ID da sub-rede que você usou durante a implantação do modelo ASCS/SCS ou a ID da sub-rede que foi criada como parte da implantação do modelo ASCS/SCS.

#### <a name="application-servers-template"></a>Modelo de servidores de aplicativos

O modelo de servidores de aplicativos implanta duas ou mais máquinas virtuais que podem ser usadas como instâncias do servidor de aplicativos SAP para um sistema SAP. Por exemplo, se você implantar um modelo ASCS/SCS para cinco sistemas SAP, será necessário implantar esse modelo cinco vezes.

Para configurar o modelo de vários SID de servidores de aplicativos, no modelo de vários Sid de [servidores de aplicativos][sap-templates-3-tier-multisid-apps-marketplace-image] ou [no modelo de vários Sid de servidores de aplicativos usando Managed disks][sap-templates-3-tier-multisid-apps-marketplace-image-md], insira valores para os seguintes parâmetros:

  -  **ID do sistema SAP**. Insira a ID do sistema SAP do sistema SAP que você deseja instalar. A ID será usada como um prefixo para os recursos que são implantados.
  -  **Tipo de so**. Selecione o sistema operacional das máquinas virtuais.
  -  **Tamanho do sistema SAP**. O número de SAPS que o novo sistema oferecerá. Se você não tiver certeza de quantos SAPS o sistema precisará, pergunte ao seu parceiro de tecnologia SAP ou ao integrador de sistemas.
  -  **Disponibilidade do sistema**. Selecione **ha**.
  -  **Nome de usuário administrador e senha de administrador**. Crie um novo usuário que possa ser usado para entrar no computador.
  -  **ID da sub-rede**. Insira a ID da sub-rede que você usou durante a implantação do modelo ASCS/SCS ou a ID da sub-rede que foi criada como parte da implantação do modelo ASCS/SCS.


### <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Rede virtual do Azure
Em nosso exemplo, o espaço de endereço da rede virtual do Azure é 10.0.0.0/16. Há uma sub-rede chamada **subnet**, com um intervalo de endereços de 10.0.0.0/24. Todas as máquinas virtuais e balanceadores de carga internos são implantados nessa rede virtual.

> [!IMPORTANT]
> Não faça nenhuma alteração nas configurações de rede dentro do sistema operacional convidado. Isso inclui endereços IP, servidores DNS e sub-rede. Defina todas as suas configurações de rede no Azure. O serviço de protocolo DHCP propaga suas configurações.
>
>

### <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>Endereços IP de DNS

Para definir os endereços IP de DNS necessários, execute as etapas a seguir.

1. Na portal do Azure, na folha **servidores DNS** , verifique se a opção **servidores DNS** da rede virtual está definida como **DNS personalizado**.
2. Selecione suas configurações com base no tipo de rede que você tem. Para obter mais informações, consulte os seguintes recursos:
   * [Conectividade de rede corporativa (entre instalações)][planning-guide-2.2]: Adicione os endereços IP dos servidores DNS locais.  
   Você pode estender os servidores DNS locais para as máquinas virtuais que estão em execução no Azure. Nesse cenário, você pode adicionar os endereços IP das máquinas virtuais do Azure em que você executa o serviço DNS.
   * Para implantações de VM isoladas no Azure: implante uma máquina virtual adicional na mesma instância de rede virtual que serve como um servidor DNS. Adicione os endereços IP das máquinas virtuais do Azure que você configurou para executar o serviço DNS.

   ![Figura 12: configurar servidores DNS para a rede virtual do Azure][sap-ha-guide-figure-3001]

   _**Figura 12:** Configurar servidores DNS para a rede virtual do Azure_

   > [!NOTE]
   > Se você alterar os endereços IP dos servidores DNS, será necessário reiniciar as máquinas virtuais do Azure para aplicar a alteração e propagar os novos servidores DNS.
   >
   >

Em nosso exemplo, o serviço DNS é instalado e configurado nessas máquinas virtuais do Windows:

| Função de máquina virtual | Nome do host da máquina virtual | Nome da placa de rede | Endereço IP estático |
| --- | --- | --- | --- |
| Primeiro servidor DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Segundo servidor DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Nomes de host e endereços IP estáticos para a instância clusterizada do SAP ASCS/SCS e instância clusterizada do DBMS

Para a implantação local, você precisará desses nomes de host e endereços IP reservados:

| Função de nome de host virtual | Nome do host virtual | Endereço IP estático virtual |
| --- | --- | --- |
| Nome de host virtual do primeiro cluster do SAP ASCS/SCS (para gerenciamento de cluster) |pr1-ascs-vir |10.0.0.42 |
| Nome de host virtual da instância do SAP ASCS/SCS |pr1-ascs-sap |10.0.0.43 |
| Nome de host virtual do segundo cluster do SAP DBMS (gerenciamento de cluster) |pr1-dbms-vir |10.0.0.32 |

Ao criar o cluster, crie os nomes de host virtual **PR1-ASCs-vir** e **PR1-DBMS-vir** e os endereços IP associados que gerenciam o próprio cluster. Para obter informações sobre como fazer isso, consulte [coletar nós de cluster em uma configuração de cluster][sap-ha-guide-8.12.1].

Você pode criar manualmente os outros dois nomes de host virtual, **PR1-ASCs-SAP** e **PR1-DBMS-SAP**, e os endereços IP associados, no servidor DNS. A instância clusterizada do SAP ASCS/SCS e a instância do DBMS clusterizado usam esses recursos. Para obter informações sobre como fazer isso, consulte [criar um nome de host virtual para uma instância clusterizada do SAP ASCS/SCS][sap-ha-guide-9.1.1].

### <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Definir endereços IP estáticos para as máquinas virtuais SAP
Depois de implantar as máquinas virtuais para usar em seu cluster, você precisará definir endereços IP estáticos para todas as máquinas virtuais. Faça isso na configuração de rede virtual do Azure e não no sistema operacional convidado.

1. No portal do Azure, selecione **grupo de recursos** > **placa de rede** > **configurações** > **endereço IP**.
2. Na folha **endereços IP** , em **atribuição**, selecione **estático**. Na caixa **endereço IP** , digite o endereço IP que você deseja usar.

   > [!NOTE]
   > Se você alterar o endereço IP da placa de rede, será necessário reiniciar as máquinas virtuais do Azure para aplicar a alteração.  
   >
   >

   ![Figura 13: definir endereços IP estáticos para a placa de rede de cada máquina virtual][sap-ha-guide-figure-3002]

   _**Figura 13:** Definir endereços IP estáticos para a placa de rede de cada máquina virtual_

   Repita essa etapa para todas as interfaces de rede, ou seja, para todas as máquinas virtuais, incluindo máquinas virtuais que você deseja usar para o serviço de Active Directory/DNS.

Em nosso exemplo, temos essas máquinas virtuais e endereços IP estáticos:

| Função de máquina virtual | Nome do host da máquina virtual | Nome da placa de rede | Endereço IP estático |
| --- | --- | --- | --- |
| Primeira instância do servidor de aplicativos SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Segunda instância do servidor de aplicativos SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Última instância do servidor de aplicativos SAP |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Primeiro nó de cluster para a instância do ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Segundo nó de cluster para a instância do ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Primeiro nó de cluster para a instância do DBMS |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Segundo nó de cluster para a instância do DBMS |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

### <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Definir um endereço IP estático para o balanceador de carga interno do Azure

O modelo SAP Azure Resource Manager cria um balanceador de carga interno do Azure que é usado para o cluster de instância do SAP ASCS/SCS e o cluster DBMS.

> [!IMPORTANT]
> O endereço IP do nome de host virtual do SAP ASCS/SCS é o mesmo que o endereço IP do balanceador de carga interno do SAP ASCS/SCS: **PR1-lb-ASCS**.
> O endereço IP do nome virtual do DBMS é o mesmo que o endereço IP do balanceador de carga interno do DBMS: **PR1-lb-DBMS**.
>
>

Para definir um endereço IP estático para o balanceador de carga interno do Azure:

1. A implantação inicial define o endereço IP do balanceador de carga interno como **dinâmico**. Na portal do Azure, na folha **endereços IP** , em **atribuição**, selecione **estático**.
2. Defina o endereço IP do balanceador de carga interno **PR1-lb-ASCs** para o endereço IP do nome de host virtual da instância do SAP ASCS/SCS.
3. Defina o endereço IP do balanceador de carga interno **PR1-lb-DBMS** para o endereço IP do nome de host virtual da instância do DBMS.

   ![Figura 14: definir endereços IP estáticos para o balanceador de carga interno para a instância do SAP ASCS/SCS][sap-ha-guide-figure-3003]

   _**Figura 14:** Definir endereços IP estáticos para o balanceador de carga interno para a instância do SAP ASCS/SCS_

Em nosso exemplo, temos dois balanceadores de carga internos do Azure que têm esses endereços IP estáticos:

| Função de balanceador de carga interno do Azure | Nome do balanceador de carga interno do Azure | Endereço IP estático |
| --- | --- | --- |
| Balanceador de carga interno da instância do SAP ASCS/SCS |pr1-lb-ascs |10.0.0.43 |
| Balanceador de carga interno do DBMS SAP |pr1-lb-dbms |10.0.0.33 |


### <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Regras de balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure

O modelo SAP Azure Resource Manager cria as portas de que você precisa:
* Uma instância de ABAP ASCS, com o número de instância padrão **00**
* Uma instância do SCS Java, com o número de instância padrão **01**

Ao instalar sua instância do SAP ASCS/SCS, você deve usar o número de instância padrão **00** para sua instância ASCS do ABAP e o número de instância padrão **01** para sua instância do SCS Java.

Em seguida, crie pontos de extremidade de balanceamento de carga internos necessários para as portas do SAP NetWeaver.

Para criar pontos de extremidade de balanceamento de carga internos necessários, primeiro crie esses pontos de extremidade de balanceamento de carga para as portas ASCS ABAP do SAP NetWeaver:

| Nome da regra de balanceamento de carga/serviço | Números de porta padrão | Portas concretas para (instância de ASCS com número de instância 00) (ERS com 10) |
| --- | --- | --- |
| Enfileirar servidor/ *lbrule3200* |32 <*InstanceNumber*> |3200 |
| Servidor de mensagens ABAP/ *lbrule3600* |36 <*InstanceNumber*> |3600 |
| Mensagem interna de ABAP/ *lbrule3900* |39 <*InstanceNumber*> |3900 |
| HTTP do servidor de mensagens/ *Lbrule8100* |81 <*InstanceNumber*> |8100 |
| SAP Start Service ASCS HTTP/ *Lbrule50013* |5 <*InstanceNumber*> 13 |50013 |
| SAP Start Service ASCS HTTPS/ *Lbrule50014* |5 <*InstanceNumber*> 14 |50014 |
| Enfileirar replicação/ *Lbrule50016* |5 <*InstanceNumber*> 16 |50016 |
| SAP Start Service ERS HTTP *Lbrule51013* |5 <*InstanceNumber*> 13 |51013 |
| SAP Start Service ERS HTTP *Lbrule51014* |5 <*InstanceNumber*> 14 |51014 |
| *Lbrule5985* do RM do Win | |5985 |
| *Arquivos lbrule445* de compartilhamento de arquivos | |445 |

_**Tabela 1:** Números de porta das instâncias do ASCS do SAP NetWeaver ABAP_

Em seguida, crie esses pontos de extremidade de balanceamento de carga para as portas SCS Java do SAP NetWeaver:

| Nome da regra de balanceamento de carga/serviço | Números de porta padrão | Portas concretas para (instância do SCS com número de instância 01) (ERS com 11) |
| --- | --- | --- |
| Enfileirar servidor/ *lbrule3201* |32 <*InstanceNumber*> |3201 |
| Servidor de gateway/ *lbrule3301* |33 <*InstanceNumber*> |3301 |
| Servidor de mensagens Java/ *lbrule3900* |39 <*InstanceNumber*> |3901 |
| HTTP do servidor de mensagens/ *Lbrule8101* |81 <*InstanceNumber*> |8101 |
| HTTP/ *Lbrule50113* do SAP Start Service SCS |5 <*InstanceNumber*> 13 |50113 |
| SAP Start Service SCS HTTPS/ *Lbrule50114* |5 <*InstanceNumber*> 14 |50114 |
| Enfileirar replicação/ *Lbrule50116* |5 <*InstanceNumber*> 16 |50116 |
| SAP Start Service ERS HTTP *Lbrule51113* |5 <*InstanceNumber*> 13 |51113 |
| SAP Start Service ERS HTTP *Lbrule51114* |5 <*InstanceNumber*> 14 |51114 |
| *Lbrule5985* do RM do Win | |5985 |
| *Arquivos lbrule445* de compartilhamento de arquivos | |445 |

_**Tabela 2:** Números de porta das instâncias do SCS Java do SAP NetWeaver_

![Figura 15: regras de balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure][sap-ha-guide-figure-3004]

_**Figura 15:** Regras de balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure_

Defina o endereço IP do balanceador de carga **PR1-lb-DBMS** para o endereço IP do nome de host virtual da instância do DBMS.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Alterar as regras de balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure

Se você quiser usar números diferentes para as instâncias do SAP ASCS ou SCS, deverá alterar os nomes e os valores de suas portas a partir dos valores padrão.

1. No portal do Azure, selecione **<*Sid*>-lb-ASCs Load balancer** > **regras de balanceamento de carga**.
2. Para todas as regras de balanceamento de carga que pertencem à instância do SAP ASCS ou SCS, altere estes valores:

   * Nome
   * Porta
   * Porta de back-end

   Por exemplo, se você quiser alterar o número da instância ASCS padrão de 00 para 31, será necessário fazer as alterações para todas as portas listadas na tabela 1.

   Aqui está um exemplo de uma atualização para a porta *lbrule3200*.

   ![Figura 16: alterar as regras de balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure][sap-ha-guide-figure-3005]

   _**Figura 16:** Alterar as regras de balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure_

### <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Adicionar máquinas virtuais do Windows ao domínio

Depois de atribuir um endereço IP estático às máquinas virtuais, adicione as máquinas virtuais ao domínio.

![Figura 17: adicionar uma máquina virtual a um domínio][sap-ha-guide-figure-3006]

_**Figura 17:** Adicionar uma máquina virtual a um domínio_

### <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Adicionar entradas de registro em ambos os nós de cluster da instância do SAP ASCS/SCS

Azure Load Balancer tem um balanceador de carga interno que fecha as conexões quando as conexões ficam ociosas por um período de tempo definido (um tempo limite de ociosidade). Os processos de trabalho do SAP em instâncias de caixa de diálogo abrem conexões com o processo de enfileiramento da SAP assim que a primeira solicitação de enfileiramento/retirada da fila precisa ser enviada. Essas conexões geralmente permanecem estabelecidas até que o processo de trabalho ou o processo de enfileiramento seja reiniciado. No entanto, se a conexão estiver ociosa por um período de tempo definido, o balanceador de carga interno do Azure fechará as conexões. Isso não é um problema porque o processo de trabalho do SAP restabelece a conexão com o processo de enfileiramento, caso ele não exista mais. Essas atividades são documentadas nos rastreamentos de desenvolvedor de processos SAP, mas elas criam uma grande quantidade de conteúdo extra nesses rastreamentos. É uma boa ideia alterar o `KeepAliveTime` de TCP/IP e `KeepAliveInterval` em ambos os nós de cluster. Combine essas alterações nos parâmetros TCP/IP com parâmetros de perfil do SAP, descritos posteriormente neste artigo.

Para adicionar entradas de registro em ambos os nós de cluster da instância do SAP ASCS/SCS, primeiro adicione essas entradas de registro do Windows em ambos os nós de cluster do Windows para SAP ASCS/SCS:

| Caminho | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome da variável |`KeepAliveTime` |
| Tipo de variável |REG_DWORD (Decimal) |
| Valor |120000 |
| Link para a documentação |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

_**Tabela 3:** Alterar o primeiro parâmetro TCP/IP_

Em seguida, adicione essas entradas de registro do Windows em ambos os nós de cluster do Windows para SAP ASCS/SCS:

| Caminho | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome da variável |`KeepAliveInterval` |
| Tipo de variável |REG_DWORD (Decimal) |
| Valor |120000 |
| Link para a documentação |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

_**Tabela 4:** Alterar o segundo parâmetro TCP/IP_

**Para aplicar as alterações, reinicie os dois nós de cluster**.

### <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Configurar um cluster de clustering de failover do Windows Server para uma instância do SAP ASCS/SCS

A configuração de um cluster de clustering de failover do Windows Server para uma instância do SAP ASCS/SCS envolve estas tarefas:

- Coletando os nós de cluster em uma configuração de cluster
- Configurando uma testemunha de compartilhamento de arquivos de cluster

#### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>Coletar os nós de cluster em uma configuração de cluster

1. No assistente Adicionar funções e recursos, adicione clustering de failover a ambos os nós de cluster.
2. Configure o cluster de failover usando Gerenciador de Cluster de Failover. Em Gerenciador de Cluster de Failover, selecione **criar cluster**e, em seguida, adicione apenas o nome do primeiro cluster, nó A. Não adicionar o segundo nó ainda; Você adicionará o segundo nó em uma etapa posterior.

   ![Figura 18: Adicionar o nome do servidor ou da máquina virtual do primeiro nó do cluster][sap-ha-guide-figure-3007]

   _**Figura 18:** Adicionar o nome do servidor ou da máquina virtual do primeiro nó do cluster_

3. Insira o nome da rede (nome do host virtual) do cluster.

   ![Figura 19: inserir o nome do cluster][sap-ha-guide-figure-3008]

   _**Figura 19:** Insira o nome do cluster_

4. Depois de criar o cluster, execute um teste de validação de cluster.

   ![Figura 20: executar a verificação de validação de cluster][sap-ha-guide-figure-3009]

   _**Figura 20:** Executar a verificação de validação de cluster_

   Você pode ignorar quaisquer avisos sobre discos neste ponto do processo. Você adicionará uma testemunha de compartilhamento de arquivos e os discos de SIOS compartilhados mais tarde. Neste estágio, você não precisa se preocupar em ter um quorum.

   ![Figura 21: nenhum disco de quorum é encontrado][sap-ha-guide-figure-3010]

   _**Figura 21:** Nenhum disco de quorum foi encontrado_

   ![Figura 22: o recurso de cluster principal precisa de um novo endereço IP][sap-ha-guide-figure-3011]

   _**Figura 22:** O recurso de cluster principal precisa de um novo endereço IP_

5. Altere o endereço IP do serviço de cluster principal. O cluster não pode ser iniciado até que você altere o endereço IP do serviço de cluster principal, pois o endereço IP do servidor aponta para um dos nós da máquina virtual. Faça isso na página **Propriedades** do recurso IP do serviço de cluster principal.

   Por exemplo, precisamos atribuir um endereço IP (em nosso exemplo, **10.0.0.42**) para o nome de host virtual do cluster **PR1-ASCs-vir**.

   ![Figura 23: na caixa de diálogo Propriedades, altere o endereço IP][sap-ha-guide-figure-3012]

   _**Figura 23:** Na caixa de diálogo **Propriedades** , altere o endereço IP_

   ![Figura 24: atribuir o endereço IP reservado para o cluster][sap-ha-guide-figure-3013]

   _**Figura 24:** Atribuir o endereço IP reservado para o cluster_

6. Coloque o nome do host virtual do cluster online.

   ![Figura 25: o serviço principal do cluster está em execução e com o endereço IP correto][sap-ha-guide-figure-3014]

   _**Figura 25:** O serviço principal do cluster está em execução e com o endereço IP correto_

7. Adicione o segundo nó de cluster.

   Agora que o serviço de cluster principal está em execução, você pode adicionar o segundo nó de cluster.

   ![Figura 26: Adicionar o segundo nó de cluster][sap-ha-guide-figure-3015]

   _**Figura 26:** Adicionar o segundo nó de cluster_

8. Insira um nome para o segundo host de nó de cluster.

   ![Figura 27: inserir o nome de host do segundo nó de cluster][sap-ha-guide-figure-3016]

   _**Figura 27:** Insira o nome de host do segundo nó de cluster_

   > [!IMPORTANT]
   > Certifique-se de que a caixa de seleção **Adicionar todo o armazenamento elegível ao cluster** **não** esteja marcada.  
   >
   >

   ![Figura 28: não marcar a caixa de seleção][sap-ha-guide-figure-3017]

   _**Figura 28:** Não **marque a** caixa de seleção_

   Você pode ignorar avisos sobre quorum e discos. Você definirá o quorum e compartilhará o disco mais tarde, conforme descrito em [instalando o sios Datakeeper Cluster Edition para o disco de compartilhamento de cluster do SAP ASCS/SCS][sap-ha-guide-8.12.3].

   ![Figura 29: ignorar avisos sobre o quorum de disco][sap-ha-guide-figure-3018]

   _**Figura 29:** Ignorar avisos sobre o quorum de disco_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Configurar uma testemunha de compartilhamento de arquivos de cluster

A configuração de uma testemunha de compartilhamento de arquivos de cluster envolve estas tarefas:

- Criando um compartilhamento de arquivos
- Definindo o quorum de testemunha de compartilhamento de arquivos em Gerenciador de Cluster de Failover

##### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Criar um compartilhamento de arquivos

1. Selecione uma testemunha de compartilhamento de arquivos em vez de um disco de quorum. O SIOS datakeeper dá suporte a essa opção.

   Nos exemplos deste artigo, a testemunha de compartilhamento de arquivos está no servidor Active Directory/DNS que está em execução no Azure. A testemunha de compartilhamento de arquivos é chamada **de domcontr-0**. Como você teria configurado uma conexão VPN com o Azure (via VPN site a site ou Azure ExpressRoute), seu serviço de Active Directory/DNS é local e não é adequado para executar uma testemunha de compartilhamento de arquivos.

   > [!NOTE]
   > Se o serviço Active Directory/DNS for executado somente no local, não configure a testemunha de compartilhamento de arquivos no sistema operacional Windows Active Directory/DNS que está sendo executado no local. A latência de rede entre os nós de cluster em execução no Azure e no Active Directory/DNS local pode ser muito grande e causar problemas de conectividade. Certifique-se de configurar a testemunha de compartilhamento de arquivos em uma máquina virtual do Azure que esteja sendo executada perto do nó do cluster.  
   >
   >

   A unidade de quorum precisa de pelo menos 1.024 MB de espaço livre. Recomendamos 2.048 MB de espaço livre para a unidade de quorum.

2. Adicione o objeto de nome do cluster.

   ![Figura 30: atribuir as permissões no compartilhamento para o objeto de nome do cluster][sap-ha-guide-figure-3019]

   _**Figura 30:** Atribuir as permissões no compartilhamento para o objeto de nome do cluster_

   Certifique-se de que as permissões incluem a autoridade para alterar os dados no compartilhamento para o objeto de nome do cluster (em nosso exemplo, **PR1-ASCs-vir $** ).

3. Para adicionar o objeto de nome de cluster à lista, selecione **Adicionar**. Altere o filtro para verificar se há objetos de computador, além daqueles mostrados na figura 31.

   ![Figura 31: alterar os tipos de objeto para incluir computadores][sap-ha-guide-figure-3020]

   _**Figura 31:** Alterar os tipos de objeto para incluir computadores_

   ![Figura 32: marque a caixa de seleção computadores][sap-ha-guide-figure-3021]

   _**Figura 32:** Marque a caixa de seleção **computadores**_

4. Insira o objeto de nome do cluster, conforme mostrado na figura 31. Como o registro já foi criado, você pode alterar as permissões, como mostrado na figura 30.

5. Selecione a guia **segurança** do compartilhamento e, em seguida, defina permissões mais detalhadas para o objeto de nome do cluster.

   ![Figura 33: definir os atributos de segurança para o objeto de nome do cluster no quorum de compartilhamento de arquivos][sap-ha-guide-figure-3022]

   _**Figura 33:** Definir os atributos de segurança para o objeto de nome do cluster no quorum de compartilhamento de arquivos_

##### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>Definir o quorum de testemunha de compartilhamento de arquivos em Gerenciador de Cluster de Failover

1. Abra o assistente para definir configurações de quorum.

   ![Figura 34: iniciar o assistente para definir configuração de quorum do cluster][sap-ha-guide-figure-3023]

   _**Figura 34:** Iniciar o assistente para definir a configuração de quorum do cluster_

2. Na página **selecionar configuração de quorum** , selecione **selecionar a testemunha de quorum**.

   ![Figura 35: configurações de quorum das quais você pode escolher][sap-ha-guide-figure-3024]

   _**Figura 35:** Configurações de quorum das quais você pode escolher_

3. Na página **selecionar testemunha de quorum** , selecione **Configurar uma testemunha de compartilhamento de arquivos**.

   ![Figura 36: selecionar a testemunha de compartilhamento de arquivos][sap-ha-guide-figure-3025]

   _**Figura 36:** Selecionar a testemunha de compartilhamento de arquivos_

4. Insira o caminho UNC para o compartilhamento de arquivos (em nosso exemplo, \\domcontr-0\FSW). Para ver uma lista das alterações que você pode fazer, selecione **Avançar**.

   ![Figura 37: definir o local do compartilhamento de arquivos para o compartilhamento de testemunha][sap-ha-guide-figure-3026]

   _**Figura 37:** Definir o local do compartilhamento de arquivos para o compartilhamento de testemunha_

5. Selecione as alterações desejadas e, em seguida, selecione **Avançar**. Você precisa reconfigurar com êxito a configuração do cluster, conforme mostrado na figura 38.  

   ![Figura 38: confirmação de que você reconfigurou o cluster][sap-ha-guide-figure-3027]

   _**Figura 38:** Confirmação de que você reconfigurou o cluster_

Depois de instalar o cluster de failover do Windows com êxito, as alterações precisam ser feitas a alguns limites para adaptar a detecção de failover às condições no Azure. Os parâmetros a serem alterados estão documentados neste blog: https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/. Supondo que as duas VMs que criam a configuração de cluster do Windows para ASCS/SCS estejam na mesma sub-rede, os parâmetros a seguir precisam ser alterados para estes valores:
- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Essas configurações foram testadas com os clientes e forneciam um bom comprometimento suficiente para serem resilientes por um lado. Por outro lado, essas configurações estavam fornecendo um failover rápido o suficiente em condições de erro reais no software SAP ou na falha de nó/VM. 

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>Instalar o SIOS datakeeper Cluster Edition para o disco de compartilhamento de cluster do SAP ASCS/SCS

Agora você tem uma configuração de clustering de failover do Windows Server em funcionamento no Azure. No entanto, para instalar uma instância do SAP ASCS/SCS, você precisa de um recurso de disco compartilhado. Você não pode criar os recursos de disco compartilhado de que precisa no Azure. O SIOS datakeeper Cluster Edition é uma solução de terceiros que você pode usar para criar recursos de disco compartilhados.

A instalação do SIOS datakeeper Cluster Edition para o disco de compartilhamento de cluster do SAP ASCS/SCS envolve estas tarefas:

- Adicionando o .NET Framework 3,5
- Instalando o SIOS datakeeper
- Configurando o SIOS datakeeper

#### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>Adicionar o .NET Framework 3,5
O Microsoft .NET Framework 3,5 não é automaticamente ativado ou instalado no Windows Server 2012 R2. Como o SIOS datakeeper requer que o .NET Framework esteja em todos os nós em que você instalar o datakeeper, você deve instalar o .NET Framework 3,5 no sistema operacional convidado de todas as máquinas virtuais no cluster.

Há duas maneiras de adicionar o .NET Framework 3,5:

- Use o assistente para adicionar funções e recursos no Windows, conforme mostrado na figura 39.

  ![Figura 39: instalar o .NET Framework 3,5 usando o assistente para adicionar funções e recursos][sap-ha-guide-figure-3028]

  _**Figura 39:** Instalar o .NET Framework 3,5 usando o assistente para adicionar funções e recursos_

  ![Figura 40: barra de progresso da instalação quando você instala o .NET Framework 3,5 usando o assistente para adicionar funções e recursos][sap-ha-guide-figure-3029]

  _**Figura 40:** Barra de progresso da instalação quando você instala o .NET Framework 3,5 usando o assistente para adicionar funções e recursos_

- Use a ferramenta de linha de comando DISM. exe. Para esse tipo de instalação, você precisa acessar o diretório SxS na mídia de instalação do Windows. Em um prompt de comandos com privilégios elevados, digite:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="dd41d5a2-8083-415b-9878-839652812102"></a>Instalar o SIOS datakeeper

Instale o SIOS datakeeper Cluster Edition em cada nó do cluster. Para criar um armazenamento compartilhado virtual com o SIOS datakeeper, crie um espelho sincronizado e, em seguida, simule o armazenamento compartilhado do cluster.

Antes de instalar o software SIOS, crie o usuário de domínio **DataKeeperSvc**.

> [!NOTE]
> Adicione o usuário **DataKeeperSvc** ao grupo de **Administradores local** em ambos os nós de cluster.
>
>

Para instalar o SIOS datakeeper:

1. Instale o software SIOS em ambos os nós de cluster.

   ![Instalador do SIOS][sap-ha-guide-figure-3030]

   ![Figura 41: primeira página da instalação do SIOS datakeeper][sap-ha-guide-figure-3031]

   _**Figura 41:** Primeira página da instalação do SIOS datakeeper_

2. Na caixa de diálogo mostrada na Figura 42, selecione **Sim**.

   ![Figura 42: o datakeeper informa que um serviço será desabilitado][sap-ha-guide-figure-3032]

   _**Figura 42:** O datakeeper informa que um serviço será desabilitado_

3. Na caixa de diálogo mostrada na Figura 43, recomendamos que você selecione **conta de domínio ou servidor**.

   ![Figura 43: seleção de usuário para SIOS datakeeper][sap-ha-guide-figure-3033]

   _**Figura 43:** Seleção de usuário para SIOS datakeeper_

4. Insira o nome de usuário e as senhas da conta de domínio que você criou para o SIOS datakeeper.

   ![Figura 44: inserir o nome de usuário do domínio e a senha para a instalação do SIOS datakeeper][sap-ha-guide-figure-3034]

   _**Figura 44:** Insira o nome de usuário de domínio e a senha para a instalação do SIOS datakeeper_

5. Instale a chave de licença da sua instância do SIOS datakeeper, conforme mostrado na Figura 45.

   ![Figura 45: Insira sua chave de licença do SIOS datakeeper][sap-ha-guide-figure-3035]

   _**Figura 45:** Insira sua chave de licença do SIOS datakeeper_

6. Quando solicitado, reinicie a máquina virtual.

#### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>Configurar o SIOS datakeeper

Depois de instalar o SIOS datakeeper em ambos os nós, você precisará iniciar a configuração. O objetivo da configuração é ter a replicação de dados síncrona entre os discos adicionais anexados a cada uma das máquinas virtuais.

1. Inicie a ferramenta de gerenciamento e configuração do datakeeper e, em seguida, selecione **conectar servidor**. (Na figura 46, essa opção está circulada em vermelho.)

   ![Figura 46: ferramenta de gerenciamento e configuração do SIOS datakeeper][sap-ha-guide-figure-3036]

   _**Figura 46:** Ferramenta de gerenciamento e configuração do SIOS datakeeper_

2. Insira o nome ou endereço TCP/IP do primeiro nó ao qual a ferramenta de gerenciamento e configuração deve se conectar e, em uma segunda etapa, o segundo nó.

   ![Figura 47: inserir o nome ou endereço TCP/IP do primeiro nó ao qual a ferramenta de gerenciamento deve se conectar e, em uma segunda etapa, o segundo nó][sap-ha-guide-figure-3037]

   _**Figura 47:** Insira o nome ou endereço TCP/IP do primeiro nó ao qual a ferramenta de gerenciamento e configuração deve se conectar e, em uma segunda etapa, o segundo nó_

3. Crie o trabalho de replicação entre os dois nós.

   ![Figura 48: criar um trabalho de replicação][sap-ha-guide-figure-3038]

   _**Figura 48:** Criar um trabalho de replicação_

   Um assistente orienta você pelo processo de criação de um trabalho de replicação.
4. Defina o nome, o endereço TCP/IP e o volume de disco do nó de origem.

   ![Figura 49: definir o nome do trabalho de replicação][sap-ha-guide-figure-3039]

   _**Figura 49:** Definir o nome do trabalho de replicação_

   ![Figura 50: definir os dados base para o nó, que deve ser o nó de origem atual][sap-ha-guide-figure-3040]

   _**Figura 50:** Definir os dados base para o nó, que deve ser o nó de origem atual_

5. Defina o nome, o endereço TCP/IP e o volume de disco do nó de destino.

   ![Figura 51: definir os dados base para o nó, que deve ser o nó de destino atual][sap-ha-guide-figure-3041]

   _**Figura 51:** Defina os dados base para o nó, que deve ser o nó de destino atual_

6. Defina os algoritmos de compactação. Em nosso exemplo, recomendamos que você compacte o fluxo de replicação. Especialmente em situações de ressincronização, a compactação do fluxo de replicação reduz drasticamente o tempo de ressincronização. Observe que a compactação usa os recursos de CPU e RAM de uma máquina virtual. À medida que a taxa de compactação aumenta, o volume de recursos de CPU é usado. Você também pode ajustar essa configuração posteriormente.

7. Outra configuração que você precisa verificar é se a replicação ocorre de forma assíncrona ou síncrona. *Ao proteger as configurações do SAP ASCS/SCS, você deve usar a replicação síncrona*.  

   ![Figura 52: definir detalhes de replicação][sap-ha-guide-figure-3042]

   _**Figura 52:** Definir detalhes de replicação_

8. Defina se o volume que é replicado pelo trabalho de replicação deve ser representado a uma configuração de cluster do Windows Server failover clustering como um disco compartilhado. Para a configuração do SAP ASCS/SCS, selecione **Sim** para que o cluster do Windows Veja o volume replicado como um disco compartilhado que pode ser usado como um volume de cluster.

   ![Figura 53: selecione Sim para definir o volume replicado como um volume de cluster][sap-ha-guide-figure-3043]

   _**Figura 53:** Selecione **Sim** para definir o volume replicado como um volume de cluster_

   Depois que o volume é criado, a ferramenta de gerenciamento e configuração do datakeeper mostra que o trabalho de replicação está ativo.

   ![Figura 54: o espelhamento síncrono do datakeeper para o disco de compartilhamento do SAP ASCS/SCS está ativo][sap-ha-guide-figure-3044]

   _**Figura 54:** O espelhamento síncrono do datakeeper para o disco de compartilhamento do SAP ASCS/SCS está ativo_

   Gerenciador de Cluster de Failover agora mostra o disco como um disco do datakeeper, como mostra a Figura 55.

   ![Figura 55: o Gerenciador de Cluster de Failover mostra o disco que o datakeeper replicau][sap-ha-guide-figure-3045]

   _**Figura 55:** Gerenciador de Cluster de Failover mostra o disco que o datakeeper replicau_

## <a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a>Instalar o sistema SAP NetWeaver

Não descreveremos a configuração do DBMS porque as configurações variam de acordo com o sistema DBMS que você usa. No entanto, supomos que preocupações de alta disponibilidade com o DBMS são abordadas com as funcionalidades que os diferentes fornecedores de DBMS dão suporte ao Azure. Por exemplo, Always On ou espelhamento de banco de dados para SQL Server e Oracle Data Guard para bancos de dados Oracle. No cenário que usamos neste artigo, não adicionamos mais proteção ao DBMS.

Não há considerações especiais quando diferentes serviços DBMS interagem com esse tipo de configuração de SAP ASCS/SCS clusterizada no Azure.

> [!NOTE]
> Os procedimentos de instalação dos sistemas SAP NetWeaver ABAP, sistemas Java e sistemas ABAP + Java são quase idênticos. A diferença mais significativa é que um sistema SAP ABAP tem uma instância ASCS. O sistema SAP Java tem uma instância SCS. O sistema SAP ABAP + Java tem uma instância ASCS e uma instância SCS em execução no mesmo grupo de clusters de failover da Microsoft. Todas as diferenças de instalação para cada pilha de instalação do SAP NetWeaver são explicitamente mencionadas. Você pode assumir que todas as outras partes são iguais.  
>
>

### <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Instalar o SAP com uma instância ASCS/SCS de alta disponibilidade

> [!IMPORTANT]
> Certifique-se de não posicionar o arquivo de paginação em volumes espelhados do datakeeper. O datakeeper não oferece suporte a volumes espelhados. Você pode deixar o arquivo de paginação na unidade temporária D de uma máquina virtual do Azure, que é o padrão. Se ainda não estiver lá, mova o arquivo de paginação do Windows para a unidade D: da sua máquina virtual do Azure.
>
>

A instalação do SAP com uma instância ASCS/SCS de alta disponibilidade envolve estas tarefas:

- Criando um nome de host virtual para a instância clusterizada do SAP ASCS/SCS
- Instalando o primeiro nó de cluster do SAP
- Modificando o perfil SAP da instância do ASCS/SCS
- Adicionando uma porta de investigação
- Abrindo a porta de investigação do firewall do Windows

#### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Criar um nome de host virtual para a instância clusterizada do SAP ASCS/SCS

1. No Gerenciador de DNS do Windows, crie uma entrada DNS para o nome de host virtual da instância do ASCS/SCS.

   > [!IMPORTANT]
   > O endereço IP que você atribui ao nome de host virtual da instância ASCS/SCS deve ser o mesmo que o endereço IP atribuído a Azure Load Balancer ( **<*Sid*>-lb-ASCS**).  
   >
   >

   O endereço IP do nome de host virtual SAP ASCS/SCS (**PR1-ASCS-SAP**) é o mesmo que o endereço IP de Azure Load Balancer (**PR1-lb-ASCS**).

   ![Figura 56: definir a entrada DNS para o nome virtual do cluster do SAP ASCS/SCS e o endereço TCP/IP][sap-ha-guide-figure-3046]

   _**Figura 56:** Definir a entrada DNS para o nome virtual do cluster do SAP ASCS/SCS e o endereço TCP/IP_

2. Para definir o endereço IP atribuído ao nome de host virtual, selecione **Gerenciador de DNS** > **domínio**.

   ![Figura 57: novo nome virtual e endereço TCP/IP para configuração de cluster do SAP ASCS/SCS][sap-ha-guide-figure-3047]

   _**Figura 57:** Novo nome virtual e endereço TCP/IP para configuração de cluster do SAP ASCS/SCS_

#### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>Instalar o primeiro nó do cluster do SAP

1. Execute a primeira opção de nó de cluster no nó A do cluster. Por exemplo, no host **PR1-ASCs-0** .
2. Para manter as portas padrão para o balanceador de carga interno do Azure, selecione:

   * **Sistema ABAP**: número de instância **ASCS** **00**
   * **Sistema Java**: número de instância do **SCS** **01**
   * **ABAP + Java System**: número da instância **ASCS** **00** e o número de instância do **SCS** **01**

   Para usar números de instância diferentes de 00 para a instância ABAP ASCS e 01 para a instância do SCS Java, primeiro você precisa alterar as regras de balanceamento de carga padrão do balanceador de carga interno do Azure, descritas em [alterar as regras de balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure][sap-ha-guide-8.9].

As próximas tarefas não são descritas na documentação de instalação padrão do SAP.

> [!NOTE]
> A documentação de instalação do SAP descreve como instalar o primeiro nó de cluster ASCS/SCS.
>
>

#### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>Modificar o perfil SAP da instância do ASCS/SCS

Você precisa adicionar um novo parâmetro de perfil. O parâmetro de perfil impede que as conexões entre os processos de trabalho do SAP e o servidor de enfileiramento sejam encerradas quando estiverem ociosas por muito tempo. Mencionamos o cenário do problema em [Adicionar entradas do registro em ambos os nós de cluster da instância do SAP ASCS/SCS][sap-ha-guide-8.11]. Nessa seção, também introduzimos duas alterações em alguns parâmetros básicos de conexão TCP/IP. Em uma segunda etapa, você precisa definir o servidor de enfileiramento para enviar um sinal de `keep_alive` para que as conexões não atinjam o limite de ociosidade do balanceador de carga interno do Azure.

Para modificar o perfil SAP da instância do ASCS/SCS:

1. Adicione este parâmetro de perfil ao perfil da instância do SAP ASCS/SCS:

   ```
   enque/encni/set_so_keepalive = true
   ```
   Em nosso exemplo, o caminho é:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Por exemplo, para o perfil de instância do SAP SCS e o caminho correspondente:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. Para aplicar as alterações, reinicie a instância do SAP ASCS/SCS.

#### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>Adicionar uma porta de investigação

Use a funcionalidade de investigação do balanceador de carga interno para fazer com que toda a configuração do cluster funcione com o Azure Load Balancer. O balanceador de carga interno do Azure geralmente distribui a carga de trabalho de entrada igualmente entre as máquinas virtuais participantes. No entanto, isso não funcionará em algumas configurações de cluster porque apenas uma instância está ativa. A outra instância é passiva e não pode aceitar nenhuma das cargas de trabalho. Uma funcionalidade de investigação ajuda quando o balanceador de carga interno do Azure atribui o trabalho somente a uma instância ativa. Com a funcionalidade de investigação, o balanceador de carga interno pode detectar quais instâncias estão ativas e, em seguida, direcionar apenas a instância com a carga de trabalho.

Para adicionar uma porta de investigação:

1. Verifique a configuração atual do **ProbePort** executando o comando do PowerShell a seguir. Execute-o de dentro de uma das máquinas virtuais na configuração do cluster.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

2. Defina uma porta de investigação. O número da porta de investigação padrão é **0**. Em nosso exemplo, usamos a porta de investigação **62000**.

   ![Figura 58: a porta de investigação de configuração do cluster é 0 por padrão][sap-ha-guide-figure-3048]

   _**Figura 58:** A porta de investigação de configuração de cluster padrão é 0_

   O número da porta é definido nos modelos de Azure Resource Manager do SAP. Você pode atribuir o número da porta no PowerShell.

   Para definir um novo valor de ProbePort para o recurso de cluster de **IP do SAP <*Sid*** , execute o seguinte script do PowerShell. Atualize as variáveis do PowerShell para seu ambiente. Depois que o script for executado, será solicitado que você reinicie o grupo de clusters SAP para ativar as alterações.

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

   Depois de colocar o **SAP <*SID*>** online a função de cluster, certifique-se de que **ProbePort** está definido para o novo valor.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```

   ![Figura 59: investigar a porta do cluster depois de definir o novo valor][sap-ha-guide-figure-3049]

   _**Figura 59:** Investigar a porta do cluster depois de definir o novo valor_

#### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>Abrir a porta de investigação do firewall do Windows

Você precisa abrir uma porta de investigação do firewall do Windows em ambos os nós de cluster. Use o script a seguir para abrir uma porta de investigação do firewall do Windows. Atualize as variáveis do PowerShell para seu ambiente.

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

O **ProbePort** é definido como **62000**. Agora você pode acessar o compartilhamento de arquivos **\\arquivos ascsha-clsap\sapmnt** de outros hosts, como de **ascsha-DBAs**.

### <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>Instalar a instância do banco de dados

Para instalar a instância do banco de dados, siga o processo descrito na documentação de instalação do SAP.

### <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>Instalar o segundo nó de cluster

Para instalar o segundo cluster, siga as etapas no guia de instalação do SAP.

### <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>Alterar o tipo de início da instância do serviço Windows ERS do SAP

Altere o tipo de início do serviço do Windows ERS do SAP para **automático (atraso na inicialização)** em ambos os nós de cluster.

![Figura 60: alterar o tipo de serviço para a instância do SAP ERS para atraso automático][sap-ha-guide-figure-3050]

_**Figura 60:** Alterar o tipo de serviço da instância ERS do SAP para atraso automático_

### <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>Instalar o servidor de aplicativos principal do SAP

Instale a instância do PAS (servidor de aplicativos primário) <*SID*>-di-0 na máquina virtual que você designou para hospedar o Pas. Não há dependências nas configurações específicas do Azure ou do datakeeper.

### <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>Instalar o servidor de aplicativos do SAP adicional

Instale um servidor de aplicativos SAP adicional (AAS) em todas as máquinas virtuais que você designou para hospedar uma instância do servidor de aplicativos SAP. Por exemplo, em <*sid*>-di-1 para <*Sid*>-di-&lt;n&gt;.

> [!NOTE]
> Isso conclui a instalação de um sistema SAP NetWeaver de alta disponibilidade. Em seguida, continue com o teste de failover.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>Testar o failover da instância do SAP ASCS/SCS e a replicação SIOS
É fácil testar e monitorar um failover de instância do SAP ASCS/SCS e a replicação de disco SIOS usando Gerenciador de Cluster de Failover e a ferramenta de gerenciamento e configuração do SIOS datakeeper.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>A instância do SAP ASCS/SCS está em execução no nó A do cluster

O grupo de clusters do **SAP PR1** está em execução no nó A do cluster. Por exemplo, em **PR1-ASCs-0**. Atribua a unidade de disco compartilhada S, que faz parte do grupo de clusters **SAP PR1** e que a instância do ASCS/SCS usa, para o nó a do cluster.

![Figura 61: Gerenciador de Cluster de Failover: o SID do SAP < > grupo de clusters está em execução no nó A do cluster][sap-ha-guide-figure-5000]

_**Figura 61:** Gerenciador de Cluster de Failover: o*Sid*do SAP < > grupo de clusters está em execução no nó A do cluster_

Na ferramenta de gerenciamento e configuração do SIOS datakeeper, você pode ver que os dados do disco compartilhado são replicados de forma síncrona da unidade do volume de origem S no nó A do cluster a para a unidade do volume de destino S no nó B do cluster. Por exemplo, ele é replicado de **PR1-ASCs-0 [10.0.0.40]** para **PR1-ASCs-1 [10.0.0.41]** .

![Figura 62: no SIOS datakeeper, replique o volume local do nó A do cluster para o nó B do cluster][sap-ha-guide-figure-5001]

_**Figura 62:** No SIOS datakeeper, replique o volume local do nó A do cluster para o nó B do cluster_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>Failover do nó A para o nó B

1. Escolha uma destas opções para iniciar um failover do grupo de clusters do SAP <*SID*> do nó a do cluster para o nó B do cluster:
   - Usar Gerenciador de Cluster de Failover  
   - Usar o PowerShell do cluster de failover

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Reinicie o nó A do cluster no sistema operacional convidado do Windows (isso inicia um failover automático do grupo de clusters do SAP <*SID*> do nó a para o nó B).  
3. Reinicie o nó A do cluster do portal do Azure (isso inicia um failover automático do SAP <*SID*> grupo de clusters do nó a para o nó B).  
4. Reinicie o nó A do cluster usando Azure PowerShell (isso inicia um failover automático do SAP <*SID*> grupo de clusters do nó a para o nó B).

   Após o failover, o grupo de clusters do SAP <*SID*> está em execução no nó B do cluster. Por exemplo, ele está em execução em **PR1-ASCs-1**.

   ![Figura 63: em Gerenciador de Cluster de Failover, o SID do SAP < > grupo de clusters está em execução no nó B do cluster][sap-ha-guide-figure-5002]

   _**Figura 63**: em Gerenciador de cluster de failover, o*SID*do SAP < > grupo de clusters está em execução no nó B do cluster_

   O disco compartilhado agora está montado no nó B do cluster. o SIOS datakeeper está replicando dados da unidade do volume de origem S no nó B do cluster para a unidade do volume de destino S no nó A do cluster. Por exemplo, ele está replicando de **PR1-ASCs-1 [10.0.0.41]** para **PR1-ASCs-0 [10.0.0.40]** .

   ![Figura 64: o SIOS datakeeper Replica o volume local do nó B do cluster para o nó A do cluster][sap-ha-guide-figure-5003]

   _**Figura 64:** O SIOS datakeeper Replica o volume local do nó B do cluster para o nó A do cluster_
