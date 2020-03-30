---
title: Criar uma configuração Multi-SID SAP em Azure Microsoft Docs
description: Guia para configuração multi-SID SAP NetWeaver de alta disponibilidade em máquinas virtuais do Windows
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d7938f7db22f004a0bf6cdf2e22dc8e103896719
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617404"
---
# <a name="create-an-sap-netweaver-multi-sid-configuration"></a>Criar uma configuração multi-SID SAP NetWeaver

[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[sap-ha-guide]:sap-high-availability-guide.md 
[sap-ha-guide-figure-6001]:./media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:./media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:./media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:./media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:./media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[sap-ha-guide-9.1.1]:sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 
[sap-ha-guide-8.8]:sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.12.3.3]:sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 
[sap-ha-guide-9]:sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 
[sap-ha-guide-9.1.2]:sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 
[sap-ha-guide-9.1.3]:sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 
[sap-ha-guide-9.1.4]:sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 
[sap-ha-guide-9.4]:sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a 
[sap-ha-guide-9.5]:sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 
[sap-ha-guide-9.6]:sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 
[sap-ha-guide-10]:sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9

Em setembro de 2016, a Microsoft lançou uma funcionalidade onde pode gerir vários endereços IP virtuais utilizando um equilibrador de carga interna Do Azure. Esta funcionalidade já existe no equilibrador de carga externa Azure.

Se tiver uma implementação SAP, pode utilizar um balancedor de carga interno para criar uma configuração de cluster Windows para SAP ASCS/SCS, conforme documentado no [guia para a alta disponibilidade Do SAP NetWeaver em VMs do Windows][sap-ha-guide].

Este artigo centra-se em como passar de uma única instalação ASCS/SCS para uma configuração multi-SID SAP, instalando instâncias adicionais agrupadas SAP ASCS/SCS num cluster de clustering de falha de servidor do Windows existente (WSFC). Quando este processo estiver concluído, terá configurado um cluster multi-SID SAP.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
Já configuraum cluster WSFC que é usado para uma instância SAP ASCS/SCS, como discutido no [guia para alta disponibilidade SAP NetWeaver em VMs Windows][sap-ha-guide] e como mostrado neste diagrama.

