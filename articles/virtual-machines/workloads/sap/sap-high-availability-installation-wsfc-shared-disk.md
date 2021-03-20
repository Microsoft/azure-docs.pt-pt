---
title: Instale o SAP NetWeaver HA num cluster de falha do Windows e o disco partilhado para uma instância SAP ASCS/SCS em Azure | Microsoft Docs
description: Saiba como instalar o SAP NetWeaver HA num cluster de failover do Windows e no disco partilhado para uma instância SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 12cfd39cf1358dcef79c3843627f0b45dc688c9e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101667951"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Instale o SAP NetWeaver HA num cluster de falha do Windows e disque partilhe o disco para uma instância SAP ASCS/SCS em Azure

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

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
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md

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
[sap-ha-guide-figure-3046-ers2]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ers2-virtual-name-ip.png
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

Este artigo descreve como instalar e configurar um sistema SAP de alta disponibilidade em Azure, utilizando um cluster de failover do Windows Server e um disco partilhado de cluster para agrupar uma instância SAP ASCS/SCS. Como descrito no [guia de arquitetura: Cluster uma instância SAP ASCS/SCS num cluster de failover do Windows utilizando um disco partilhado de cluster,][sap-high-availability-guide-wsfc-shared-disk]existem duas alternativas para *o disco compartilhado do cluster*:

