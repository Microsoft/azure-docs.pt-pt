---
title: Instalar a alta disponibilidade do SAP NetWeaver em um cluster de failover do Windows e em um disco compartilhado para uma instância do SAP ASCS/SCS no Azure | Microsoft Docs
description: Saiba como instalar o SAP NetWeaver HA em um cluster de failover do Windows e em um disco compartilhado para uma instância do SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bfbff1f95eaad41813ee0741a6b133dccdae181d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647531"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Instalar o SAP NetWeaver HA em um cluster de failover do Windows e em um disco compartilhado para uma instância do SAP ASCS/SCS no Azure

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

Este artigo descreve como instalar e configurar um sistema SAP de alta disponibilidade no Azure usando um cluster de failover do Windows Server e um disco compartilhado de cluster para clustering de uma instância do SAP ASCS/SCS.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a instalação, examine estes documentos:

* [Guia de arquitetura: cluster de uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado do cluster][sap-high-availability-guide-wsfc-shared-disk]

* [Preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e um disco compartilhado para uma instância do SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk]

Não descrevemos a configuração do DBMS neste artigo porque as configurações variam de acordo com o sistema DBMS que você usa. Supomos que preocupações de alta disponibilidade com o DBMS são abordadas com as funcionalidades que diferentes fornecedores de DBMS dão suporte ao Azure. Os exemplos são o AlwaysOn ou o espelhamento de banco de dados para SQL Server e Oracle Data Guard para bancos de dados Oracle. No cenário que usamos neste artigo, não adicionamos mais proteção ao DBMS.

Não há considerações especiais quando diferentes serviços DBMS interagem com uma configuração de SAP ASCS ou SCS clusterizada no Azure.

> [!NOTE]
> Os procedimentos de instalação dos sistemas SAP NetWeaver ABAP, sistemas Java e sistemas ABAP + Java são quase idênticos. A diferença mais significativa é que um sistema SAP ABAP tem uma instância ASCS. O sistema SAP Java tem uma instância SCS. O sistema SAP ABAP + Java tem uma instância ASCS e uma instância SCS em execução no mesmo grupo de clusters de failover da Microsoft. Todas as diferenças de instalação para cada pilha de instalação do SAP NetWeaver são explicitamente mencionadas. Você pode assumir que todas as outras partes são iguais.  
>
>

## <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Instalar o SAP com uma instância ASCS/SCS de alta disponibilidade

> [!IMPORTANT]
> Certifique-se de não posicionar o arquivo de paginação em volumes espelhados do SIOS datakeeper. O datakeeper não oferece suporte a volumes espelhados. Você pode deixar o arquivo de paginação na unidade temporária D de uma máquina virtual do Azure, que é o padrão. Se ainda não estiver lá, mova o arquivo de paginação do Windows para a unidade D da sua máquina virtual do Azure.
>
>

A instalação do SAP com uma instância ASCS/SCS de alta disponibilidade envolve estas tarefas:

* Crie um nome de host virtual para a instância clusterizada do SAP ASCS/SCS.
* Instale o primeiro nó do cluster do SAP.
* Modifique o perfil SAP da instância do ASCS/SCS.
* Adicione uma porta de investigação.
* Abra a porta de investigação do firewall do Windows.

### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Criar um nome de host virtual para a instância clusterizada do SAP ASCS/SCS

1. No Gerenciador de DNS do Windows, crie uma entrada DNS para o nome de host virtual da instância do ASCS/SCS.

   > [!IMPORTANT]
   > O endereço IP que você atribui ao nome de host virtual da instância ASCS/SCS deve ser o mesmo que o endereço IP atribuído a Azure Load Balancer (\<SID\>-lb-ASCS).  
   >
   >

   O endereço IP do nome de host virtual SAP ASCS/SCS (PR1-ASCS-SAP) é o mesmo que o endereço IP de Azure Load Balancer (PR1-lb-ASCS).

   ![Figura 1: definir a entrada DNS para o nome virtual do cluster do SAP ASCS/SCS e o endereço TCP/IP][sap-ha-guide-figure-3046]

   _**Figura 1:** Definir a entrada DNS para o nome virtual do cluster do SAP ASCS/SCS e o endereço TCP/IP_

2. Para definir o endereço IP atribuído ao nome de host virtual, selecione Gerenciador de **DNS** > **domínio**.

   ![Figura 2: novo nome virtual e endereço TCP/IP para configuração de cluster do SAP ASCS/SCS][sap-ha-guide-figure-3047]

   _**Figura 2:** Novo nome virtual e endereço TCP/IP para configuração de cluster do SAP ASCS/SCS_

### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>Instalar o primeiro nó do cluster do SAP