![Instância SAP ASCS/SCS de alta disponibilidade][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>Arquitetura-alvo

O objetivo é instalar múltiplas instâncias agrupadas SAP ABAP ASCS ou SAP Java SCS agrupadas no mesmo cluster WSFC, como ilustrado aqui:

![Múltiplas instâncias agrupadas SAP ASCS/SCS em Azure][sap-ha-guide-figure-6002]

> [!NOTE]
>Existe um limite para o número de IPs frontais privados para cada equilibrador de carga interna do Azure.
>
>O número máximo de instâncias SAP ASCS/SCS num cluster WSFC é igual ao número máximo de IPs frontais privados para cada equilíbrio interno de carga azure.
>

Para obter mais informações sobre os limites do equilíbrio de carga, consulte "IP frontal privado por equilibrante de carga" nos limites de [networking: Azure Resource Manager][networking-limits-azure-resource-manager].

A paisagem completa com dois sistemas SAP de alta disponibilidade seria assim:

![Configuração multi-SID de alta disponibilidade SAP com dois SIDs do sistema SAP][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> A configuração deve satisfazer as seguintes condições:
> - As instâncias SAP ASCS/SCS devem partilhar o mesmo cluster WSFC.
> - Cada DBMS SID deve ter o seu próprio cluster WSFC dedicado.
> - Os servidores de aplicações SAP que pertencem a um sistema SAP SID devem ter os seus próprios VMs dedicados.


## <a name="prepare-the-infrastructure"></a>Preparar a infraestrutura
Para preparar a sua infraestrutura, pode instalar uma instância Adicional SAP ASCS/SCS com os seguintes parâmetros:

| Nome do parâmetro | Valor |
| --- | --- |
| SAP ASCS/SCS SID |pr1-lb-ascs |
| Balanceador de carga interna SAP DBMS | PR5 |
| Nome de anfitrião virtual SAP | pr5-sap-cl |
| Endereço IP do hospedeiro virtual SAP ASCS/SCS (endereço IP adicional do equilíbrio de carga Azure) | 10.0.0.50 |
| Número de instância SAP ASCS/SCS | 50 |
| Porta sonda ILB para instância adicional SAP ASCS/SCS | 62350 |

> [!NOTE]
> Para as instâncias de cluster SAP ASCS/SCS, cada endereço IP requer uma porta de sonda única. Por exemplo, se um endereço IP num equilibrador de carga interna do Azure utilizar a porta de sonda 62300, nenhum outro endereço IP nesse equilíbrio de carga pode utilizar a porta de sonda 62300.
>
>Para os nossos propósitos, porque a porta de sonda 62300 já está reservada, estamos a usar a porta de sonda 62350.

Pode instalar instâncias sAP ASCS/SCS adicionais no cluster WSFC existente com dois nós:

| Papel de máquina virtual | Nome do hospedeiro da máquina virtual | Endereço IP estático |
| --- | --- | --- |
| 1º nó de cluster para instância ASCS/SCS |pr1-ascs-0 |10.0.0.10 |
| 2º nó de cluster para instância ASCS/SCS |pr1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Crie um nome de anfitrião virtual para a instância SAP ASCS/SCS agrupada no servidor DNS

Pode criar uma entrada DNS para o nome de anfitrião virtual da instância ASCS/SCS utilizando os seguintes parâmetros:

| Novo nome de hospedeiro virtual SAP ASCS/SCS | Endereço IP associado |
| --- | --- |
|pr5-sap-cl |10.0.0.50 |

O novo nome do anfitrião e o endereço IP são apresentados no Gestor DNS, como mostra a seguinte imagem:

![Lista de Gestor dNS destacando a entrada definida de DNS para o novo nome virtual do cluster SAP ASCS/SCS e endereço TCP/IP][sap-ha-guide-figure-6004]

O procedimento para a criação de uma entrada DNS também é descrito em detalhe no guia principal [para a alta disponibilidade SAP NetWeaver em VMs windows][sap-ha-guide-9.1.1].

> [!NOTE]
> O novo endereço IP que atribui ao nome de anfitrião virtual da instância ascs/SCS adicional deve ser o mesmo que o novo endereço IP que atribuiu ao balancedor de carga SAP Azure.
>
>No nosso cenário, o endereço IP é 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>Adicione um endereço IP a um equilibrador de carga interna Azure existente utilizando powerShell

Para criar mais do que uma instância SAP ASCS/SCS no mesmo cluster WSFC, utilize a PowerShell para adicionar um endereço IP a um equilibrador de carga interna Azure existente. Cada endereço IP requer as suas próprias regras de equilíbrio de carga, porta de sonda, piscina IP frontal e piscina traseira.

O seguinte script adiciona um novo endereço IP a um equilibrador de carga existente. Atualize as variáveis PowerShell para o seu ambiente. O script criará todas as regras necessárias de equilíbrio de carga para todas as portas SAP ASCS/SCS.

```powershell

# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure VNet and subnet
$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzLoadBalancer

# Get new updated configuration
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
# Get new updated LP FrontendIP COnfig
$FEConfig = Get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add new back-end configuration into existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzLoadBalancer

# Get new updated config
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to backend pool
$BEPool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzNetworkInterface -NetworkInterface $NIC
        #start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzLoadBalancer

Write-Host "Successfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
Após a execução do script, os resultados são apresentados no portal Azure, como mostra a seguinte imagem:

![Novo pool IP frontal no portal Azure][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Adicione discos às máquinas de cluster e configure o disco de partilha de cluster SIOS

Deve adicionar um novo disco de partilha de cluster para cada instância adicional SAP ASCS/SCS. Para o Windows Server 2012 R2, o disco de partilha de cluster WSFC atualmente em uso é a solução de software SIOS DataKeeper.

Faça o seguinte:
1. Adicione um disco adicional ou discos do mesmo tamanho (que precisa de riscar) a cada um dos nós do cluster e forme-os.
2. Configure a replicação de armazenamento com o SIOS DataKeeper.

Este procedimento pressupõe que já instalou o SIOS DataKeeper nas máquinas de cluster WSFC. Se o instalou, tem agora de configurar a replicação entre as máquinas. O processo é descrito em detalhe no guia principal [para a alta disponibilidade SAP NetWeaver em VMs windows][sap-ha-guide-8.12.3.3].  

![DataKeeper espelhamento sincronizado para o novo disco de partilha SAP ASCS/SCS][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>Implementar VMs para servidores de aplicações SAP e cluster DBMS

Para completar a preparação da infraestrutura para o segundo sistema SAP, faça o seguinte:

1. Implemente VMs dedicados para servidores de aplicações SAP e coloque-os no seu próprio grupo de disponibilidade dedicado.
2. Implemente VMs dedicados para cluster DBMS e coloque-os no seu próprio grupo de disponibilidade dedicado.


## <a name="install-the-second-sap-sid2-netweaver-system"></a>Instale o segundo sistema SAP SID2 NetWeaver

O processo completo de instalação de um segundo sistema SAP SID2 é descrito no guia principal [para a alta disponibilidade SAP NetWeaver em VMs windows][sap-ha-guide-9].

O procedimento de alto nível é o seguinte:

1. [Instale o primeiro nó][sap-ha-guide-9.1.2]de cluster SAP .  
 Neste passo, está a instalar o SAP com uma instância ASCS/SCS de alta disponibilidade no **nó de cluster WSFC existente 1**.

2. [Modificar o perfil SAP da instância ASCS/SCS][sap-ha-guide-9.1.3].

3. [Configure uma porta de sonda][sap-ha-guide-9.1.4].  
 Neste passo, está a configurar uma porta de sonda SAP-SID2-IP de recurso de cluster SAP-SID2 utilizando o PowerShell. Execute esta configuração num dos nós de cluster SAP ASCS/SCS.

4. [Instale a instância da base de dados] [sap-ha-guide-9.2].  
 Neste passo, está a instalar o DBMS num cluster WSFC dedicado.

5. [Instale o segundo nó de cluster] [sap-ha-guide-9.3].  
 Neste passo, está a instalar o SAP com uma instância ASCS/SCS de alta disponibilidade no nó 2 do cluster WSFC existente.

6. Abra as portas de Firewall do Windows para a instância SAP ASCS/SCS e ProbePort.  
 Em ambos os nós de cluster que são utilizados para instâncias SAP ASCS/SCS, está a abrir todas as portas do Windows Firewall que são utilizadas pelo SAP ASCS/SCS. Estas portas estão listadas no [guia para a alta disponibilidade SAP NetWeaver em VMs windows][sap-ha-guide-8.8].  
 Abra também a porta de sonda de sonorizador de carga interna Azure, que é 62350 no nosso cenário.

7. [Altere o tipo de início da instância de serviço SAP ERS Windows][sap-ha-guide-9.4].

8. [Instale o servidor de aplicações primárias SAP][sap-ha-guide-9.5] no novo VM dedicado.

9. [Instale o servidor de aplicações adicional SAP][sap-ha-guide-9.6] no novo VM dedicado.

10. [Teste a falha da instância SAP ASCS/SCS e a replicação do SIOS][sap-ha-guide-10].

## <a name="next-steps"></a>Passos seguintes

- [Limites de networking: Gestor de Recursos Azure][networking-limits-azure-resource-manager]
- [Múltiplos VIPs para O Equilíbrio de Carga Azure][load-balancer-multivip-overview]
- [Guia para alta disponibilidade SAP NetWeaver em VMs windows][sap-ha-guide]
