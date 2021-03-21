---
title: Infraestrutura Azure para SAP ASCS/SCS com WSFC&| de disco partilhado Microsoft Docs
description: Aprenda a preparar a infraestrutura Azure para SAP HA utilizando um cluster de failover do Windows e disco partilhado para uma instância SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4218b4c00b79d78965eaf6e73028e63f52b1ff17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673617"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Prepare a infraestrutura Azure para o SAP HA utilizando um cluster de failover do Windows e disco partilhado para SAP ASCS/SCS

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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Configuração de alta disponibilidade DE SAP multi-SID)

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


> ![SO Windows][Logo_Windows] Windows


Este artigo descreve os passos que toma para preparar a infraestrutura Azure para instalar e configurar uma instância SAP ASCS/SCS de alta disponibilidade num cluster de failover do Windows, utilizando um *disco partilhado* de cluster como opção para agrupar uma instância SAP ASCS.
Na documentação são apresentadas duas alternativas para o disco partilhado do *cluster:*

- [Discos partilhados do Azure](../../disks-shared.md)
- Usando [a SIOS DataKeeper Cluster Edition](https://us.sios.com/products/datakeeper-cluster/) para criar armazenamento espelhado, que simulará disco compartilhado agrupado 

A configuração apresentada está a contar com [grupos de colocação de proximidade Azure (PPG)](./sap-proximity-placement-scenarios.md) para obter a latência ideal da rede para cargas de trabalho SAP. A documentação não cobre a camada da base de dados.  

> [!NOTE]
> Os grupos de colocação de proximidade azul são pré-requisitos para a utilização do Disco Compartilhado Azure.
 

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar a instalação, reveja este artigo:

* [Guia de arquitetura: Cluster uma instância SAP ASCS/SCS num cluster de failover do Windows utilizando um disco partilhado de cluster][sap-high-availability-guide-wsfc-shared-disk]

## <a name="create-the-ascs-vms"></a>Criar os VMs ASCS

Para o cluster SAP ASCS/SCS, coloque dois VMs no Conjunto de Disponibilidade Azure. Implementar os VMs no mesmo Grupo de Colocação de Proximidade. Uma vez implantados os VMs:  
- Criar balanceador interno Azure para a instância SAP ASCS /SCS 
- Adicionar VMs do Windows ao domínio AD

Os nomes dos anfitriões e os endereços IP para o cenário apresentado são:

| Papel de nome de anfitrião | Nome do anfitrião | Endereço IP estático | Conjunto de disponibilidade | Grupo de colocação de proximidade |
| --- | --- | --- |---| ---|
| 1º cluster nó ASCS/SCS cluster |pr1-ascs-10 |10.0.0.4 |pr1-ascs-avset |PR1PPG |
| 2º cluster nó ASCS/SCS cluster |pr1-ascs-11 |10.0.0.5 |pr1-ascs-avset |PR1PPG |
| Nome da rede de cluster | pr1clust |10.0.0.42(**apenas** para o conjunto Win 2016) | n/a | n/a |
| Nome da rede de cluster ASCS | pr1-ascscl |10.0.0.43 | n/a | n/a |
| Nome da rede de cluster ERS **(apenas** para ERS2) | pr1-erscl |10.0.0.44 | n/a | n/a |


## <a name="create-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Criar esquilibrador interno de carga Azure

SAP ASCS, SAP SCS e o novo SAP ERS2, utilizem o nome de hospedeiro virtual e endereços IP virtuais. No Azure é necessário um [equilibrador](../../../load-balancer/load-balancer-overview.md) de carga para utilizar um endereço IP virtual. Recomendamos vivamente a utilização [do balanceador de carga Standard](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). 

> [!IMPORTANT]
> O IP flutuante não é suportado numa configuração IP secundária do NIC em cenários de equilíbrio de carga. Para mais detalhes consulte [as limitações do balançador de carga Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Se precisar de um endereço IP adicional para o VM, implante um segundo NIC.    


A lista a seguir mostra a configuração do balançador de carga (A)SCS/ERS. A configuração tanto para SAP ASCS como ERS2 executada no mesmo equilibrador de carga Azure.  

**a (A) SCS**
- Configuração frontend
    - Endereço IP ascs/SCS estático **10.0.0.43**
- Configuração de backend  
    Adicione todas as máquinas virtuais que devem fazer parte do cluster (A)SCS/ERS. Neste exemplo, vMs **pr1-ascs-10** e **pr1-ascs-11**.
- Porto de Sonda
    - Porta 620 **nr** Deixe a opção por defeito para Protocolo (TCP), Intervalo (5), limiar pouco saudável (2)
- Regras de equilíbrio de carga
    - Se utilizar o Balanceador de Carga Padrão, selecione portas HA
    - Se utilizar o Balanceador de Carga Básica, crie regras de equilíbrio de carga para as seguintes portas
        - 32 **nr** TCP
        - 36 **nr** TCP
        - 39 **nr** TCP
        - 81 **nr** TCP
        - 5 **nr** 13 TCP
        - 5 **nr** 14 TCP
        - 5 **nr** 16 TCP

    - Certifique-se de que o tempo limite de marcha lenta (minutos) está definido para o valor máximo 30 e que o IP flutuante (retorno direto do servidor) está ativado.

**ERS2**

Uma vez que o Enqueue Replication Server 2 (ERS2) também está agrupado, o endereço IP virtual ERS2 também deve ser configurado no Azure ILB, além de acima de SAP ASCS/SCS IP. Esta secção só se aplica se utilizar a arquitetura do servidor de replicação Enqueue 2.  
- 2ª configuração frontend
    - Endereço IP ESTÁTICO SAP ERS2 **10.0.0.44**

- Configuração de backend  
  Os VMs já foram adicionados à piscina de backend ILB.  

- 2ª Porta de Sonda
    - Porto 621 **nr**  
    Deixe a opção por defeito para Protocolo (TCP), Intervalo (5), limiar pouco saudável (2)

- 2ª Regras de equilíbrio de carga
    - Se utilizar o Balanceador de Carga Padrão, selecione portas HA
    - Se utilizar o Balanceador de Carga Básica, crie regras de equilíbrio de carga para as seguintes portas
        - 32 **nr** TCP
        - 33 **nr** TCP
        - 5 **nr** 13 TCP
        - 5 **nr** 14 TCP
        - 5 **nr** 16 TCP

    - Certifique-se de que o tempo limite de marcha lenta (minutos) está definido para o valor máximo 30 e que o IP flutuante (retorno direto do servidor) está ativado.


> [!TIP]
> Com o [modelo de gestor de recursos Azure para WSFC para a versão SAP ASCS/SCS com disco partilhado Azure,](https://github.com/robotechredmond/301-shared-disk-sap)pode automatizar a preparação da infraestrutura, utilizando o Disco Compartilhado Azure para um SAP SID com ERS1.  
> O modelo Azure ARM criará dois VMs Windows 2019 ou 2016, criará disco partilhado Azure e anexará aos VMs. O Azure Internal Load Balancer será criado e configurado também. Para mais detalhes - consulte o modelo ARM. 

## <a name="add-registry-entries-on-both-cluster-nodes-of-the-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Adicionar entradas de registo em ambos os nós de cluster da instância ASCS/SCS

O Balançador de Carga Azure pode fechar as ligações, se as ligações estiverem inativas durante um período e excederem o tempo limite de marcha lenta. O trabalho da SAP processa ligações abertas ao processo de enquese SAP logo que o primeiro pedido de enqueue/dequeue tenha de ser enviado. Para evitar interromper estas ligações, altere os valores TCP/IP KeepAliveTime e KeepAliveInterval em ambos os nós de cluster. Se utilizar o ERS1, também é necessário adicionar parâmetros de perfil SAP, como descrito mais tarde neste artigo.
As seguintes entradas de registo devem ser alteradas em ambos os nós do cluster:

- KeepAliveTime
- KeepAliveInterval

| Caminho| Nome da variável | Tipo variável  | Valor | Documentação |
| --- | --- | --- |---| ---|
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parâmetros |KeepAliveTime |REG_DWORD (Decimal) |120000 |[KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)) |
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parâmetros |KeepAliveInterval |REG_DWORD (Decimal) |120000 |[KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) |


Para aplicar as alterações, reinicie ambos os nós de cluster.
  
## <a name="add-the-windows-vms-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Adicione os VMs do Windows ao domínio
Depois de atribuir endereços IP estáticos às máquinas virtuais, adicione as máquinas virtuais ao domínio. 

## <a name="install-and-configure--windows-failover-cluster"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Instale e configuure o cluster de failover do Windows 

### <a name="install-the-windows-failover-cluster-feature"></a>Instale a funcionalidade de cluster failover do Windows

Executar este comando num dos nóns de cluster:

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # Install Windows features.
    # After the feature installs, manually reboot both nodes
    Invoke-Command $ClusterNodes {Install-WindowsFeature Failover-Clustering, FS-FileServer -IncludeAllSubFeature -IncludeManagementTools }
   ```

Uma vez concluída a instalação da função, reinicie ambos os nós de cluster.  

### <a name="test-and-configure-windows-failover-cluster"></a>Teste e configurar o cluster de failover do Windows 

No Windows 2019, o cluster reconhecerá automaticamente que está a funcionar no Azure, e como opção padrão para a gestão do cluster IP, utilizará o nome de Rede Distribuída. Portanto, utilizará qualquer um dos nós de cluster endereços IP locais. Como resultado, não há necessidade de um nome de rede dedicado (virtual) para o cluster, e não há necessidade de configurar este endereço IP no Azure Internal Load Balancer.

Para mais informações consulte, [o Windows Server 2019 Failover Clustering Novas funcionalidades](https://techcommunity.microsoft.com/t5/failover-clustering/windows-server-2019-failover-clustering-new-features/ba-p/544029) Executar este comando num dos nóns de cluster:

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # IP adress for cluster network name is needed ONLY on Windows Server 2016 cluster
    $ClusterStaticIPAddress = "10.0.0.42"
        
    # Test cluster
    Test-Cluster –Node $ClusterNodes -Verbose
    
    $ComputerInfo = Get-ComputerInfo
    
    $WindowsVersion = $ComputerInfo.WindowsProductName
    
    if($WindowsVersion -eq "Windows Server 2019 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2019 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes -Verbose
    }elseif($WindowsVersion -eq "Windows Server 2016 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2016 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes –StaticAddress $ClusterStaticIPAddress -Verbose 
    }else{
        Write-Error "Not supported Windows version!"
    }
   ```

### <a name="configure-cluster-cloud-quorum"></a>Configurar o quórum da nuvem de cluster
Ao utilizar o Windows Server 2016 ou 2019, recomendamos a configuração [do Azure Cloud Witness](/windows-server/failover-clustering/deploy-cloud-witness), como quórum de cluster.

Executar este comando num dos nóns de cluster:

   ```powershell
    $AzureStorageAccountName = "cloudquorumwitness"
    Set-ClusterQuorum –CloudWitness –AccountName $AzureStorageAccountName -AccessKey <YourAzureStorageAccessKey> -Verbose
   ```

### <a name="tuning-the-windows-failover-cluster-thresholds"></a>Sintonização dos limiares de cluster de failover do Windows
 
Depois de instalar com sucesso o cluster de failover do Windows, é necessário ajustar alguns limiares, para ser adequado para os clusters implantados no Azure. Os parâmetros a alterar são documentados nos [limiares da rede de clusters de falha de sintonização](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834). Assumindo que os seus dois VMs que compõem a configuração do cluster Do Windows para ASCS/SCS estão na mesma sub-rede, altere os seguintes parâmetros para estes valores:
- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- EncaminhamentoHistoryLength = 30

Estas configurações foram testadas com os clientes e oferecem um bom compromisso. São suficientemente resistentes, mas também fornecem falhas suficientemente rápidas para condições reais de erro nas cargas de trabalho do SAP ou falha em VM.  

## <a name="configure-azure-shared-disk"></a>Configurar disco compartilhado Azure
Esta secção só é aplicável se estiver a utilizar o disco partilhado Azure. 

### <a name="create-and-attach-azure-shared-disk-with-powershell"></a>Criar e anexar o disco compartilhado Azure com o PowerShell
Executar este comando num dos nós do cluster. Terá de ajustar os valores para o seu grupo de recursos, região de Azure, SAPSID, e assim por diante.  

   ```powershell
    #############################
    # Create Azure Shared Disk
    #############################
    
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyAzureRegion"
    $SAPSID = "PR1"
    
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    
    # With parameter '-MaxSharesCount', we define the maximum number of cluster nodes to attach the shared disk
    $NumberOfWindowsClusterNodes = 2
            
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "$SAPSID-ascs-10"
    
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "$SAPSID-ascs-11"
    
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```

### <a name="format-the-shared-disk-with-powershell"></a>Formato do disco compartilhado com PowerShell
1. Pegue o número do disco. Executar estes comandos PowerShell num dos nóns de cluster:

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 2      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. Formate o disco. Neste exemplo, é o disco número 2. 

   ```powershell
    # Format SAP ASCS Disk number '2', with drive letter 'S'
    $SAPSID = "PR1"
    $DiskNumber = 2
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR1SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. Verifique se o disco está agora visível como um disco de cluster.  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : 88ff1d94-0cf1-4c70-89ae-cbbb2826a484
    # Name       : Cluster Disk 1
    # Number     : 2
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk2\Partition2\}
   ```
4. Registe o disco no cluster.  
   ```powershell
    # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output     
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 1 Online Available Storage Physical Disk
   ```

## <a name="sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Sios DataKeeper Cluster Edition para o disco de partilha de cluster SAP ASCS/SCS
Esta secção só é aplicável se estiver a utilizar o software de terceiros SIOS DataKeeper Cluster Edition para criar um armazenamento espelhado que simula o disco partilhado do cluster.  

Agora, tem uma configuração de clustering de falha do Windows Server em funcionamento no Azure. Para instalar uma instância SAP ASCS/SCS, precisa de um recurso de disco partilhado. Uma das opções é utilizar o SIOS DataKeeper Cluster Edition é uma solução de terceiros que pode utilizar para criar recursos de disco partilhado.  

A instalação da SIOS DataKeeper Cluster Edition para o disco de partilha de cluster SAP ASCS/SCS envolve estas tarefas:
- Adicione o Quadro Microsoft .NET, se necessário. Consulte a documentação [SIOS](para https://us.sios.com/products/datakeeper-cluster/) os requisitos de enquadramento .NET mais atualizados 
- Instalar sios datakeeper
- Configure SIOS DataKeeper

### <a name="install-sios-datakeeper"></a>Instalar sios datakeeper
Instale a SIOS DataKeeper Cluster Edition em cada nó no cluster. Para criar armazenamento partilhado virtual com o SIOS DataKeeper, crie um espelho sincronizado e, em seguida, simular o armazenamento partilhado do cluster.

Antes de instalar o software SIOS, crie o utilizador de domínio DataKeeperSvc.

> [!NOTE]
> Adicione o utilizador de domínio DataKeeperSvc ao grupo administrador local em ambos os nós de cluster.
>

1. Instale o software SIOS em ambos os nós de cluster.

   ![Instalador SIOS][sap-ha-guide-figure-3030]

   ![Figura 31: Primeira página da instalação SIOS DataKeeper][sap-ha-guide-figure-3031]

   _Primeira página da instalação SIOS DataKeeper_

2. Na caixa de diálogo, selecione **Sim**.

   ![Figura 32: DataKeeper informa-o de que um serviço será desativado][sap-ha-guide-figure-3032]

   _DataKeeper informa que um serviço será desativado_

3. Na caixa de diálogo, recomendamos que selecione a **conta De domínio ou servidor.**

   ![Figura 33: Seleção de utilizadores para SIOS DataKeeper][sap-ha-guide-figure-3033]

   _Seleção do utilizador para SIOS DataKeeper_

4. Introduza o nome de utilizador da conta de domínio e a palavra-passe que criou para o SIOS DataKeeper.

   ![Figura 34: Introduza o nome de utilizador de domínio e a palavra-passe para a instalação SIOS DataKeeper][sap-ha-guide-figure-3034]

   _Introduza o nome de utilizador de domínio e a palavra-passe para a instalação SIOS DataKeeper_

5. Instale a chave de licença para a sua instância SIOS DataKeeper, como mostra a Figura 35.

   ![Figura 35: Introduza a sua chave de licença SIOS DataKeeper][sap-ha-guide-figure-3035]

   _Introduza a chave de licença SIOS DataKeeper_

6. Quando solicitado, reinicie a máquina virtual.

### <a name="configure-sios-datakeeper"></a>Configure SIOS DataKeeper
Depois de instalar o SIOS DataKeeper em ambos os nós, inicie a configuração. O objetivo da configuração é ter uma replicação de dados sincronizada entre os discos adicionais que estão ligados a cada uma das máquinas virtuais.

1. Inicie a ferramenta de Gestão e Configuração de DataKeeper e, em seguida, selecione **Connect Server**.

   ![Figura 36: Ferramenta sios datakeeper e configuração][sap-ha-guide-figure-3036]

   _Ferramenta sios datakeeper gestão e configuração_

2. Introduza o nome ou endereço TCP/IP do primeiro nó a ferramenta Gestão e Configuração deve ligar-se e, num segundo passo, o segundo nó.

   ![Figura 37: Insira o nome ou endereço TCP/IP do primeiro nó a ferramenta Gestão e Configuração deve ligar-se e, num segundo passo, o segundo nó][sap-ha-guide-figure-3037]

   _Insira o nome ou endereço TCP/IP do primeiro nó a ferramenta Gestão e Configuração deve ligar-se e, num segundo passo, o segundo nó_

3. Crie o trabalho de replicação entre os dois nós.

   ![Figura 38: Criar um trabalho de replicação][sap-ha-guide-figure-3038]

   _Criar um trabalho de replicação_

   Um assistente guia-o através do processo de criação de um trabalho de replicação.

4. Defina o nome do trabalho de replicação.

   ![Figura 39: Definir o nome do trabalho de replicação][sap-ha-guide-figure-3039]

   _Definir o nome do trabalho de replicação_

   ![Figura 40: Definir os dados base para o nó, que deve ser o nó de origem atual][sap-ha-guide-figure-3040]

   _Defina os dados base para o nó, que deve ser o nó de origem atual_

5. Defina o nome, endereço TCP/IP e volume de disco do nó-alvo.

   ![Figura 41: Definir o nome, endereço TCP/IP e volume de disco do nó-alvo atual][sap-ha-guide-figure-3041]

   _Defina o nome, endereço TCP/IP e volume de disco do nó-alvo atual_

6. Defina os algoritmos de compressão. No nosso exemplo, recomendamos que comprima o fluxo de replicação. Especialmente em situações de resincronização, a compressão do fluxo de replicação reduz drasticamente o tempo de ressincronização. A compressão utiliza os recursos cpu e RAM de uma máquina virtual. À medida que a taxa de compressão aumenta, também o volume de recursos da CPU que são utilizados. Pode ajustar esta definição mais tarde.

7. Outra definição que precisa de verificar é se a replicação ocorre de forma assíncronea ou sincronizada. Quando proteger as configurações SAP ASCS/SCS, deve utilizar a replicação sincronizada.  

   ![Figura 42: Definir detalhes de replicação][sap-ha-guide-figure-3042]

   _Definir detalhes de replicação_

8. Defina se o volume replicado pela função de replicação deve ser representado numa configuração de cluster de falha do Windows Server como um disco partilhado. Para a configuração SAP ASCS/SCS, selecione **Sim** para que o cluster Do Windows veja o volume replicado como um disco partilhado que pode usar como um volume de cluster.

   ![Figura 43: Selecione Sim para definir o volume replicado como um volume de cluster][sap-ha-guide-figure-3043]

   _Selecione **Sim** para definir o volume replicado como um volume de cluster_

   Após a criação do volume, a ferramenta de Gestão e Configuração do DataKeeper mostra que o trabalho de replicação está ativo.

   ![Figura 44: O espelhamento sincronizado do DataKeeper para o disco de partilha SAP ASCS/SCS está ativo][sap-ha-guide-figure-3044]

   _O espelhamento sincronizado do DataKeeper para o disco de partilha SAP ASCS/SCS está ativo_

   O Gestor de Cluster Failover mostra agora o disco como um disco DataKeeper, como mostra a Figura 45:

   ![Figura 45: Gestor de Cluster Failover mostra o disco que o DataKeeper replicado][sap-ha-guide-figure-3045]

   _O Gestor de Cluster Failover mostra o disco que o DataKeeper replicado_


## <a name="next-steps"></a>Passos seguintes

* [Instale o SAP NetWeaver HA utilizando um cluster de falha do Windows e disco partilhado para uma instância SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]