1. Execute a primeira opção de nó de cluster no nó A do cluster. Por exemplo, no host PR1-ASCs-0 *.
2. Para manter as portas padrão para o balanceador de carga interno do Azure, selecione:

   * **Sistema ABAP**: número de instância **ASCS** **00**
   * **Sistema Java**: número de instância do **SCS** **01**
   * **ABAP + Java System**: número da instância **ASCS** **00** e o número de instância do **SCS** **01**

   Para usar números de instância diferentes de 00 para a instância ABAP ASCS e 01 para a instância do SCS Java, primeiro, altere as regras de balanceamento de carga padrão do balanceador de carga interno do Azure. Para obter mais informações, consulte [alterar as regras de balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure][sap-ha-guide-8.9].

As próximas tarefas não são descritas na documentação de instalação padrão do SAP.

> [!NOTE]
> A documentação de instalação do SAP descreve como instalar o primeiro nó de cluster ASCS/SCS.
>
>

### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>Modificar o perfil SAP da instância do ASCS/SCS

Primeiro, adicione um novo parâmetro de perfil. O parâmetro de perfil impede que as conexões entre os processos de trabalho do SAP e o servidor de enfileiramento sejam encerradas quando estiverem ociosas por muito tempo. Mencionamos o cenário do problema em [Adicionar entradas do registro em ambos os nós de cluster da instância do SAP ASCS/SCS][sap-ha-guide-8.11]. Nessa seção, também apresentamos duas alterações em alguns parâmetros básicos de conexão TCP/IP. Em uma segunda etapa, você precisa definir o servidor de enfileiramento para enviar um sinal de `keep_alive` para que as conexões não atinjam o limite de ociosidade do balanceador de carga interno do Azure.

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

### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>Adicionar uma porta de investigação

Use a funcionalidade de investigação do balanceador de carga interno para fazer com que toda a configuração do cluster funcione com o Azure Load Balancer. O balanceador de carga interno do Azure geralmente distribui a carga de trabalho de entrada igualmente entre as máquinas virtuais participantes.

 No entanto, isso não funcionará em algumas configurações de cluster porque apenas uma instância está ativa. A outra instância é passiva e não pode aceitar nenhuma das cargas de trabalho. Uma funcionalidade de investigação ajuda quando o balanceador de carga interno do Azure atribui o trabalho somente a uma instância ativa. Com a funcionalidade de investigação, o balanceador de carga interno pode detectar quais instâncias estão ativas e, em seguida, direcionar apenas a instância com a carga de trabalho.

Para adicionar uma porta de investigação:

1. Verifique o valor de **ProbePort** atual executando o seguinte comando do PowerShell:

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

   Execute o comando de dentro de uma das máquinas virtuais na configuração do cluster.

2. Defina uma porta de investigação. O número da porta de investigação padrão é 0. Em nosso exemplo, usamos a porta de investigação 62000.

   ![Figura 3: a porta de investigação de configuração de cluster é 0 por padrão][sap-ha-guide-figure-3048]

   _**Figura 3:** A porta de investigação de configuração de cluster padrão é 0_

   O número da porta é definido nos modelos de Azure Resource Manager do SAP. Você pode atribuir o número da porta no PowerShell.

   Para definir um novo valor de ProbePort para o recurso de cluster de IP do SAP \<SID\>, execute o seguinte script do PowerShell para atualizar as variáveis do PowerShell para seu ambiente:

   ```powershell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure internal load balancer

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

   Depois de colocar o SID do SAP \<\> função de cluster online, verifique se **ProbePort** está definido como o novo valor.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```
   Depois que o script for executado, será solicitado que você reinicie o grupo de clusters SAP para ativar as alterações.

   ![Figura 4: investigar a porta do cluster depois de definir o novo valor][sap-ha-guide-figure-3049]

   _**Figura 4:** Investigar a porta do cluster depois de definir o novo valor_

### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>Abrir a porta de investigação do firewall do Windows

Abra uma porta de investigação do firewall do Windows em ambos os nós de cluster. Use o script a seguir para abrir uma porta de investigação do firewall do Windows. Atualize as variáveis do PowerShell para seu ambiente.

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** é definido como **62000**. Agora, você pode acessar o compartilhamento de arquivos \\arquivos ascsha-clsap\sapmnt de outros hosts, como de ascsha-DBAs.

## <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>Instalar a instância do banco de dados

Para instalar a instância do banco de dados, siga o processo descrito na documentação de instalação do SAP.

## <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>Instalar o segundo nó de cluster

Para instalar o segundo cluster, siga as etapas descritas no guia de instalação do SAP.

