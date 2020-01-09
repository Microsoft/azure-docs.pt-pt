---
title: Preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e um disco compartilhado para SAP ASCS/SCS | Microsoft Docs
description: Saiba como preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e um disco compartilhado para uma instância do SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e4de954d55725f36d48d09ac46ef3700787d937b
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647650"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e um disco compartilhado para SAP ASCS/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

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
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f


[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Configuração de alta disponibilidade do SAP multi-SID)

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

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows][Logo_Windows] Windows
>

Este artigo descreve as etapas necessárias para preparar a infraestrutura do Azure para instalar e configurar um sistema SAP de alta disponibilidade em um cluster de failover do Windows usando um *disco compartilhado de cluster* como uma opção para clustering de uma instância do SAP ASCS.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a instalação, leia este artigo:

* [Guia de arquitetura: cluster de uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado do cluster][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Preparar a infraestrutura para o modelo de arquitetura 1
Azure Resource Manager modelos para SAP ajudam a simplificar a implantação de recursos necessários.

Os modelos de três camadas no Azure Resource Manager também oferecem suporte a cenários de alta disponibilidade. Por exemplo, o modelo de arquitetura 1 tem dois clusters. Cada cluster é um ponto único de falha do SAP para SAP ASCS/SCS e DBMS.

Aqui está onde você pode obter Azure Resource Manager modelos para o cenário de exemplo que descrevemos neste artigo:

* [Imagem do Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Imagem do Azure Marketplace usando o Azure Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Imagem personalizada usando Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Para preparar a infraestrutura para o modelo de arquitetura 1:

- No portal do Azure, no painel **parâmetros** , na caixa **SYSTEMAVAILABILITY** , selecione **ha**.

  ![Figura 1: definir parâmetros de Azure Resource Manager de alta disponibilidade do SAP][sap-ha-guide-figure-3000]

_**Figura 1:** Definir parâmetros de Azure Resource Manager de alta disponibilidade do SAP_


  Os modelos criam:

  * **Máquinas virtuais**:
    * Máquinas virtuais do servidor de aplicativos SAP: \<SAPSystemSID\>-di-\<número\>
    * Máquinas virtuais de cluster ASCS/SCS: \<SAPSystemSID\>-ASCS-número de\<\>
    * Cluster DBMS: \<SAPSystemSID\>-DB-\<número\>

  * **Placas de rede para todas as máquinas virtuais, com endereços IP associados**:
    * \<SAPSystemSID\>-nic-di-\<Number\>
    * \<SAPSystemSID\>-nic-ascs-\<Number\>
    * \<SAPSystemSID\>-nic-db-\<Number\>

  * **Contas de armazenamento do Azure (somente discos não gerenciados)** :

  * **Grupos de disponibilidade** para:
    * Máquinas virtuais do servidor de aplicativos SAP: \<SAPSystemSID\>-avset-di
    * Máquinas virtuais de cluster do SAP ASCS/SCS: \<SAPSystemSID\>-avset-ASCS
    * Máquinas virtuais do cluster DBMS: \<SAPSystemSID\>-avset-DB

  * **Balanceador de carga interno do Azure**:
    * Com todas as portas para a instância do ASCS/SCS e o endereço IP \<SAPSystemSID\>-lb-ASCS
    * Com todas as portas para o SQL Server DBMS e o endereço IP \<SAPSystemSID\>-lb-DB

  * **Grupo de segurança de rede**: \<SAPSystemSID\>-NSG-ASCs-0  
    * Com uma porta de RDP (Open external protocolo RDP) para a máquina virtual \<SAPSystemSID\>-ASCs-0

> [!NOTE]
> Todos os endereços IP das placas de rede e dos balanceadores de carga internos do Azure são dinâmicos por padrão. Altere-os para endereços IP estáticos. Descreveremos como fazer isso posteriormente neste artigo.
>
>

## <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Implantar máquinas virtuais com conectividade de rede corporativa (entre locais) para usar na produção
Para sistemas SAP de produção, implante máquinas virtuais do Azure com [conectividade de rede corporativa (entre instalações)][planning-guide-2.2] usando o gateway de VPN do Azure ou o Azure ExpressRoute.

> [!NOTE]
> Você pode usar sua instância de rede virtual do Azure. A rede virtual e a sub-rede já foram criadas e preparadas.
>
>

1. No portal do Azure, no painel **parâmetros** , na caixa **NEWOREXISTINGSUBNET** , selecione **existente**.
2. Na caixa **sub-rede** , adicione a cadeia de caracteres completa da sua ID de sub-rede de rede do Azure preparada na qual você planeja implantar suas máquinas virtuais do Azure.
3. Para obter uma lista de todas as sub-redes de rede do Azure, execute este comando do PowerShell:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   O campo **ID** mostra o valor para a ID de sub-rede.
4. Para obter uma lista de todos os valores de ID de sub-rede, execute este comando do PowerShell:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   A ID da sub-rede tem esta aparência:

   ```
   /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
   ```

## <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Implantar instâncias SAP somente na nuvem para teste e demonstração
Você pode implantar seu sistema SAP de alta disponibilidade em um modelo de implantação somente em nuvem. Esse tipo de implantação é principalmente útil para casos de uso de demonstração e teste. Não é adequado para casos de uso de produção.

- No portal do Azure, no painel **parâmetros** , na caixa **NEWOREXISTINGSUBNET** , selecione **novo**. Deixe o campo **subnetid** vazio.

  O modelo SAP Azure Resource Manager cria automaticamente a rede virtual e a sub-rede do Azure.

> [!NOTE]
> Você também precisa implantar pelo menos uma máquina virtual dedicada para Active Directory e o serviço DNS na mesma instância de rede virtual do Azure. O modelo não cria essas máquinas virtuais.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Preparar a infraestrutura para o modelo arquitetônico 2

Você pode usar este modelo de Azure Resource Manager para SAP para ajudar a simplificar a implantação de recursos de infraestrutura necessários para o modelo 2 de arquitetura do SAP.

Aqui está onde você pode obter Azure Resource Manager modelos para este cenário de implantação:

* [Imagem do Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Imagem do Azure Marketplace usando Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Imagem personalizada usando Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Preparar a infraestrutura para o modelo arquitetônico 3

Você pode preparar a infraestrutura e configurar o SAP para vários SID. Por exemplo, você pode adicionar uma instância do SAP ASCS/SCS adicional a uma configuração de cluster *existente* . Para obter mais informações, consulte [Configurar uma instância adicional do SAP ASCS/SCS para uma configuração de cluster existente para criar uma configuração de vários SID do SAP no Azure Resource Manager][sap-ha-multi-sid-guide].

Se você quiser criar um novo cluster de vários SID, poderá usar os [modelos de início rápido](https://github.com/Azure/azure-quickstart-templates)de vários Sid no github.

Para criar um novo cluster de vários SID, você deve implantar os três modelos a seguir:

* [Modelo ASCS/SCS](#ASCS-SCS-template)
* [Modelo de banco de dados](#database-template)
* [Modelo de servidores de aplicativos](#application-servers-template)

As seções a seguir têm mais detalhes sobre os modelos e parâmetros que você precisa fornecer nos modelos.

### <a name="ASCS-SCS-template"></a>Modelo ASCS/SCS

O modelo ASCS/SCS implanta duas máquinas virtuais que você pode usar para criar um cluster de failover do Windows Server que hospeda várias instâncias ASCS/SCS.

Para configurar o modelo de vários SID do ASCS/SCS, no modelo de vários SID do [ASCS/SCS][sap-templates-3-tier-multisid-xscs-marketplace-image] ou no [modelo de vários SID do ASCS/SCS usando Managed disks][sap-templates-3-tier-multisid-xscs-marketplace-image-md], insira valores para os seguintes parâmetros:

- **Prefixo de recurso**: defina o prefixo de recurso, que é usado para prefixar todos os recursos que são criados durante a implantação. Como os recursos não pertencem a apenas um sistema SAP, o prefixo do recurso não é o SID de um sistema SAP.  O prefixo deve ter entre três e seis caracteres.
- **Tipo de pilha**: selecione o tipo de pilha do sistema SAP. Dependendo do tipo de pilha, Azure Load Balancer tem apenas um (ABAP ou Java) ou dois (ABAP + Java) endereços IP privados por sistema SAP.
- **Tipo de so**: selecione o sistema operacional das máquinas virtuais.
- **Contagem de sistema SAP**: selecione o número de sistemas SAP que você deseja instalar neste cluster.
- **Disponibilidade do sistema**: selecione **ha**.
- Nome de usuário do **administrador e senha do administrador**: Crie um novo usuário que possa ser usado para entrar no computador.
- **Sub-rede nova ou existente**: Defina se deseja criar uma nova rede virtual e uma sub-rede ou usar uma sub-rede existente. Se você já tiver uma rede virtual conectada à sua rede local, selecione **existente**.
- **ID da sub-rede**: se você deseja implantar a VM em uma VNet existente em que você tem uma sub-rede definida, a VM deve ser atribuída, nomear a ID dessa sub-rede específica. A ID geralmente tem esta aparência:

  /subscriptions/\<ID da assinatura\>/resourceGroups/\<nome do grupo de recursos\>/providers/Microsoft.Network/virtualNetworks/\<nome da rede virtual\>/Subnets/\<nome da sub-rede\>

O modelo implanta um Azure Load Balancer instância, que dá suporte a vários sistemas SAP:

- As instâncias ASCS são configuradas para o número de instância 00, 10, 20...
- As instâncias do SCS são configuradas para o número de instância 01, 11, 21...
- As instâncias do ASCS enqueue Replication Server (ERS) (somente Linux) são configuradas para o número de instância 02, 12, 22...
- As instâncias do SCS ERS (somente Linux) são configuradas para o número de instância 03, 13, 23...

O balanceador de carga contém 1 VIP (2 para Linux), 1x VIP para ASCS/SCS e 1x VIP para ERS (somente Linux).

#### <a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a>Portas SAP ASCS/SCS
A lista a seguir contém todas as regras de balanceamento de carga (em que x é o número do sistema SAP, por exemplo, 1, 2, 3...):
- Portas específicas do Windows para cada sistema SAP: 445, 5985
- Portas ASCS (número de instância x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- Portas SCS (número de instância X1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- Portas ASCS ERS no Linux (número de instância x2): 33X2, 5x213, 5x214, 5x216
- Portas SCS ERS no Linux (número da instância X3): 33x3, 5x313, 5x314, 5x316

O balanceador de carga está configurado para usar as seguintes portas de investigação (em que x é o número do sistema SAP, por exemplo, 1, 2, 3...):
- Porta de investigação do balanceador de carga interno do ASCS/SCS: 620x0
- Porta de investigação do balanceador de carga interno do ERS (somente Linux): 621x2

### <a name="database-template"></a>Modelo de banco de dados

O modelo de banco de dados implanta uma ou duas máquinas virtuais que você pode usar para instalar o RDBMS (sistema de gerenciamento de banco de dados relacional) para um sistema SAP. Por exemplo, se você implantar um modelo ASCS/SCS para cinco sistemas SAP, será necessário implantar esse modelo cinco vezes.

Para configurar o modelo de vários Sid de banco de dados, no modelo de vários SID [de banco de dados ou][sap-templates-3-tier-multisid-db-marketplace-image] [modelo de vários Sid][sap-templates-3-tier-multisid-db-marketplace-image-md]de banco de dados usando Managed disks, insira valores para os seguintes parâmetros:

- **ID do sistema SAP**: Insira a ID do sistema SAP do sistema SAP que você deseja instalar. A ID é usada como um prefixo para os recursos que são implantados.
- **Tipo de so**: selecione o sistema operacional das máquinas virtuais.
- **DbType**: selecione o tipo de banco de dados que você deseja instalar no cluster. Selecione **SQL** se você deseja instalar Microsoft SQL Server. Selecione **Hana** se você planeja instalar SAP Hana nas máquinas virtuais. Certifique-se de selecionar o tipo de sistema operacional correto. Selecione **Windows** para SQL e selecione uma distribuição do Linux para Hana. Azure Load Balancer que está conectado às máquinas virtuais está configurado para dar suporte ao tipo de banco de dados selecionado:
  * **SQL**: o balanceador de carga balancear a carga da porta 1433. Certifique-se de usar essa porta para a instalação do SQL Server AlwaysOn.
  * **Hana**: o balanceador de carga equilibra a carga das portas 35015 e 35017. Certifique-se de instalar SAP HANA com o número de instância **50**.
  O balanceador de carga usa a porta de investigação 62550.
- **Tamanho do sistema SAP**: defina o número de SAPs que o novo sistema fornece. Se você não tiver certeza de quantos SAPS o sistema precisará, pergunte ao seu parceiro de tecnologia SAP ou ao integrador de sistemas.
- **Disponibilidade do sistema**: selecione **ha**.
- Nome de usuário do **administrador e senha do administrador**: Crie um novo usuário que possa ser usado para entrar no computador.
- **ID da sub-rede**: Insira a ID da sub-rede que você usou durante a implantação do modelo ASCS/SCS ou a ID da sub-rede que foi criada como parte da implantação do modelo ASCS/SCS.

### <a name="application-servers-template"></a>Modelo de servidores de aplicativos

O modelo de servidores de aplicativos implanta duas ou mais máquinas virtuais que podem ser usadas como instâncias do servidor de aplicativos SAP para um sistema SAP. Por exemplo, se você implantar um modelo ASCS/SCS para cinco sistemas SAP, será necessário implantar esse modelo cinco vezes.

Para configurar o modelo de vários SID de servidores de aplicativos, no modelo de vários Sid de [servidores de aplicativos][sap-templates-3-tier-multisid-apps-marketplace-image] ou [no modelo de vários Sid de servidores de aplicativos usando Managed disks][sap-templates-3-tier-multisid-apps-marketplace-image-md], insira valores para os seguintes parâmetros:

  -  **ID do sistema SAP**: Insira a ID do sistema SAP do sistema SAP que você deseja instalar. A ID é usada como um prefixo para os recursos que são implantados.
  -  **Tipo de so**: selecione o sistema operacional das máquinas virtuais.
  -  **Tamanho do sistema SAP**: o número de SAPs que o novo sistema fornece. Se você não tiver certeza de quantos SAPS o sistema precisará, pergunte ao seu parceiro de tecnologia SAP ou ao integrador de sistemas.
  -  **Disponibilidade do sistema**: selecione **ha**.
  -  Nome de usuário do **administrador e senha do administrador**: Crie um novo usuário que possa ser usado para entrar no computador.
  -  **ID da sub-rede**: Insira a ID da sub-rede que você usou durante a implantação do modelo ASCS/SCS ou a ID da sub-rede que foi criada como parte da implantação do modelo ASCS/SCS.


## <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Rede virtual do Azure
Em nosso exemplo, o espaço de endereço da instância de rede virtual do Azure é 10.0.0.0/16. Há uma sub-rede chamada subnet, com um intervalo de endereços de 10.0.0.0/24. Todas as máquinas virtuais e balanceadores de carga internos são implantados nessa rede virtual.

> [!IMPORTANT]
> Não faça nenhuma alteração nas configurações de rede dentro do sistema operacional convidado. Isso inclui endereços IP, servidores DNS e sub-rede. Defina todas as suas configurações de rede no Azure. O serviço de protocolo DHCP propaga suas configurações.
>
>

## <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>Endereços IP de DNS

Para definir os endereços IP de DNS necessários, conclua as seguintes etapas:

1. No portal do Azure, no painel **servidores DNS** , verifique se a opção **servidores DNS** da rede virtual está definida como **DNS personalizado**.
2. Selecione suas configurações com base no tipo de rede que você tem. Para obter mais informações, consulte os seguintes recursos:
   * [Conectividade de rede corporativa (entre instalações)][planning-guide-2.2]: Adicione os endereços IP dos servidores DNS locais.  
   Você pode estender os servidores DNS locais para as máquinas virtuais que estão em execução no Azure. Nesse cenário, você pode adicionar os endereços IP das máquinas virtuais do Azure em que você executa o serviço DNS.
   * Para implantações de VM isoladas no Azure: implante uma máquina virtual adicional na mesma instância de rede virtual que serve como um servidor DNS. Adicione os endereços IP das máquinas virtuais do Azure que você configurou para executar o serviço DNS.

   ![Figura 2: configurar servidores DNS para a rede virtual do Azure][sap-ha-guide-figure-3001]

   _**Figura 2:** Configurar servidores DNS para a rede virtual do Azure_

   > [!NOTE]
   > Se você alterar os endereços IP dos servidores DNS, será necessário reiniciar as máquinas virtuais do Azure para aplicar a alteração e propagar os novos servidores DNS.
   >
   >

Em nosso exemplo, o serviço DNS é instalado e configurado nessas máquinas virtuais do Windows:

| Função de máquina virtual | Nome do host da máquina virtual | Nome da placa de rede | Endereço IP estático |
| --- | --- | --- | --- |
| Primeiro servidor DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Segundo servidor DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

## <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Nomes de host e endereços IP estáticos para a instância clusterizada do SAP ASCS/SCS e instância clusterizada do DBMS

Para a implantação local, você precisará desses nomes de host e endereços IP reservados:

| Função de nome de host virtual | Nome do host virtual | Endereço IP estático virtual |
| --- | --- | --- |
| Nome de host virtual do primeiro cluster do SAP ASCS/SCS (para gerenciamento de cluster) |pr1-ascs-vir |10.0.0.42 |
| Nome de host virtual da instância do SAP ASCS/SCS |pr1-ascs-sap |10.0.0.43 |
| Nome de host virtual do segundo cluster do SAP DBMS (gerenciamento de cluster) |pr1-dbms-vir |10.0.0.32 |

Ao criar o cluster, crie os nomes de host virtual PR1-ASCs-vir e PR1-DBMS-vir e os endereços IP associados que gerenciam o próprio cluster. Para obter informações sobre como fazer isso, consulte [coletar nós de cluster em uma configuração de cluster][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config].

Você pode criar manualmente os outros dois nomes de host virtual, PR1-ASCs-SAP e PR1-DBMS-SAP, e os endereços IP associados, no servidor DNS. A instância clusterizada do SAP ASCS/SCS e a instância do DBMS clusterizado usam esses recursos. Para obter informações sobre como fazer isso, consulte [criar um nome de host virtual para uma instância clusterizada do SAP ASCS/SCS][sap-ha-guide-9.1.1].

## <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Definir endereços IP estáticos para as máquinas virtuais SAP
Depois de implantar as máquinas virtuais para usar em seu cluster, você precisará definir endereços IP estáticos para todas as máquinas virtuais. Faça isso na configuração de rede virtual do Azure e não no sistema operacional convidado.

1. No portal do Azure, selecione **grupo de recursos** > **placa de rede** > **configurações** > **endereço IP**.
2. No painel **endereços IP** , em **atribuição**, selecione **estático**. Na caixa **endereço IP** , digite o endereço IP que você deseja usar.

   > [!NOTE]
   > Se você alterar o endereço IP da placa de rede, será necessário reiniciar as máquinas virtuais do Azure para aplicar a alteração.  
   >
   >

   ![Figura 3: definir endereços IP estáticos para a placa de rede de cada máquina virtual][sap-ha-guide-figure-3002]

   _**Figura 3:** Definir endereços IP estáticos para a placa de rede de cada máquina virtual_

   Repita essa etapa para todas as interfaces de rede, ou seja, para todas as máquinas virtuais, incluindo máquinas virtuais que você deseja usar para o serviço de Active Directory ou DNS.

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

## <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Definir um endereço IP estático para o balanceador de carga interno do Azure

O modelo SAP Azure Resource Manager cria um balanceador de carga interno do Azure que é usado para o cluster de instância do SAP ASCS/SCS e o cluster DBMS.

> [!IMPORTANT]
> O endereço IP do nome de host virtual do SAP ASCS/SCS é o mesmo que o endereço IP do balanceador de carga interno do SAP ASCS/SCS: PR1-lb-ASCS.
> O endereço IP do nome virtual do DBMS é o mesmo que o endereço IP do balanceador de carga interno do DBMS: PR1-lb-DBMS.
>
>

Para definir um endereço IP estático para o balanceador de carga interno do Azure:

1. A implantação inicial define o endereço IP do balanceador de carga interno como **dinâmico**. No portal do Azure, no painel **endereços IP** , em **atribuição**, selecione **estático**.
2. Defina o endereço IP do balanceador de carga interno **PR1-lb-ASCs** para o endereço IP do nome de host virtual da instância do SAP ASCS/SCS.
3. Defina o endereço IP do balanceador de carga interno **PR1-lb-DBMS** para o endereço IP do nome de host virtual da instância do DBMS.

   ![Figura 4: definir endereços IP estáticos para o balanceador de carga interno para a instância do SAP ASCS/SCS][sap-ha-guide-figure-3003]

   _**Figura 4:** Definir endereços IP estáticos para o balanceador de carga interno para a instância do SAP ASCS/SCS_

Em nosso exemplo, temos dois balanceadores de carga internos do Azure que têm esses endereços IP estáticos:

| Função de balanceador de carga interno do Azure | Nome do balanceador de carga interno do Azure | Endereço IP estático |
| --- | --- | --- |
| Balanceador de carga interno da instância do SAP ASCS/SCS |pr1-lb-ascs |10.0.0.43 |
| Balanceador de carga interno do DBMS SAP |pr1-lb-dbms |10.0.0.33 |


## <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Regras de balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure

O modelo SAP Azure Resource Manager cria as portas de que você precisa:
* Uma instância de ABAP ASCS, com o número de instância padrão 00
* Uma instância do SCS Java, com o número de instância padrão 01

Ao instalar sua instância do SAP ASCS/SCS, você deve usar o número de instância padrão 00 para sua instância ASCS do ABAP e o número de instância padrão 01 para sua instância do SCS Java.

Em seguida, crie os pontos de extremidade de balanceamento de carga internos necessários para as portas do SAP NetWeaver.

Para criar pontos de extremidade de balanceamento de carga internos necessários, primeiro crie esses pontos de extremidade de balanceamento de carga para as portas ASCS ABAP do SAP NetWeaver:

| Nome da regra de balanceamento de carga/serviço | Números de porta padrão | Portas concretas para (instância de ASCS com número de instância 00) (ERS com 10) |
| --- | --- | --- |
| Enfileirar servidor/ *lbrule3200* |32\<InstanceNumber\> |3200 |
| Servidor de mensagens ABAP/ *lbrule3600* |36\<InstanceNumber\> |3600 |
| Mensagem interna de ABAP/ *lbrule3900* |39\<InstanceNumber\> |3900 |
| HTTP do servidor de mensagens/ *Lbrule8100* |81\<InstanceNumber\> |8100 |
| SAP Start Service ASCS HTTP/ *Lbrule50013* |5\<InstanceNumber\>13 |50013 |
| SAP Start Service ASCS HTTPS/ *Lbrule50014* |5\<InstanceNumber\>14 |50014 |
| Enfileirar replicação/ *Lbrule50016* |5\<InstanceNumber\>16 |50016 |
| SAP Start Service ERS HTTP *Lbrule51013* |5\<InstanceNumber\>13 |51013 |
| SAP Start Service ERS HTTP *Lbrule51014* |5\<InstanceNumber\>14 |51014 |
| Gerenciamento Remoto do Windows (WinRM) *Lbrule5985* | |5985 |
| *Arquivos lbrule445* de compartilhamento de arquivos | |445 |

**Tabela 1:** Números de porta das instâncias do ASCS do SAP NetWeaver ABAP

Em seguida, crie esses pontos de extremidade de balanceamento de carga para as portas SCS Java do SAP NetWeaver:

| Nome da regra de balanceamento de carga/serviço | Números de porta padrão | Portas concretas para (instância do SCS com número de instância 01) (ERS com 11) |
| --- | --- | --- |
| Enfileirar servidor/ *lbrule3201* |32\<InstanceNumber\> |3201 |
| Servidor de gateway/ *lbrule3301* |33\<InstanceNumber\> |3301 |
| Servidor de mensagens Java/ *lbrule3900* |39\<InstanceNumber\> |3901 |
| HTTP do servidor de mensagens/ *Lbrule8101* |81\<InstanceNumber\> |8101 |
| HTTP/ *Lbrule50113* do SAP Start Service SCS |5\<InstanceNumber\>13 |50113 |
| SAP Start Service SCS HTTPS/ *Lbrule50114* |5\<InstanceNumber\>14 |50114 |
| Enfileirar replicação/ *Lbrule50116* |5\<InstanceNumber\>16 |50116 |
| SAP Start Service ERS HTTP *Lbrule51113* |5\<InstanceNumber\>13 |51113 |
| SAP Start Service ERS HTTP *Lbrule51114* |5\<InstanceNumber\>14 |51114 |
| *Lbrule5985* WinRM | |5985 |
| *Arquivos lbrule445* de compartilhamento de arquivos | |445 |

**Tabela 2:** Números de porta das instâncias do SCS Java do SAP NetWeaver

![Figura 5: regras de balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure][sap-ha-guide-figure-3004]

_**Figura 5:** Regras de balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure_

Defina o endereço IP do balanceador de carga PR1-lb-DBMS para o endereço IP do nome de host virtual da instância do DBMS.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Alterar as regras de balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure

Se você quiser usar números diferentes para as instâncias do SAP ASCS ou SCS, deverá alterar os nomes e os valores de suas portas a partir dos valores padrão.

1. No portal do Azure, selecione **\<SID\>-lb-ASCs Load balancer** > **regras de balanceamento de carga**.
2. Para todas as regras de balanceamento de carga que pertencem à instância do SAP ASCS ou SCS, altere estes valores:

   * Nome
   * Porta
   * Porta de back-end

   Por exemplo, se você quiser alterar o número da instância ASCS padrão de 00 para 31, será necessário fazer as alterações para todas as portas listadas na tabela 1.

   Aqui está um exemplo de uma atualização para a porta *lbrule3200*.

   ![Figura 6: alterar as regras de balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure][sap-ha-guide-figure-3005]

   _**Figura 6:** Alterar as regras de balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure_

## <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Adicionar máquinas virtuais do Windows ao domínio

Depois de atribuir um endereço IP estático às máquinas virtuais, adicione as máquinas virtuais ao domínio.

![Figura 7: adicionar uma máquina virtual a um domínio][sap-ha-guide-figure-3006]

_**Figura 7:** Adicionar uma máquina virtual a um domínio_

## <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Adicionar entradas de registro em ambos os nós de cluster da instância do SAP ASCS/SCS

Azure Load Balancer tem um balanceador de carga interno que fecha as conexões quando as conexões ficam ociosas por um período de tempo definido (um tempo limite de ociosidade). Os processos de trabalho do SAP em instâncias de caixa de diálogo abrem conexões com o processo de enfileiramento da SAP assim que a primeira solicitação de enfileiramento/retirada da fila precisa ser enviada. Essas conexões geralmente permanecem estabelecidas até que o processo de trabalho ou o processo de enfileiramento seja reiniciado. No entanto, se a conexão estiver ociosa por um período de tempo definido, o balanceador de carga interno do Azure fechará as conexões. Isso não é um problema porque o processo de trabalho do SAP restabelece a conexão com o processo de enfileiramento, caso ele não exista mais. Essas atividades são documentadas nos rastreamentos de desenvolvedor de processos SAP, mas elas criam uma grande quantidade de conteúdo extra nesses rastreamentos. É uma boa ideia alterar o `KeepAliveTime` de TCP/IP e `KeepAliveInterval` em ambos os nós de cluster. Combine essas alterações nos parâmetros TCP/IP com parâmetros de perfil do SAP, descritos posteriormente neste artigo.

Para adicionar entradas de registro em ambos os nós de cluster da instância do SAP ASCS/SCS, primeiro adicione essas entradas de registro do Windows em ambos os nós de cluster do Windows para SAP ASCS/SCS:

| Caminho | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome da variável |`KeepAliveTime` |
| Tipo de variável |REG_DWORD (Decimal) |
| Valor |120000 |
| Link para a documentação |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**Tabela 3:** Alterar o primeiro parâmetro TCP/IP

Em seguida, adicione essa entrada de registro do Windows em ambos os nós de cluster do Windows para SAP ASCS/SCS:

| Caminho | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome da variável |`KeepAliveInterval` |
| Tipo de variável |REG_DWORD (Decimal) |
| Valor |120000 |
| Link para a documentação |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**Tabela 4:** Alterar o segundo parâmetro TCP/IP

Para aplicar as alterações, reinicie os dois nós de cluster.

## <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Configurar um cluster de failover do Windows Server para uma instância do SAP ASCS/SCS

A configuração de um cluster de failover do Windows Server para uma instância do SAP ASCS/SCS envolve estas tarefas:

- Colete os nós de cluster em uma configuração de cluster.
- Configurar uma testemunha de compartilhamento de arquivos de cluster.

### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>Coletar os nós de cluster em uma configuração de cluster

1. No assistente Adicionar funções e recursos, adicione clustering de failover a ambos os nós de cluster.
2. Configure o cluster de failover usando Gerenciador de Cluster de Failover. Em Gerenciador de Cluster de Failover, selecione **criar cluster**e, em seguida, adicione apenas o nome do primeiro cluster (nó A). Não adicionar o segundo nó ainda; Você adiciona o segundo nó em uma etapa posterior.

   ![Figura 8: Adicionar o nome do servidor ou da máquina virtual do primeiro nó do cluster][sap-ha-guide-figure-3007]

   _**Figura 8:** Adicionar o nome do servidor ou da máquina virtual do primeiro nó do cluster_

3. Insira o nome da rede (nome do host virtual) do cluster.

   ![Figura 9: inserir o nome do cluster][sap-ha-guide-figure-3008]

   _**Figura 9:** Insira o nome do cluster_

4. Depois de criar o cluster, execute um teste de validação de cluster.

   ![Figura 10: executar a verificação de validação de cluster][sap-ha-guide-figure-3009]

   _**Figura 10:** Executar a verificação de validação de cluster_

   Você pode ignorar quaisquer avisos sobre discos neste ponto do processo. Você adicionará uma testemunha de compartilhamento de arquivos e os discos de SIOS compartilhados mais tarde. Neste estágio, você não precisa se preocupar em ter um quorum.

   ![Figura 11: nenhum disco de quorum é encontrado][sap-ha-guide-figure-3010]

   _**Figura 11:** Nenhum disco de quorum foi encontrado_

   ![Figura 12: um recurso de cluster principal precisa de um novo endereço IP][sap-ha-guide-figure-3011]

   _**Figura 12:** Um recurso de cluster principal precisa de um novo endereço IP_

5. Altere o endereço IP do serviço de cluster principal. O cluster não pode ser iniciado até que você altere o endereço IP do serviço de cluster principal, pois o endereço IP do servidor aponta para um dos nós da máquina virtual. Faça isso na página **Propriedades** do recurso IP do serviço de cluster principal.

   Por exemplo, precisamos atribuir um endereço IP (em nosso exemplo, 10.0.0.42) para o nome de host virtual do cluster PR1-ASCs-vir.

   ![Figura 13: na caixa de diálogo Propriedades, altere o endereço IP][sap-ha-guide-figure-3012]

   _**Figura 13:** Na caixa de diálogo **Propriedades** , altere o endereço IP_

   ![Figura 14: atribuir o endereço IP reservado para o cluster][sap-ha-guide-figure-3013]

   _**Figura 14:** Atribuir o endereço IP reservado para o cluster_

6. Coloque o nome do host virtual do cluster online.

   ![Figura 15: o serviço principal do cluster está em execução, com o endereço IP correto][sap-ha-guide-figure-3014]

   _**Figura 15:** O serviço principal do cluster está em execução, com o endereço IP correto_

7. Adicione o segundo nó de cluster.

   Agora que o serviço de cluster principal está em execução, você pode adicionar o segundo nó de cluster.

   ![Figura 16 adicionar o segundo nó de cluster][sap-ha-guide-figure-3015]

   _**Figura 16:** Adicionar o segundo nó de cluster_

8. Insira um nome para o segundo host de nó de cluster.

   ![Figura 17: inserir o nome de host do segundo nó de cluster][sap-ha-guide-figure-3016]

   _**Figura 17:** Insira o nome de host do segundo nó de cluster_

   > [!IMPORTANT]
   > Certifique-se de que a caixa de seleção **Adicionar todo o armazenamento elegível ao cluster** *não* esteja marcada.  
   >
   >

   ![Figura 18: não marcar a caixa de seleção][sap-ha-guide-figure-3017]

   _**Figura 18:** Não *marque a* caixa de seleção_

   Você pode ignorar avisos sobre quorum e discos. Você definirá o quorum e compartilhará o disco mais tarde, conforme descrito em [instalar o sios Datakeeper Cluster Edition para um disco de compartilhamento de cluster do SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].

   ![Figura 19: ignorar avisos sobre o quorum de disco][sap-ha-guide-figure-3018]

   _**Figura 19:** Ignorar avisos sobre o quorum de disco_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Configurar uma testemunha de compartilhamento de arquivos de cluster

A configuração de uma testemunha de compartilhamento de arquivos de cluster envolve estas tarefas:

- Crie um compartilhamento de arquivos.
- Defina o quorum de testemunha de compartilhamento de arquivos em Gerenciador de Cluster de Failover.

#### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Criar um compartilhamento de arquivos

1. Selecione uma testemunha de compartilhamento de arquivos em vez de um disco de quorum. O SIOS datakeeper dá suporte a essa opção.

   Nos exemplos deste artigo, a testemunha de compartilhamento de arquivos está no Active Directory ou no servidor DNS em execução no Azure. A testemunha de compartilhamento de arquivos é chamada de domcontr-0. Como você teria configurado uma conexão VPN com o Azure (via gateway de VPN ou Azure ExpressRoute), seu serviço de Active Directory ou DNS é local e não é adequado para executar uma testemunha de compartilhamento de arquivos.

   > [!NOTE]
   > Se o serviço de Active Directory ou DNS for executado somente no local, não configure a testemunha de compartilhamento de arquivos no sistema operacional Windows Active Directory ou DNS que está sendo executado no local. A latência de rede entre os nós de cluster em execução no Azure e Active Directory ou DNS local pode ser muito grande e causar problemas de conectividade. Certifique-se de configurar a testemunha de compartilhamento de arquivos em uma máquina virtual do Azure que esteja sendo executada perto do nó do cluster.  
   >
   >

   A unidade de quorum precisa de pelo menos 1.024 MB de espaço livre. Recomendamos 2.048 MB de espaço livre para a unidade de quorum.

2. Adicione o objeto de nome do cluster.

   ![Figura 20: atribuir as permissões no compartilhamento para o objeto de nome do cluster][sap-ha-guide-figure-3019]

   _**Figura 20:** Atribuir as permissões no compartilhamento para o objeto de nome do cluster_

   Certifique-se de que as permissões incluem a autoridade para alterar os dados no compartilhamento para o objeto de nome do cluster (em nosso exemplo, PR1-ASCs-vir $).

3. Para adicionar o objeto de nome de cluster à lista, selecione **Adicionar**. Altere o filtro para verificar se há objetos de computador, além daqueles mostrados na Figura 22.

   ![Figura 21: alterar os tipos de objetos para incluir computadores][sap-ha-guide-figure-3020]

   _**Figura 21:** Alterar **tipos de objetos** para incluir computadores_

   ![Figura 22: marque a caixa de seleção computadores][sap-ha-guide-figure-3021]

   _**Figura 22:** Marque a caixa de seleção **computadores**_

4. Insira o objeto de nome do cluster, conforme mostrado na figura 21. Como o registro já foi criado, você pode alterar as permissões, como mostrado na Figura 20.

5. Selecione a guia **segurança** do compartilhamento e, em seguida, defina permissões mais detalhadas para o objeto de nome do cluster.

   ![Figura 23: definir os atributos de segurança para o objeto de nome do cluster no quorum de compartilhamento de arquivos][sap-ha-guide-figure-3022]

   _**Figura 23:** Definir os atributos de segurança para o objeto de nome do cluster no quorum de compartilhamento de arquivos_

#### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>Definir o quorum de testemunha de compartilhamento de arquivos em Gerenciador de Cluster de Failover

1. Abra o assistente para definir configurações de quorum.

   ![Figura 24: iniciar o assistente para definir configuração de quorum de cluster][sap-ha-guide-figure-3023]

   _**Figura 24:** Iniciar o assistente para definir a configuração de quorum do cluster_

2. Na página **selecionar opção de configuração de quorum** , selecione **selecionar a testemunha de quorum**.

   ![Figura 25: configurações de quorum das quais você pode escolher][sap-ha-guide-figure-3024]

   _**Figura 25:** Configurações de quorum das quais você pode escolher_

3. Na página **selecionar testemunha de quorum** , selecione **Configurar uma testemunha de compartilhamento de arquivos**.

   ![Figura 26: selecionar a testemunha de compartilhamento de arquivos][sap-ha-guide-figure-3025]

   _**Figura 26:** Selecionar a testemunha de compartilhamento de arquivos_

4. Insira o caminho UNC para o compartilhamento de arquivos (em nosso exemplo, \\domcontr-0\FSW). Para ver uma lista das alterações que você pode fazer, selecione **Avançar**.

   ![Figura 27: definir o local do compartilhamento de arquivos para o compartilhamento de testemunha][sap-ha-guide-figure-3026]

   _**Figura 27:** Definir o local do compartilhamento de arquivos para o compartilhamento de testemunha_

5. Selecione as alterações desejadas e, em seguida, selecione **Avançar**. Você precisa reconfigurar com êxito a configuração do cluster, conforme mostrado na Figura 28:  

   ![Figura 28: confirmação de que você reconfigurou o cluster][sap-ha-guide-figure-3027]

   _**Figura 28:** Confirmação de que você reconfigurou o cluster_

Depois de instalar com êxito o cluster de failover do Windows, você precisa alterar alguns limites para que eles adaptem a detecção de failover às condições no Azure. Os parâmetros a serem alterados estão documentados em [ajustando limites de rede de cluster de failover][tuning-failover-cluster-network-thresholds]. Supondo que as duas VMs que compõem a configuração de cluster do Windows para ASCS/SCS estejam na mesma sub-rede, altere os seguintes parâmetros para esses valores:

- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

Essas configurações foram testadas com os clientes e oferecem um bom comprometimento. Elas são resistentes o suficiente, mas também fornecem failover rápido o suficiente em condições de erro reais em um software SAP ou em uma falha de nó ou VM.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>Instalar o SIOS datakeeper Cluster Edition para o disco de compartilhamento de cluster do SAP ASCS/SCS

Agora você tem uma configuração de clustering de failover do Windows Server em funcionamento no Azure. Para instalar uma instância do SAP ASCS/SCS, você precisa de um recurso de disco compartilhado. Você não pode criar os recursos de disco compartilhado de que precisa no Azure. O SIOS datakeeper Cluster Edition é uma solução de terceiros que você pode usar para criar recursos de disco compartilhados.

A instalação do SIOS datakeeper Cluster Edition para o disco de compartilhamento de cluster do SAP ASCS/SCS envolve estas tarefas:

- Adicione Microsoft .NET Framework 3,5.
- Instale o SIOS datakeeper.
- Configure o SIOS datakeeper.

### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>Adicionar .NET Framework 3,5
.NET Framework 3,5 não é automaticamente ativado ou instalado no Windows Server 2012 R2. Como o SIOS datakeeper requer que o .NET esteja em todos os nós em que você instala o datakeeper, você deve instalar o .NET Framework 3,5 no sistema operacional convidado de todas as máquinas virtuais no cluster.

Há duas maneiras de adicionar .NET Framework 3,5:

- Use o assistente para adicionar funções e recursos no Windows, conforme mostrado na figura 29:

  ![Figura 29: instalar o .NET Framework 3,5 usando o assistente para adicionar funções e recursos][sap-ha-guide-figure-3028]

  _**Figura 29:** Instalar o .NET Framework 3,5 usando o assistente para adicionar funções e recursos_

  ![Figura 30: barra de progresso da instalação quando você instala o .NET Framework 3,5 usando o assistente para adicionar funções e recursos][sap-ha-guide-figure-3029]

  _**Figura 30:** Barra de progresso da instalação quando você instala o .NET Framework 3,5 usando o assistente para adicionar funções e recursos_

- Use a ferramenta de linha de comando DISM. exe. Para esse tipo de instalação, você precisa acessar o diretório SxS na mídia de instalação do Windows. Em um prompt de comandos com privilégios elevados, digite este comando:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="dd41d5a2-8083-415b-9878-839652812102"></a>Instalar o SIOS datakeeper

Instale o SIOS datakeeper Cluster Edition em cada nó do cluster. Para criar um armazenamento compartilhado virtual com o SIOS datakeeper, crie um espelho sincronizado e, em seguida, simule o armazenamento compartilhado do cluster.

Antes de instalar o software SIOS, crie o usuário de domínio DataKeeperSvc.

> [!NOTE]
> Adicione o usuário de domínio DataKeeperSvc ao grupo de Administradores local em ambos os nós de cluster.
>
>

Para instalar o SIOS datakeeper:

1. Instale o software SIOS em ambos os nós de cluster.

   ![Instalador do SIOS][sap-ha-guide-figure-3030]

   ![Figura 31: primeira página da instalação do SIOS datakeeper][sap-ha-guide-figure-3031]

   _**Figura 31:** Primeira página da instalação do SIOS datakeeper_

2. Na caixa de diálogo, selecione **Sim**.

   ![Figura 32: o datakeeper informa que um serviço será desabilitado][sap-ha-guide-figure-3032]

   _**Figura 32:** O datakeeper informa que um serviço será desabilitado_

3. Na caixa de diálogo, recomendamos que você selecione **conta de domínio ou servidor**.

   ![Figura 33: seleção de usuário para SIOS datakeeper][sap-ha-guide-figure-3033]

   _**Figura 33:** Seleção de usuário para SIOS datakeeper_

4. Insira o nome de usuário e a senha da conta de domínio que você criou para o SIOS datakeeper.

   ![Figura 34: inserir o nome de usuário do domínio e a senha para a instalação do SIOS datakeeper][sap-ha-guide-figure-3034]

   _**Figura 34:** Insira o nome de usuário de domínio e a senha para a instalação do SIOS datakeeper_

5. Instale a chave de licença da sua instância do SIOS datakeeper, conforme mostrado na Figura 35.

   ![Figura 35: Insira sua chave de licença do SIOS datakeeper][sap-ha-guide-figure-3035]

   _**Figura 35:** Insira sua chave de licença do SIOS datakeeper_

6. Quando solicitado, reinicie a máquina virtual.

### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>Configurar o SIOS datakeeper

Depois de instalar o SIOS datakeeper em ambos os nós, inicie a configuração. O objetivo da configuração é ter a replicação de dados síncrona entre os discos adicionais anexados a cada uma das máquinas virtuais.

1. Inicie a ferramenta de gerenciamento e configuração do datakeeper e, em seguida, selecione **conectar servidor**.

   ![Figura 36: ferramenta de gerenciamento e configuração do SIOS datakeeper][sap-ha-guide-figure-3036]

   _**Figura 36:** Ferramenta de gerenciamento e configuração do SIOS datakeeper_

2. Insira o nome ou endereço TCP/IP do primeiro nó ao qual a ferramenta de gerenciamento e configuração deve se conectar e, em uma segunda etapa, o segundo nó.

   ![Figura 37: inserir o nome ou endereço TCP/IP do primeiro nó ao qual a ferramenta de gerenciamento deve se conectar e, em uma segunda etapa, o segundo nó][sap-ha-guide-figure-3037]

   _**Figura 37:** Insira o nome ou endereço TCP/IP do primeiro nó ao qual a ferramenta de gerenciamento e configuração deve se conectar e, em uma segunda etapa, o segundo nó_

3. Crie o trabalho de replicação entre os dois nós.

   ![Figura 38: criar um trabalho de replicação][sap-ha-guide-figure-3038]

   _**Figura 38:** Criar um trabalho de replicação_

   Um assistente orienta você pelo processo de criação de um trabalho de replicação.

4. Defina o nome do trabalho de replicação.

   ![Figura 39: definir o nome do trabalho de replicação][sap-ha-guide-figure-3039]

   _**Figura 39:** Definir o nome do trabalho de replicação_

   ![Figura 40: definir os dados base para o nó, que deve ser o nó de origem atual][sap-ha-guide-figure-3040]

   _**Figura 40:** Definir os dados base para o nó, que deve ser o nó de origem atual_

5. Defina o nome, o endereço TCP/IP e o volume de disco do nó de destino.

   ![Figura 41: definir o nome, o endereço TCP/IP e o volume de disco do nó de destino atual][sap-ha-guide-figure-3041]

   _**Figura 41:** Definir o nome, o endereço TCP/IP e o volume de disco do nó de destino atual_

6. Defina os algoritmos de compactação. Em nosso exemplo, recomendamos que você compacte o fluxo de replicação. Especialmente em situações de ressincronização, a compactação do fluxo de replicação reduz drasticamente o tempo de ressincronização. A compactação usa os recursos de CPU e RAM de uma máquina virtual. À medida que a taxa de compactação aumenta, o volume de recursos de CPU usado. Você pode ajustar essa configuração mais tarde.

7. Outra configuração que você precisa verificar é se a replicação ocorre de forma assíncrona ou síncrona. Ao proteger as configurações do SAP ASCS/SCS, você deve usar a replicação síncrona.  

   ![Figura 42: definir detalhes de replicação][sap-ha-guide-figure-3042]

   _**Figura 42:** Definir detalhes de replicação_

8. Defina se o volume que é replicado pelo trabalho de replicação deve ser representado a uma configuração de cluster de failover do Windows Server como um disco compartilhado. Para a configuração do SAP ASCS/SCS, selecione **Sim** para que o cluster do Windows Veja o volume replicado como um disco compartilhado que pode ser usado como um volume de cluster.

   ![Figura 43: selecione Sim para definir o volume replicado como um volume de cluster][sap-ha-guide-figure-3043]

   _**Figura 43:** Selecione **Sim** para definir o volume replicado como um volume de cluster_

   Depois que o volume é criado, a ferramenta de gerenciamento e configuração do datakeeper mostra que o trabalho de replicação está ativo.

   ![Figura 44: o espelhamento síncrono do datakeeper para o disco de compartilhamento do SAP ASCS/SCS está ativo][sap-ha-guide-figure-3044]

   _**Figura 44:** O espelhamento síncrono do datakeeper para o disco de compartilhamento do SAP ASCS/SCS está ativo_

   Gerenciador de Cluster de Failover agora mostra o disco como um disco do datakeeper, como mostrado na Figura 45:

   ![Figura 45: o Gerenciador de Cluster de Failover mostra o disco que o datakeeper replicau][sap-ha-guide-figure-3045]

   _**Figura 45:** Gerenciador de Cluster de Failover mostra o disco que o datakeeper replicau_

## <a name="next-steps"></a>Passos seguintes

* [Instalar o SAP NetWeaver HA usando um cluster de failover do Windows e um disco compartilhado para uma instância do SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]
