---
title: Infraestrutura azure para SAP ASCS/SCS com WSFC&disco partilhado Microsoft Docs
description: Saiba como preparar a infraestrutura Azure para o SAP HA utilizando um cluster de failover windows e um disco partilhado para uma instância SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f5e0eda72f39a70f02b596a8fd69728336eac333
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594819"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Prepare a infraestrutura Azure para SAP HA utilizando um cluster de falha do Windows e um disco partilhado para SAP ASCS/SCS

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

Este artigo descreve os passos que toma para preparar a infraestrutura Azure para instalar e configurar um sistema SAP de alta disponibilidade num cluster de falhas do Windows, utilizando um *disco partilhado* de cluster como uma opção para agrupar uma instância SAP ASCS.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar a instalação, reveja este artigo:

* [Guia de arquitetura: Cluster uma instância SAP ASCS/SCS num cluster de falhas do Windows utilizando um disco partilhado cluster][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Preparar a infraestrutura para o modelo arquitetônico 1
Os modelos do Gestor de Recursos Azure para SAP ajudam a simplificar a implementação dos recursos necessários.

Os modelos de três níveis no Azure Resource Manager também suportam cenários de alta disponibilidade. Por exemplo, o Modelo Arquitetônico 1 tem dois clusters. Cada cluster é um ponto único de falha do SAP ASCS/SCS e DBMS.

Aqui é onde você pode obter modelos de Gestor de Recursos Azure para o cenário de exemplo que descrevemos neste artigo:

* [Imagem do Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/)  
* [Imagem do Azure Marketplace utilizando discos geridos azure](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Imagem personalizada](https://github.com/Azure/azure-quickstart-templates/)
* [Imagem personalizada usando discos geridos](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Para preparar a infraestrutura para o modelo arquitetônico 1:

- No portal Azure, no painel **Parâmetros,** na caixa **SYSTEMAVAILABILITY,** selecione **HA**.

  ![Figura 1: Definir parâmetros de alta disponibilidade do SAP Do Gestor de Recursos Azure][sap-ha-guide-figure-3000]

_**Figura 1:** Definir parâmetros de gestor de recursos Azure de alta disponibilidade SAP_


  Os modelos criam:

  * **Máquinas virtuais:**
    * Máquinas virtuais do Servidor \<de Aplicações SAP: SAPSystemSID\>-di-\<Número\>
    * Máquinas virtuais de cluster ASCS/SCS: \<SAPSystemSID\>-ascs-\<Número\>
    * Cluster DBMS: \<SAPSystemSID\>\<-db- Número\>

  * **Cartões de rede para todas as máquinas virtuais, com endereços IP associados:**
    * \<SAPSystemSID\>-nic-di-Número\<\>
    * \<SAPSystemSID\>-nic-ascs-\<Número\>
    * \<SAPSystemSID\>-nic-db-\<Número\>

  * Contas de **armazenamento azure (apenas discos não geridos)**:

  * **Grupos de disponibilidade** para:
    * Máquinas virtuais do Servidor \<de Aplicações SAP: SAPSystemSID\>-avset-di
    * Máquinas virtuais de cluster SAP ASCS/SCS: \<SAPSystemSID\>-avset-ascs
    * Máquinas virtuais do cluster \<DBMS: SAPSystemSID\>-avset-db

  * Equilíbrio interno de **carga azul:**
    * Com todas as portas para a instância ASCS/SCS e endereço \<IP SAPSystemSID\>-lb-ascs
    * Com todas as portas para o DbMS \<do Servidor\>SQL e endereço IP SAPSystemSID -lb-db

  * **Grupo de** \<segurança da rede : SAPSystemSID\>-nsg-ascs-0  
    * Com uma porta aberta de Protocolo de \<Ambiente de\>Trabalho Remoto (RDP) para a máquina virtual SAPSystemSID -ascs-0

> [!NOTE]
> Todos os endereços IP dos cartões de rede e dos equilibradores de carga internos Azure são dinâmicos por padrão. Mude-os para endereços IP estáticos. Descrevemos como fazê-lo mais tarde no artigo.
>
>

## <a name="deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Implementar máquinas virtuais com conectividade de rede corporativa (cross-premises) para utilizar na produção
Para produção de sistemas SAP, implemente máquinas virtuais Azure com conectividade de rede corporativa utilizando o Azure VPN Gateway ou o Azure ExpressRoute.

> [!NOTE]
> Pode utilizar a sua instância de Rede Virtual Azure. A rede virtual e a subnet já foram criadas e preparadas.
>
>

1. No portal Azure, no painel **Parâmetros,** na caixa **NEWOREXISTINGSUBNET,** selecione **existindo**.
2. Na caixa **SUBNETID,** adicione a cadeia completa do seu ID de sub-rede azure preparado onde planeia implantar as suas máquinas virtuais Azure.
3. Para obter uma lista de todas as subredes da rede Azure, execute este comando PowerShell:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   O campo **de identificação** mostra o valor para o ID da sub-rede.
4. Para obter uma lista de todos os valores de ID da sub-rede, execute este comando PowerShell:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   O ID da sub-rede é assim:

   ```
   /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
   ```

## <a name="deploy-cloud-only-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Implementar instâncias SAP apenas em nuvem para teste e demonstração
Pode implementar o seu sistema SAP de alta disponibilidade num modelo de implementação apenas em nuvem. Este tipo de implantação é principalmente útil para casos de demo e de utilização de testes. Não é adequado para casos de uso de produção.

- No portal Azure, no painel **Parâmetros,** na caixa **NEWOREXISTINGSUBNET,** selecione **novos**. Deixe o campo **SUBNETID** vazio.

  O modelo SAP Azure Resource Manager cria automaticamente a rede virtual azure e a subnet.

> [!NOTE]
> Também precisa de implantar pelo menos uma máquina virtual dedicada para o serviço Ative Directory e DNS na mesma instância da Rede Virtual Azure. O modelo não cria estas máquinas virtuais.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Preparar a infraestrutura para o modelo arquitetônico 2

Você pode usar este modelo de Gestor de Recursos Azure para SAP para ajudar a simplificar a implementação de recursos de infraestrutura necessários para o Modelo Arquitetónico SAP 2.

Aqui é onde você pode obter modelos de Gestor de Recursos Azure para este cenário de implementação:

* [Imagem do Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/)  
* [Imagem do Azure Marketplace utilizando discos geridos](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Imagem personalizada](https://github.com/Azure/azure-quickstart-templates/)
* [Imagem personalizada usando discos geridos](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Preparar a infraestrutura para o modelo arquitetônico 3

Pode preparar a infraestrutura e configurar o SAP para multi-SID. Por exemplo, pode adicionar uma instância adicional SAP ASCS/SCS numa configuração de cluster *existente.* Para mais informações, consulte [Configurar uma instância adicional sAP ASCS/SCS para uma configuração de cluster existente para criar uma configuração multi-SID SAP em Azure Resource Manager][sap-ha-multi-sid-guide].

Se quiser criar um novo cluster multi-SID, pode utilizar os modelos de arranque rápido multi-SID [no GitHub](https://github.com/Azure/azure-quickstart-templates).

Para criar um novo cluster multi-SID, deve implementar os seguintes três modelos:

* [Modelo ASCS/SCS](#ASCS-SCS-template)
* [Modelo de base de dados](#database-template)
* [Modelo de servidores de aplicação](#application-servers-template)

As seguintes secções têm mais detalhes sobre os modelos e parâmetros que você precisa fornecer nos modelos.

### <a name="ascsscs-template"></a><a name="ASCS-SCS-template"></a>Modelo ASCS/SCS

O modelo ASCS/SCS implementa duas máquinas virtuais que pode utilizar para criar um cluster de falhas do Windows Server que acolhe várias instâncias ASCS/SCS.

Para configurar o modelo ASCS/SCS multi-SID, no [modelo ASCS/SCS multi-SID][sap-templates-3-tier-multisid-xscs-marketplace-image] ou no [modelo ASCS/SCS multi-SID utilizando discos geridos,][sap-templates-3-tier-multisid-xscs-marketplace-image-md]introduza valores para os seguintes parâmetros:

- **Prefixo de recursos**: Desconeda o prefixo do recurso, que é utilizado para pré-fixar todos os recursos criados durante a implementação. Como os recursos não pertencem apenas a um sistema SAP, o prefixo do recurso não é o SID de um sistema SAP.  O prefixo deve estar entre três e seis caracteres.
- **Tipo de pilha**: Selecione o tipo de pilha do sistema SAP. Dependendo do tipo de pilha, o Azure Load Balancer tem um (apenas ABAP ou Java) ou dois endereços IP privados (ABAP+Java) por sistema SAP.
- **Tipo OS**: Selecione o sistema operativo das máquinas virtuais.
- **Contagem do sistema SAP**: Selecione o número de sistemas SAP que pretende instalar neste cluster.
- **Disponibilidade do Sistema**: Selecione **HA**.
- Nome de **utilizador e palavra-passe do Administrador**: Crie um novo utilizador que possa ser utilizado para iniciar sessão na máquina.
- **Subnet nova ou existente**: Definir se criar uma nova rede virtual e sub-rede ou utilizar uma subrede existente. Se já tem uma rede virtual ligada à sua rede no local, **selecione existindo**.
- **Id sub-rede**: Se pretender implantar o VM numa VNet existente onde tenha uma sub-rede definida a VM deve ser atribuída, diga o nome da identificação dessa sub-rede específica. A identificação geralmente é assim:

  /subscrições/\<id\>\<de subscrição /recursosGroups/ nome\>de grupo de\<recursos\>/fornecedores/Microsoft.Network/virtualNetworks/ virtual network name /subnets/\<subnet name\>

O modelo implementa uma instância azure Load Balancer, que suporta vários sistemas SAP:

- Os casos ASCS estão configurados por exemplo número 00, 10, 20...
- Os casos scs estão configurados por exemplo número 01, 11, 21...
- As instâncias do Servidor de Replicação de Enfila ASCS (ERS) (apenas Linux) estão configuradas por exemplo o número 02, 12, 22...
- Os casos SCS ERS (apenas Linux) estão configurados por exemplo número 03, 13, 23...

O equilibrador de carga contém 1 VIP (2 para Linux), 1x VIP para ASCS/SCS e 1x VIP para ERS (apenas Linux).

#### <a name="sap-ascsscs-ports"></a><a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a>Portas SAP ASCS/SCS
A lista que se segue contém todas as regras de equilíbrio de carga (onde x é o número do sistema SAP, por exemplo, 1, 2, 3...):
- Portas específicas do Windows para cada sistema SAP: 445,5985
- Portas ASCS (número de instância x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- Portas SCS (número de instância x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- Portas ASCS ERS em Linux (número de instância x2): 33x2, 5x213, 5x214, 5x216
- Portas SCS ERS em Linux (número de instância x3): 33x3, 5x313, 5x314, 5x316

O equilibrista de carga está configurado para utilizar as seguintes portas de sonda (onde x é o número do sistema SAP, por exemplo, 1, 2, 3...):
- Porta de sonda de balanço de carga interna ASCS/SCS: 620x0
- Porta de sonda de balanço interno da ERS (apenas Linux): 621x2

### <a name="database-template"></a><a name="database-template"></a>Modelo de base de dados

O modelo de base de dados implementa uma ou duas máquinas virtuais que pode utilizar para instalar o sistema de gestão relacional de bases de dados (RDBMS) para um sistema SAP. Por exemplo, se implementar um modelo ASCS/SCS para cinco sistemas SAP, precisa de implementar este modelo cinco vezes.

Para configurar o modelo multi-SID da base de dados, no [modelo multi-SID][sap-templates-3-tier-multisid-db-marketplace-image] ou na base de [dados multi-SID utilizando discos geridos,][sap-templates-3-tier-multisid-db-marketplace-image-md]introduza valores para os seguintes parâmetros:

- Id do **sistema Sap**: Introduza o ID do sistema SAP do sistema SAP que pretende instalar. O ID é usado como prefixo para os recursos que são implantados.
- **Tipo os si:** Selecione o sistema operativo das máquinas virtuais.
- **Dbtype**: Selecione o tipo de base de dados que pretende instalar no cluster. Selecione **SQL** se pretender instalar o Microsoft SQL Server. Selecione **HANA** se planeia instalar o SAP HANA nas máquinas virtuais. Certifique-se de que seleciona o tipo correto do sistema operativo. Selecione **Windows** para SQL e selecione uma distribuição Linux para HANA. O Equilíbrio de Carga Azure que está ligado às máquinas virtuais está configurado para suportar o tipo de base de dados selecionado:
  * **SQL**: A porta de equilíbrio de carga do equilibrante de carga 1433. Certifique-se de utilizar esta porta para a configuração do SQL Server AlwaysOn.
  * **HANA**: As portas de equilíbrio de carga do equilibrador de carga 35015 e 35017. Certifique-se de instalar o SAP HANA com a instância número **50**.
  O equilibrador de carga utiliza a porta de sonda 62550.
- **Tamanho do sistema seiva**: Definir o número de SAPS que o novo sistema fornece. Se não tem a certeza de quantos SAPS o sistema necessita, pergunte ao seu Parceiro de Tecnologia SAP ou ao Integrador de Sistemas.
- **Disponibilidade do Sistema**: Selecione **HA**.
- Nome de **utilizador e palavra-passe do Administrador**: Crie um novo utilizador que possa ser utilizado para iniciar sessão na máquina.
- **Id sub-rede**: Introduza o ID da sub-rede que utilizou durante a implantação do modelo ASCS/SCS, ou o ID da sub-rede que foi criada como parte da implantação do modelo ASCS/SCS.

### <a name="application-servers-template"></a><a name="application-servers-template"></a>Modelo de servidores de aplicação

O modelo de servidores de aplicações implementa duas ou mais máquinas virtuais que podem ser usadas como instâncias do Servidor de Aplicações SAP para um sistema SAP. Por exemplo, se implementar um modelo ASCS/SCS para cinco sistemas SAP, precisa de implementar este modelo cinco vezes.

Para configurar o modelo multi-SID dos servidores de aplicações, no [modelo multi-SID][sap-templates-3-tier-multisid-apps-marketplace-image] dos servidores de aplicações dos servidores de aplicações, [utilizando discos geridos,][sap-templates-3-tier-multisid-apps-marketplace-image-md]introduza valores para os seguintes parâmetros:

  -  Id do **sistema Sap**: Introduza o ID do sistema SAP do sistema SAP que pretende instalar. O ID é usado como prefixo para os recursos que são implantados.
  -  **Tipo os si:** Selecione o sistema operativo das máquinas virtuais.
  -  **Tamanho do sistema sap**: O número de SAPS que o novo sistema fornece. Se não tem a certeza de quantos SAPS o sistema necessita, pergunte ao seu Parceiro de Tecnologia SAP ou ao Integrador de Sistemas.
  -  **Disponibilidade do Sistema**: Selecione **HA**.
  -  Nome de **utilizador e palavra-passe do Administrador**: Crie um novo utilizador que possa ser utilizado para iniciar sessão na máquina.
  -  **Id sub-rede**: Introduza o ID da sub-rede que utilizou durante a implantação do modelo ASCS/SCS, ou o ID da sub-rede que foi criada como parte da implantação do modelo ASCS/SCS.


## <a name="azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Rede Virtual Azure
No nosso exemplo, o espaço de endereço da rede virtual Azure é 10.0.0.0.0/16. Há uma sub-rede chamada Subnet, com uma gama de endereços de 10.0.0.0/24. Todas as máquinas virtuais e os equilibradores internos de carga são implantados nesta rede virtual.

> [!IMPORTANT]
> Não faça alterações nas definições de rede dentro do sistema operativo do hóspede. Isto inclui endereços IP, servidores DNS e sub-rede. Configure todas as definições da sua rede em Azure. O protocolo de configuração do hospedeiro dinâmico (DHCP) propaga as suas definições.
>
>

## <a name="dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>Endereços IP DNS

Para definir os endereços IP dNS necessários, preencha os seguintes passos:

1. No portal Azure, no painel de **servidores DNS,** certifique-se de que a opção de **servidores DNS** da rede virtual está definida para **DNS Personalizado**.
2. Selecione as suas definições com base no tipo de rede que tem. Para obter mais informações, consulte os seguintes recursos:
   * Adicione os endereços IP dos servidores DNS no local.  
   Pode estender os servidores DNS no local às máquinas virtuais que estão a funcionar em Azure. Nesse cenário, pode adicionar os endereços IP das máquinas virtuais Azure nas quais executa o serviço DNS.
   * Para implementações VM isoladas em Azure: Implementar uma máquina virtual adicional na mesma instância de Rede Virtual que serve como servidor DNS. Adicione os endereços IP das máquinas virtuais Azure que criou para executar o serviço DNS.

   ![Figura 2: Configure os servidores DNS para a Rede Virtual Azure][sap-ha-guide-figure-3001]

   _**Figura 2:** Configure servidores DNS para rede virtual Azure_

   > [!NOTE]
   > Se alterar os endereços IP dos servidores DNS, tem de reiniciar as máquinas virtuais Azure para aplicar a alteração e propagar os novos servidores DNS.
   >
   >

No nosso exemplo, o serviço DNS está instalado e configurado nestas máquinas virtuais do Windows:

| Papel de máquina virtual | Nome do hospedeiro da máquina virtual | Nome do cartão de rede | Endereço IP estático |
| --- | --- | --- | --- |
| Primeiro servidor DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Segundo servidor DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

## <a name="host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Nomes de anfitriões e endereços IP estáticos para a instância agrupada SAP ASCS/SCS e instância agrupada DE DBMS

Para a implantação no local, você precisa destes nomes de anfitriões reservados e endereços IP:

| Papel de nome de anfitrião virtual | Nome de anfitrião virtual | Endereço IP estático virtual |
| --- | --- | --- |
| Primeiro nome de anfitrião virtual do cluster SAP ASCS/SCS (para gestão de clusters) |pr1-ascs-vir |10.0.0.42 |
| Nome de anfitrião virtual sap ASCS/SCS |pr1-ascs-seiva |10.0.0.43 |
| Nome de anfitrião virtual do segundo cluster SAP DBMS (gestão do cluster) |pr1-dbms-vir |10.0.0.32 |

Quando criar o cluster, crie os nomes de anfitriões virtuais pr1-ascs-vir e pr1-dbms-vir e os endereços IP associados que gerem o próprio cluster. Para obter informações sobre como fazê-lo, consulte Recolher nós de [cluster numa configuração de cluster][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config].

Pode criar manualmente os outros dois nomes de anfitriões virtuais, pr1-ascs-seap e pr1-dbms-sap, e os endereços IP associados, no servidor DNS. A instância SAP ASCS/SCS agrupada e a instância DBMS agrupada utilizam estes recursos. Para obter informações sobre como fazê-lo, consulte [Criar um nome de anfitrião virtual para uma instância SAP ASCS/SCS agrupada][sap-ha-guide-9.1.1].

## <a name="set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Definir endereços IP estáticos para as máquinas virtuais SAP
Depois de colocar as máquinas virtuais a utilizar no seu cluster, precisa de definir endereços IP estáticos para todas as máquinas virtuais. Faça-o na configuração da Rede Virtual Azure e não no sistema operativo dos hóspedes.

1. No portal Azure, selecione **Resource Group Network** > **Card** > **Settings** > IP**Address**.
2. No painel de **endereços IP,** em **Atribuição,** **selecione Static**. Na caixa de **endereços IP,** introduza o endereço IP que pretende utilizar.

   > [!NOTE]
   > Se alterar o endereço IP do cartão de rede, tem de reiniciar as máquinas virtuais Azure para aplicar a alteração.  
   >
   >

   ![Figura 3: Definir endereços IP estáticos para o cartão de rede de cada máquina virtual][sap-ha-guide-figure-3002]

   _**Figura 3:** Detete os endereços IP estáticos para o cartão de rede de cada máquina virtual_

   Repita este passo para todas as interfaces de rede, isto é, para todas as máquinas virtuais, incluindo máquinas virtuais que pretende utilizar para o seu serviço Ative Directory ou DNS.

No nosso exemplo, temos estas máquinas virtuais e endereços IP estáticos:

| Papel de máquina virtual | Nome do hospedeiro da máquina virtual | Nome do cartão de rede | Endereço IP estático |
| --- | --- | --- | --- |
| Primeira instância do servidor de aplicações SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Segunda instância do servidor de aplicações SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Última instância do servidor de aplicações SAP |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Primeiro nó de cluster para instância ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Segundo nó de cluster para instância ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Primeiro nó de cluster para a instância DBMS |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Segundo nó de cluster para a instância DBMS |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

## <a name="set-a-static-ip-address-for-the-azure-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Defino um endereço IP estático para o equilibrador de carga interna Azure

O modelo SAP Azure Resource Manager cria um equilibrador de carga interna Azure que é utilizado para o cluster de instânciaS AsCS/SCS e o cluster DBMS.

> [!IMPORTANT]
> O endereço IP do nome de anfitrião virtual do SAP ASCS/SCS é o mesmo que o endereço IP do equilíbrio interno de carga SAP ASCS/SCS: pr1-lb-ascs.
> O endereço IP do nome virtual do DBMS é o mesmo que o endereço IP do equilíbrio interno de carga DBMS: pr1-lb-dbms.
>
>

Para definir um endereço IP estático para o equilibrador de carga interna Azure:

1. A implementação inicial define o endereço IP do equilíbrio interno para **Dynamic**. No portal Azure, no painel de **endereços IP,** em **Atribuição,** **selecione Static**.
2. Defino o endereço IP do equilibrador de carga interno **pr1-lb-ascs** no endereço IP do nome de hospedeiro virtual da instância SAP ASCS/SCS.
3. Defino o endereço IP do equilibrador de carga interno **pr1-lb-dbms** no endereço IP do nome de hospedeiro virtual da instância DBMS.

   ![Figura 4: Definir endereços IP estáticos para o equilibrador de carga interno para a instância SAP ASCS/SCS][sap-ha-guide-figure-3003]

   _**Figura 4:** Definir endereços IP estáticos para o equilibrador de carga interno para a instância SAP ASCS/SCS_

No nosso exemplo, temos dois equilibradores de carga internos Azure que têm estes endereços IP estáticos:

| Papel de equilibrador de carga interna azure | Nome do equilibrador de carga interna Azure | Endereço IP estático |
| --- | --- | --- |
| Balanceador interno de carga sAP ASCS/SCS |pr1-lb-ascs |10.0.0.43 |
| Balanceador de carga interna SAP DBMS |pr1-lb-dbms |10.0.0.33 |


## <a name="default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Regras de equilíbrio de carga ASCS/SCS padrão para o equilibrador de carga interna Azure

O modelo SAP Azure Resource Manager cria as portas de que necessita:
* Uma instância AsCS ABAP, com a instância padrão número 00
* Uma instância Java SCS, com a instância padrão número 01

Quando instalar a sua instância SAP ASCS/SCS, deve utilizar o número de instância padrão 00 para a sua instância AsCS ABAP e a instância padrão número 01 para a sua instância Java SCS.

Em seguida, crie os pontos finais de equilíbrio de carga interna necessários para as portas SAP NetWeaver.

Para criar pontos finais de equilíbrio interno de carga necessários, em primeiro lugar, crie estes pontos finais de equilíbrio de carga para as portas ASCS SAP NetWeaver ABAP:

| Nome da regra de equilíbrio de serviço/carga | Números de porta padrão | Portas de betão para (instância ASCS com número de exemplo 00) (ERS com 10) |
| --- | --- | --- |
| Servidor Enqueue / *lbrule3200* |32\<InstânciasNúmero\> |3200 |
| Servidor de mensagem ABAP / *lbrule3600* |36\<InstânciasNúmero\> |3600 |
| Mensagem Interna ABAP / *lbrule3900* |39\<InstânciasNúmero\> |3900 |
| Servidor de mensagem HTTP / *Lbrule8100* |81\<Instâncianúmero\> |8100 |
| Serviço de arranque SAP ASCS HTTP / *Lbrule50013* |5\<Instâncias Número\>13 |50013 |
| Serviço de arranque SAP ASCS HTTPS / *Lbrule50014* |5\<Instâncias Número\>14 |50014 |
| Replicação enfila / *Lbrule50016* |5\<Instâncias Número\>16 |50016 |
| SAP iniciar serviço ERS HTTP *Lbrule51013* |5\<Instâncias Número\>13 |51013 |
| SAP inicia serviço ERS HTTP *Lbrule51014* |5\<Instâncias Número\>14 |51014 |
| Gestão remota do Windows (WinRM) *Lbrule5985* | |5985 |
| Partilha de ficheiros *Lbrule445* | |445 |

**Quadro 1:** Números de porta dos casos SAP NetWeaver ABAP ASCS

Em seguida, crie estes pontos finais de equilíbrio de carga para as portas SAP NetWeaver Java SCS:

| Nome da regra de equilíbrio de serviço/carga | Números de porta padrão | Portas de betão para (instância SCS com instância número 01) (ERS com 11) |
| --- | --- | --- |
| Servidor Enqueue / *lbrule3201* |32\<InstânciasNúmero\> |3201 |
| Servidor gateway / *lbrule3301* |33\<Instâncias Número\> |3301 |
| Servidor de mensagem Java / *lbrule3900* |39\<InstânciasNúmero\> |3901 |
| Servidor de mensagem HTTP / *Lbrule8101* |81\<Instâncianúmero\> |8101 |
| Serviço de arranque SAP SCS HTTP / *Lbrule50113* |5\<Instâncias Número\>13 |50113 |
| Serviço de arranque SCS HTTPS / *Lbrule50114* |5\<Instâncias Número\>14 |50114 |
| Replicação enfila / *Lbrule50116* |5\<Instâncias Número\>16 |50116 |
| SAP iniciar serviço ERS HTTP *Lbrule51113* |5\<Instâncias Número\>13 |51113 |
| SAP iniciar serviço ERS HTTP *Lbrule51114* |5\<Instâncias Número\>14 |51114 |
| WinRM *Lbrule5985* | |5985 |
| Partilha de ficheiros *Lbrule445* | |445 |

**Quadro 2:** Números de porta dos casos SAP NetWeaver Java SCS

![Figura 5: Regras padrão de equilíbrio de carga ASCS/SCS para o equilibrador de carga interna Azure][sap-ha-guide-figure-3004]

_**Figura 5:** Regras de equilíbrio de carga ASCS/SCS padrão para o equilibrador de carga interna Azure_

Defino o endereço IP do equilibrador de carga pr1-lb-dbms no endereço IP do nome de hospedeiro virtual da instância DBMS.

### <a name="change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Alterar as regras de equilíbrio da carga padrão ASCS/SCS para o equilibrador de carga interna Azure

Se pretender utilizar diferentes números para as instâncias SAP ASCS ou SCS, tem de alterar os nomes e valores das suas portas a partir de valores predefinidos.

1. No portal Azure, selecione >  ** \<\>SID-lb-ascs carregar**regras de equilíbrio de carga de**carga**.
2. Para todas as regras de equilíbrio de carga que pertencem à instância SAP ASCS ou SCS, altere estes valores:

   * Name
   * Porta
   * Porto de back-end

   Por exemplo, se pretender alterar o número de instância ASCS padrão de 00 para 31, tem de efazer as alterações para todas as portas listadas no Quadro 1.

   Aqui está um exemplo de uma atualização para o port *lbrule3200*.

   ![Figura 6: Alterar as regras de equilíbrio da carga padrão ASCS/SCS para o equilibrador de carga interna Azure][sap-ha-guide-figure-3005]

   _**Figura 6:** Alterar as regras de equilíbrio da carga padrão ASCS/SCS para o equilibrador de carga interna Azure_

## <a name="add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Adicione máquinas virtuais do Windows ao domínio

Depois de atribuir um endereço IP estático às máquinas virtuais, adicione as máquinas virtuais ao domínio.

![Figura 7: Adicione uma máquina virtual a um domínio][sap-ha-guide-figure-3006]

_**Figura 7:** Adicione uma máquina virtual a um domínio_

## <a name="add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Adicione entradas de registo em ambos os nós de cluster da instância SAP ASCS/SCS

O Azure Load Balancer tem um equilibrador de carga interna que fecha as ligações quando as ligações estão inativas durante um determinado período de tempo (um tempo inativo). Os processos de trabalho da SAP em casos de diálogo abrem ligações ao processo de fila sap logo que o primeiro pedido de fila/defilação precisa de ser enviado. Estas ligações geralmente permanecem estabelecidas até que o processo de trabalho ou o processo de fila reinicie. No entanto, se a ligação estiver inativa durante um determinado período de tempo, o equilibrista de carga interna Azure fecha as ligações. Isto não é um problema porque o processo de trabalho da SAP restabelece a ligação ao processo de fila se já não existir. Estas atividades estão documentadas nos vestígios de desenvolvedores de processos SAP, mas criam uma grande quantidade de conteúdo extra nesses vestígios. É uma boa ideia mudar o TCP/IP `KeepAliveTime` e `KeepAliveInterval` em ambos os nós de cluster. Combine estas alterações nos parâmetros TCP/IP com os parâmetros de perfil SAP, descritos posteriormente no artigo.

Para adicionar entradas de registo em ambos os nós de cluster da instância SAP ASCS/SCS, em primeiro lugar, adicione estas entradas de registo windows em ambos os nós do cluster Windows para SAP ASCS/SCS:

| Caminho | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome da variável |`KeepAliveTime` |
| Tipo variável |REG_DWORD (Decimal) |
| Valor |120000 |
| Ligação à documentação |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**Tabela 3:** Alterar o primeiro parâmetro TCP/IP

Em seguida, adicione esta entrada de registo do Windows em ambos os nós do cluster Windows para SAP ASCS/SCS:

| Caminho | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome da variável |`KeepAliveInterval` |
| Tipo variável |REG_DWORD (Decimal) |
| Valor |120000 |
| Ligação à documentação |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**Quadro 4:** Alterar o segundo parâmetro TCP/IP

Para aplicar as alterações, reinicie os dois nós do cluster.

## <a name="set-up-a-windows-server-failover-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Configurar um cluster de falha do Windows Server para uma instância SAP ASCS/SCS

A criação de um cluster de falha do Windows Server para uma instância SAP ASCS/SCS envolve estas tarefas:

- Colete os nós do cluster numa configuração de cluster.
- Configure uma testemunha de partilha de ficheiros de cluster.

### <a name="collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>Recolher os nós do cluster numa configuração de cluster

1. No Add Role and Features Wizard, adicione o agrupamento de falhas a ambos os nós do cluster.
2. Configurar o cluster failover utilizando o Failover Cluster Manager. No Failover Cluster Manager, selecione **Create Cluster**, e adicione apenas o nome do primeiro cluster (nó A). Não adicione o segundo nó ainda; adicione o segundo nó num passo posterior.

   ![Figura 8: Adicione o nome do servidor ou máquina virtual do primeiro nó de cluster][sap-ha-guide-figure-3007]

   _**Figura 8:** Adicione o nome do servidor ou máquina virtual do primeiro nó de cluster_

3. Introduza o nome da rede (nome de anfitrião virtual) do cluster.

   ![Figura 9: Introduza o nome do cluster][sap-ha-guide-figure-3008]

   _**Figura 9:** Introduza o nome do cluster_

4. Depois de criar o cluster, faça um teste de validação de cluster.

   ![Figura 10: Executar a verificação de validação do cluster][sap-ha-guide-figure-3009]

   _**Figura 10:** Executar a verificação de validação do cluster_

   Pode ignorar quaisquer avisos sobre discos nesta altura do processo. Você adicionará uma testemunha de partilha de ficheiros e os discos partilhados sIOS mais tarde. Nesta fase, não precisas de te preocupar em ter um quórum.

   ![Figura 11: Não é encontrado nenhum disco de quórum][sap-ha-guide-figure-3010]

   _**Figura 11:** Nenhum disco de quórum é encontrado_

   ![Figura 12: Um recurso de cluster central precisa de um novo endereço IP][sap-ha-guide-figure-3011]

   _**Figura 12:** Um recurso de cluster central precisa de um novo endereço IP_

5. Altere o endereço IP do serviço de cluster core. O cluster não pode arrancar até alterar o endereço IP do serviço de cluster core, porque o endereço IP do servidor aponta para um dos nós da máquina virtual. Faça isso na página **propriedades** do recurso IP do serviço de cluster central.

   Por exemplo, precisamos atribuir um endereço IP (no nosso exemplo, 10.0.0.42) para o nome de anfitrião virtual do cluster pr1-ascs-vir.

   ![Figura 13: Na caixa de diálogo Properties, altere o endereço IP][sap-ha-guide-figure-3012]

   _**Figura 13:** Na caixa de diálogo **Properties,** altere o endereço IP_

   ![Figura 14: Atribuir o endereço IP reservado para o cluster][sap-ha-guide-figure-3013]

   _**Figura 14:** Atribuir o endereço IP reservado para o cluster_

6. Traga o nome de anfitrião virtual do cluster on-line.

   ![Figura 15: O serviço de núcleo de cluster está em funcionamento, com o endereço IP correto][sap-ha-guide-figure-3014]

   _**Figura 15:** O serviço de núcleo de cluster está em funcionamento, com o endereço IP correto_

7. Adicione o segundo nó de cluster.

   Agora que o serviço de cluster principal está em funcionamento, pode adicionar o segundo nó de cluster.

   ![Figura 16 Adicione o segundo nó de cluster][sap-ha-guide-figure-3015]

   _**Figura 16:** Adicione o segundo nó de cluster_

8. Insira um nome para o segundo anfitrião do nó cluster.

   ![Figura 17: Introduza o segundo nome de anfitrião do nó cluster][sap-ha-guide-figure-3016]

   _**Figura 17:** Insira o segundo nome anfitrião do nó cluster_

   > [!IMPORTANT]
   > Certifique-se de que o Adicionar todo o **armazenamento elegível à** caixa de verificação do cluster *não* está selecionado.  
   >
   >

   ![Figura 18: Não selecione a caixa de verificação][sap-ha-guide-figure-3017]

   _**Figura 18:** Não *not* selecione a caixa de verificação_

   Pode ignorar avisos sobre quórum e discos. Irá definir o quórum e partilhar o disco mais tarde, conforme descrito na [Instalação SIOS DataKeeper Cluster Edition para um disco de partilha de cluster SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].

   ![Figura 19: Ignore os avisos sobre o quórum do disco][sap-ha-guide-figure-3018]

   _**Figura 19:** Ignore avisos sobre o quórum do disco_


#### <a name="configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Configure uma testemunha de partilha de ficheiros de cluster

Configurar uma testemunha de partilha de ficheiros de cluster envolve estas tarefas:

- Criar uma partilha de ficheiros.
- Desloque o quórum de partilha de ficheiros no Failover Cluster Manager.

#### <a name="create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Criar uma partilha de ficheiros

1. Selecione uma testemunha de partilha de ficheiros em vez de um disco de quórum. O SIOS DataKeeper suporta esta opção.

   Nos exemplos deste artigo, a testemunha de partilha de ficheiros está no servidor Ative Directory ou DNS que está a funcionar em Azure. A testemunha de partilha de ficheiros chama-se Domcontr-0. Como teria configurado uma ligação VPN ao Azure (via VPN Gateway ou Azure ExpressRoute), o seu serviço Ative Directory ou DNS está no local e não é adequado para executar uma testemunha de partilha de ficheiros.

   > [!NOTE]
   > Se o seu serviço Ative Directory ou DNS funcionar apenas no local, não configure a sua testemunha de partilha de ficheiros no sistema operativo Ative Directory ou DNS Windows que esteja a funcionar no local. A latência da rede entre os nós de cluster em funcionamento em Azure e Ative Directory ou DNS no local pode ser demasiado grande e causar problemas de conectividade. Certifique-se de configurar a testemunha de partilha de ficheiros numa máquina virtual Azure que está a funcionar perto do nó de cluster.  
   >
   >

   A unidade de quórum precisa de pelo menos 1.024 MB de espaço livre. Recomendamos 2.048 MB de espaço livre para a unidade de quórum.

2. Adicione o objeto de nome do cluster.

   ![Figura 20: Atribuir as permissões na parte para o objeto de nome do cluster][sap-ha-guide-figure-3019]

   _**Figura 20:** Atribuir as permissões na parte para o objeto de nome do cluster_

   Certifique-se de que as permissões incluem a autoridade para alterar dados na parte para o objeto de nome do cluster (no nosso exemplo, pr1-ascs-vir$).

3. Para adicionar o objeto de nome do cluster à lista, **selecione Adicionar**. Mude o filtro para verificar se há objetos de computador, para além dos mostrados na Figura 22.

   ![Figura 21: Alterar tipos de objetos para incluir computadores][sap-ha-guide-figure-3020]

   _**Figura 21:** Alterar **tipos de objetos** para incluir computadores_

   ![Figura 22: Selecione a caixa de verificação de computadores][sap-ha-guide-figure-3021]

   _**Figura 22:** Selecione a caixa de verificação **de Computadores**_

4. Introduza o objeto de nome do cluster como mostrado na Figura 21. Como o disco já foi criado, pode alterar as permissões, como mostra a Figura 20.

5. Selecione o separador **de segurança** da parte e, em seguida, detete permissões mais detalhadas para o objeto de nome do cluster.

   ![Figura 23: Definir os atributos de segurança para o objeto de nome do cluster no quórum de partilha de ficheiros][sap-ha-guide-figure-3022]

   _**Figura 23:** Desdefinir os atributos de segurança para o objeto de nome do cluster no quórum de partilha de ficheiros_

#### <a name="set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>Desloque o quórum de partilha de ficheiros no Failover Cluster Manager

1. Abra o Assistente de Definição de Quórum configurado.

   ![Figura 24: Inicie o assistente de definição de quórum de cluster configure][sap-ha-guide-figure-3023]

   _**Figura 24:** Inicie o assistente de definição de quórum de cluster configurar_

2. Na página Escolha da Opção de Configuração do **Quórum,** **selecione Selecione a testemunha quórum**.

   ![Figura 25: Configurações de quórum que pode escolher][sap-ha-guide-figure-3024]

   _**Figura 25:** Configurações de quórum que pode escolher_

3. Na página 'Testemunha do **Quorum Select',** selecione **Configurar uma testemunha de partilha**de ficheiros .

   ![Figura 26: Selecione a testemunha de partilha de ficheiros][sap-ha-guide-figure-3025]

   _**Figura 26:** Selecione a testemunha de partilha de ficheiros_

4. Insira o caminho do CNU para \\a partilha de ficheiros (no nosso exemplo, domcontr-0\FSW). Para ver uma lista das alterações que pode fazer, selecione **Next**.

   ![Figura 27: Definir a localização da partilha de ficheiros para a partilha de testemunhas][sap-ha-guide-figure-3026]

   _**Figura 27:** Defina a localização da partilha de ficheiros para a partilha de testemunhas_

5. Selecione as alterações desejadas e, em seguida, selecione **Next**. É necessário reconfigurar com sucesso a configuração do cluster, como mostra a Figura 28:  

   ![Figura 28: Confirmação de que reconfigurou o cluster][sap-ha-guide-figure-3027]

   _**Figura 28:** Confirmação de que reconfigurou o cluster_

Depois de instalar com sucesso o cluster failover do Windows, é necessário alterar alguns limiares para que adaptem a deteção de falhas às condições em Azure. Os parâmetros a alterar são documentados nos limiares da rede de [clusters de afinação.][tuning-failover-cluster-network-thresholds] Assumindo que os seus dois VMs que compõem a configuração do cluster Windows para ASCS/SCS estão na mesma sub-rede, altere os seguintes parâmetros para estes valores:

- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutHistoryLength = 30

Estas configurações foram testadas com os clientes, e oferecem um bom compromisso. São suficientemente resistentes, mas também fornecem falhas que são suficientemente rápidas em condições reais de erro num software SAP ou num nó ou falha vM.

### <a name="install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>Instale a Edição de Cluster SIOS DataKeeper para o disco de partilha de cluster SAP ASCS/SCS

Tem agora uma configuração de clusterde clusterde falha do Windows Server em Funcionamento no Azure. Para instalar uma instância SAP ASCS/SCS, necessita de um recurso de disco partilhado. Não é possível criar os recursos de disco partilhados de que necessita em Azure. SIOS DataKeeper Cluster Edition é uma solução de terceiros que pode usar para criar recursos de disco partilhado.

A instalação da Edição de Cluster SIOS DataKeeper para o disco de partilha de cluster SAP ASCS/SCS envolve estas tarefas:

- Adicione a Microsoft .NET Framework 3.5.
- Instale o Datakeeper SIOS.
- Configurar o DataKeeper SIOS.

### <a name="add-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>Adicionar .NET Quadro 3.5
.NET A estrutura 3.5 não é ativada ou instalada automaticamente no Windows Server 2012 R2. Uma vez que o SIOS DataKeeper necessita de .NET para estar em todos os nós onde instala o DataKeeper, tem de instalar a .NET Framework 3.5 no sistema operativo de todos os dispositivos virtuais do cluster.

Existem duas formas de adicionar .NET Framework 3.5:

- Utilize o Assistente de Adicionar e Funcionalidades no Windows, como mostra a Figura 29:

  ![Figura 29: Instale .NET Framework 3.5 utilizando o Assistente de Adicionar e Funcionalidades][sap-ha-guide-figure-3028]

  _**Figura 29:** Instale a .NET Framework 3.5 utilizando o Assistente de Adicionar e Funcionalidades_

  ![Figura 30: Barra de progresso de instalação quando instalar .NET Framework 3.5 utilizando o Assistente de Adicionar e Funcionalidades][sap-ha-guide-figure-3029]

  _**Figura 30:** Barra de progresso de instalação quando instalar .NET Framework 3.5 utilizando o Assistente de Adicionar e Funcionalidades_

- Utilize a ferramenta de linha de comando dism.exe. Para este tipo de instalação, é necessário aceder ao diretório SxS nos meios de instalação windows. Num pedido de comando elevado, insira este comando:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a>Instalar datakeeper SIOS

Instale a Edição de Cluster SIOS DataKeeper em cada nó do cluster. Para criar armazenamento partilhado virtual com o SIOS DataKeeper, crie um espelho sincronizado e, em seguida, simular o armazenamento partilhado do cluster.

Antes de instalar o software SIOS, crie o utilizador do domínio DataKeeperSvc.

> [!NOTE]
> Adicione o utilizador do domínio DataKeeperSvc ao grupo Deadministrador Local em ambos os nós do cluster.
>
>

Para instalar o DataKeeper SIOS:

1. Instale o software SIOS em ambos os nós do cluster.

   ![Instalador SIOS][sap-ha-guide-figure-3030]

   ![Figura 31: Primeira página da instalação SIOS DataKeeper][sap-ha-guide-figure-3031]

   _**Figura 31:** Primeira página da instalação SIOS DataKeeper_

2. Na caixa de diálogo, selecione **Sim**.

   ![Figura 32: DataKeeper informa que um serviço será desativado][sap-ha-guide-figure-3032]

   _**Figura 32:** DataKeeper informa que um serviço será desativado_

3. Na caixa de diálogo, recomendamos que selecione **a conta De domínio ou servidor**.

   ![Figura 33: Seleção de utilizadores para DataKeeper SIOS][sap-ha-guide-figure-3033]

   _**Figura 33:** Seleção de utilizadores para SIOS DataKeeper_

4. Introduza o nome de utilizador da conta de domínio e a palavra-passe que criou para o SIOS DataKeeper.

   ![Figura 34: Introduza o nome de utilizador de domínio e a palavra-passe para a instalação SIOS DataKeeper][sap-ha-guide-figure-3034]

   _**Figura 34:** Introduza o nome de utilizador de domínio e a palavra-passe para a instalação SIOS DataKeeper_

5. Instale a chave de licença para a sua instância SIOS DataKeeper, como mostra a Figura 35.

   ![Figura 35: Introduza a sua chave de licença SIOS DataKeeper][sap-ha-guide-figure-3035]

   _**Figura 35:** Insira a sua chave de licença SIOS DataKeeper_

6. Quando solicitado, reinicie a máquina virtual.

### <a name="set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>Configurar datakeeper SIOS

Depois de instalar o SIOS DataKeeper em ambos os nós, inicie a configuração. O objetivo da configuração é ter replicação de dados sincronizados entre os discos adicionais que estão ligados a cada uma das máquinas virtuais.

1. Inicie a ferramenta de gestão e configuração do DataKeeper e, em seguida, selecione **Connect Server**.

   ![Figura 36: Ferramenta de Gestão e Configuração de Gestão e Configuração de DataKeeper sIOS][sap-ha-guide-figure-3036]

   _**Figura 36:** Ferramenta de gestão e configuração do DataKeeper SIOS_

2. Introduza o nome ou endereço TCP/IP do primeiro nó a ferramenta de gestão e configuração deve ligar-se e, num segundo passo, o segundo nó.

   ![Figura 37: Insira o nome ou endereço TCP/IP do primeiro nó a ferramenta de gestão e configuração deve ligar-se, e num segundo passo, o segundo nó][sap-ha-guide-figure-3037]

   _**Figura 37:** Insira o nome ou endereço TCP/IP do primeiro nó a ferramenta de gestão e configuração deve ligar-se, e num segundo passo, o segundo nó_

3. Crie o trabalho de replicação entre os dois nós.

   ![Figura 38: Criar um trabalho de replicação][sap-ha-guide-figure-3038]

   _**Figura 38:** Criar um trabalho de replicação_

   Um feiticeiro guia-o através do processo de criação de um trabalho de replicação.

4. Defina o nome do trabalho de replicação.

   ![Figura 39: Definir o nome do trabalho de replicação][sap-ha-guide-figure-3039]

   _**Figura 39:** Defina o nome do trabalho de replicação_

   ![Figura 40: Definir os dados base para o nó, que deve ser o nó de origem atual][sap-ha-guide-figure-3040]

   _**Figura 40:** Defina os dados base para o nó, que deve ser o nó de origem atual_

5. Defina o nome, endereço TCP/IP e volume de disco do nó alvo.

   ![Figura 41: Definir o nome, endereço TCP/IP e volume de disco do nó alvo atual][sap-ha-guide-figure-3041]

   _**Figura 41:** Defina o nome, endereço TCP/IP e volume de disco do nó alvo atual_

6. Defina os algoritmos de compressão. No nosso exemplo, recomendamos que comprima o fluxo de replicação. Especialmente em situações de ressincronização, a compressão do fluxo de replicação reduz drasticamente o tempo de ressincronização. A compressão utiliza os recursos da CPU e da RAM de uma máquina virtual. À medida que a taxa de compressão aumenta, também o volume de recursos de CPU que são utilizados. Pode ajustar esta definição mais tarde.

7. Outra definição que precisa de verificar é se a replicação ocorre de forma assíncrona ou sincronizada. Quando protege as configurações SAP ASCS/SCS, deve utilizar uma replicação sincronizada.  

   ![Figura 42: Definir detalhes de replicação][sap-ha-guide-figure-3042]

   _**Figura 42:** Definir detalhes de replicação_

8. Defina se o volume replicado pelo trabalho de replicação deve ser representado para uma configuração de cluster de falha do Windows Server como um disco partilhado. Para a configuração SAP ASCS/SCS, selecione **Sim** para que o cluster Windows veja o volume replicado como um disco partilhado que pode usar como volume de cluster.

   ![Figura 43: Selecione Sim para definir o volume replicado como um volume de cluster][sap-ha-guide-figure-3043]

   _**Figura 43:** Selecione **Sim** para definir o volume replicado como um volume de cluster_

   Após a criação do volume, a ferramenta dataKeeper Management and Configuration mostra que o trabalho de replicação está ativo.

   ![Figura 44: O espelhamento sincronizado do DataKeeper para o disco de partilha SAP ASCS/SCS está ativo][sap-ha-guide-figure-3044]

   _**Figura 44:** O espelhamento sincronizado dataKeeper para o disco de partilha SAP ASCS/SCS está ativo_

   O Failover Cluster Manager mostra agora o disco como um disco DataKeeper, como mostra a Figura 45:

   ![Figura 45: Gestor de cluster de failover mostra o disco que dataKeeper replicado][sap-ha-guide-figure-3045]

   _**Figura 45:** Failover Cluster Manager mostra o disco que dataKeeper replicava_

## <a name="next-steps"></a>Passos seguintes

* [Instale o SAP NetWeaver HA utilizando um cluster de falha do Windows e um disco partilhado para uma instância SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]