## <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>Alterar o tipo de início da instância do serviço Windows ERS do SAP

Altere o tipo de início do serviço do Windows ERS do SAP para **automático (atraso na inicialização)** em ambos os nós de cluster.

![Figura 5: alterar o tipo de serviço para a instância do SAP ERS para atraso automático][sap-ha-guide-figure-3050]

_**Figura 5:** Alterar o tipo de serviço da instância ERS do SAP para atraso automático_

## <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>Instalar o servidor de aplicativos principal do SAP

Instale a instância do PAS (servidor de aplicativos primário) \<SID\>-di-0 na máquina virtual que você designou para hospedar o PAS. Não há dependências no Azure. Não há configurações específicas do datakeeper.

## <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>Instalar o servidor de aplicativos do SAP adicional

Instale um servidor de aplicativos SAP adicional (AAS) em todas as máquinas virtuais que você designou para hospedar uma instância do servidor de aplicativos SAP. Por exemplo, em \<SID\>-di-1 para \<SID\>-di-&lt;n&gt;.

> [!NOTE]
> Isso conclui a instalação de um sistema SAP NetWeaver de alta disponibilidade. Em seguida, continue com o teste de failover.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>Testar o failover da instância do SAP ASCS/SCS e a replicação SIOS
É fácil testar e monitorar um failover de instância do SAP ASCS/SCS e a replicação de disco SIOS usando Gerenciador de Cluster de Failover e a ferramenta de gerenciamento e configuração do SIOS datakeeper.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>A instância do SAP ASCS/SCS está em execução no nó A do cluster

O grupo de clusters do SAP PR1 está em execução no nó A do cluster. Por exemplo, em PR1-ASCs-0. Atribua a unidade de disco compartilhada S, que faz parte do grupo de clusters SAP PR1, ao nó A do cluster. A instância do ASCS/SCS também usa a unidade de disco S. 

![Figura 6: Gerenciador de Cluster de Failover: o SID do SAP \<\> grupo de clusters está em execução no nó A do cluster][sap-ha-guide-figure-5000]

_**Figura 6:** Gerenciador de Cluster de Failover: o SID do SAP \<\> grupo de clusters está em execução no nó A do cluster_

Na ferramenta de gerenciamento e configuração do SIOS datakeeper, você pode ver que os dados do disco compartilhado são replicados de forma síncrona da unidade do volume de origem S no nó A do cluster a para a unidade do volume de destino S no nó B do cluster. Por exemplo, ele é replicado de PR1-ASCs-0 [10.0.0.40] para PR1-ASCs-1 [10.0.0.41].

![Figura 7: no SIOS datakeeper, replique o volume local do nó A do cluster para o nó B do cluster][sap-ha-guide-figure-5001]

_**Figura 7:** No SIOS datakeeper, replique o volume local do nó A do cluster para o nó B do cluster_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>Failover do nó A para o nó B

1. Escolha uma destas opções para iniciar um failover do grupo de clusters do SAP \<SID\> do nó A do cluster para o nó B do cluster:
   - Failover Cluster Manager  
   - PowerShell do cluster de failover

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Reinicie o nó A do cluster no sistema operacional convidado do Windows. Isso inicia um failover automático do grupo de clusters do SAP \<SID\> do nó A para o nó B.  
3. Reinicie o nó a do cluster do portal do Azure. Isso inicia um failover automático do grupo de clusters do SAP \<SID\> do nó A para o nó B.  
4. Reinicie o nó A do cluster usando Azure PowerShell. Isso inicia um failover automático do grupo de clusters do SAP \<SID\> do nó A para o nó B.

   Após o failover, o grupo de clusters do SAP \<SID\> está em execução no nó B do cluster. Por exemplo, ele está em execução em PR1-ASCs-1.

   ![Figura 8: em Gerenciador de Cluster de Failover, o SID do SAP \<\> grupo de clusters está em execução no nó B do cluster][sap-ha-guide-figure-5002]

   _**Figura 8**: em Gerenciador de cluster de failover, o SID do SAP \<\> grupo de clusters está em execução no nó B do cluster_

   O disco compartilhado agora está montado no nó B do cluster. o SIOS datakeeper está replicando dados da unidade do volume de origem S no nó B do cluster para a unidade do volume de destino S no nó A do cluster. Por exemplo, ele está replicando de PR1-ASCs-1 [10.0.0.41] para PR1-ASCs-0 [10.0.0.40].

   ![Figura 9: o SIOS datakeeper Replica o volume local do nó B do cluster para o nó A do cluster][sap-ha-guide-figure-5003]

   _**Figura 9:** O SIOS datakeeper Replica o volume local do nó B do cluster para o nó A do cluster_
