---
title: Azure máquinas virtuais elevada disponibilidade para SAP NetWeaver | Documentos da Microsoft
description: Guia de elevada disponibilidade para SAP NetWeaver em máquinas de virtuais do Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a6b6728d7eaa263bb7e9da0f08a47ffe2f1e961a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58009455"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver"></a>Azure máquinas virtuais elevada disponibilidade para SAP NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Configuração de elevada disponibilidade de múltiplos SID SAP)


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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md



Máquinas virtuais do Azure é a solução para organizações que precisam de computação, armazenamento e recursos de rede, no mínimo de tempo e sem ciclos de aprovisionamento demorada. Pode utilizar máquinas virtuais do Azure para implementar aplicativos clássicos, como ABAP com base em SAP NetWeaver, Java e uma pilha ABAP + Java. Expanda a fiabilidade e disponibilidade sem recursos no local adicionais. Máquinas virtuais do Azure suporta a conectividade em vários locais, para que possa integrar máquinas virtuais do Azure em domínios no local, nuvens privadas e ambiente de sistema SAP da sua organização.

Neste artigo, vamos abordar os passos que pode efetuar para implementar sistemas SAP de elevada disponibilidade no Azure utilizando o modelo de implementação Azure Resource Manager. Orientaremos por estas tarefas principais:

* Encontre o SAP certa e guias de instalação, listados na [recursos] [ sap-ha-guide-2] secção. Este artigo complementa a documentação de instalação SAP e SAP Notes, que são os recursos principais que podem ajudar a instalar e implementar software SAP em plataformas específicas.
* Aprenda as diferenças entre o modelo de implementação Azure Resource Manager e o modelo de implementação clássica do Azure.
* Saiba mais sobre os modos de quórum de cluster de ativação pós-falha do Windows Server, pelo que pode selecionar o modelo mais adequado para a implementação do Azure.
* Saiba mais sobre o Windows Server Clustering de ativação pós-falha partilhado armazenamento nos serviços do Azure.
* Saiba como ajudar a proteger o único ponto de falha componentes, como o Advanced Business Application Programming (ABAP) SAP Central Services (ASCS) / SAP Central Services (SCS) e sistemas de gestão de bases de dados (DBMS) e componentes redundantes como aplicação SAP Servidor, no Azure.
* Seguir um exemplo passo a passo de uma instalação e configuração de um sistema SAP de elevada disponibilidade num cluster de Clustering de ativação pós-falha do Windows Server no Azure utilizando o Azure Resource Manager.
* Saiba mais sobre os passos adicionais necessários para utilizar o Clustering de ativação pós-falha no Windows Server no Azure, mas que não são necessários numa implantação no local.

Para simplificar a implantação e configuração, neste artigo, utilizamos modelos do Resource Manager de elevada disponibilidade de três camadas SAP. Os modelos de automatizam a implantação de toda a infraestrutura que necessita para um sistema SAP de elevada disponibilidade. A infraestrutura também suporta o dimensionamento de SAP aplicação desempenho padrão (SAPS) do seu sistema SAP.

## <a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Pré-requisitos
Antes de começar, certifique-se de que cumpre os pré-requisitos que são descritos nas secções seguintes. Além disso, certifique-se de que consulta todos os recursos indicados no [recursos] [ sap-ha-guide-2] secção.

Neste artigo, utilizamos modelos do Azure Resource Manager para [três camadas SAP NetWeaver utilizar discos geridos](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md/). Para obter uma descrição útil de modelos, veja [modelos Azure Resource Manager da SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).

## <a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Recursos
Estes artigos abrangem implementações de SAP no Azure:

* [Máquinas de virtuais de planeamento e implementação para o SAP NetWeaver do Azure][planning-guide]
* [Implementação de máquinas virtuais do Azure para SAP NetWeaver][deployment-guide]
* [Implementação de DBMS de máquinas virtuais do Azure para SAP NetWeaver][dbms-guide]
* [Azure máquinas virtuais elevada disponibilidade para SAP NetWeaver (neste guia)][sap-ha-guide]

> [!NOTE]
> Sempre que possível, damos-lhe uma ligação para o guia de instalação SAP referência (consulte a [guias de instalação SAP][sap-installation-guides]). Pré-requisitos e informações sobre o processo de instalação, é uma boa idéia para ler os guias de instalação SAP NetWeaver cuidadosamente. Este artigo abrange apenas as tarefas específicas para sistemas baseados em SAP NetWeaver, que pode utilizar com máquinas virtuais do Azure.
>
>

Estas notas de SAP estão relacionadas ao tópico de SAP no Azure:

| Número de nota | Título |
| --- | --- |
| [1928533] |Aplicações de SAP no Azure: Produtos suportados e o dimensionamento |
| [2015553] |SAP no Microsoft Azure: Pré-requisitos de suporte |
| [1999351] |Melhorada a monitorização do Azure para SAP |
| [2178632] |Chave de métricas de monitorização para o SAP no Microsoft Azure |
| [1999351] |Virtualização no Windows: Melhorada a monitorização |
| [2243692] |Utilização do armazenamento SSD Premium do Azure para a instância do SAP DBMS |

Saiba mais sobre o [limitações das subscrições do Azure][azure-subscription-service-limits-subscription], incluindo limitações gerais predefinida e limitações máximas.

## <a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>SAP de elevada disponibilidade com o Azure Resource Manager vs. o modelo de implementação clássica do Azure
Os modelos de implementação clássica do Azure e Azure Resource Manager são diferentes nas seguintes áreas:

- Grupos de recursos
- Dependência de Balanceador de carga interno do Azure no grupo de recursos do Azure
- Suporte para cenários de múltiplos SID SAP

