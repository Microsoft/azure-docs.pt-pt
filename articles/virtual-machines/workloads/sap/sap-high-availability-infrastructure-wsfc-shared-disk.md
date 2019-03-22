---
title: Preparar a infraestrutura do Azure para SAP HA através de um cluster de ativação pós-falha do Windows e o disco partilhado para o SAP ASCS/SCS | Documentos da Microsoft
description: Saiba como preparar a infraestrutura do Azure para SAP HA com um cluster de ativação pós-falha do Windows e o disco partilhado para uma instância do SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 43e4cf27d9a57db58c0f90b269e7a52622508ee1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57998761"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Preparar a infraestrutura do Azure para SAP HA através de um cluster de ativação pós-falha do Windows e o disco partilhado para o SAP ASCS/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Configuração de elevada disponibilidade de múltiplos SID SAP)

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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows][Logo_Windows] Windows
>

Este artigo descreve os passos que efetuar para preparar a infraestrutura do Azure para instalar e configurar um sistema SAP de elevada disponibilidade num cluster de ativação pós-falha do Windows utilizando um *disco partilhado de cluster* como uma opção para clustering de um Instância do SAP ASCS.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar a instalação, consulte este artigo:

* [Guia de arquitetura: Uma instância do SAP ASCS/SCS de cluster num cluster de ativação pós-falha do Windows utilizando um disco partilhado de cluster][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Preparar a infraestrutura para a arquitetura de modelo de 1
Os modelos do Resource Manager do Azure para SAP ajudam a simplificar a implantação de recursos necessários.

Os modelos de três camadas no Azure Resource Manager também suportam cenários de elevada disponibilidade. Por exemplo, 1 arquitetura do modelo tem dois clusters. Cada cluster é um ponto único de SAP da falha para o SAP ASCS/SCS e do DBMS.

Aqui é onde pode obter os modelos Azure Resource Manager para o cenário de exemplo descritos neste artigo:

* [Imagem do Marketplace do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Imagem do Marketplace do Azure com o Managed Disks do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Imagem personalizada com o Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Para preparar a infraestrutura para a arquitetura de modelo de 1:

- No portal do Azure, no **parâmetros** painel, na **SYSTEMAVAILABILITY** caixa, selecione **HA**.

  ![Figura 1: Definir parâmetros de Gestor de recursos do Azure de elevada disponibilidade do SAP][sap-ha-guide-figure-3000]

_**Figura 1:** Definir parâmetros de Gestor de recursos do Azure de elevada disponibilidade do SAP_


  Os modelos de criam:

  * **Máquinas virtuais**:
    * Máquinas de virtuais de servidor de aplicações SAP: \<SAPSystemSID\>-di-\<Number\>
    * Máquinas virtuais de cluster do ASCS/SCS: \<SAPSystemSID\>-ascs-\<Number\>
    * Cluster do DBMS: \<SAPSystemSID\>-db-\<Number\>

  * **Cartões de todas as máquinas virtuais, com endereços IP associados de rede**:
    * \<SAPSystemSID\>-nic-di-\<Number\>
    * \<SAPSystemSID\>-nic-ascs-\<Number\>
    * \<SAPSystemSID\>-nic-db-\<Number\>

  * **Contas de armazenamento do Azure (apenas discos não geridos)**:

  * **Grupos de disponibilidade** para:
    * Máquinas de virtuais de servidor de aplicações SAP: \<SAPSystemSID\>-avset-di
    * SAP ASCS/SCS máquinas virtuais do cluster: \<SAPSystemSID\>-avset-ascs
    * DBMS máquinas virtuais do cluster: \<SAPSystemSID\>-avset-db

  * **Balanceador de carga interno do Azure**:
    * Com todas as portas para a instância do ASCS/SCS e o endereço IP \<SAPSystemSID\>ascs - lb
    * Com todas as portas para o endereço IP e do SQL Server DBMS \<SAPSystemSID\>- lb-db

  * **Grupo de segurança de rede**: \<SAPSystemSID\>-nsg-ascs-0  
    * Com uma porta aberta de protocolo RDP (Remote Desktop) externo para o \<SAPSystemSID\>máquina de virtual - ascs-0

> [!NOTE]
> Todos os endereços IP das placas de rede e Balanceadores de carga interno do Azure são dinâmicos por predefinição. Altere-as para endereços IP estáticos. Descrevemos como fazê-lo posteriormente neste artigo.
>
>

## <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Implementar máquinas virtuais com a conectividade de rede da empresa (em vários locais) para utilizar na produção
Sistemas de produção SAP, implementar máquinas virtuais do Azure com [conectividade de rede da empresa (em vários locais)] [ planning-guide-2.2] com o Gateway de VPN do Azure ou Azure ExpressRoute.

> [!NOTE]
> Pode utilizar a instância de rede Virtual do Azure. A rede virtual e sub-rede já foi criados e preparados.
>
>

1. No portal do Azure, no **parâmetros** painel, na **NEWOREXISTINGSUBNET** caixa, selecione **existente**.
2. Na **SUBNETID** caixa, adicione a cadeia de caracteres completa de sua ID de sub-rede de rede do Azure preparado onde planeia implementar máquinas virtuais do Azure.
3. Para obter uma lista de todas as sub-redes de rede do Azure, execute este comando do PowerShell:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   O **ID** campo mostra o valor para o ID de sub-rede.
4. Para obter uma lista de todos os valores de ID de sub-rede, execute este comando do PowerShell:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   O ID de sub-rede tem esta aparência:

   ```
   /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
   ```

## <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Implementar instâncias SAP apenas na cloud para teste e demonstração
Pode implementar o sistema SAP de elevada disponibilidade num modelo de implementação apenas na cloud. Esse tipo de implementação é principalmente útil para casos de utilização de demonstração e teste. Não é adequada para casos de utilização de produção.

- No portal do Azure, no **parâmetros** painel, na **NEWOREXISTINGSUBNET** caixa, selecione **novo**. Deixe o **SUBNETID** campo vazio.

  O modelo de SAP do Azure Resource Manager cria automaticamente a rede virtual do Azure e a sub-rede.

> [!NOTE]
> Também terá de implementar, pelo menos, uma máquina de virtual dedicada para o serviço Active Directory e DNS na mesma instância de rede Virtual do Azure. O modelo não cria estas máquinas virtuais.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Preparar a infraestrutura para a arquitetura de modelo de 2

Pode utilizar este modelo de Gestor de recursos do Azure para SAP para o ajudar a simplificar a implantação de recursos de infraestrutura necessária para 2 modelo de arquitetura do SAP.

Aqui é onde pode obter os modelos Azure Resource Manager para este cenário de implementação:

* [Imagem do Marketplace do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Imagem do Marketplace do Azure ao utilizar discos geridos](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Imagem personalizada com o Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Preparar a infraestrutura para a arquitetura de modelo de 3

Pode preparar a infraestrutura e configurar o SAP para múltiplos SID. Por exemplo, pode adicionar uma instância do SAP ASCS/SCS adicional para um *existente* configuração de cluster. Para obter mais informações, consulte [configurar uma instância do SAP ASCS/SCS adicional para uma configuração de cluster existente criar uma configuração de múltiplos SID SAP no Azure Resource Manager][sap-ha-multi-sid-guide].

Se quiser criar um novo cluster de múltiplos SID, pode utilizar o múltiplos SID [modelos de início rápido no GitHub](https://github.com/Azure/azure-quickstart-templates).

Para criar um novo cluster de múltiplos SID, tem de implementar os modelos de três seguintes:

* [Modelo do ASCS/SCS](#ASCS-SCS-template)
* [Modelo de base de dados](#database-template)
* [Modelo de aplicativo de servidores](#application-servers-template)

As secções seguintes têm mais detalhes sobre os parâmetros que tem de fornecer nos modelos e modelos.

### <a name="ASCS-SCS-template"></a> Modelo do ASCS/SCS

O modelo do ASCS/SCS implementa duas máquinas virtuais que pode utilizar para criar um cluster de ativação pós-falha do Windows Server que hospeda várias instâncias do ASCS/SCS.

Para configurar o modelo de múltiplos SID do ASCS/SCS, além da [modelo de múltiplos SID do ASCS/SCS] [ sap-templates-3-tier-multisid-xscs-marketplace-image] ou [modelo de múltiplos SID do ASCS/SCS ao utilizar discos geridos] [ sap-templates-3-tier-multisid-xscs-marketplace-image-md], introduza os valores para os seguintes parâmetros:

- **Prefixo de recurso**:  Defina o prefixo de recursos, o que é utilizado para o prefixo de todos os recursos que são criados durante a implantação. Como os recursos não pertencem a apenas um sistema SAP, o prefixo do recurso não é o SID de um sistema SAP.  O prefixo tem de ter entre três e seis carateres.
- **Tipo de pilha**: Selecione o tipo de pilha do sistema SAP. Dependendo do tipo de pilha, o Balanceador de carga do Azure tem um (ABAP ou Java apenas) ou dois (ABAP + Java) endereços IP privados por sistema SAP.
- **Tipo de SO**: Selecione o sistema operativo das máquinas virtuais.
- **Contagem de sistema SAP**: Selecione o número de sistemas SAP que pretende instalar neste cluster.
- **Disponibilidade do sistema**: Selecione **HA**.
- **Nome de utilizador administrador e a palavra-passe de administrador**: Crie um novo utilizador, que pode ser utilizado para iniciar sessão máquina.
- **Sub-rede nova ou existente**: Defina se pretende criar uma nova rede virtual e uma sub-rede ou utilizar uma sub-rede existente. Se já tiver uma rede virtual que está ligada à sua rede no local, selecione **existente**.
- **Id de sub-rede**: Se pretender implementar a VM para uma VNet já existente em que tem uma sub-rede definida a VM deve ser atribuída para nomear o ID dessa sub-rede. O ID normalmente fica assim:

  /subscriptions/\<id de subscrição\>/resourceGroups/\<nome do grupo de recursos\>/providers/Microsoft.Network/virtualNetworks/\<nome da rede virtual\>/subnets/ \<nome da sub-rede\>

O modelo implementa uma instância de Balanceador de carga do Azure, que suporta vários sistemas SAP:

- As instâncias do ASCS estão configuradas para o número de instância 00, 10, 20...
- As instâncias SCS estão configuradas para o número de instância 01, 11, 21...
- As instâncias de servidor de replicação de colocar em fila do ASCS (ERS) (apenas Linux) estão configuradas para o número de instância 02, 12, 22...
- As instâncias de SCS ERS (apenas Linux) estão configuradas para o número de instância 03, 13, 23...

O Balanceador de carga contém 1 VIP(s) (2 para Linux), 1 x VIP para ASCS/SCS e 1 x VIP para ERS (apenas Linux).

#### <a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a> Portas de SAP ASCS/SCS
A lista seguinte contém todas as regras (onde x é o número do sistema SAP, por exemplo, 1, 2, 3...) de balanceamento de carga:
- Portas de específicas do Windows para todos os sistemas SAP: 445, 5985
- Portas do ASCS (número de instância x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- Portas SCS (número de instância x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- Portas de ASCS ERS no Linux (número de instância x2): 33x2, 5x213, 5x214, 5x216
- Portas de SCS ERS no Linux (número de instância x3): 33x3, 5x313, 5x314, 5x316

O Balanceador de carga está configurado para utilizar as seguintes portas da sonda (onde x é o número do sistema SAP, por exemplo, 1, 2, 3...):
- Porta de sonda de Balanceador de carga interno do ASCS/SCS: 620x0
- Porta de sonda (apenas Linux) do Balanceador de carga ERS internos: 621x2

### <a name="database-template"></a> Modelo de base de dados

O modelo de base de dados implementa uma ou duas máquinas virtuais que pode utilizar para instalar o sistema de gestão de base de dados relacional (RDBMS) para um sistema SAP. Por exemplo, se implementar um modelo do ASCS/SCS para cinco sistemas SAP, terá de implementar este modelo de cinco vezes.

Para configurar o modelo de múltiplos SID de base de dados, além da [modelo de múltiplos SID do banco de dados] [ sap-templates-3-tier-multisid-db-marketplace-image] ou [modelo de múltiplos SID de banco de dados ao utilizar discos geridos] [ sap-templates-3-tier-multisid-db-marketplace-image-md], introduza os valores para os seguintes parâmetros:

- **Id de sistema de SAP**: Introduza o ID de sistema SAP do sistema SAP que pretende instalar. O ID é utilizado como um prefixo para os recursos que são implementados.
- **Tipo de SO**: Selecione o sistema operativo das máquinas virtuais.
- **DbType**: Selecione o tipo de base de dados que pretende instalar no cluster. Selecione **SQL** se pretender instalar o Microsoft SQL Server. Selecione **HANA** se planear instalar o SAP HANA nas máquinas virtuais. Certifique-se de que seleciona o tipo de sistema operacional correto. Selecione **Windows** para SQL e selecione uma distribuição do Linux para o HANA. Balanceador de carga do Azure que está ligada às máquinas virtuais está configurado para suportar o tipo de base de dados selecionada:
  * **SQL**: A load balancer balanceamento de carga porta 1433. Certifique-se utilizar esta porta para a configuração do SQL Server AlwaysOn.
  * **HANA**: As load balancer balanceamento de carga as portas 35015 e 35017. Certifique-se instalar o SAP HANA com o número de instância **50**.
  O Balanceador de carga utiliza a porta de sonda 62550.
- **Tamanho do sistema de SAP**: Defina o número de SAPS fornece o novo sistema. Se tiver a não certeza SAPS quantos requer que o sistema, peça ao seu parceiro de tecnologia de SAP ou integrador de sistemas.
- **Disponibilidade do sistema**: Selecione **HA**.
- **Nome de utilizador administrador e a palavra-passe de administrador**: Crie um novo utilizador, que pode ser utilizado para iniciar sessão máquina.
- **Id de sub-rede**: Introduza o ID da sub-rede que utilizou durante a implementação do modelo ASCS/SCS ou o ID da sub-rede que foi criada como parte da implementação de modelo do ASCS/SCS.

### <a name="application-servers-template"></a> Modelo de aplicativo de servidores

O modelo de servidores de aplicativo implementa dois ou mais máquinas virtuais que podem ser utilizadas como instâncias de servidor de aplicações SAP para um sistema SAP. Por exemplo, se implementar um modelo do ASCS/SCS para cinco sistemas SAP, terá de implementar este modelo de cinco vezes.

Para configurar o modelo de múltiplos SID de servidores de aplicativo, além da [o modelo de múltiplos SID de servidores de aplicativo] [ sap-templates-3-tier-multisid-apps-marketplace-image] ou [servidores múltiplos SID o modelo de aplicativo ao utilizar discos geridos] [ sap-templates-3-tier-multisid-apps-marketplace-image-md], introduza os valores para os seguintes parâmetros:

  -  **Id de sistema de SAP**: Introduza o ID de sistema SAP do sistema SAP que pretende instalar. O ID é utilizado como um prefixo para os recursos que são implementados.
  -  **Tipo de SO**: Selecione o sistema operativo das máquinas virtuais.
  -  **Tamanho do sistema de SAP**: O número de SAPS fornece o novo sistema. Se tiver a não certeza SAPS quantos requer que o sistema, peça ao seu parceiro de tecnologia de SAP ou integrador de sistemas.
  -  **Disponibilidade do sistema**: Selecione **HA**.
  -  **Nome de utilizador administrador e a palavra-passe de administrador**: Crie um novo utilizador, que pode ser utilizado para iniciar sessão máquina.
  -  **Id de sub-rede**: Introduza o ID da sub-rede que utilizou durante a implementação do modelo ASCS/SCS ou o ID da sub-rede que foi criada como parte da implementação de modelo do ASCS/SCS.


## <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Rede Virtual do Azure
No nosso exemplo, o espaço de endereços da instância de rede Virtual do Azure é 10.0.0.0/16. Existe uma sub-rede denominada sub-rede, com um intervalo de endereços de 10.0.0.0/24. Todas as máquinas virtuais e Balanceadores de carga interno são implementadas nesta rede virtual.

> [!IMPORTANT]
> Não efetue quaisquer alterações às definições de rede dentro do sistema operativo convidado. Isto inclui os endereços IP, servidores DNS e sub-rede. Configure todas as definições de rede no Azure. O serviço de anfitrião configuração protocolo DHCP (Dynamic) propaga as suas definições.
>
>

## <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> Endereços IP de DNS

Para definir endereços de IP de DNS necessários, conclua os seguintes passos:

1. No portal do Azure, no **servidores DNS** painel, certifique-se de que a rede virtual **servidores DNS** opção estiver definida como **DNS personalizado**.
2. Selecione as definições com base no tipo de rede que tiver. Para obter mais informações, consulte os seguintes recursos:
   * [Conectividade de rede da empresa (em vários locais)][planning-guide-2.2]: Adicione os endereços IP dos servidores DNS no local.  
   Pode ampliar servidores DNS no local para as máquinas virtuais que estão em execução no Azure. Nesse cenário, pode adicionar os endereços IP das máquinas virtuais do Azure no qual executa o serviço DNS.
   * Para implementações de VM isoladas no Azure: Implemente uma máquina virtual adicional na mesma instância de rede Virtual que funciona como um servidor DNS. Adicione os endereços IP das máquinas virtuais do Azure que tenha configurado para executar o serviço DNS.

   ![Figura 2: Configurar servidores DNS para rede Virtual do Azure][sap-ha-guide-figure-3001]

   _**Figura 2:** Configurar servidores DNS para rede Virtual do Azure_

   > [!NOTE]
   > Se alterar os endereços IP dos servidores DNS, terá de reiniciar as máquinas virtuais do Azure para aplicar a alteração e propagar os novos servidores DNS.
   >
   >

No nosso exemplo, o serviço DNS é instalado e configurado nestas máquinas virtuais do Windows:

| Função de máquina virtual | Nome de anfitrião de máquina virtual | Nome da placa de rede | Endereço IP estático |
| --- | --- | --- | --- |
| Primeiro servidor DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Segundo servidor DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

## <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Os nomes de anfitrião e endereços IP estáticos para a instância em cluster do SAP ASCS/SCS e a instância em cluster do DBMS

Para implementação no local, terá destes nomes de anfitrião reservados e endereços IP:

| Função de nome de anfitrião virtual | Nome de anfitrião virtual | Endereço IP estático virtual |
| --- | --- | --- |
| SAP ASCS/SCS primeiro cluster virtual nome de anfitrião (a gestão de clusters) |pr1-ascs-vir |10.0.0.42 |
| Nome de anfitrião virtual de instância do SAP ASCS/SCS |pr1-ascs-sap |10.0.0.43 |
| Nome de anfitrião virtual de cluster segundo do SAP DBMS (gerenciamento de cluster) |pr1-dbms-vir |10.0.0.32 |

Quando criar o cluster, crie o anfitrião virtual nomes pr1-ascs-vir e pr1-dbms-vir e os endereços IP associados que gerem o próprio cluster. Para obter informações sobre como fazer isso, consulte [recolher nós de cluster numa configuração de cluster][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config].

Pode criar manualmente os outros dois nomes de anfitrião virtual, pr1-ascs-sap e pr1-dbms-sap e os endereços IP associados, no servidor DNS. A instância do SAP ASCS/SCS em cluster e a instância em cluster do DBMS usam estes recursos. Para obter informações sobre como fazer isso, consulte [criar um nome de anfitrião virtual para uma instância em cluster do SAP ASCS/SCS][sap-ha-guide-9.1.1].

## <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Conjunto de endereços IP estáticos para as máquinas de virtuais SAP
Depois de implementar as máquinas virtuais para utilizar no seu cluster, terá de definir endereços IP estáticos para todas as máquinas virtuais. Opte por fazê-lo na configuração de rede Virtual do Azure e não no sistema operativo convidado.

1. No portal do Azure, selecione **grupo de recursos** > **placa de rede** > **definições** > **deendereçodeIP**.
2. Na **endereços IP** painel, em **atribuição**, selecione **estático**. Na **endereço IP** , introduza o endereço IP que pretende utilizar.

   > [!NOTE]
   > Se alterar o endereço IP da placa de rede, terá de reiniciar as máquinas virtuais do Azure para aplicar a alteração.  
   >
   >

   ![Figura 3: Conjunto de endereços IP estáticos para a placa de rede de cada máquina virtual][sap-ha-guide-figure-3002]

   _**Figura 3:** Conjunto de endereços IP estáticos para a placa de rede de cada máquina virtual_

   Repita este passo para todas as interfaces de rede, que é, todas as máquinas virtuais, incluindo máquinas virtuais que pretende utilizar para o seu serviço Active Directory ou DNS.

No nosso exemplo, temos estas máquinas virtuais e endereços IP estáticos:

| Função de máquina virtual | Nome de anfitrião de máquina virtual | Nome da placa de rede | Endereço IP estático |
| --- | --- | --- | --- |
| Primeira instância de servidor de aplicação SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Segunda instância de servidor de aplicações SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Última instância de servidor de aplicação SAP |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Primeiro nó de cluster para a instância ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Segundo nó de cluster para a instância ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Primeiro nó de cluster para a instância de DBMS |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Segundo nó de cluster para a instância de DBMS |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

## <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Definir um endereço IP estático para o Balanceador de carga interno do Azure

O modelo de SAP do Azure Resource Manager cria um balanceador de carga interno do Azure que é utilizado para o cluster de instância do SAP ASCS/SCS e o cluster do DBMS.

> [!IMPORTANT]
> O endereço IP do nome de anfitrião virtual de SAP ASCS/SCS é o mesmo que o endereço IP do Balanceador de carga interno de SAP ASCS/SCS: pr1-lb-ascs.
> O endereço IP do nome virtual do DBMS é o mesmo que o endereço IP do Balanceador de carga interno DBMS: pr1-lb-dbms.
>
>

Para definir um endereço IP estático para o Balanceador de carga interno do Azure:

1. A implementação inicial define o endereço IP do Balanceador de carga interno para **dinâmica**. No portal do Azure, sobre o **endereços IP** painel, em **atribuição**, selecione **estático**.
2. Definir o endereço IP do Balanceador de carga interno **pr1-lb-ascs** para o endereço IP do nome de anfitrião virtual de instância do SAP ASCS/SCS.
3. Definir o endereço IP do Balanceador de carga interno **pr1-lb-dbms** para o endereço IP do nome de anfitrião virtual de instância do DBMS.

   ![Figura 4: Conjunto de endereços IP estáticos para o Balanceador de carga interno para a instância do SAP ASCS/SCS][sap-ha-guide-figure-3003]

   _**Figura 4:** Conjunto de endereços IP estáticos para o Balanceador de carga interno para a instância do SAP ASCS/SCS_

No nosso exemplo, temos dois balanceadores de carga interno do Azure que estes endereços IP estáticos:

| Função de Balanceador de carga interno do Azure | Nome do Balanceador de carga interno do Azure | Endereço IP estático |
| --- | --- | --- |
| Balanceador de carga interno de instância do SAP ASCS/SCS |pr1-lb-ascs |10.0.0.43 |
| Balanceador de carga interno do SAP DBMS |pr1-lb-dbms |10.0.0.33 |


## <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Regras para o Balanceador de carga interno do Azure de balanceamento de carga do ASCS/SCS de predefinição

O modelo de SAP do Azure Resource Manager cria as portas que precisa:
* Uma instância de ABAP ASCS, com o número de instância padrão 00
* Uma instância de SCS de Java, com o número de instância padrão 01

Quando instala sua instância do SAP ASCS/SCS, tem de utilizar o número de instância padrão 00 para sua instância do ASCS de ABAP e o número de instância padrão 01 para a sua instância do SCS de Java.

Em seguida, crie a necessária pontos de extremidade para as portas de SAP NetWeaver de balanceamento de carga interno.

Para criar pontos finais de balanceamento de carga interna necessária, primeiro, crie esses pontos de extremidade para as portas de SAP NetWeaver ABAP ASCS de balanceamento de carga:

| Nome da regra balanceamento de carga do serviço | Números de porta predefinidos | Portas concretas para (instância ASCS com o número de instância 00) (ERS com 10) |
| --- | --- | --- |
| Servidor de colocar em fila / *lbrule3200* |32\<InstanceNumber\> |3200 |
| Servidor de mensagens ABAP / *lbrule3600* |36\<InstanceNumber\> |3600 |
| Mensagem ABAP interna / *lbrule3900* |39\<InstanceNumber\> |3900 |
| Servidor de mensagens HTTP / *Lbrule8100* |81\<InstanceNumber\> |8100 |
| Iniciar o SAP ASCS HTTP do serviço / *Lbrule50013* |5\<InstanceNumber\>13 |50013 |
| SAP iniciar o serviço de HTTPS do ASCS / *Lbrule50014* |5\<InstanceNumber\>14 |50014 |
| Replicação de colocar em fila / *Lbrule50016* |5\<InstanceNumber\>16 |50016 |
| SAP iniciar o serviço ERS HTTP *Lbrule51013* |5\<InstanceNumber\>13 |51013 |
| SAP iniciar o serviço ERS HTTP *Lbrule51014* |5\<InstanceNumber\>14 |51014 |
| Gestão remota do Windows (WinRM) *Lbrule5985* | |5985 |
| Partilha de ficheiros *Lbrule445* | |445 |

**Tabela 1:** Números de porta das instâncias do ASCS de ABAP SAP NetWeaver

Em seguida, crie esses pontos de extremidade para as portas de SAP NetWeaver Java SCS de balanceamento de carga:

| Nome da regra balanceamento de carga do serviço | Números de porta predefinidos | Portas concretas para (a instância SCS com o número de instância 01) (ERS com 11) |
| --- | --- | --- |
| Servidor de colocar em fila / *lbrule3201* |32\<InstanceNumber\> |3201 |
| Servidor de gateway / *lbrule3301* |33\<InstanceNumber\> |3301 |
| Servidor de mensagens de Java / *lbrule3900* |39\<InstanceNumber\> |3901 |
| Servidor de mensagens HTTP / *Lbrule8101* |81\<InstanceNumber\> |8101 |
| SAP iniciar o serviço SCS HTTP / *Lbrule50113* |5\<InstanceNumber\>13 |50113 |
| SAP iniciar serviço SCS HTTPS / *Lbrule50114* |5\<InstanceNumber\>14 |50114 |
| Replicação de colocar em fila / *Lbrule50116* |5\<InstanceNumber\>16 |50116 |
| SAP iniciar o serviço ERS HTTP *Lbrule51113* |5\<InstanceNumber\>13 |51113 |
| SAP iniciar o serviço ERS HTTP *Lbrule51114* |5\<InstanceNumber\>14 |51114 |
| WinRM *Lbrule5985* | |5985 |
| Partilha de ficheiros *Lbrule445* | |445 |

**Tabela 2:** Números de porta das instâncias do SAP NetWeaver Java SCS

![Figura 5: Regras para o Balanceador de carga interno do Azure de balanceamento de carga do ASCS/SCS de predefinição][sap-ha-guide-figure-3004]

_**Figura 5:** Regras para o Balanceador de carga interno do Azure de balanceamento de carga do ASCS/SCS de predefinição_

Defina o endereço IP do load balancer pr1-lb-dbms para o endereço IP do nome de anfitrião virtual de instância do DBMS.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Alterar a regras para o Balanceador de carga interno do Azure de balanceamento de carga do padrão ASCS/SCS

Se pretender utilizar números diferentes para as instâncias de SAP ASCS ou SCS, tem de alterar os nomes e valores de suas portas valores predefinidos.

1. No portal do Azure, selecione  **\<SID\>Balanceador de carga do ascs - lb** > **regras de balanceamento de carga**.
2. Todas as regras que pertencem à instância do SAP ASCS ou SCS de balanceamento de carga, altere estes valores:

   * Name
   * Porta
   * Porta de back-end

   Por exemplo, se pretender alterar o número de instância do ASCS predefinido de 00 e 31, terá de efetuar as alterações para todas as portas listadas na tabela 1.

   Eis um exemplo de uma atualização para a porta *lbrule3200*.

   ![Figura 6: Alterar a regras para o Balanceador de carga interno do Azure de balanceamento de carga do padrão ASCS/SCS][sap-ha-guide-figure-3005]

   _**Figura 6:** Alterar a regras para o Balanceador de carga interno do Azure de balanceamento de carga do padrão ASCS/SCS_

## <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Adicionar máquinas de virtuais do Windows para o domínio

Depois de atribuir um endereço IP estático para as máquinas virtuais, adicione as máquinas virtuais no domínio.

![Figura 7: Adicionar uma máquina virtual a um domínio][sap-ha-guide-figure-3006]

_**Figura 7:** Adicionar uma máquina virtual a um domínio_

## <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Adicionar entradas de Registro em ambos os nós de cluster da instância do SAP ASCS/SCS

O Balanceador de carga do Azure tem um balanceador de carga interno que fecha ligações quando as ligações estão Inativas durante um determinado período de tempo (um tempo limite de inatividade). Processos de trabalho SAP na caixa de diálogo conexões abertas de instâncias a colocar em fila de SAP processam assim que o primeiro colocar em fila/remover da fila do pedido tem de ser enviada. Estas ligações normalmente mantêm estabelecidas até o processo de trabalho ou reinicia o processo de colocar em fila. No entanto, se a ligação estiver inativa durante um determinado período de tempo, o Balanceador de carga interno do Azure fecha as ligações. Isso não é um problema porque o processo de trabalho SAP reestablishes a ligação para o processo de colocar em fila se já não existir. Essas atividades estão documentadas nos rastreios de desenvolvedor de processos SAP, mas eles criam uma grande quantidade de conteúdo extra nos rastreios. É uma boa idéia para alterar o TCP/IP `KeepAliveTime` e `KeepAliveInterval` em ambos os nós de cluster. Combine essas alterações nos parâmetros do TCP/IP com os parâmetros de perfil do SAP, descritos posteriormente neste artigo.

Para adicionar entradas de Registro em ambos os nós de cluster da instância do SAP ASCS/SCS, primeiro, adicione estas entradas de Registro do Windows em ambos os nós de cluster do Windows para o SAP ASCS/SCS:

| Caminho | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome da variável |`KeepAliveTime` |
| Tipo de variável |REG_DWORD (Decimal) |
| Value |120000 |
| Ligar a documentação |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**Tabela 3:** Alterar o primeiro parâmetro de TCP/IP

Em seguida, adicione esta entrada de registo do Windows em ambos os nós de cluster do Windows para o SAP ASCS/SCS:

| Caminho | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome da variável |`KeepAliveInterval` |
| Tipo de variável |REG_DWORD (Decimal) |
| Value |120000 |
| Ligar a documentação |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**Tabela 4:** Altere o segundo parâmetro de TCP/IP

Para aplicar as alterações, reinicie ambos os nós de cluster.

## <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Configurar um cluster de ativação pós-falha do Windows Server para uma instância do SAP ASCS/SCS

Configurar um cluster de ativação pós-falha do Windows Server para uma instância do SAP ASCS/SCS envolve estas tarefas:

- Recolha os nós de cluster numa configuração de cluster.
- Configure um testemunho de partilha de ficheiros do cluster.

### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Recolher os nós de cluster numa configuração de cluster

1. No Assistente de funcionalidades e Adicionar função, adicione o clustering para ambos os nós de cluster de ativação pós-falha.
2. Como configurar o cluster de ativação pós-falha utilizando o Gestor de clusters de ativação pós-falha. No Gestor de clusters de ativação pós-falha, selecione **criar clusters**e, em seguida, adicione apenas o nome do cluster primeiro (nó A). Não adicione o segundo nó ainda; adicionar o segundo nó num passo posterior.

   ![Figura 8: Adicione o nome do servidor ou máquina virtual do primeiro nó de cluster][sap-ha-guide-figure-3007]

   _**Figura 8:** Adicione o nome do servidor ou máquina virtual do primeiro nó de cluster_

3. Introduza o nome de rede (nome de anfitrião virtual) do cluster.

   ![Figura 9: Introduza o nome do cluster][sap-ha-guide-figure-3008]

   _**Figura 9:** Introduza o nome do cluster_

4. Depois de criar o cluster, execute um teste de validação de cluster.

   ![Figura 10: Executar a verificação de validação de cluster][sap-ha-guide-figure-3009]

   _**Figura 10:** Executar a verificação de validação de cluster_

   Pode ignorar quaisquer avisos sobre discos neste momento no processo. Adicionará que um testemunho de partilha de ficheiros e o SIOS discos partilhados mais tarde. Nesta fase, não precisa se preocupar sobre ter um quórum.

   ![Figura 11: Não foi encontrado nenhum disco de quórum][sap-ha-guide-figure-3010]

   _**Figura 11:** Não foi encontrado nenhum disco de quórum_

   ![Figura 12: Um recurso fundamental do cluster tem um novo endereço IP][sap-ha-guide-figure-3011]

   _**Figura 12:** Um recurso fundamental do cluster tem um novo endereço IP_

5. Altere o endereço IP do serviço de cluster básico. O cluster não é possível iniciar até alterar o endereço IP do serviço de cluster básico, porque o endereço IP do servidor aponta para um de nós de máquinas virtuais. Fazê-lo na **propriedades** página de recurso IP do serviço de cluster principal.

   Por exemplo, é necessário atribuir um endereço IP (no nosso exemplo, 10.0.0.42) para o cluster anfitrião virtual nome pr1-ascs-vir.

   ![Figura 13: Na caixa de diálogo de propriedades, altere o endereço IP][sap-ha-guide-figure-3012]

   _**Figura 13:** Na **propriedades** diálogo caixa, altere o endereço IP_

   ![Figura 14: Atribuir o endereço IP que está reservado para o cluster][sap-ha-guide-figure-3013]

   _**Figura 14:** Atribuir o endereço IP que está reservado para o cluster_

6. Coloque online o nome de anfitrião virtual de cluster.

   ![Figura 15: O serviço de núcleo de cluster está em execução, com o endereço IP correto][sap-ha-guide-figure-3014]

   _**Figura 15:** O serviço de núcleo de cluster está em execução, com o endereço IP correto_

7. Adicione o segundo nó de cluster.

   Agora que o serviço de cluster principal está em execução, pode adicionar o segundo nó de cluster.

   ![Figura 16 adicionar o segundo nó de cluster][sap-ha-guide-figure-3015]

   _**Figura 16:** Adicionar o segundo nó de cluster_

8. Introduza um nome para o anfitrião de nó de cluster segundo.

   ![Figura 17: Introduza o nome de anfitrião do segundo nó de cluster][sap-ha-guide-figure-3016]

   _**Figura 17:** Introduza o nome de anfitrião do segundo nó de cluster_

   > [!IMPORTANT]
   > Certifique-se de que o **adicionar todo o armazenamento elegível ao cluster** caixa de verificação está *não* selecionado.  
   >
   >

   ![Figura 18: Não selecione a caixa de verificação][sap-ha-guide-figure-3017]

   _**Figura 18:** Fazer *não* selecione a caixa de verificação_

   Pode ignorar avisos sobre o quórum e discos. Irá configurar o quórum e partilhar o disco mais tarde, conforme descrito em [instalar o SIOS DataKeeper Cluster Edition para um disco de compartilhamento de cluster do SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].

   ![Figura 19: Ignorar avisos sobre o quórum de disco][sap-ha-guide-figure-3018]

   _**Figura 19:** Ignorar avisos sobre o quórum de disco_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Configurar um testemunho de partilha de ficheiros do cluster

Configurar um testemunho de partilha de ficheiros do cluster envolve estas tarefas:

- Crie uma partilha de ficheiros.
- Defina o quórum de testemunho de partilha de ficheiros no Gestor de clusters de ativação pós-falha.

#### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Criar uma partilha de ficheiros

1. Selecione um testemunho de partilha de ficheiros em vez de um disco de quórum. O SIOS DataKeeper suportar esta opção.

   Os exemplos neste artigo, é o testemunho de partilha de ficheiros no servidor do Active Directory ou o DNS que está em execução no Azure. A testemunha de compartilhamento de arquivo é chamada domcontr-0. Porque iria configurar uma ligação de VPN para o Azure (através do Gateway de VPN ou ExpressRoute do Azure), o seu serviço do Active Directory ou o DNS está no local e não é adequado executar um testemunho de partilha de ficheiros.

   > [!NOTE]
   > Se o seu serviço do Active Directory ou o DNS é executado apenas no local, não configure seu testemunho de partilha de ficheiros no sistema de operativo Active Directory ou o Windows de DNS que está a executar no local. Latência de rede entre nós de cluster em execução no Azure Active Directory ou do DNS no local poderá ser demasiado grande e causar problemas de conectividade. Certifique-se de que configurar a testemunha de compartilhamento de arquivos numa máquina virtual do Azure que está a executar o próximo nó do cluster.  
   >
   >

   O disco de quórum tem, pelo menos, 1024 MB de espaço livre. Recomendamos a 2048 MB de espaço livre para a unidade de quórum.

2. Adicione o objeto de nome de cluster.

   ![Figura 20: Atribuir as permissões na partilha para o objeto de nome de cluster][sap-ha-guide-figure-3019]

   _**Figura 20:** Atribuir as permissões na partilha para o objeto de nome de cluster_

   Certifique-se de que as permissões de incluam a autoridade para alterar os dados na partilha para o objeto de nome de cluster (no nosso exemplo, pr1-ascs-vir$).

3. Para adicionar o objeto de nome do cluster à lista, selecione **adicionar**. Altere o filtro para verificar a existência de objetos de computador, para além dos mostrado na figura 22.

   ![Figura 21: Alterar os tipos de objeto para incluir computadores][sap-ha-guide-figure-3020]

   _**Figura 21:** Alteração **tipos de objeto** para incluir computadores_

   ![Figura 22: Selecione a caixa de verificação de computadores][sap-ha-guide-figure-3021]

   _**Figura 22:** Selecione o **computadores** caixa de verificação_

4. Introduza o objeto de nome de cluster, conforme mostrado na figura 21. Uma vez que o registo já foi criado, pode alterar as permissões, conforme mostrado na figura 20.

5. Selecione o **segurança** separador da partilha e, em seguida, defina mais detalhado de permissões para o objeto de nome de cluster.

   ![Figura 23: Definir os atributos de segurança para o objeto de nome de cluster no quórum de partilha de ficheiros][sap-ha-guide-figure-3022]

   _**Figura 23:** Definir os atributos de segurança para o objeto de nome de cluster no quórum de partilha de ficheiros_

#### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Definir o quórum de testemunho de partilha de ficheiros no Gestor de clusters de ativação pós-falha

1. Abra a configurar o Assistente de configuração de quórum.

   ![Figura 24: Iniciar o Assistente de configuração de quórum de Cluster de configurar][sap-ha-guide-figure-3023]

   _**Figura 24:** Iniciar o Assistente de configuração de quórum de Cluster de configurar_

2. Sobre o **selecionar opção de configuração de quórum** , selecione **selecione o testemunho de quórum**.

   ![Figura 25: Pode escolher de entre as configurações de quórum][sap-ha-guide-figure-3024]

   _**Figura 25:** Pode escolher de entre as configurações de quórum_

3. Sobre o **selecionar testemunho de quórum** , selecione **configurar um testemunho de partilha de ficheiros**.

   ![Figura 26: Selecione o testemunho de partilha de ficheiros][sap-ha-guide-figure-3025]

   _**Figura 26:** Selecione o testemunho de partilha de ficheiros_

4. Introduza o caminho UNC para a partilha de ficheiros (no nosso exemplo, \\domcontr 0\FSW). Para ver uma lista de alterações pode fazer, selecione **seguinte**.

   ![Figura 27: Definir a localização da partilha de ficheiros para a partilha de testemunho][sap-ha-guide-figure-3026]

   _**Figura 27:** Definir a localização da partilha de ficheiros para a partilha de testemunho_

5. Selecione as alterações que pretende e, em seguida, selecione **seguinte**. Terá de reconfigurar com êxito a configuração do cluster, conforme mostrado na figura 28:  

   ![Figura 28: Confirmação de que já reconfigurado o cluster][sap-ha-guide-figure-3027]

   _**Figura 28:** Confirmação de que já reconfigurado o cluster_

Depois de instalar com êxito o cluster de ativação pós-falha do Windows, terá de alterar alguns limites para que eles se adaptar a deteção de ativação pós-falha para condições no Azure. Os parâmetros de ser alteradas estão documentados em [ajuste os limites de rede de cluster de ativação pós-falha][tuning-failover-cluster-network-thresholds]. Supondo que as duas VMs que constituem a configuração de cluster do Windows para ASCS/SCS estão na mesma sub-rede, altere os parâmetros seguintes para estes valores:

- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Estas definições foram testadas com os clientes e oferecem uma boa solução. Eles são flexíveis o suficiente, mas também fornecem ativação pós-falha for rápida o suficiente em condições de erro real numa software SAP ou num nó ou uma falha VM.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Instalar o SIOS DataKeeper Cluster Edition para o disco de compartilhamento de cluster do SAP ASCS/SCS

Agora tem uma configuração de clustering de ativação pós-falha do Windows Server no Azure. Para instalar uma instância do SAP ASCS/SCS, terá de um recurso de disco partilhado. Não é possível criar os recursos de disco partilhado que necessita no Azure. O SIOS DataKeeper Cluster Edition é uma solução de terceiros que pode utilizar para criar recursos de disco partilhado.

Instalar o SIOS DataKeeper Cluster Edition para o disco de compartilhamento de cluster do SAP ASCS/SCS envolve estas tarefas:

- Adicione o Microsoft .NET Framework 3.5.
- Instale o SIOS DataKeeper.
- Configure o SIOS DataKeeper.

### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Adicionar o .NET Framework 3.5
.NET framework 3.5 não é automaticamente ativado ou instalado no Windows Server 2012 R2. Uma vez que o SIOS DataKeeper requer o .NET ser em todos os nós em que instalar DataKeeper, tem de instalar o .NET Framework 3.5 no sistema operativo convidado de todas as máquinas virtuais no cluster.

Existem duas formas de adicionar o .NET Framework 3.5:

- Utilize a adicionar assistente funções e funcionalidades no Windows, conforme mostrado na figura 29:

  ![Figura 29: Instale o .NET Framework 3.5 ao utilizar a adicionar assistente funções e funcionalidades][sap-ha-guide-figure-3028]

  _**Figura 29:** Instale o .NET Framework 3.5 ao utilizar a adicionar assistente funções e funcionalidades_

  ![Figura 30: Progresso da instalação de barra quando instala o .NET Framework 3.5 ao utilizar a adicionar assistente funções e funcionalidades][sap-ha-guide-figure-3029]

  _**Figura 30:** Progresso da instalação de barra quando instala o .NET Framework 3.5 ao utilizar a adicionar assistente funções e funcionalidades_

- Utilize a ferramenta de linha de comando dism.exe. Para este tipo de instalação, precisa acessar o diretório SxS no suporte de dados de instalação do Windows. Na linha de comandos elevada, introduza este comando:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Instalar o SIOS DataKeeper

Instale o SIOS DataKeeper Cluster Edition em cada nó no cluster. Para criar o armazenamento partilhado virtual com o SIOS DataKeeper, criar um espelho sincronizado e, em seguida, simular o armazenamento partilhado de cluster.

Antes de instalar o software do SIOS, crie o utilizador de domínio DataKeeperSvc.

> [!NOTE]
> Adicione o utilizador de domínio de DataKeeperSvc para o grupo de administrador Local em ambos os nós de cluster.
>
>

Para instalar o SIOS DataKeeper:

1. Instale o software do SIOS em ambos os nós de cluster.

   ![Instalador do SIOS][sap-ha-guide-figure-3030]

   ![Figura 31: Primeira página da instalação do SIOS DataKeeper][sap-ha-guide-figure-3031]

   _**Figura 31:** Primeira página da instalação do SIOS DataKeeper_

2. Na caixa de diálogo, selecione **Sim**.

   ![Figura 32: DataKeeper informa que um serviço é desativado][sap-ha-guide-figure-3032]

   _**Figura 32:** DataKeeper informa que um serviço é desativado_

3. Na caixa de diálogo, é recomendável que selecione **conta de domínio ou servidor**.

   ![Figura 33: Seleção do usuário para o SIOS DataKeeper][sap-ha-guide-figure-3033]

   _**Figura 33:** Seleção do usuário para o SIOS DataKeeper_

4. Introduza o nome de utilizador da conta de domínio e palavra-passe que criou para o SIOS DataKeeper.

   ![Figura 34: Introduza o nome de utilizador de domínio e a palavra-passe para a instalação do SIOS DataKeeper][sap-ha-guide-figure-3034]

   _**Figura 34:** Introduza o nome de utilizador de domínio e a palavra-passe para a instalação do SIOS DataKeeper_

5. Instale a chave de licença para a sua instância do SIOS DataKeeper, conforme mostrado na figura 35.

   ![Figura 35: Introduza a chave de licença do SIOS DataKeeper][sap-ha-guide-figure-3035]

   _**Figura 35:** Introduza a chave de licença do SIOS DataKeeper_

6. Quando lhe for pedido, reinicie a máquina virtual.

### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Configurar o SIOS DataKeeper

Depois de instalar o SIOS DataKeeper em ambos os nós, inicie a configuração. O objetivo da configuração é ter a replicação de dados síncrona entre os discos adicionais que estejam anexados a cada uma das máquinas virtuais.

1. Inicie a ferramenta de configuração e gerenciamento de DataKeeper e, em seguida, selecione **ligar servidor**.

   ![Figura 36: Ferramenta de configuração e gerenciamento do SIOS DataKeeper][sap-ha-guide-figure-3036]

   _**Figura 36:** Ferramenta de configuração e gerenciamento do SIOS DataKeeper_

2. Introduza o nome ou endereço de TCP/IP do nó primeiro que a ferramenta de gerenciamento e a configuração deve se conectar para e, numa segunda etapa, o segundo nó.

   ![Figura 37: Inserir o nome ou endereço de TCP/IP do nó primeiro que a ferramenta de gerenciamento e a configuração deve se conectar a e, numa segunda etapa, o segundo nó][sap-ha-guide-figure-3037]

   _**Figura 37:** Inserir o nome ou endereço de TCP/IP do nó primeiro que a ferramenta de gerenciamento e a configuração deve se conectar a e, numa segunda etapa, o segundo nó_

3. Crie a tarefa de replicação entre os dois nós.

   ![Figura 38: Criar uma tarefa de replicação][sap-ha-guide-figure-3038]

   _**Figura 38:** Criar uma tarefa de replicação_

   Um assistente orienta-o ao longo do processo de criação de uma tarefa de replicação.

4. Defina o nome da tarefa de replicação.

   ![Figura 39: Definir o nome da tarefa de replicação][sap-ha-guide-figure-3039]

   _**Figura 39:** Definir o nome da tarefa de replicação_

   ![Figura 40: Definir a base de dados para o nó, que deve ser o nó de origem atual][sap-ha-guide-figure-3040]

   _**Figura 40:** Definir a base de dados para o nó, que deve ser o nó de origem atual_

5. Defina o nome, o endereço TCP/IP e o volume de disco do nó de destino.

   ![Figura 41: Definir o nome, o endereço TCP/IP e o volume de disco do nó de destino atual][sap-ha-guide-figure-3041]

   _**Figura 41:** Definir o nome, o endereço TCP/IP e o volume de disco do nó de destino atual_

6. Defina os algoritmos de compactação. No nosso exemplo, recomendamos que comprimir o fluxo de replicação. Especialmente em situações de ressincronização, a compressão do fluxo de replicação reduz drasticamente o tempo de ressincronização. Compressão utiliza os recursos de CPU e RAM de uma máquina virtual. À medida que a taxa de compressão aumenta, assim como o volume de recursos de CPU que são utilizados. Pode ajustar esta definição mais tarde.

7. Outra configuração que precisa verificar é se ocorre a replicação assíncrona ou síncrona. Quando protege configurações de SAP ASCS/SCS, tem de utilizar a replicação síncrona.  

   ![Figura 42: Definir os detalhes de replicação][sap-ha-guide-figure-3042]

   _**Figura 42:** Definir os detalhes de replicação_

8. Defina se o volume que é replicado pela tarefa de replicação deve ser representado para uma configuração de cluster de ativação pós-falha do Windows Server como um disco partilhado. Para a configuração de SAP ASCS/SCS, selecione **Sim** para que o cluster do Windows vê o volume replicado como um disco partilhado que pode ser usado como um volume de cluster.

   ![Figura 43: Selecione Sim para definir o volume replicado como um volume de cluster][sap-ha-guide-figure-3043]

   _**Figura 43:** Selecione **Sim** para definir o volume replicado como um volume de cluster_

   Depois de criar o volume, a ferramenta de configuração e gerenciamento de DataKeeper mostra que a tarefa de replicação está ativa.

   ![Figura 44: DataKeeper espelhamento síncrono para o disco de partilha da SAP ASCS/SCS está ativo][sap-ha-guide-figure-3044]

   _**Figura 44:** DataKeeper espelhamento síncrono para o disco de partilha da SAP ASCS/SCS está ativo_

   Gestor de clusters de ativação pós-falha mostra agora o disco como um disco de DataKeeper, conforme mostrado na figura 45:

   ![Figura 45: Gestor de clusters de ativação pós-falha mostra o disco que DataKeeper replicados][sap-ha-guide-figure-3045]

   _**Figura 45:** Gestor de clusters de ativação pós-falha mostra o disco que DataKeeper replicados_

## <a name="next-steps"></a>Passos Seguintes

* [Instalar o SAP NetWeaver HA com um cluster de ativação pós-falha do Windows e o disco partilhado para uma instância do SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]