- [Discos partilhados do Azure](../../disks-shared.md)
- Usando [a SIOS DataKeeper Cluster Edition](https://us.sios.com/products/datakeeper-cluster/) para criar armazenamento espelhado, que simulará disco compartilhado agrupado 

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar a instalação, reveja estes documentos:

* [Guia de arquitetura: Cluster uma instância SAP ASCS/SCS num cluster de failover do Windows utilizando um disco partilhado de cluster][sap-high-availability-guide-wsfc-shared-disk]

* [Prepare a infraestrutura Azure para o SAP HA utilizando um cluster de failover do Windows e disco partilhado para uma instância SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk]

Não descrevemos a configuração DBMS neste artigo porque as configurações variam consoante o sistema DBMS que utiliza. Assumimos que as preocupações de alta disponibilidade com o DBMS são abordadas com as funcionalidades que diferentes fornecedores DBMS suportam para o Azure. Os exemplos são o espelho de AlwaysOn ou base de dados para o SQL Server e o Oracle Data Guard para bases de dados Oracle. Os cenários de elevada disponibilidade para o DBMS não estão abrangidos por este artigo.

Não existem considerações especiais quando diferentes serviços DBMS interagem com uma configuração SAP ASCS ou SCS agrupada em Azure.

> [!NOTE]
> Os procedimentos de instalação dos sistemas SAP NetWeaver ABAP, sistemas Java e sistemas ABAP+Java são quase idênticos. A diferença mais significativa é que um sistema SAP ABAP tem uma instância ASCS. O sistema SAP Java tem uma instância SCS. O sistema SAP ABAP+Java tem uma instância ASCS e uma instância SCS a decorrer no mesmo grupo de cluster de failover da Microsoft. Quaisquer diferenças de instalação para cada pilha de instalação SAP NetWeaver são explicitamente mencionadas. Pode assumir que o resto dos passos são os mesmos.  

## <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Instale o SAP com uma instância ASCS/SCS de alta disponibilidade

> [!IMPORTANT]
> Se utilizar o SIOS para apresentar o disco partilhado, não coloque o ficheiro da página nos volumes espelhados do SIOS DataKeeper. Pode deixar o ficheiro da página na unidade temporária D de uma máquina virtual Azure, que é o padrão. Se ainda não estiver lá, mova o ficheiro da página do Windows para conduzir D da sua máquina virtual Azure.  

A instalação do SAP com uma instância ASCS/SCS de alta disponibilidade envolve estas tarefas:

* Crie um nome de hospedeiro virtual para a instância SAP ASCS/SCS agrupada.
* Instale o SAP no primeiro nó de cluster.
* Modifique o perfil SAP da instância ASCS/SCS.
* Adicione uma porta de sonda.
* Abra a porta de sonda de firewall do Windows.

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Criar um nome de hospedeiro virtual para a instância SAP ASCS/SCS agrupada

1. No gestor DNS do Windows, crie uma entrada DNS para o nome de anfitrião virtual da instância ASCS/SCS.

   > [!IMPORTANT]
   > O endereço IP que atribui ao nome de anfitrião virtual da instância ASCS/SCS deve ser o mesmo que o endereço IP que atribuiu ao Azure Load Balancer.  

   ![Figura 1: Definir a entrada de DNS para o nome virtual do cluster SAP ASCS/SCS e o endereço TCP/IP][sap-ha-guide-figure-3046]

   _Definir a entrada dns para o nome virtual do cluster SAP ASCS/SCS e o endereço TCP/IP_

2. Se estiver a utilizar o novo SERVIDOR de Replicação DE Enqueue SAP 2, que também é um exemplo agrupado, então também precisa de reservar no DNS um nome de anfitrião virtual para ERS2. 

   > [!IMPORTANT]
   > O endereço IP que atribui ao nome de anfitrião virtual da instância ERS2 deve ser o segundo endereço IP que atribuiu ao Azure Load Balancer.    

   ![Figura 1A: Definir a entrada de DNS para o nome virtual do cluster SAP ASCS/SCS e o endereço TCP/IP][sap-ha-guide-figure-3046-ers2]

   _Defina a entrada DNS para o nome virtual do cluster SAP ERS2 e o endereço TCP/IP_

3. Para definir o endereço IP atribuído ao nome de anfitrião virtual, selecione **DNS Manager**  >  **Domain**.

   ![Figura 2: Novo nome virtual e endereço TCP/IP para configuração do cluster SAP ASCS/SCS][sap-ha-guide-figure-3047]

   _Novo nome virtual e endereço TCP/IP para configuração do cluster SAP ASCS/SCS_

### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Instale o primeiro nó de cluster SAP

1. Execute a primeira opção de nó de cluster no nó de cluster A. Selecione:

   * **Sistema ABAP**: **Instância ASCS** número **00**
   * **Sistema Java**: **Instância SCS** número **01**
   * **Sistema ABAP+Java**: **Instância ASCS** número **00** e **instância SCS** número **01**

   > [!IMPORTANT]
   > Tenha em mente que a configuração nas regras de equilíbrio interno de carga do balançador de carga interna Azure (se utilizar o SKU Básico) e os números de instância SAP selecionados devem coincidir.

2. Siga o procedimento de instalação descrito pela SAP. Certifique-se de que na opção de instalação inicial "Primeiro Nó de Cluster", escolha "Cluster Shared Disk" como opção de configuração.

> [!TIP]
> A documentação de instalação SAP descreve como instalar o primeiro nó de cluster ASCS/SCS.

### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Modificar o perfil SAP da instância ASCS/SCS

Se tiver o Servidor de Replicação de Enqueue 1, adicione o parâmetro de perfil `enque/encni/set_so_keepalive` SAP, conforme descrito abaixo. O parâmetro de perfil evita que as ligações entre os processos de trabalho SAP e o servidor de enqueue se fechem quando estão inativas por muito tempo. O parâmetro SAP não é necessário para o ERS2. 

1. Adicione este parâmetro de perfil ao perfil de instância SAP ASCS/SCS, se utilizar o ERS1.

   ```
   enque/encni/set_so_keepalive = true
   ```

   Para o ERS1 e ERS2, certifique-se de que os `keepalive` parâmetros de SO estão definidos como descrito na nota [SAP 1410736](https://launchpad.support.sap.com/#/notes/1410736).   

2. Para aplicar as alterações do parâmetro de perfil SAP, reinicie a instância SAP ASCS/SCS.

### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Adicione uma porta de sonda

Utilize a funcionalidade da sonda do balançador interno para fazer com que toda a configuração do cluster funcione com o Balançador de Carga Azure. O balançador interno de carga Azure distribui normalmente a carga de trabalho de entrada igualmente entre máquinas virtuais participantes.

No entanto, isto não funcionará em algumas configurações de cluster porque apenas um caso está ativo. A outra instância é passiva e não pode aceitar nenhuma carga de trabalho. Uma funcionalidade da sonda ajuda quando o equilibrador interno de carga Azure deteta qual a instância está ativa, e apenas direciona a instância ativa.  

> [!IMPORTANT]
> Nesta configuração de exemplo, o **ProbePort** está definido para 620 **Nr**. Para a caixa ASCS SAP com o número **00** é 620 **00**. Terá de ajustar a configuração para corresponder aos números de instância SAP e ao seu SAP SID.

Para adicionar uma porta de sonda, executar este Módulo PowerShell num dos VMs do cluster:

- No caso da SEPARAÇÃO SAP ASC/SCS 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID SID -ProbePort 62000
   ```

- Se utilizar o ERS2, que está agrupado. Não há necessidade de configurar a porta de sonda para a ERS1, uma vez que não está agrupada.  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID SID -ProbePort 62001 -IsSAPERSClusteredInstance $True
   ```

 O código para a função `Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource` seria:
   ```powershell
    function Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource {

    <#
    .SYNOPSIS 
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.

    .DESCRIPTION
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    It will also restart SAP Cluster group (default behavior), to activate the changes. 

    You need to run it on one of the SAP ASCS/SCS Windows cluster nodes.

    Expectation is that SAP group is installed with official SWPM installation tool, which will set default expected naming convention for:
    - SAP Cluster Group:               'SAP $SAPSID'
    - SAP Cluster IP Address Resource: 'SAP $SAPSID IP' 

    .PARAMETER SAPSID 
    SAP SID - 3 characters staring with letter.

    .PARAMETER ProbePort 
    Azure Load Balancer Health Check Probe Port.

    .PARAMETER RestartSAPClusterGroup 
    Optional parameter. Default value is '$True', so SAP cluster group will be restarted to activate the changes.

    .PARAMETER IsSAPERSClusteredInstance 
    Optional parameter.Default value is '$False'.
    If set to $True , then handle clsutered new SAP ERS2 instance.

    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP', and restart the SAP cluster group 'SAP AB1', to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 

    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP'. SAP cluster group 'SAP AB1' IS NOT restarted, therefore changes are NOT active.
    # To activate the changes you need to manualy restart 'SAP AB1' cluster group.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -RestartSAPClusterGroup $False

    .EXAMPLE 
    # Set probe port to 62001, on SAP cluster resource 'SAP AB1 ERS IP'. SAP cluster group 'SAP AB1 ERS' IS restarted, to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -IsSAPERSClusteredInstance $True

    #> 

        [CmdletBinding()]
        param(

            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]  
            [ValidateLength(3,3)]      
            [string]$SAPSID,

            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]        
            [int] $ProbePort,

            [Parameter(Mandatory=$False)] 
            [bool] $RestartSAPClusterGroup = $True,

            [Parameter(Mandatory=$False)] 
            [bool] $IsSAPERSClusteredInstance = $False
        )

        BEGIN{}

        PROCESS{
            try{                                      

                if($IsSAPERSClusteredInstance){
                    #Handle clustered SAP ERS Instance
                    $SAPClusterRoleName = "SAP $SAPSID ERS"
                    $SAPIPresourceName = "SAP $SAPSID ERS IP"            
                }else{
                    #Handle clustered SAP ASCS/SCS Instance
                    $SAPClusterRoleName = "SAP $SAPSID"
                    $SAPIPresourceName = "SAP $SAPSID IP"
                }

                $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
                $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
                $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
                $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
                $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
                $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
                $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

                $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 

                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "

                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

                Write-Output " "

                if($RestartSAPClusterGroup){
                    Write-Output ""
                    Write-Output "Activating changes..." 

                    Write-Output " "
                    Write-Output "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..."
                    Stop-ClusterResource -Name $SAPIPresourceName
                    sleep 5

                    Write-Output "Starting SAP cluster role '$SAPClusterRoleName' ..."
                    Start-ClusterGroup -Name $SAPClusterRoleName

                    Write-Output "New ProbePort parameter is active." 
                    Write-Output " "

                    Write-Output "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" 
                    Write-Output " " 
                    Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
                }else
                {
                    Write-Output "SAP cluster role '$SAPClusterRoleName' is not restarted, therefore changes are not activated."
                }
            }
            catch{
               Write-Error  $_.Exception.Message
           }
        }
        END {}
    }

   ```

### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Abra a porta da sonda de firewall do Windows

Abra uma porta de sonda de firewall do Windows em ambos os nós de cluster. Utilize o seguinte script para abrir uma porta de sonda de firewall do Windows. Atualize as variáveis PowerShell para o seu ambiente.  
Se utilizar o ERS2, também terá de abrir a porta de firewall para a porta da sonda ERS2.  

  ```powershell
    $ProbePort = 62000   # ProbePort of the Azure internal load balancer
    New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

## <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Instale a instância da base de dados

Para instalar a instância da base de dados, siga o processo descrito na documentação de instalação DA SAP.

## <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Instale o segundo nó de cluster

Para instalar o segundo cluster, siga os passos descritos no guia de instalação SAP.

## <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Instale o Servidor de Aplicações Primárias SAP

Instale a instância do Servidor de Aplicação Primária (PAS) \<SID\> -di-0 na máquina virtual que designou para hospedar o PAS. Não há dependências do Azure. Se utilizar o SIOS, não existem definições específicas do DataKeeper.

## <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Instale o Servidor de Aplicações Adicionais SAP

Instale um Servidor de Aplicações Adicionais SAP (AAS) em todas as máquinas virtuais que designou para hospedar uma instância do Servidor de Aplicações SAP. 

## <a name="test-the-sap-ascsscs-instance-failover"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testar a falha da instância SAP ASCS/SCS

Para os testes de failover delineados, assumimos que o SAP ASCS está ativo no nó A.  

1. Verifique se o sistema SAP pode falhar com sucesso do nó A para o nó B Escolha uma destas opções para iniciar uma falha do grupo de cluster SAP \<SID\> do nó de cluster A ao nó de cluster B:
    - Gestor de Cluster Failover  
    - Falha no cluster PowerShell

    ```powershell
    $SAPSID = "PR1"     # SAP <SID>

    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```

2. Reinicie o nó de cluster A dentro do sistema operativo de hóspedes Windows. Isto inicia uma falha automática do grupo de cluster SAP \<SID\> do nó A ao nó B.  
3. Reinicie o nó de cluster A do portal Azure. Isto inicia uma falha automática do grupo de cluster SAP \<SID\> do nó A ao nó B.  
4. Reinicie o nó de cluster A utilizando a Azure PowerShell. Isto inicia uma falha automática do grupo de cluster SAP \<SID\> do nó A ao nó B.

5. Verificação
   - Após o failover, verifique se o grupo de cluster SAP \<SID\> está em funcionamento no nó de cluster B. 

      ![Figura 8: No Failover Cluster Manager, o \< grupo de cluster SAP SID está a funcionar no nó de cluster \> B][sap-ha-guide-figure-5002]

      _No Failover Cluster Manager, o grupo de cluster SAP \<SID\> está a funcionar no nó de cluster B_

   - Após o failover, verifique se o disco partilhado está agora montado no nó de cluster B. 
   - Após falha, se utilizar o SIOS, verifique se o SIOS DataKeeper está a replicar dados da unidade de volume de origem S no nó de cluster B para a unidade de volume S no nó de cluster A. 

      ![Figura 9: SiOS DataKeeper replica o volume local do nó de cluster B ao nó de cluster A][sap-ha-guide-figure-5003]

      _SiOS DataKeeper replica o volume local do nó de cluster B para o nó de cluster A_