### <a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Grupos de recursos
No Gestor de recursos do Azure, pode utilizar grupos de recursos para gerir todos os recursos de aplicação na sua subscrição do Azure. Uma abordagem integrada, no grupo de recursos, todos os recursos tenham o mesmo ciclo de vida. Por exemplo, todos os recursos são criados ao mesmo tempo, e eles são eliminados ao mesmo tempo. Saiba mais sobre [grupos de recursos](../../../azure-resource-manager/resource-group-overview.md#resource-groups).

### <a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Dependência de Balanceador de carga interno do Azure no grupo de recursos do Azure

No modelo de implementação clássica do Azure, existe uma dependência entre o Balanceador de carga interno do Azure (serviço de Balanceador de carga do Azure) e o serviço em nuvem. Cada Balanceador de carga interno tem de único serviço cloud.

No Azure Resource Manager, todos os recursos do Azure tem de ser colocados num grupo de recursos do Azure, e isso é válido para o Balanceador de carga do Azure também. No entanto, não é necessário ter um grupo de recursos do Azure por Balanceador de carga do Azure, por exemplo, um grupo de recursos do Azure pode conter vários balanceadores de carga do Azure. O ambiente é mais simples e mais flexível. 

### <a name="support-for-sap-multi-sid-scenarios"></a>Suporte para cenários de múltiplos SID SAP

No Gestor de recursos do Azure, pode instalar vários SAP (SID Identificador) ASCS/SCS instâncias de sistema num cluster. Instâncias de múltiplos SID são possíveis devido ao suporte para vários endereços IP para cada Balanceador de carga interno do Azure.

Para utilizar o modelo de implementação clássica do Azure, siga os procedimentos descritos em [SAP NetWeaver no Azure: Clustering de instâncias do SAP ASCS/SCS ao utilizar o Clustering de ativação pós-falha do Windows Server no Azure com o SIOS DataKeeper](https://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> Recomendamos vivamente que utilize o modelo de implementação Azure Resource Manager para as instalações do SAP. Ele oferece muitos benefícios que não estão disponíveis no modelo de implementação clássica. Saiba mais sobre o Azure [modelos de implementação][virtual-machines-azure-resource-manager-architecture-benefits-arm].   
>
>

## <a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Clustering de Failover do Windows Server
Clustering de ativação pós-falha do Windows Server é a base de uma instalação de SAP ASCS/SCS e com elevada disponibilidade DBMS no Windows.

Um cluster de ativação pós-falha é um grupo de 1 + n servidores independentes (nós) que funcionam em conjunto para aumentar a disponibilidade de aplicações e serviços. Se ocorrer uma falha de nó, o Clustering de ativação pós-falha do Windows Server calcula o número de falhas que podem ocorrer durante a manutenção de um cluster de bom estado de funcionamento para fornecer aplicativos e serviços. Pode escolher entre modos de quorum diferentes para alcançar o clustering de ativação pós-falha.

### <a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Modos de quorum
Pode escolher entre quatro modos de quorum ao utilizar o Clustering de ativação pós-falha do Windows Server:

* **Maioria de nós**. Cada nó do cluster pode votar. O cluster funciona apenas com a maioria dos votos, ou seja, com mais de metade os votos. Recomendamos esta opção para os clusters que tem um número desigual de nós. Por exemplo, três nós num cluster de sete nós podem falhar e stills o cluster atinge a maioria e continua a ser executado.  
* **Disco maioria de nós e**. Cada nó e um disco designado (um testemunho de disco) no armazenamento de cluster podem votar quando estiverem disponíveis e de comunicação. O cluster funciona apenas com a maioria dos votos, ou seja, com mais de metade os votos. Esse modo faz sentido num ambiente de cluster com um número par de nós. Se o disco e metade de nós estão online, o cluster permanecer em bom estado.
* **Nó e o ficheiro compartilham maioria**. Cada nó e uma partilha de ficheiros designado (um testemunho de partilha de ficheiros) que o administrador cria pode votar, independentemente se a nós e a partilha de ficheiros estão disponíveis e de comunicação. O cluster funciona apenas com a maioria dos votos, ou seja, com mais de metade os votos. Esse modo faz sentido num ambiente de cluster com um número par de nós. Ele é semelhante para o modo de disco maioria de nós e, mas utiliza uma partilha de ficheiros testemunha, em vez de um disco testemunha. Esse modo é fácil de implementar, mas se a partilha de ficheiros em si não é de elevada disponibilidade, pode se tornar um ponto único de falha.
* **Sem maioria: Disco apenas**. O cluster tem um quórum se um nó estiver disponível e de comunicação com um disco específico no armazenamento de cluster. Apenas os nós que também estão em comunicação com esse disco podem associar o cluster. Recomendamos que não use este modo.
 

## <a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> No local de Clustering de Failover do Windows Server
Figura 1 mostra um cluster de dois nós. Se partilhar a ligação de rede entre a falha de nós e mantenha-se de nós se tanto em execução, um ficheiro ou o disco de quórum determina qual nó irá continuar a fornecer aplicações e serviços do cluster. O nó que tem acesso à partilha de ficheiro ou de disco de quórum é o nó que garante que os serviços continuam.

Como este exemplo utiliza um cluster de dois nós, podemos usar o modo de quórum maioria de partilha de ficheiros de nós e. O disco maioria de nós e também é uma opção válida. Num ambiente de produção, recomendamos que utilize um disco de quórum. Pode utilizar a tecnologia de sistema de rede e armazenamento para conferir elevada disponibilidade.

![Figura 1: Exemplo de uma configuração de Clustering de ativação pós-falha do Windows Server para o SAP ASCS/SCS no Azure][sap-ha-guide-figure-1000]

_**Figura 1:** Exemplo de uma configuração de Clustering de ativação pós-falha do Windows Server para o SAP ASCS/SCS no Azure_

### <a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Armazenamento partilhado
Figura 1 também mostra um cluster de dois nós de armazenamento partilhado. Num cluster de armazenamento partilhado no local, todos os nós do cluster detetar armazenamento partilhado. Um mecanismo de bloqueio protege os dados da corrupção. Todos os nós podem detectar se outro nó falha. Se um nó falhar, o nó restante assumirá a propriedade dos recursos de armazenamento e garante a disponibilidade dos serviços.

> [!NOTE]
> Não precisa discos partilhados para elevada disponibilidade com algumas aplicações DBMS, como com o SQL Server. SQL Server Always On replica os ficheiros de dados e de registo do DBMS do disco local do nó de cluster para o disco local de outro nó de cluster. Nesse caso, a configuração de cluster do Windows não precisa de um disco partilhado.
>
>

### <a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Funcionamento em rede e resolução de nomes
Computadores cliente alcançar o cluster mais de um endereço IP virtual e um nome de anfitrião virtual que fornece o servidor DNS. Os nós no local e o servidor DNS podem processar vários endereços IP.

Uma configuração típica, vai utilizar dois ou mais ligações de rede:

* Uma ligação dedicada para o armazenamento
* Uma ligação de rede interna para o cluster para o heartbeat
* Uma rede pública que os clientes utilizam para ligar ao cluster

## <a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Windows Server Clustering de ativação pós-falha no Azure
Em comparação com implementações de nuvem privada ou metal bare-, máquinas virtuais do Azure requer passos adicionais para configurar o Clustering de ativação pós-falha do Windows Server. Quando cria um disco de cluster partilhado, terá de definir vários endereços IP e nomes de anfitrião virtual para a instância do SAP ASCS/SCS.

Neste artigo, discutimos a conceitos chave e os passos adicionais necessários para criar um cluster de serviços de central de alta disponibilidade do SAP no Azure. Vamos mostrar como configurar a ferramenta de terceiros o SIOS DataKeeper e como configurar o Balanceador de carga interno do Azure. Pode usar essas ferramentas para criar um cluster de ativação pós-falha do Windows com um testemunho de partilha de ficheiros no Azure.

![Figura 2: Configuração Clustering de ativação pós-falha do Windows Server no Azure sem um disco partilhado][sap-ha-guide-figure-1001]

_**Figura 2:** Configuração Clustering de ativação pós-falha do Windows Server no Azure sem um disco partilhado_

### <a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Partilhado disco no Azure com o SIOS DataKeeper
Precisa de cluster armazenamento partilhado para uma instância do SAP ASCS/SCS de elevada disponibilidade. A partir de Setembro de 2016, o Azure não oferece armazenamento partilhado que pode utilizar para criar um cluster de armazenamento partilhado. Pode utilizar o software de terceiros SIOS DataKeeper Cluster Edition para criar um armazenamento espelhado que simula o armazenamento partilhado de cluster. A solução SIOS fornece replicação de dados síncrona em tempo real. Isso é como pode criar um recurso de disco partilhado para um cluster:

1. Anexe um disco adicional para cada uma das máquinas virtuais (VMs) numa configuração de cluster do Windows.
2. Execute o SIOS DataKeeper Cluster Edition em ambos os nós de máquina virtual.
3. Configure o SIOS DataKeeper Cluster Edition, para que ele Espelha o conteúdo do volume de disco adicionais ligado da máquina virtual de origem para o volume de disco adicionais ligado de máquina virtual de destino. O SIOS DataKeeper abstrai os volumes de locais de origem e de destino e, em seguida, apresenta ao Clustering de ativação pós-falha no Windows Server como um disco partilhado.

Obtenha mais informações sobre [SIOS DataKeeper](http://us.sios.com/products/datakeeper-cluster/).

![Figura 3: Configuração de Clustering de ativação pós-falha do Windows Server no Azure com o SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Figura 3:** Configuração de Clustering de ativação pós-falha do Windows Server no Azure com o SIOS DataKeeper_

> [!NOTE]
> Não precisa discos partilhados para elevada disponibilidade com alguns produtos DBMS, como o SQL Server. SQL Server Always On replica os ficheiros de dados e de registo do DBMS do disco local do nó de cluster para o disco local de outro nó de cluster. Neste caso, a configuração de cluster do Windows não precisa de um disco partilhado.
>
>

### <a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Resolução de nomes no Azure
A plataforma de cloud do Azure não oferece a opção para configurar endereços IP virtuais, como endereços IP flutuantes. Precisa de uma solução alternativa para configurar um endereço IP virtual para alcançar o recurso de cluster na cloud.
O Azure tem um balanceador de carga interno no serviço de Balanceador de carga do Azure. O Balanceador de carga interno, clientes chegar ao cluster através do endereço IP virtual do cluster.
Precisa implementar o Balanceador de carga interno no grupo de recursos que contém nós do cluster. Em seguida, configure todas as portas necessárias as regras de reencaminhamento com a sonda de portas de Balanceador de carga interno.
Os clientes podem ligar através do nome de anfitrião virtual. O servidor DNS resolve o endereço IP do cluster e a porta de identificadores de Balanceador de carga interno de reencaminhamento para o nó ativo do cluster.

## <a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> SAP NetWeaver elevada disponibilidade no Azure infraestrutura-como-serviço (IaaS)
Para obter disponibilidade elevada da aplicação SAP, tal como para componentes de software SAP, terá de proteger os seguintes componentes:

* Instância do servidor de aplicações SAP
* Instância do SAP ASCS/SCS
* Servidor DBMS

Para obter mais informações sobre como proteger os componentes SAP em cenários de elevada disponibilidade, consulte [máquinas virtuais do Azure de planeamento e implementação para o SAP NetWeaver][planning-guide-11].

### <a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> Servidor de aplicações SAP de elevada disponibilidade
Normalmente, não precisa de uma solução de elevada disponibilidade específica para as instâncias de servidor de aplicações SAP e a caixa de diálogo. Obtenha elevada disponibilidade por redundância e irá configurar várias instâncias de caixa de diálogo em diferentes instâncias das máquinas virtuais do Azure. Deve ter, pelo menos, duas instâncias da aplicação SAP instaladas em duas instâncias das máquinas virtuais do Azure.

![Figura 4: Servidor de aplicações SAP de elevada disponibilidade][sap-ha-guide-figure-2000]

_**Figura 4:** Servidor de aplicações SAP de elevada disponibilidade_

Tem de colocar todas as máquinas virtuais que o conjunto de instâncias de servidor de aplicações SAP de anfitrião no mesmo de disponibilidade do Azure. Um conjunto de disponibilidade do Azure garante que:

* Todas as máquinas virtuais fazem parte do mesmo domínio de atualização. Por exemplo, um domínio de atualização, torna-se de que as máquinas virtuais não são atualizadas ao mesmo tempo durante o período de indisponibilidade de manutenção planeada.
* Todas as máquinas virtuais fazem parte do mesmo domínio de falha. Por exemplo, um domínio de falha, torna-se de que as máquinas virtuais são implementadas para que nenhum ponto único de falha afeta a disponibilidade de todas as máquinas virtuais.

Saiba mais sobre como [gerir a disponibilidade das máquinas virtuais][virtual-machines-manage-availability].

Apenas disco não gerido: Como a conta de armazenamento do Azure é um potencial ponto único de falha, é importante ter, pelo menos, duas contas de armazenamento do Azure, em que são distribuídas, pelo menos, duas máquinas virtuais. Numa configuração ideal, os discos de cada máquina virtual que está a executar uma instância de caixa de diálogo SAP seriam implementados numa conta de armazenamento diferentes.

### <a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Instância do SAP ASCS/SCS de elevada disponibilidade
Figura 5 é um exemplo de uma instância do SAP ASCS/SCS de elevada disponibilidade.

![Figura 5: Instância do SAP ASCS/SCS de elevada disponibilidade][sap-ha-guide-figure-2001]

_**Figura 5:** Instância do SAP ASCS/SCS de elevada disponibilidade_

#### <a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> Elevada disponibilidade com Clustering de ativação pós-falha no Windows Server no Azure de instância do SAP ASCS/SCS
Em comparação com implementações de nuvem privada ou metal bare-, máquinas virtuais do Azure requer passos adicionais para configurar o Clustering de ativação pós-falha do Windows Server. Para criar um cluster de ativação pós-falha do Windows, terá de um disco de cluster partilhado, vários endereços IP, vários nomes de anfitrião virtual e um balanceador de carga interno do Azure para o agrupamento de uma instância do SAP ASCS/SCS. Abordaremos isso em mais detalhes posteriormente neste artigo.

![Figura 6: Clustering de ativação pós-falha no Windows Server para uma configuração de SAP ASCS/SCS no Azure utilizando o SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Figura 6:** Clustering de ativação pós-falha no Windows Server para uma configuração de SAP ASCS/SCS no Azure com o SIOS DataKeeper_

### <a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>Instância do DBMS de elevada disponibilidade
O DBMS é também um único ponto de contato num sistema SAP. Tem de protegê-los através de uma solução de elevada disponibilidade. A figura 7 mostra uma solução de elevada disponibilidade SQL Server Always On no Azure, com o Clustering de ativação pós-falha do Windows Server e do Azure interno Balanceador de carga. SQL Server Always On replica os ficheiros de dados e de registo do DBMS utilizando a sua própria replicação DBMS. Neste caso, não precisa agrupar discos partilhados, que simplifica a configuração inteira.

![Figura 7: Exemplo de um DBMS de SAP de elevada disponibilidade, com o SQL Server Always On][sap-ha-guide-figure-2003]

_**Figura 7:** Exemplo de um DBMS de SAP de elevada disponibilidade, com o SQL Server Always On_

Para obter mais informações sobre o agrupamento do SQL Server no Azure utilizando o modelo de implementação Azure Resource Manager, veja estes artigos:

* [Configurar o grupo de disponibilidade Always On em máquinas de virtuais do Azure manualmente utilizando o Gestor de recursos] [virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Configurar um balanceador de carga interno do Azure para um grupo de disponibilidade Always On no Azure] [virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Cenários de implementação de elevada disponibilidade de ponto a ponto

### <a name="deployment-scenario-using-architectural-template-1"></a>Cenário de implantação usando a arquitetura de modelo de 1

Figura 8 mostra um exemplo de uma arquitetura de elevada disponibilidade do SAP NetWeaver no Azure para **um** sistema SAP. Este cenário é configurado da seguinte forma:

- Um cluster dedicado é utilizado para a instância do SAP ASCS/SCS.
- Um cluster dedicado é utilizado para a instância do DBMS.
- Instâncias de servidor de aplicações SAP são implementadas em suas próprias VMs dedicadas.

![Figura 8: Modelo de arquitetura da elevada disponibilidade 1, com o cluster dedicado para ASCS/SCS e DBMS de SAP][sap-ha-guide-figure-2004]

_**Figura 8:** SAP 1 modelo de arquitetura do elevada disponibilidade, os clusters dedicados do ASCS/SCS e DBMS_

### <a name="deployment-scenario-using-architectural-template-2"></a>Cenário de implantação usando a arquitetura de modelo de 2

Figura 9 mostra um exemplo de uma arquitetura de elevada disponibilidade do SAP NetWeaver no Azure para **um** sistema SAP. Este cenário é configurado da seguinte forma:

- Um cluster dedicado é utilizado para **ambos** a instância do SAP ASCS/SCS e o DBMS.
- Instâncias de servidor de aplicações SAP são implementadas no próprias VMs dedicadas.

![Figura 9: Elevada disponibilidade arquitetura modelo 2, com um cluster dedicado para ASCS/SCS e um cluster dedicado para o DBMS de SAP][sap-ha-guide-figure-2005]

_**Figura 9:** Elevada disponibilidade arquitetura modelo 2, com um cluster dedicado para ASCS/SCS e um cluster dedicado para o DBMS de SAP_

### <a name="deployment-scenario-using-architectural-template-3"></a>Cenário de implantação usando a arquitetura de modelo de 3

Figura 10 mostra um exemplo de uma arquitetura de elevada disponibilidade do SAP NetWeaver no Azure para **dois** SAP sistemas, com &lt;SID1&gt; e &lt;SID2&gt;. Este cenário é configurado da seguinte forma:

- Um cluster dedicado é utilizado para **ambos** a instância do SAP ASCS/SCS SID1 *e* a instância do SAP ASCS/SCS SID2 (um cluster).
- Um cluster dedicado é utilizado para DBMS SID1 e outro cluster dedicado é utilizado para DBMS SID2 (dois clusters).
- Instâncias de servidor de aplicações SAP para o sistema SAP SID1 tem suas próprias VMs dedicadas.
- Instâncias de servidor de aplicações SAP para o sistema SAP SID2 tem suas próprias VMs dedicadas.

![Figura 10: Elevada disponibilidade arquitetura modelo 3, com um cluster dedicado para diferentes instâncias do ASCS/SCS de SAP][sap-ha-guide-figure-6003]

_**Figura 10:** Elevada disponibilidade arquitetura modelo 3, com um cluster dedicado para diferentes instâncias do ASCS/SCS de SAP_

## <a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Preparar a infraestrutura

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Preparar a infraestrutura para a arquitetura de modelo de 1
Os modelos do Resource Manager do Azure para SAP ajudam a simplificar a implantação de recursos necessários.

Os modelos de três camadas no Azure Resource Manager também suportam cenários de elevada disponibilidade, tal como na arquitetura de modelo de 1, que tem dois clusters. Cada cluster é um ponto único de SAP da falha para o SAP ASCS/SCS e do DBMS.

Aqui é onde pode obter os modelos Azure Resource Manager para o cenário de exemplo descritos neste artigo:

* [Imagem do Marketplace do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Imagem do Marketplace do Azure com o Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Imagem personalizada com o Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Para preparar a infraestrutura para a arquitetura de modelo de 1:

- No portal do Azure, sobre o **parâmetros** painel, na **SYSTEMAVAILABILITY** caixa, selecione **HA**.

  ![Figura 11: Definir parâmetros de Gestor de recursos do Azure de elevada disponibilidade do SAP][sap-ha-guide-figure-3000]

_**Figura 11:** Definir parâmetros de Gestor de recursos do Azure de elevada disponibilidade do SAP_


  Os modelos de criam:

  * **Máquinas virtuais**:
    * Máquinas de virtuais de servidor de aplicações SAP: <*SAPSystemSID*> - injeção de dependência - <*número*>
    * Máquinas virtuais de cluster do ASCS/SCS: <*SAPSystemSID*> - ascs - <*número*>
    * Cluster do DBMS: <*SAPSystemSID*> - db - <*número*>

  * **Cartões de todas as máquinas virtuais, com endereços IP associados de rede**:
    * <*SAPSystemSID*>-nic-di-<*Number*>
    * <*SAPSystemSID*>-nic-ascs-<*Number*>
    * <*SAPSystemSID*>-nic-db-<*Number*>

  * **Contas de armazenamento do Azure (apenas discos não geridos)**

  * **Grupos de disponibilidade** para:
    * Máquinas de virtuais de servidor de aplicações SAP: <*SAPSystemSID*> - avset - injeção de dependência
    * Máquinas virtuais de cluster do SAP ASCS/SCS: <*SAPSystemSID*> - avset - ascs
    * Máquinas virtuais de cluster do DBMS: <*SAPSystemSID*> - avset - db

  * **Balanceador de carga interno do Azure**:
    * Com todas as portas para a instância do ASCS/SCS e o endereço IP <*SAPSystemSID*> - lb - ascs
    * Com todas as portas para o SQL Server DBMS e o endereço IP <*SAPSystemSID*> - lb - db

  * **Grupo de segurança de rede**: <*SAPSystemSID*> ascs - nsg--0  
    * Com uma porta aberta de protocolo RDP (Remote Desktop) externo para o <*SAPSystemSID*> máquina virtual de - ascs - 0

> [!NOTE]
> Todos os endereços IP das placas de rede e Balanceadores de carga interno do Azure são **dinâmica** por predefinição. Alterá-los para **estático** endereços IP. Descrevemos como fazê-lo posteriormente neste artigo.
>
>

### <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Implementar máquinas virtuais com a conectividade de rede da empresa (em vários locais) para utilizar na produção
Sistemas de produção SAP, implementar máquinas virtuais do Azure com [conectividade de rede da empresa (em vários locais)] [ planning-guide-2.2] através da utilização do Azure Site-to-Site VPN ou Azure ExpressRoute.

> [!NOTE]
> Pode utilizar a instância de rede Virtual do Azure. A rede virtual e sub-rede já foi criados e preparados.
>
>

1. No portal do Azure, sobre o **parâmetros** painel, na **NEWOREXISTINGSUBNET** caixa, selecione **existente**.
2. Na **SUBNETID** caixa, adicione a cadeia de caracteres completa da sua rede do Azure preparado SubnetID onde planeia implementar máquinas virtuais do Azure.
3. Para obter uma lista de todas as sub-redes de rede do Azure, execute este comando do PowerShell:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   O **ID** campo mostra o **SUBNETID**.
4. Para obter uma lista de todos os **SUBNETID** valores, execute este comando do PowerShell:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   O **SUBNETID** semelhante ao seguinte:

   ```
   /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
   ```

### <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Implementar instâncias SAP apenas na cloud para teste e demonstração
Pode implementar o sistema SAP de elevada disponibilidade num modelo de implementação apenas na cloud. Esse tipo de implementação é principalmente útil para casos de utilização de demonstração e teste. Não é adequada para casos de utilização de produção.

- No portal do Azure, sobre o **parâmetros** painel, na **NEWOREXISTINGSUBNET** caixa, selecione **novo**. Deixe o **SUBNETID** campo vazio.

  O modelo de SAP do Azure Resource Manager cria automaticamente a rede virtual do Azure e a sub-rede.

> [!NOTE]
> Também terá de implementar, pelo menos, uma máquina virtual dedicada para o Active Directory e DNS na mesma instância de rede Virtual do Azure. O modelo não cria estas máquinas virtuais.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Preparar a infraestrutura para a arquitetura de modelo de 2

Pode utilizar este modelo de Gestor de recursos do Azure para SAP para o ajudar a simplificar a implantação de recursos de infraestrutura necessária para 2 modelo de arquitetura do SAP.

Aqui é onde pode obter os modelos Azure Resource Manager para este cenário de implementação:

* [Imagem do Marketplace do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Imagem do Marketplace do Azure com o Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Imagem personalizada com o Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Preparar a infraestrutura para a arquitetura de modelo de 3

Pode preparar a infraestrutura e configurar o SAP para **múltiplos SID**. Por exemplo, pode adicionar uma instância do SAP ASCS/SCS adicional para um *existente* configuração de cluster. Para obter mais informações, consulte [configurar uma instância adicional do SAP ASCS/SCS numa configuração de cluster existente para criar uma configuração de múltiplos SID SAP no Azure Resource Manager][sap-ha-multi-sid-guide].

Se quiser criar um novo cluster de múltiplos SID, pode utilizar o múltiplos SID [modelos de início rápido no GitHub](https://github.com/Azure/azure-quickstart-templates).
Para criar um novo cluster de múltiplos SID, terá de implementar os modelos de três seguintes:

* [Modelo do ASCS/SCS](#ASCS-SCS-template)
* [Modelo de base de dados](#database-template)
* [Modelo de aplicativo de servidores](#application-servers-template)

As secções seguintes têm mais detalhes sobre os modelos e os parâmetros, que tem de fornecer nos modelos.

#### <a name="ASCS-SCS-template"></a> Modelo do ASCS/SCS

O modelo do ASCS/SCS implementa duas máquinas virtuais que pode utilizar para criar um cluster de ativação pós-falha do Windows Server que hospeda várias instâncias do ASCS/SCS.

Para configurar o modelo de múltiplos SID do ASCS/SCS, além da [modelo de múltiplos SID do ASCS/SCS] [ sap-templates-3-tier-multisid-xscs-marketplace-image] ou [modelo de múltiplos SID do ASCS/SCS utilizar discos geridos] [ sap-templates-3-tier-multisid-xscs-marketplace-image-md], introduza os valores para os seguintes parâmetros:

  - **Prefixo de recurso**.  Defina o prefixo de recursos, o que é utilizado para o prefixo de todos os recursos que são criados durante a implantação. Como os recursos não pertencem a apenas um sistema SAP, o prefixo do recurso não é o SID de um sistema SAP.  O prefixo tem de estar entre **três e seis carateres**.
  - **Tipo de pilha**. Selecione o tipo de pilha do sistema SAP. Dependendo do tipo de pilha, o Balanceador de carga do Azure tem um (ABAP ou Java apenas) ou dois (ABAP + Java) endereços IP privados por sistema SAP.
  -  **Tipo de SO**. Selecione o sistema operativo das máquinas virtuais.
  -  **Contagem de sistema SAP**. Selecione o número de sistemas SAP que pretende instalar neste cluster.
  -  **Disponibilidade do sistema**. Selecione **HA**.
  -  **Nome de utilizador administrador e a palavra-passe de administrador**. Crie um novo utilizador, que pode ser utilizado para iniciar sessão máquina.
  -  **Sub-rede nova ou existente**. Defina se devem ser criadas uma nova rede virtual e uma sub-rede ou uma sub-rede existente deve ser utilizada. Se já tiver uma rede virtual que está ligada à sua rede no local, selecione **existente**.
  -  **Id de sub-rede**. Se pretender implementar a VM para uma VNet já existente em que tem uma sub-rede definida a VM deve ser atribuída para nomear o ID dessa sub-rede. O ID é normalmente fica assim: /subscriptions/ <*id de subscrição*> /resourceGroups/ <*nome do grupo de recursos*> /providers/Microsoft.Network/virtualNetworks/ < *nome da rede virtual*> /subnets/ <*nome da sub-rede*>

O modelo implementa uma instância de Balanceador de carga do Azure, que suporta vários sistemas SAP.

- As instâncias do ASCS estão configuradas para o número de instância 00, 10, 20...
- As instâncias SCS estão configuradas para o número de instância 01, 11, 21...
- As instâncias de servidor de replicação de colocar em fila do ASCS (ERS) (apenas Linux) estão configuradas para o número de instância 02, 12, 22...
- As instâncias de SCS ERS (apenas Linux) estão configuradas para o número de instância 03, 13, 23...

O Balanceador de carga contém de 1 (2 para Linux) VIP(s), 1 x VIP para ASCS/SCS e 1 x VIP para ERS (apenas Linux).

A lista seguinte contém todas as regras (onde x é o número do sistema SAP, por exemplo, 1, 2, 3...) de balanceamento de carga:
- Portas de específicas do Windows para todos os sistemas SAP: 445, 5985
- Portas do ASCS (número de instância x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- Portas SCS (número de instância x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- Portas de ASCS ERS no Linux (número de instância x2): 33x2, 5x213, 5x214, 5x216
- Portas de SCS ERS no Linux (número de instância x3): 33x3, 5x313, 5x314, 5x316

O Balanceador de carga está configurado para utilizar as seguintes portas da sonda (onde x é o número do sistema SAP, por exemplo, 1, 2, 3...):
- Porta de sonda de Balanceador de carga interno do ASCS/SCS: 620x0
- Porta de sonda (apenas Linux) do Balanceador de carga ERS internos: 621x2

#### <a name="database-template"></a> Modelo de base de dados

O modelo de base de dados implementa uma ou duas máquinas virtuais que pode utilizar para instalar o sistema de gestão de base de dados relacional (RDBMS) para um sistema SAP. Por exemplo, se implementar um modelo do ASCS/SCS para cinco sistemas SAP, terá de implementar este modelo de cinco vezes.

Para configurar o modelo de múltiplos SID de base de dados, além da [modelo de múltiplos SID do banco de dados] [ sap-templates-3-tier-multisid-db-marketplace-image] ou [modelo de múltiplos SID de base de dados com o Managed Disks] [ sap-templates-3-tier-multisid-db-marketplace-image-md], introduza os valores para os seguintes parâmetros:

- **Id de sistema de SAP**. Introduza o ID de sistema SAP do sistema SAP que pretende instalar. O ID será utilizado como um prefixo para os recursos que são implementados.
- **Tipo de SO**. Selecione o sistema operativo das máquinas virtuais.
- **DbType**. Selecione o tipo da base de dados que pretende instalar no cluster. Selecione **SQL** se pretender instalar o Microsoft SQL Server. Selecione **HANA** se planear instalar o SAP HANA nas máquinas virtuais. Certifique-se de selecionar o tipo de sistema operacional correto: selecione **Windows** para SQL e selecione uma distribuição do Linux para o HANA. O Balanceador de carga do Azure que está ligada às máquinas virtuais será configurado para suportar o tipo de base de dados selecionada:
  * **SQL**. O Balanceador de carga será a porta 1433 do balanceamento de carga. Certifique-se utilizar esta porta para a configuração do SQL Server Always On.
  * **HANA**. O Balanceador de carga será portas 35015 e 35017 de balanceamento de carga. Certifique-se instalar o SAP HANA com o número de instância **50**.
  O Balanceador de carga utilizará a porta de sonda 62550.
- **Tamanho do sistema de SAP**. Defina o número de SAPS fornecerá o novo sistema. Se não forem-se de que SAPS quantos, o sistema irá necessitar, peça ao seu parceiro de tecnologia de SAP ou integrador de sistemas.
- **Disponibilidade do sistema**. Selecione **HA**.
- **Nome de utilizador administrador e a palavra-passe de administrador**. Crie um novo utilizador, que pode ser utilizado para iniciar sessão máquina.
- **Id de sub-rede**. Introduza o ID da sub-rede que utilizou durante a implementação do modelo ASCS/SCS ou o ID da sub-rede que foi criada como parte da implementação de modelo do ASCS/SCS.

#### <a name="application-servers-template"></a> Modelo de aplicativo de servidores

O modelo de servidores de aplicativo implementa dois ou mais máquinas virtuais que podem ser utilizadas como instâncias de servidor de aplicações SAP para um sistema SAP. Por exemplo, se implementar um modelo do ASCS/SCS para cinco sistemas SAP, terá de implementar este modelo de cinco vezes.

Para configurar o modelo de múltiplos SID de servidores de aplicativo, além da [o modelo de múltiplos SID de servidores de aplicativo] [ sap-templates-3-tier-multisid-apps-marketplace-image] ou [utilizar discos geridosomodelodeaplicativoservidoresmúltiplosSID] [ sap-templates-3-tier-multisid-apps-marketplace-image-md], introduza os valores para os seguintes parâmetros:

  -  **Id de sistema de SAP**. Introduza o ID de sistema SAP do sistema SAP que pretende instalar. O ID será utilizado como um prefixo para os recursos que são implementados.
  -  **Tipo de SO**. Selecione o sistema operativo das máquinas virtuais.
  -  **Tamanho do sistema de SAP**. O número de SAPS fornecerá o novo sistema. Se não forem-se de que SAPS quantos, o sistema irá necessitar, peça ao seu parceiro de tecnologia de SAP ou integrador de sistemas.
  -  **Disponibilidade do sistema**. Selecione **HA**.
  -  **Nome de utilizador administrador e a palavra-passe de administrador**. Crie um novo utilizador, que pode ser utilizado para iniciar sessão máquina.
  -  **Id de sub-rede**. Introduza o ID da sub-rede que utilizou durante a implementação do modelo ASCS/SCS ou o ID da sub-rede que foi criada como parte da implementação de modelo do ASCS/SCS.


### <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Rede virtual do Azure
No nosso exemplo, o espaço de endereços da rede virtual do Azure é 10.0.0.0/16. Existe uma sub-rede denominada **sub-rede**, com um intervalo de endereços de 10.0.0.0/24. Todas as máquinas virtuais e Balanceadores de carga interno são implementadas nesta rede virtual.

> [!IMPORTANT]
> Não efetue quaisquer alterações às definições de rede dentro do sistema operativo convidado. Isto inclui os endereços IP, servidores DNS e sub-rede. Configure todas as definições de rede no Azure. O serviço de anfitrião configuração protocolo DHCP (Dynamic) propaga as suas definições.
>
>

### <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> Endereços IP de DNS

Para definir endereços de IP de DNS necessários, siga os passos abaixo.

1. No portal do Azure, sobre o **servidores DNS** painel, certifique-se de que a rede virtual **servidores DNS** opção estiver definida como **DNS personalizado**.
2. Selecione as definições com base no tipo de rede que tiver. Para obter mais informações, consulte os seguintes recursos:
   * [Conectividade de rede da empresa (em vários locais)][planning-guide-2.2]: Adicione os endereços IP dos servidores DNS no local.  
   Pode ampliar servidores DNS no local para as máquinas virtuais que estão em execução no Azure. Nesse cenário, pode adicionar os endereços IP das máquinas virtuais do Azure no qual executa o serviço DNS.
   * Para implementações de VM isoladas no Azure: Implemente uma máquina virtual adicional na mesma instância de rede Virtual que funciona como um servidor DNS. Adicione os endereços IP das máquinas virtuais do Azure que configurou para executar o serviço DNS.

   ![Figura 12: Configurar servidores DNS para rede Virtual do Azure][sap-ha-guide-figure-3001]

   _**Figura 12:** Configurar servidores DNS para rede Virtual do Azure_

   > [!NOTE]
   > Se alterar os endereços IP dos servidores DNS, terá de reiniciar as máquinas virtuais do Azure para aplicar a alteração e propagar os novos servidores DNS.
   >
   >

No nosso exemplo, o serviço DNS é instalado e configurado nestas máquinas virtuais do Windows:

| Função de máquina virtual | Nome de anfitrião de máquina virtual | Nome da placa de rede | Endereço IP estático |
| --- | --- | --- | --- |
| Primeiro servidor DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Segundo servidor DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Os nomes de anfitrião e endereços IP estáticos para a instância em cluster do SAP ASCS/SCS e a instância em cluster do DBMS

Para implementação no local, terá destes nomes de anfitrião reservados e endereços IP:

| Função de nome de anfitrião virtual | Nome de anfitrião virtual | Endereço IP estático virtual |
| --- | --- | --- |
| SAP ASCS/SCS primeiro cluster virtual nome de anfitrião (a gestão de clusters) |pr1-ascs-vir |10.0.0.42 |
| Nome de anfitrião virtual de instância do SAP ASCS/SCS |pr1-ascs-sap |10.0.0.43 |
| Nome de anfitrião virtual de cluster segundo do SAP DBMS (gerenciamento de cluster) |pr1-dbms-vir |10.0.0.32 |

Quando criar o cluster, criar os nomes de anfitrião virtual **pr1-ascs-vir** e **pr1-dbms-vir** e os endereços IP associados que gerem o próprio cluster. Para obter informações sobre como fazer isso, consulte [recolher nós de cluster numa configuração de cluster][sap-ha-guide-8.12.1].

Pode criar manualmente os outros dois nomes de anfitrião virtual, **pr1-ascs-sap** e **pr1-dbms-sap**e os endereços IP associados, no servidor DNS. A instância do SAP ASCS/SCS em cluster e a instância em cluster do DBMS usam estes recursos. Para obter informações sobre como fazer isso, consulte [criar um nome de anfitrião virtual para uma instância em cluster do SAP ASCS/SCS][sap-ha-guide-9.1.1].

### <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Conjunto de endereços IP estáticos para as máquinas de virtuais SAP
Depois de implementar as máquinas virtuais para utilizar no seu cluster, terá de definir endereços IP estáticos para todas as máquinas virtuais. Opte por fazê-lo na configuração de rede Virtual do Azure e não no sistema operativo convidado.

1. No portal do Azure, selecione **grupo de recursos** > **placa de rede** > **definições** > **deendereçodeIP**.
2. Sobre o **endereços IP** painel, em **atribuição**, selecione **estático**. Na **endereço IP** , introduza o endereço IP que pretende utilizar.

   > [!NOTE]
   > Se alterar o endereço IP da placa de rede, terá de reiniciar as máquinas virtuais do Azure para aplicar a alteração.  
   >
   >

   ![Figura 13: Conjunto de endereços IP estáticos para a placa de rede de cada máquina virtual][sap-ha-guide-figure-3002]

   _**Figura 13:** Conjunto de endereços IP estáticos para a placa de rede de cada máquina virtual_

   Repita este passo para todas as interfaces de rede, que é, todas as máquinas virtuais, incluindo máquinas virtuais que pretende utilizar para o seu serviço DNS/do Active Directory.

No nosso exemplo, temos estas máquinas virtuais e endereços IP estáticos:

| Função de máquina virtual | Nome de anfitrião de máquina virtual | Nome da placa de rede | Endereço IP estático |
| --- | --- | --- | --- |
| Primeira instância de servidor de aplicações SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Segunda instância de servidor de aplicações SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Última instância do servidor de aplicações SAP |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Primeiro nó de cluster para a instância ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Segundo nó de cluster para a instância ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Primeiro nó de cluster para a instância de DBMS |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Segundo nó de cluster para a instância de DBMS |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

### <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Definir um endereço IP estático para o Balanceador de carga interno do Azure

O modelo de SAP do Azure Resource Manager cria um balanceador de carga interno do Azure que é utilizado para o cluster de instância do SAP ASCS/SCS e o cluster do DBMS.

> [!IMPORTANT]
> O endereço IP do nome de anfitrião virtual de SAP ASCS/SCS é o mesmo que o endereço IP do Balanceador de carga interno de SAP ASCS/SCS: **pr1-lb-ascs**.
> O endereço IP do nome virtual do DBMS é o mesmo que o endereço IP do Balanceador de carga interno DBMS: **pr1-lb-dbms**.
>
>

Para definir um endereço IP estático para o Balanceador de carga interno do Azure:

1. A implementação inicial define o endereço IP do Balanceador de carga interno para **dinâmica**. No portal do Azure, sobre o **endereços IP** painel, em **atribuição**, selecione **estático**.
2. Definir o endereço IP do Balanceador de carga interno **pr1-lb-ascs** para o endereço IP do nome de anfitrião virtual de instância do SAP ASCS/SCS.
3. Definir o endereço IP do Balanceador de carga interno **pr1-lb-dbms** para o endereço IP do nome de anfitrião virtual de instância do DBMS.

   ![Figura 14: Conjunto de endereços IP estáticos para o Balanceador de carga interno para a instância do SAP ASCS/SCS][sap-ha-guide-figure-3003]

   _**Figura 14:** Conjunto de endereços IP estáticos para o Balanceador de carga interno para a instância do SAP ASCS/SCS_

No nosso exemplo, temos dois balanceadores de carga interno do Azure que estes endereços IP estáticos:

| Função de Balanceador de carga interno do Azure | Nome do Balanceador de carga interno do Azure | Endereço IP estático |
| --- | --- | --- |
| Balanceador de carga interno de instância do SAP ASCS/SCS |pr1-lb-ascs |10.0.0.43 |
| Balanceador de carga interno do SAP DBMS |pr1-lb-dbms |10.0.0.33 |


### <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Regras para o Balanceador de carga interno do Azure de balanceamento de carga do ASCS/SCS de predefinição

O modelo de SAP do Azure Resource Manager cria as portas que precisa:
* Uma instância de ABAP ASCS, com o número de instância padrão **00**
* Uma instância de SCS de Java, com o número de instância padrão **01**

Quando instala sua instância do SAP ASCS/SCS, tem de utilizar o número de instância padrão **00** para a sua instância do ASCS de ABAP e o número de instância padrão **01** para a sua instância do SCS de Java.

Em seguida, crie necessária pontos de extremidade para as portas de SAP NetWeaver de balanceamento de carga interno.

Para criar pontos finais de balanceamento de carga interna necessária, primeiro, crie esses pontos de extremidade para as portas de SAP NetWeaver ABAP ASCS de balanceamento de carga:

| Nome da regra balanceamento de carga do serviço | Números de porta predefinidos | Portas concretas para (instância ASCS com o número de instância 00) (ERS com 10) |
| --- | --- | --- |
| Servidor de colocar em fila / *lbrule3200* |32 <*InstanceNumber*> |3200 |
| Servidor de mensagens ABAP / *lbrule3600* |36 <*InstanceNumber*> |3600 |
| Mensagem ABAP interna / *lbrule3900* |39 <*InstanceNumber*> |3900 |
| O servidor HTTP de mensagens / *Lbrule8100* |81 <*InstanceNumber*> |8100 |
| HTTP de ASCS de serviço de início SAP / *Lbrule50013* |5 <*InstanceNumber*> 13 |50013 |
| SAP iniciar serviço ASCS HTTPS / *Lbrule50014* |5 <*InstanceNumber*> 14 |50014 |
| Replicação de colocar em fila / *Lbrule50016* |5 <*InstanceNumber*> 16 |50016 |
| HTTP de ERS de serviço de início SAP *Lbrule51013* |5 <*InstanceNumber*> 13 |51013 |
| HTTP de ERS de serviço de início SAP *Lbrule51014* |5 <*InstanceNumber*> 14 |51014 |
| Ganhar RM *Lbrule5985* | |5985 |
| Partilha de ficheiros *Lbrule445* | |445 |

_**Tabela 1:** Números de porta das instâncias do ASCS de ABAP SAP NetWeaver_

Em seguida, crie esses pontos de extremidade para as portas de SAP NetWeaver Java SCS de balanceamento de carga:

| Nome da regra balanceamento de carga do serviço | Números de porta predefinidos | Portas concretas para (a instância SCS com o número de instância 01) (ERS com 11) |
| --- | --- | --- |
| Servidor de colocar em fila / *lbrule3201* |32 <*InstanceNumber*> |3201 |
| Servidor de gateway / *lbrule3301* |33 <*InstanceNumber*> |3301 |
| Servidor de mensagens de Java / *lbrule3900* |39 <*InstanceNumber*> |3901 |
| O servidor HTTP de mensagens / *Lbrule8101* |81 <*InstanceNumber*> |8101 |
| HTTP de SCS de serviço de início SAP / *Lbrule50113* |5 <*InstanceNumber*> 13 |50113 |
| SAP iniciar serviço SCS HTTPS / *Lbrule50114* |5 <*InstanceNumber*> 14 |50114 |
| Replicação de colocar em fila / *Lbrule50116* |5 <*InstanceNumber*> 16 |50116 |
| HTTP de ERS de serviço de início SAP *Lbrule51113* |5 <*InstanceNumber*> 13 |51113 |
| HTTP de ERS de serviço de início SAP *Lbrule51114* |5 <*InstanceNumber*> 14 |51114 |
| Ganhar RM *Lbrule5985* | |5985 |
| Partilha de ficheiros *Lbrule445* | |445 |

_**Tabela 2:** Números de porta das instâncias do SAP NetWeaver Java SCS_

![Figura 15: Regras para o Balanceador de carga interno do Azure de balanceamento de carga do ASCS/SCS de predefinição][sap-ha-guide-figure-3004]

_**Figura 15:** Regras para o Balanceador de carga interno do Azure de balanceamento de carga do ASCS/SCS de predefinição_

Definir o endereço IP do Balanceador de carga **pr1-lb-dbms** para o endereço IP do nome de anfitrião virtual de instância do DBMS.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Alterar a regras para o Balanceador de carga interno do Azure de balanceamento de carga do padrão ASCS/SCS

Se pretender utilizar números diferentes para as instâncias de SAP ASCS ou SCS, tem de alterar os nomes e valores de suas portas valores predefinidos.

1. No portal do Azure, selecione  **< *SID*> Balanceador de carga - lb - ascs** > **regras de balanceamento de carga**.
2. Todas as regras que pertencem à instância do SAP ASCS ou SCS de balanceamento de carga, altere estes valores:

   * Name
   * Porta
   * Porta de back-end

   Por exemplo, se pretender alterar o número de instância do ASCS predefinido de 00 e 31, terá de efetuar as alterações para todas as portas listadas na tabela 1.

   Eis um exemplo de uma atualização para a porta *lbrule3200*.

   ![Figura 16: Alterar a regras para o Balanceador de carga interno do Azure de balanceamento de carga do padrão ASCS/SCS][sap-ha-guide-figure-3005]

   _**Figura 16:** Alterar a regras para o Balanceador de carga interno do Azure de balanceamento de carga do padrão ASCS/SCS_

### <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Adicionar máquinas de virtuais do Windows para o domínio

Depois de atribuir um endereço IP estático para as máquinas virtuais, adicione as máquinas virtuais no domínio.

![Figura 17: Adicionar uma máquina virtual a um domínio][sap-ha-guide-figure-3006]

_**Figura 17:** Adicionar uma máquina virtual a um domínio_

### <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Adicionar entradas de Registro em ambos os nós de cluster da instância do SAP ASCS/SCS

O Balanceador de carga do Azure tem um balanceador de carga interno que fecha ligações quando as ligações estão Inativas durante um determinado período de tempo (um tempo limite de inatividade). Processos de trabalho SAP na caixa de diálogo conexões abertas de instâncias a colocar em fila de SAP processam assim que o primeiro colocar em fila/remover da fila do pedido tem de ser enviada. Estas ligações normalmente mantêm estabelecidas até o processo de trabalho ou reinicia o processo de colocar em fila. No entanto, se a ligação estiver inativa durante um determinado período de tempo, o Balanceador de carga interno do Azure fecha as ligações. Isso não é um problema porque o processo de trabalho SAP reestablishes a ligação para o processo de colocar em fila se já não existir. Essas atividades estão documentadas nos rastreios de desenvolvedor de processos SAP, mas eles criam uma grande quantidade de conteúdo extra nos rastreios. É uma boa idéia para alterar o TCP/IP `KeepAliveTime` e `KeepAliveInterval` em ambos os nós de cluster. Combine essas alterações nos parâmetros do TCP/IP com os parâmetros de perfil do SAP, descritos posteriormente neste artigo.

Para adicionar entradas de Registro em ambos os nós de cluster da instância do SAP ASCS/SCS, primeiro, adicione estas entradas de Registro do Windows em ambos os nós de cluster do Windows para o SAP ASCS/SCS:

| Caminho | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome da variável |`KeepAliveTime` |
| Tipo de variável |REG_DWORD (Decimal) |
| Value |120000 |
| Ligar a documentação |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

_**Tabela 3:** Alterar o primeiro parâmetro de TCP/IP_

Em seguida, adicione este entradas de Registro do Windows em ambos os nós de cluster do Windows para o SAP ASCS/SCS:

| Caminho | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome da variável |`KeepAliveInterval` |
| Tipo de variável |REG_DWORD (Decimal) |
| Value |120000 |
| Ligar a documentação |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

_**Tabela 4:** Altere o segundo parâmetro de TCP/IP_

**Para aplicar as alterações, reiniciar ambos os nós de cluster**.

### <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Configurar um cluster de Clustering de ativação pós-falha do Windows Server para uma instância do SAP ASCS/SCS

Configurar um cluster de Clustering de ativação pós-falha do Windows Server para uma instância do SAP ASCS/SCS envolve estas tarefas:

- Recolher os nós de cluster numa configuração de cluster
- Configurar um testemunho de partilha de ficheiros do cluster

#### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Recolher os nós de cluster numa configuração de cluster

1. No Assistente de funcionalidades e Adicionar função, adicione o clustering para ambos os nós de cluster de ativação pós-falha.
2. Como configurar o cluster de ativação pós-falha utilizando o Gestor de clusters de ativação pós-falha. No Gestor de clusters de ativação pós-falha, selecione **criar clusters**e, em seguida, adicione apenas o nome do cluster primeiro, a nó. Não adicione o segundo nó ainda; adicionará o segundo nó num passo posterior.

   ![Figura 18: Adicione o nome do servidor ou máquina virtual do primeiro nó de cluster][sap-ha-guide-figure-3007]

   _**Figura 18:** Adicione o nome do servidor ou máquina virtual do primeiro nó de cluster_

3. Introduza o nome de rede (nome de anfitrião virtual) do cluster.

   ![Figura 19: Introduza o nome do cluster][sap-ha-guide-figure-3008]

   _**Figura 19:** Introduza o nome do cluster_

4. Depois de criar o cluster, execute um teste de validação de cluster.

   ![Figura 20: Executar a verificação de validação de cluster][sap-ha-guide-figure-3009]

   _**Figura 20:** Executar a verificação de validação de cluster_

   Pode ignorar quaisquer avisos sobre discos neste momento no processo. Adicionará que um testemunho de partilha de ficheiros e o SIOS discos partilhados mais tarde. Nesta fase, não precisa se preocupar sobre ter um quórum.

   ![Figura 21: Não foi encontrado nenhum disco de quórum][sap-ha-guide-figure-3010]

   _**Figura 21:** Não foi encontrado nenhum disco de quórum_

   ![Figura 22: Recurso fundamental do cluster tem um novo endereço IP][sap-ha-guide-figure-3011]

   _**Figura 22:** Recurso fundamental do cluster tem um novo endereço IP_

5. Altere o endereço IP do serviço de cluster básico. O cluster não é possível iniciar até alterar o endereço IP do serviço de cluster básico, porque o endereço IP do servidor aponta para um de nós de máquinas virtuais. Fazê-lo na **propriedades** página de recurso IP do serviço de cluster principal.

   Por exemplo, é necessário atribuir um endereço IP (no nosso exemplo, **10.0.0.42**) para o nome de anfitrião virtual de cluster **pr1-ascs-vir**.

   ![Figura 23: Na caixa de diálogo de propriedades, altere o endereço IP][sap-ha-guide-figure-3012]

   _**Figura 23:** Na **propriedades** diálogo caixa, altere o endereço IP_

   ![Figura 24: Atribuir o endereço IP que está reservado para o cluster][sap-ha-guide-figure-3013]

   _**Figura 24:** Atribuir o endereço IP que está reservado para o cluster_

6. Coloque online o nome de anfitrião virtual de cluster.

   ![Figura 25: Serviço de núcleo de cluster está ativo e em execução e com o IP correto de endereços][sap-ha-guide-figure-3014]

   _**Figura 25:** Serviço de núcleo de cluster está ativo e em execução e com o IP correto de endereços_

7. Adicione o segundo nó de cluster.

   Agora que o serviço de cluster principal está em execução, pode adicionar o segundo nó de cluster.

   ![Figura 26: Adicionar o segundo nó de cluster][sap-ha-guide-figure-3015]

   _**Figura 26:** Adicionar o segundo nó de cluster_

8. Introduza um nome para o anfitrião de nó de cluster segundo.

   ![Figura 27: Introduza o nome de anfitrião do segundo nó de cluster][sap-ha-guide-figure-3016]

   _**Figura 27:** Introduza o nome de anfitrião do segundo nó de cluster_

   > [!IMPORTANT]
   > Certifique-se de que o **adicionar todo o armazenamento elegível ao cluster** caixa de verificação está **não** selecionado.  
   >
   >

   ![Figura 28: Não selecione a caixa de verificação][sap-ha-guide-figure-3017]

   _**Figura 28:** Fazer **não** selecione a caixa de verificação_

   Pode ignorar avisos sobre o quórum e discos. Irá configurar o quórum e partilhar o disco mais tarde, conforme descrito em [instalar o SIOS DataKeeper Cluster Edition para a partilha de disco em cluster SAP ASCS/SCS][sap-ha-guide-8.12.3].

   ![Figura 29: Ignorar avisos sobre o quórum de disco][sap-ha-guide-figure-3018]

   _**Figura 29:** Ignorar avisos sobre o quórum de disco_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Configurar um testemunho de partilha de ficheiros do cluster

Configurar um testemunho de partilha de ficheiros do cluster envolve estas tarefas:

- Criar uma partilha de ficheiros
- Definir o quórum de testemunho de partilha de ficheiros no Gestor de clusters de ativação pós-falha

##### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Criar uma partilha de ficheiros

1. Selecione um testemunho de partilha de ficheiros em vez de um disco de quórum. O SIOS DataKeeper suportar esta opção.

   Os exemplos neste artigo, é o testemunho de partilha de ficheiros no servidor DNS/do Active Directory que está em execução no Azure. A testemunha de compartilhamento de arquivo é chamada **domcontr-0**. Uma vez que poderia ter configurado uma ligação de VPN para o Azure (via Site-to-Site VPN ou ExpressRoute do Azure), DNS do Active Directory/serviço está no local e não é adequado executar um arquivo de testemunho de partilha.

   > [!NOTE]
   > Se o seu serviço DNS/do Active Directory é executado apenas no local, não configure seu testemunho de partilha de ficheiros no sistema operativo Windows do Active Directory/DNS que está a executar no local. Latência de rede entre nós de cluster em execução no Azure e o DNS/do Active Directory no local poderá ser demasiado grande e causar problemas de conectividade. Certifique-se de que configurar a testemunha de compartilhamento de arquivos numa máquina virtual do Azure que está a executar o próximo nó do cluster.  
   >
   >

   O disco de quórum tem, pelo menos, 1024 MB de espaço livre. Recomendamos a 2048 MB de espaço livre para a unidade de quórum.

2. Adicione o objeto de nome de cluster.

   ![Figura 30: Atribuir as permissões na partilha para o objeto de nome de cluster][sap-ha-guide-figure-3019]

   _**Figura 30:** Atribuir as permissões na partilha para o objeto de nome de cluster_

   Certifique-se de que as permissões de incluam a autoridade para alterar os dados na partilha para o objeto de nome de cluster (no nosso exemplo, **pr1-ascs-vir$**).

3. Para adicionar o objeto de nome do cluster à lista, selecione **adicionar**. Altere o filtro para verificar a existência de objetos de computador, para além dos mostrado na figura 31.

   ![Figura 31: Altere os tipos de objeto para incluir computadores][sap-ha-guide-figure-3020]

   _**Figura 31:** Altere os tipos de objeto para incluir computadores_

   ![Figura 32: Selecione a caixa de verificação de computadores][sap-ha-guide-figure-3021]

   _**Figura 32:** Selecione o **computadores** caixa de verificação_

4. Introduza o objeto de nome de cluster, conforme mostrado na figura 31. Uma vez que o registo já foi criado, pode alterar as permissões, conforme mostrado na figura 30.

5. Selecione o **segurança** separador da partilha e, em seguida, defina mais detalhado de permissões para o objeto de nome de cluster.

   ![Figura 33: Definir os atributos de segurança para o objeto de nome de cluster no quórum de partilha de ficheiros][sap-ha-guide-figure-3022]

   _**Figura 33:** Definir os atributos de segurança para o objeto de nome de cluster no quórum de partilha de ficheiros_

##### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Definir o quórum de testemunho de partilha de ficheiros no Gestor de clusters de ativação pós-falha

1. Abra a configurar o Assistente de configuração de quórum.

   ![Figura 34: Iniciar o Assistente de configuração de quórum de Cluster de configurar][sap-ha-guide-figure-3023]

   _**Figura 34:** Iniciar o Assistente de configuração de quórum de Cluster de configurar_

2. Sobre o **selecionar configuração de quórum** , selecione **selecione o testemunho de quórum**.

   ![Figura 35: Pode escolher de entre as configurações de quórum][sap-ha-guide-figure-3024]

   _**Figura 35:** Pode escolher de entre as configurações de quórum_

3. Sobre o **selecionar testemunho de quórum** , selecione **configurar um testemunho de partilha de ficheiros**.

   ![Figura 36: Selecione o testemunho de partilha de ficheiros][sap-ha-guide-figure-3025]

   _**Figura 36:** Selecione o testemunho de partilha de ficheiros_

4. Introduza o caminho UNC para a partilha de ficheiros (no nosso exemplo, \\domcontr 0\FSW). Para ver uma lista de alterações pode fazer, selecione **seguinte**.

   ![Figura 37: Definir a localização da partilha de ficheiros para a partilha de testemunho][sap-ha-guide-figure-3026]

   _**Figura 37:** Definir a localização da partilha de ficheiros para a partilha de testemunho_

5. Selecione as alterações que pretende e, em seguida, selecione **seguinte**. Terá de reconfigurar com êxito a configuração do cluster, conforme mostrado na figura 38.  

   ![Figura 38: Confirmação de que já reconfigurado o cluster][sap-ha-guide-figure-3027]

   _**Figura 38:** Confirmação de que já reconfigurado o cluster_

Depois de instalar o Cluster de ativação pós-falha do Windows com êxito, as alterações precisam ser feitas para alguns limites para se adaptar a deteção de ativação pós-falha para condições no Azure. Os parâmetros de ser alteradas estão documentados neste blogue: https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/ . Supondo que as duas VMs que criar a configuração de Cluster do Windows para ASCS/SCS estão na mesma sub-rede, tem de ser alterada para esses valores os seguintes parâmetros:
- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Estas definições foram testadas com os clientes e forneceu uma boa solução para que seja resiliente do lado. Por outro lado essas definições estavam a fornecer rapidamente suficiente de ativação pós-falha em condições de erro real em caso de falha SAP, software ou o nó/VM. 

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Instalar o SIOS DataKeeper Cluster Edition para o disco de compartilhamento de cluster do SAP ASCS/SCS

Agora tem uma configuração de Clustering de ativação pós-falha do Windows Server no Azure. No entanto, para instalar uma instância do SAP ASCS/SCS, tem um recurso de disco partilhado. Não é possível criar os recursos de disco partilhado que necessita no Azure. O SIOS DataKeeper Cluster Edition é uma solução de terceiros que pode utilizar para criar recursos de disco partilhado.

Instalar o SIOS DataKeeper Cluster Edition para o disco de compartilhamento de cluster do SAP ASCS/SCS envolve estas tarefas:

- Adicionar o .NET Framework 3.5
- Instalar o SIOS DataKeeper
- Como configurar o SIOS DataKeeper

#### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Adicionar o .NET Framework 3.5
O Microsoft .NET Framework 3.5 não é automaticamente ativado ou instalado no Windows Server 2012 R2. Como o SIOS DataKeeper requer o .NET Framework para ser em todos os nós que instala DataKeeper no, tem de instalar o .NET Framework 3.5 no sistema operativo convidado de todas as máquinas virtuais no cluster.

Existem duas formas de adicionar o .NET Framework 3.5:

- Utilize a adicionar assistente funções e funcionalidades no Windows como mostrado na figura 39.

  ![Figura 39: Instalar o .NET Framework 3.5 ao utilizar a adicionar assistente funções e funcionalidades][sap-ha-guide-figure-3028]

  _**Figura 39:** Instalar o .NET Framework 3.5 ao utilizar a adicionar assistente funções e funcionalidades_

  ![Figura 40: Progresso da instalação de barra quando instala o .NET Framework 3.5 ao utilizar a adicionar assistente funções e funcionalidades][sap-ha-guide-figure-3029]

  _**Figura 40:** Progresso da instalação de barra quando instala o .NET Framework 3.5 ao utilizar a adicionar assistente funções e funcionalidades_

- Utilize a ferramenta da linha de comandos dism.exe. exe. Para este tipo de instalação, precisa acessar o diretório SxS no suporte de dados de instalação do Windows. Na linha de comandos elevada, escreva:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Instalar o SIOS DataKeeper

Instale o SIOS DataKeeper Cluster Edition em cada nó no cluster. Para criar o armazenamento partilhado virtual com o SIOS DataKeeper, criar um espelho sincronizado e, em seguida, simular o armazenamento partilhado de cluster.

Antes de instalar o software do SIOS, criar o utilizador de domínio **DataKeeperSvc**.

> [!NOTE]
> Adicionar a **DataKeeperSvc** utilizador para o **Administrador Local** grupo em ambos os nós de cluster.
>
>

Para instalar o SIOS DataKeeper:

1. Instale o software do SIOS em ambos os nós de cluster.

   ![Instalador do SIOS][sap-ha-guide-figure-3030]

   ![Figura 41: Primeira página da instalação do SIOS DataKeeper][sap-ha-guide-figure-3031]

   _**Figura 41:** Primeira página da instalação do SIOS DataKeeper_

2. Na caixa de diálogo mostrada na figura 42, selecione **Sim**.

   ![Figura 42: DataKeeper informa que um serviço é desativado][sap-ha-guide-figure-3032]

   _**Figura 42:** DataKeeper informa que um serviço é desativado_

3. Na caixa de diálogo mostrada na figura 43, recomendamos que seleciona **conta de domínio ou servidor**.

   ![Figura 43: Seleção do usuário para o SIOS DataKeeper][sap-ha-guide-figure-3033]

   _**Figura 43:** Seleção do usuário para o SIOS DataKeeper_

4. Introduza o nome de utilizador da conta de domínio e as palavras-passe que criou para o SIOS DataKeeper.

   ![Figura 44: Introduza o nome de utilizador de domínio e a palavra-passe para a instalação do SIOS DataKeeper][sap-ha-guide-figure-3034]

   _**Figura 44:** Introduza o nome de utilizador de domínio e a palavra-passe para a instalação do SIOS DataKeeper_

5. Instale a chave de licença para a sua instância do SIOS DataKeeper como mostrado na figura 45.

   ![Figura 45: Introduza a chave de licença do SIOS DataKeeper][sap-ha-guide-figure-3035]

   _**Figura 45:** Introduza a chave de licença do SIOS DataKeeper_

6. Quando lhe for pedido, reinicie a máquina virtual.

#### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Configurar o SIOS DataKeeper

Depois de instalar o SIOS DataKeeper em ambos os nós, terá de iniciar a configuração. O objetivo da configuração é ter a replicação de dados síncrona entre os outros discos anexados a cada uma das máquinas virtuais.

1. Inicie a ferramenta de configuração e gerenciamento de DataKeeper e, em seguida, selecione **ligar servidor**. (Na figura 46, esta opção é com círculos em vermelho.)

   ![Figura 46: Ferramenta de configuração e gerenciamento do SIOS DataKeeper][sap-ha-guide-figure-3036]

   _**Figura 46:** Ferramenta de configuração e gerenciamento do SIOS DataKeeper_

2. Introduza o nome ou endereço de TCP/IP do nó primeiro que a ferramenta de gerenciamento e a configuração deve se conectar para e, numa segunda etapa, o segundo nó.

   ![Figura 47: Inserir o nome ou endereço de TCP/IP do nó primeiro que a ferramenta de gerenciamento e a configuração deve se conectar a e, numa segunda etapa, o segundo nó][sap-ha-guide-figure-3037]

   _**Figura 47:** Inserir o nome ou endereço de TCP/IP do nó primeiro que a ferramenta de gerenciamento e a configuração deve se conectar a e, numa segunda etapa, o segundo nó_

3. Crie a tarefa de replicação entre os dois nós.

   ![Figura 48: Criar uma tarefa de replicação][sap-ha-guide-figure-3038]

   _**Figura 48:** Criar uma tarefa de replicação_

   Um assistente orienta-o ao longo do processo de criação de uma tarefa de replicação.
4. Defina o nome, o endereço TCP/IP e o volume de disco de nó de origem.

   ![Figura 49: Definir o nome da tarefa de replicação][sap-ha-guide-figure-3039]

   _**Figura 49:** Definir o nome da tarefa de replicação_

   ![Figura 50: Definir a base de dados para o nó, que deve ser o nó de origem atual][sap-ha-guide-figure-3040]

   _**Figura 50:** Definir a base de dados para o nó, que deve ser o nó de origem atual_

5. Defina o nome, o endereço TCP/IP e o volume de disco do nó de destino.

   ![Figura 51: Definir a base de dados para o nó, que deve ser o nó de destino atual][sap-ha-guide-figure-3041]

   _**Figura 51:** Definir a base de dados para o nó, que deve ser o nó de destino atual_

6. Defina os algoritmos de compactação. No nosso exemplo, recomendamos que comprimir o fluxo de replicação. Especialmente em situações de ressincronização, a compressão do fluxo de replicação reduz drasticamente o tempo de ressincronização. Tenha em atenção que a compactação utiliza os recursos de CPU e RAM de uma máquina virtual. À medida que a taxa de compressão aumenta, assim como o volume de recursos da CPU utilizado. Também pode ajustar esta definição mais tarde.

7. Outra configuração que precisa verificar é se ocorre a replicação assíncrona ou síncrona. *Quando protege configurações de SAP ASCS/SCS, tem de utilizar a replicação síncrona*.  

   ![Figura 52: Definir os detalhes de replicação][sap-ha-guide-figure-3042]

   _**Figura 52:** Definir os detalhes de replicação_

8. Defina se o volume que é replicado pela tarefa de replicação deve ser representado para uma configuração de cluster de Clustering de ativação pós-falha do Windows Server como um disco partilhado. Para a configuração de SAP ASCS/SCS, selecione **Sim** para que o cluster do Windows vê o volume replicado como um disco partilhado que pode ser usado como um volume de cluster.

   ![Figura 53: Selecione Sim para definir o volume replicado como um volume de cluster][sap-ha-guide-figure-3043]

   _**Figura 53:** Selecione **Sim** para definir o volume replicado como um volume de cluster_

   Depois de criar o volume, a ferramenta de configuração e gerenciamento de DataKeeper mostra que a tarefa de replicação está ativa.

   ![Figura 54: DataKeeper espelhamento síncrono para o disco de partilha da SAP ASCS/SCS está ativo][sap-ha-guide-figure-3044]

   _**Figura 54:** DataKeeper espelhamento síncrono para o disco de partilha da SAP ASCS/SCS está ativo_

   Gestor de clusters de ativação pós-falha mostra agora o disco como um disco de DataKeeper, conforme mostrado na figura 55.

   ![Figura 55: Gestor de clusters de ativação pós-falha mostra o disco que DataKeeper replicados][sap-ha-guide-figure-3045]

   _**Figura 55:** Gestor de clusters de ativação pós-falha mostra o disco que DataKeeper replicados_

## <a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Instalar o sistema SAP NetWeaver

Não Descrevemos a configuração do DBMS como as configurações variam consoante o sistema do DBMS que utilizar. No entanto, partimos do princípio de que as preocupações de elevada disponibilidade com o DBMS são tratadas com as funcionalidades de que fornecedores diferentes DBMS suportam para o Azure. Por exemplo, Always On ou espelhamento de banco de dados para SQL Server e Oracle Data Guard para bases de dados Oracle. No cenário que usamos neste artigo, não adicionamos mais proteção para o DBMS.

Não há nenhuma consideração especial ao diferentes serviços do DBMS interagirem com esse tipo de configuração de SAP ASCS/SCS em cluster no Azure.

> [!NOTE]
> Os procedimentos de instalação de sistemas do SAP NetWeaver ABAP, sistemas de Java e sistemas ABAP + Java são quase idênticos. A diferença mais significativa é que um sistema SAP ABAP tem uma instância do ASCS. O sistema SAP Java tem uma instância SCS. O sistema SAP ABAP + Java tem uma instância do ASCS e uma SCS instância em execução no mesmo grupo de cluster de ativação pós-falha de Microsoft. As diferenças de instalação para cada pilha de instalação SAP NetWeaver explicitamente são mencionadas. Pode assumir que todas as outras partes são os mesmos.  
>
>

### <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Instalar o SAP com uma instância do ASCS/SCS de elevada disponibilidade

> [!IMPORTANT]
> Certifique-se de que não coloque o ficheiro de paginação em volumes DataKeeper espelhado. DataKeeper não suporta volumes espelhados. Pode deixar o ficheiro de paginação na unidade temporária D de uma máquina virtual do Azure, que é a predefinição. Se ainda não estiver lá, mova o ficheiro de paginação do Windows para a unidade d: da sua máquina virtual do Azure.
>
>

A instalação de SAP com uma instância do ASCS/SCS de elevada disponibilidade envolve estas tarefas:

- Criar um nome de anfitrião virtual para a instância do SAP ASCS/SCS em cluster
- Instalar o SAP primeiro nó de cluster
- Modificar o perfil SAP da instância do ASCS/SCS
- Adicionar uma porta de sonda
- Abrir a porta de sonda de firewall do Windows

#### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Criar um nome de anfitrião virtual para a instância do SAP ASCS/SCS em cluster

1. No Gestor de DNS do Windows, crie uma entrada DNS para o nome de anfitrião virtual de instância do ASCS/SCS.

   > [!IMPORTANT]
   > O endereço IP que atribuir como o nome de anfitrião virtual de instância do ASCS/SCS tem de ser o mesmo que o endereço IP que atribuiu ao balanceador de carga do Azure (**<*SID*> - lb - ascs**).  
   >
   >

   O endereço IP do nome de anfitrião do SAP ASCS/SCS virtual (**pr1-ascs-sap**) é o mesmo que o endereço IP do Balanceador de carga do Azure (**pr1-lb-ascs**).

   ![Figura 56: Definir a entrada DNS para o nome virtual do SAP ASCS/SCS cluster e o endereço de TCP/IP][sap-ha-guide-figure-3046]

   _**Figura 56:** Definir a entrada DNS para o nome virtual do SAP ASCS/SCS cluster e o endereço de TCP/IP_

2. Para definir o endereço IP atribuído para o nome de anfitrião virtual, selecione **Gestor de DNS** > **domínio**.

   ![Figura 57: Novo nome virtual e endereço de TCP/IP para a configuração de cluster do SAP ASCS/SCS][sap-ha-guide-figure-3047]

   _**Figura 57:** Novo nome virtual e endereço de TCP/IP para a configuração de cluster do SAP ASCS/SCS_

#### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Instalar o SAP primeiro nó de cluster

1. Executar a primeira opção de nó de cluster no nó de cluster A. Por exemplo, na **pr1-ascs-0** anfitrião.
2. Para manter as predefinições de portas para o Balanceador de carga interno do Azure, selecione:

   * **Sistema ABAP**: **ASCS** número de instâncias **00**
   * **Sistema de Java**: **SCS** número de instâncias **01**
   * **Sistema ABAP + Java**: **ASCS** número de instâncias **00** e **SCS** número de instâncias **01**

   Para utilizar números de instância que não seja 00 para a instância do ASCS de ABAP e 01 para a instância de SCS de Java, primeiro terá de alterar a carga interno do Azure balanceador padrão de balanceamento de carga regras, descritas em [alterar a regras para de balanceamento de carga do padrão ASCS/SCS o Balanceador de carga interno do Azure][sap-ha-guide-8.9].

As próxima algumas tarefas não são descritas na documentação de instalação padrão do SAP.

> [!NOTE]
> A documentação de instalação SAP descreve como instalar o primeiro nó de cluster do ASCS/SCS.
>
>

#### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Modificar o perfil SAP da instância do ASCS/SCS

Tem de adicionar um novo parâmetro de perfil. O parâmetro de perfil impede que as ligações entre os processos de trabalho SAP e o servidor de colocar em fila fechar quando estão ociosos durante muito tempo. Mencionamos o cenário de problema no [adicionar entradas de Registro em ambos os nós de cluster da instância do SAP ASCS/SCS][sap-ha-guide-8.11]. Nesta secção, também introduzimos duas alterações para alguns parâmetros de ligação de TCP/IP básicos. Na segunda etapa, precisa definir o servidor de colocar em fila para enviar um `keep_alive` Sinalizar para que as ligações não atingem o limite de inatividade do Balanceador de carga interno do Azure.

Para modificar o perfil SAP da instância do ASCS/SCS:

1. Adicione este parâmetro de perfil para o perfil de instância do SAP ASCS/SCS:

   ```
   enque/encni/set_so_keepalive = true
   ```
   No nosso exemplo, o caminho é:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Por exemplo, para o perfil de instância do SAP SCS e o caminho correspondente:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. Para aplicar as alterações, reinicie a instância do SAP ASCS /SCS.

#### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Adicionar uma porta de sonda

Utilize a funcionalidade de sonda do Balanceador de carga interno para tornar a configuração de cluster completo funcionar com o Azure Load Balancer. O Balanceador de carga interno do Azure, normalmente, distribui a carga de trabalho de entrada igualmente entre máquinas de virtuais participantes. No entanto, isso não funcionará em algumas configurações de cluster porque apenas uma instância está ativa. A outra instância é passiva e não pode aceitar qualquer um da carga de trabalho. Uma funcionalidade de pesquisa ajuda quando o Balanceador de carga interno do Azure atribui trabalho apenas para uma instância do Active Directory. Com a funcionalidade de pesquisa, o Balanceador de carga interno pode detectar quais as instâncias estão ativas e, em seguida, apenas a instância com a carga de trabalho de destino.

Para adicionar uma porta de sonda:

1. Verifique o atual **ProbePort** definição executando o seguinte comando do PowerShell. Executá-lo de dentro de uma das máquinas virtuais na configuração do cluster.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

2. Defina uma porta de sonda. O número de porta de sonda de predefinido é **0**. No nosso exemplo, vamos utilizar a porta de sonda **62000**.

   ![Figura 58: A porta de sonda de configuração de cluster é 0 por predefinição][sap-ha-guide-figure-3048]

   _**Figura 58:** A porta de sonda de configuração de cluster predefinido é 0_

   O número da porta é definido em modelos do Resource Manager do SAP do Azure. Pode atribuir o número de porta no PowerShell.

   Para definir um novo valor de ProbePort para o **SAP <*SID*> IP** recurso de cluster, execute o seguinte script do PowerShell. Atualize as variáveis do PowerShell para o seu ambiente. Depois do script é executado, será solicitado para reiniciar o grupo de cluster do SAP para ativar as alterações.

   ```PowerShell
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

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```

   ![Figura 59: Sonda a porta de cluster depois de definir o novo valor][sap-ha-guide-figure-3049]

   _**Figura 59:** Sonda a porta de cluster depois de definir o novo valor_

#### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Abra a porta de sonda de firewall do Windows

Terá de abrir uma porta de sonda de firewall do Windows em ambos os nós de cluster. Utilize o seguinte script para abrir uma porta de sonda de firewall do Windows. Atualize as variáveis do PowerShell para o seu ambiente.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

O **ProbePort** está definida como **62000**. Agora pode aceder à partilha de ficheiros  **\\\ascsha-clsap\sapmnt** a partir de outros anfitriões, como, por exemplo, desde **ascsha dbas**.

### <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Instalar a instância de base de dados

Para instalar a instância de base de dados, siga o processo descrito na documentação de instalação do SAP.

### <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Instalar o segundo nó de cluster

Para instalar o segundo cluster, siga os passos no guia de instalação de SAP.

### <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Alterar o tipo de início da instância de serviço do Windows de ERS SAP

Alterar o tipo de início do serviço Windows do SAP ERS para **automático (início atrasado)** em ambos os nós de cluster.

![Figura 60: Altere o tipo de serviço para a instância do SAP ERS para automático atrasado][sap-ha-guide-figure-3050]

_**Figura 60:** Altere o tipo de serviço para a instância do SAP ERS para automático atrasado_

### <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Instalar o servidor de principal da aplicação SAP

Instalar a instância de servidor de aplicativo principal (PAS) <*SID*> - injeção de dependência - 0 na máquina virtual que tiver designado para alojar os PAS. Não há nenhuma dependência nas definições específicas do DataKeeper ou do Azure.

### <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Instalar o servidor de aplicações adicionais do SAP

Instale um servidor SAP aplicativo adicionais (AAS) em todas as máquinas virtuais que tiver designado para alojar uma instância de servidor de aplicações SAP. Por exemplo, num <*SID*> - injeção de dependência - 1 para <*SID*> - injeção de dependência -&lt;n&gt;.

> [!NOTE]
> Isto conclui a instalação de um sistema de SAP NetWeaver de elevada disponibilidade. Em seguida, continue com a ativação pós-falha de teste.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testar a ativação pós-falha de instância do SAP ASCS/SCS e os replicação SIOS
É fácil de testar e monitorar um failover de instâncias do SAP ASCS/SCS e a replicação de discos do SIOS utilizando a ferramenta de Gestor de clusters de ativação pós-falha e o SIOS DataKeeper gestão e a configuração.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> Instância do SAP ASCS/SCS está em execução no nó de cluster A

O **SAP PR1** grupo de cluster está em execução no nó de cluster A. Por exemplo, num **pr1-ascs-0**. Atribuir a unidade de disco S, partilhada que é parte do **SAP PR1** grupo de cluster e que utiliza a instância do ASCS/SCS, colocar em cluster a do nó.

![Figura 61: Gestor de clusters de ativação pós-falha: O grupo de cluster SAP < SID > está em execução no nó de cluster A][sap-ha-guide-figure-5000]

_**Figura 61:** Gestor de clusters de ativação pós-falha: O SAP <*SID*> grupo de cluster está em execução no nó de cluster A_

Na ferramenta de configuração e gerenciamento do SIOS DataKeeper, pode ver que os dados de disco partilhado são replicados sincronizadamente da unidade de volume de origem S no nó de cluster A para a unidade do volume de destino S no nó de cluster B. Por exemplo, são replicado desde **pr1-ascs-0 [10.0.0.40]** ao **pr1-ascs-1 [10.0.0.41]**.

![Figura 62: No SIOS DataKeeper, replicar o volume local do nó de cluster A para o nó de cluster B][sap-ha-guide-figure-5001]

_**Figura 62:** No SIOS DataKeeper, replicar o volume local do nó de cluster A para o nó de cluster B_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Ativação pós-falha do nó para nó B

1. Escolha uma destas opções para iniciar uma ativação pós-falha do SAP <*SID*> grupo de cluster de nó de cluster A para o nó de cluster b:
   - Utilize o Gestor de clusters de ativação pós-falha  
   - Utilizar o PowerShell do Cluster de ativação pós-falha

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Reiniciar um nó de cluster no sistema de operativo convidado do Windows (esta ação inicia uma ativação pós-falha automática do SAP <*SID*> grupo de cluster de nó para nó B).  
3. Reinicie nós de cluster A partir do portal do Azure (esta ação inicia uma ativação pós-falha automática do SAP <*SID*> grupo de cluster de nó para nó B).  
4. Reinicie um nó de cluster, com o Azure PowerShell (esta ação inicia uma ativação pós-falha automática do SAP <*SID*> grupo de cluster de nó para nó B).

   Após a ativação pós-falha, a SAP <*SID*> grupo de cluster está em execução no nó de cluster B. Por exemplo, ele está em execução no **pr1-ascs-1**.

   ![Figura 63: No Gestor de clusters de ativação pós-falha, o grupo de cluster SAP < SID > está em execução no nó de cluster B][sap-ha-guide-figure-5002]

   _**Figura 63**: Na ativação pós-falha Gestor de clusters, o SAP <*SID*> grupo de cluster está em execução no nó de cluster B_

   O disco partilhado está agora montado num cluster de nó B. o SIOS DataKeeper é replicar os dados da unidade de volume de origem S no nó de cluster B para unidade de volume de destino S no nó de cluster A. Por exemplo, está a replicar partir **pr1-ascs-1 [10.0.0.41]** ao **pr1-ascs-0 [10.0.0.40]**.

   ![Figura 64: O SIOS DataKeeper replica o volume local a partir do nó de cluster B para um nó de cluster][sap-ha-guide-figure-5003]

   _**Figura 64:** O SIOS DataKeeper replica o volume local a partir do nó de cluster B para um nó de cluster_
