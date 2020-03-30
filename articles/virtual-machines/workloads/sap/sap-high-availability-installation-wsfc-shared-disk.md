---
title: Instale o SAP NetWeaver HA num cluster de falhas do Windows e disco partilhado para uma instância SAP ASCS/SCS em Azure [ Microsoft Docs
description: Saiba como instalar o SAP NetWeaver HA num cluster de falhas do Windows e num disco partilhado para uma instância SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e50733c843dfd21e35572f00fc6690e1e84aba97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279835"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Instale o SAP NetWeaver HA num cluster de falhas do Windows e disco partilhado para uma instância SAP ASCS/SCS em Azure

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

Este artigo descreve como instalar e configurar um sistema SAP de alta disponibilidade em Azure utilizando um cluster de falha do Windows Server e um disco partilhado de cluster para agrupar uma instância SAP ASCS/SCS.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar a instalação, reveja estes documentos:

* [Guia de arquitetura: Cluster uma instância SAP ASCS/SCS num cluster de falhas do Windows utilizando um disco partilhado cluster][sap-high-availability-guide-wsfc-shared-disk]

* [Prepare a infraestrutura Azure para SAP HA utilizando um cluster de falha do Windows e um disco partilhado para uma instância SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk]

Não descrevemos a configuração do DBMS neste artigo porque as configurações variam consoante o sistema DBMS que utiliza. Assumimos que as preocupações de alta disponibilidade com o DBMS são abordadas com as funcionalidades que diferentes fornecedores dbmS suportam para o Azure. Exemplos são AlwaysOn ou database mirroring para SQL Server e Oracle Data Guard para bases de dados Oracle. No cenário que usamos neste artigo, não adicionamos mais proteção ao DBMS.

Não existem considerações especiais quando diferentes serviços DBMS interagem com uma configuração SAP ASCS ou SCS agrupada em Azure.

> [!NOTE]
> Os procedimentos de instalação dos sistemas SAP NetWeaver ABAP, sistemas Java e ABAP+Java são quase idênticos. A diferença mais significativa é que um sistema SAP ABAP tem uma instância ASCS. O sistema SAP Java tem uma instância SCS. O sistema SAP ABAP+Java tem uma instância ASCS e uma instância SCS a funcionar no mesmo grupo de clusters da Microsoft failover. Quaisquer diferenças de instalação para cada pilha de instalação SAP NetWeaver são explicitamente mencionadas. Pode supor que todas as outras partes são iguais.  
>
>

## <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Instale o SAP com uma instância ASCS/SCS de alta disponibilidade

> [!IMPORTANT]
> Certifique-se de que não coloca o ficheiro da página em volumes espelhados sIOS DataKeeper. DataKeeper não suporta volumes espelhados. Pode deixar o ficheiro de página na unidade temporária D de uma máquina virtual Azure, que é a falha. Se ainda não estiver lá, mova o ficheiro da página do Windows para conduzir D da sua máquina virtual Azure.
>
>

Instalar o SAP com uma instância ASCS/SCS de alta disponibilidade envolve estas tarefas:

* Crie um nome de anfitrião virtual para a instância SAP ASCS/SCS agrupada.
* Instale o nó de primeiro cluster SAP.
* Modificar o perfil SAP da instância ASCS/SCS.
* Adicione uma porta de sonda.
* Abra a porta de sonda de firewall do Windows.

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Criar um nome de anfitrião virtual para a instância SAP ASCS/SCS agrupada

1. No gestor do DNS do Windows, crie uma entrada DNS para o nome de anfitrião virtual da instância ASCS/SCS.

   > [!IMPORTANT]
   > O endereço IP que atribui ao nome de anfitrião virtual da instância ASCS/SCS deve ser o mesmo\<que\>o endereço IP que atribuiu ao Azure Load Balancer (SID-lb-ascs).  
   >
   >

   O endereço IP do nome de anfitrião virtual SAP ASCS/SCS (pr1-ascs-seap) é o mesmo que o endereço IP do Azure Load Balancer (pr1-lb-ascs).

   ![Figura 1: Definir a entrada dNS para o nome virtual do cluster SAP ASCS/SCS e endereço TCP/IP][sap-ha-guide-figure-3046]

   _**Figura 1:** Definir a entrada DNS para o nome virtual do cluster SAP ASCS/SCS e endereço TCP/IP_

2. Para definir o endereço IP atribuído ao nome de anfitrião virtual, selecione **DNS Manager** > **Domain**.

   ![Figura 2: Novo nome virtual e endereço TCP/IP para configuração do cluster SAP ASCS/SCS][sap-ha-guide-figure-3047]

   _**Figura 2:** Novo nome virtual e endereço TCP/IP para configuração de cluster SAP ASCS/SCS_

### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>Instale o primeiro nó de cluster SAP

1. Execute a primeira opção do nó de cluster no nó de cluster A. Por exemplo, no pr1-ascs-0*host.
2. Para manter as portas padrão para o equilibrador de carga interna Azure, selecione:

   * **Sistema ABAP**: **Ascs** instância número **00**
   * **Sistema Java**: **SCS** exemplo número **01**
   * **Sistema ABAP+Java**: **AscS** exemplo número **00** e **scs** número **01**

   Para utilizar números de instância que não 00 para a instância AsCS ABAP e 01 para a instância Java SCS, em primeiro lugar, altere as regras de equilíbrio de carga de carga interna Azure. Para obter mais informações, consulte Alterar as regras de equilíbrio de carga padrão [ASCS/SCS para o equilibrador de carga interna Azure][sap-ha-guide-8.9].

As próximas tarefas não são descritas na documentação padrão de instalação do SAP.

> [!NOTE]
> A documentação de instalação SAP descreve como instalar o primeiro nó de cluster ASCS/SCS.
>
>

### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>Modificar o perfil SAP da instância ASCS/SCS

Primeiro, adicione um novo parâmetro de perfil. O parâmetro de perfil impede que as ligações entre os processos de trabalho do SAP e o servidor de fila de enfila fechem quando estão inativas durante demasiado tempo. Mencionamos o cenário problemático em Adicionar entradas de [registo em ambos os nós de cluster da instância SAP ASCS/SCS][sap-ha-guide-8.11]. Nessa secção, introduzimos também duas alterações a alguns parâmetros básicos de ligação TCP/IP. Num segundo passo, é necessário definir o servidor `keep_alive` de fila para enviar um sinal para que as ligações não atinjam o limiar de marcha lenta do equilíbrio interno do Azure.

Para modificar o perfil SAP da instância ASCS/SCS:

1. Adicione este parâmetro de perfil ao perfil de instância SAP ASCS/SCS:

   ```
   enque/encni/set_so_keepalive = true
   ```
   No nosso exemplo, o caminho é:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Por exemplo, para o perfil de instância SAP SCS e caminho correspondente:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. Para aplicar as alterações, reinicie a instância SAP ASCS/SCS.

### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>Adicione uma porta de sonda

Utilize a funcionalidade de sonda interna do equilibrante de carga para que toda a configuração do cluster funcione com o Azure Load Balancer. O equilibrador de carga interna Azure geralmente distribui a carga de trabalho de entrada igualmente entre as máquinas virtuais participantes.

 No entanto, isto não funcionará em algumas configurações de cluster porque apenas uma instância está ativa. A outra instância é passiva e não pode aceitar nenhuma carga de trabalho. Uma funcionalidade de sonda ajuda quando o balanceador de carga interna Azure atribui trabalho apenas a uma instância ativa. Com a funcionalidade da sonda, o equilibrador de carga interna pode detetar quais as instâncias ativas e, em seguida, visar apenas a instância com a carga de trabalho.

Para adicionar uma porta de sonda:

1. Verifique o valor atual do **ProbePort** executando o seguinte comando PowerShell:

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

   Execute o comando a partir de dentro de uma das máquinas virtuais na configuração do cluster.

2. Defina uma porta de sonda. O número da porta da sonda padrão é 0. No nosso exemplo, usamos a porta da sonda 62000.

   ![Figura 3: A porta de sonda de configuração do cluster é 0 por padrão][sap-ha-guide-figure-3048]

   _**Figura 3:** A porta de sonda de configuração de cluster padrão é 0_

   O número da porta é definido nos modelos sAP Azure Resource Manager. Pode atribuir o número de porta no PowerShell.

   Para definir um novo valor ProbePort para o recurso de cluster IP SAP \<SID,\> execute o seguinte script PowerShell para atualizar as variáveis PowerShell para o seu ambiente:

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

   Depois de colocar \<online\> a função de cluster SAP SID, verifique se o **ProbePort** está definido para o novo valor.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```
   Após a eção do script, é-lhe solicitado que reinicie o grupo de cluster SAP para ativar as alterações.

   ![Figura 4: Sondar a porta do cluster depois de definir o novo valor][sap-ha-guide-figure-3049]

   _**Figura 4:** Sondar a porta do cluster depois de definir o novo valor_

### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>Abra a porta de sonda de firewall windows

Abra uma porta de sonda de firewall Windows em ambos os nós do cluster. Utilize o seguinte script para abrir uma porta de sonda de firewall Windows. Atualize as variáveis PowerShell para o seu ambiente.

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**A ProbePort** está definida para **62000**. Agora, você pode aceder \\à partilha de ficheiros \ascsha-clsap\sapmnt de outros anfitriões, como de ascsha-dbas.

## <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>Instale a instância da base de dados

Para instalar a instância de base de dados, siga o processo descrito na documentação de instalação SAP.

## <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>Instale o segundo nó de cluster

Para instalar o segundo cluster, siga os passos descritos no guia de instalação SAP.

## <a name="change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>Alterar o tipo de início da instância de serviço SAP ERS Windows

Altere o tipo de início do serviço SAP ERS Windows para **Automático (Arranque Atrasado)** em ambos os nós do cluster.

![Figura 5: Alterar o tipo de serviço para a instância SAP ERS para atraso automático][sap-ha-guide-figure-3050]

_**Figura 5:** Alterar o tipo de serviço para a instância SAP ERS para atraso automático_

## <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>Instalar o Servidor de Aplicações Primárias SAP

Instale a instância \<do Servidor\>de Aplicações Primárias (PAS) SID -di-0 na máquina virtual que designou para acolher o PAS. Não há dependências do Azure. Não existem configurações específicas do DataKeeper.

## <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>Instalar o Servidor de Aplicações Adicionais SAP

Instale um Servidor de Aplicações Adicional SAP (AAS) em todas as máquinas virtuais que designou para hospedar uma instância do Servidor de Aplicações SAP. Por exemplo, \<\>em SID -di-1&lt;para&gt; \<SID\>-di-n .

> [!NOTE]
> Isto termina a instalação de um sistema SAP NetWeaver de alta disponibilidade. Em seguida, proceda com os testes de failover.
>


## <a name="test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>Teste a falha da instância SAP ASCS/SCS e a replicação do SIOS
É fácil testar e monitorizar uma falha de falha na instância SAP ASCS/SCS e replicação do disco SIOS utilizando o Failover Cluster Manager e a ferramenta de gestão e configuração do DataKeeper sIOS.

### <a name="sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>A instância SAP ASCS/SCS está a funcionar no nó de cluster A

O grupo de cluster SAP PR1 está a funcionar no nó de cluster A. Por exemplo, em pr1-ascs-0. Atribuir a unidade de disco partilhada S, que faz parte do grupo de cluster SAP PR1, ao nó de cluster A. A instância ASCS/SCS também utiliza a unidade de disco S. 

![Figura 6: Failover Cluster Manager: O grupo de cluster SAP \<SID\> está em execução no nó de cluster A][sap-ha-guide-figure-5000]

_**Figura 6:** Failover Cluster Manager: \<O\> grupo de cluster SAP SID está em execução no nó de cluster A_

Na ferramenta SIOS DataKeeper Management and Configuration, pode ver que os dados do disco partilhado são sincronizados replicados desde a unidade de volume de origem S no nó de cluster A até à unidade de volume alvo S no nó de cluster B. Por exemplo, é replicado de pr1-ascs-0 [10.0.0.40] a pr1-ascs-1 [10.0.0.41].

![Figura 7: No SIOS DataKeeper, replicar o volume local do nó de cluster A ao nó de cluster B][sap-ha-guide-figure-5001]

_**Figura 7:** No SIOS DataKeeper, replicar o volume local do nó de cluster A ao nó de cluster B_

### <a name="failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>Falha do nó A ao nó B

1. Escolha uma destas opções para iniciar \<uma\> falha do cluster SAP SID do nó de cluster A ao nó de cluster B:
   - Gestor de Cluster failover  
   - Failover Cluster PowerShell

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Reinicie o nó de cluster A dentro do sistema operativo windows convidado. Isto inicia uma falha automática do \<\> grupo de cluster SAP SID do nó A ao nó B.  
3. Reinicie o nó de cluster A do portal Azure. Isto inicia uma falha automática do \<\> grupo de cluster SAP SID do nó A ao nó B.  
4. Reinicie o nó de cluster A utilizando o Azure PowerShell. Isto inicia uma falha automática do \<\> grupo de cluster SAP SID do nó A ao nó B.

   Após a falha, \<o\> grupo de cluster SAP SID está funcionando no nó de cluster B. Por exemplo, está a funcionar em pr1-ascs-1.

   ![Figura 8: Em Failover Cluster \<Manager, o grupo de cluster SAP SID\> está em execução no nó de cluster B][sap-ha-guide-figure-5002]

   _**Figura 8**: No Failover Cluster \<\> Manager, o grupo de cluster SAP SID está em execução no nó de cluster B_

   O disco partilhado está agora montado no nó de cluster B. SIOS DataKeeper está a replicar dados da unidade de volume de origem S no nó de cluster B para o volume de destino S no nó de cluster A. Por exemplo, está a replicar-se de pr1-ascs-1 [10.0.0.41] para pr1-ascs-0 [10.0.0.40].

   ![Figura 9: SIOS DataKeeper replica o volume local do nó de cluster B ao nó de cluster A][sap-ha-guide-figure-5003]

   _**Figura 9:** SIOS DataKeeper replica o volume local do nó de cluster B ao nó de cluster A_
