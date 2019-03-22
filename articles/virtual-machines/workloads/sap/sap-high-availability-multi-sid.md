---
title: Criar uma configuração de múltiplos SID SAP no Azure | Documentos da Microsoft
description: Guia para configuração de múltiplos SID SAP NetWeaver de elevada disponibilidade, no Windows, máquinas virtuais
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e5d8357d17f8a6b463546789dd4627b74924a763
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58081003"
---
# <a name="create-an-sap-netweaver-multi-sid-configuration"></a>Criar uma configuração de múltiplos SID SAP NetWeaver

[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[sap-ha-guide]:sap-high-availability-guide.md 
[sap-ha-guide-figure-6001]:./media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:./media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:./media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:./media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:./media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png
[networking-limits-azure-resource-manager]:../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
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

Em Setembro de 2016, a Microsoft lançou um recurso onde pode gerir vários endereços IP virtual utilizando um balanceador de carga interno do Azure. Esta funcionalidade já existe no balanceador de carga externo do Azure.

Se tiver uma implementação de SAP, pode utilizar um balanceador de carga interno para criar uma configuração de cluster do Windows para o SAP ASCS/SCS, conforme documentado no [guia de elevada disponibilidade SAP NetWeaver em VMs do Windows] [ sap-ha-guide].

Este artigo se concentra em como mover de uma única instalação ASCS/SCS a uma configuração de múltiplos SID SAP, ao instalar instâncias adicionais do SAP ASCS/SCS em cluster para um cluster de Clustering de ativação pós-falha de servidor ao Windows ' (WSFC) existente. Quando esse processo é concluído, terá configurado um cluster de múltiplos SID SAP.


## <a name="prerequisites"></a>Pré-requisitos
Já configurou um cluster WSFC que é utilizado para uma instância do SAP ASCS/SCS, tal como explicado no [guia de elevada disponibilidade SAP NetWeaver em VMs do Windows] [ sap-ha-guide] e conforme mostrado neste diagrama.

![Instância do SAP ASCS/SCS de elevada disponibilidade][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>Arquitetura de destino

O objetivo é instalar vários SAP ABAP ASCS ou SAP Java SCS em cluster instâncias no mesmo cluster WSFC, como ilustrado aqui:

![Várias instâncias do SAP ASCS/SCS em cluster no Azure][sap-ha-guide-figure-6002]

> [!NOTE]
>Existe um limite ao número de IPs de front-end privados para cada Balanceador de carga interno do Azure.
>
>O número máximo de instâncias do SAP ASCS/SCS num cluster WSFC é igual ao número máximo de IPs de front-end privado para cada Balanceador de carga interno do Azure.
>

Para obter mais informações sobre os limites de Balanceador de carga, consulte o "IP de front-end privado por Balanceador de carga" no [limites de rede: Azure Resource Manager][networking-limits-azure-resource-manager].

O cenário completo com dois sistemas SAP de elevada disponibilidade teria o seguinte aspeto:

![Configuração de elevada disponibilidade múltiplos SID SAP com o sistema SAP duas SIDs][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> A configuração tem de cumprir as seguintes condições:
> - As instâncias do SAP ASCS/SCS têm de partilhar o mesmo cluster WSFC.
> - Cada SID DBMS tem de ter seu próprio cluster WSFC dedicado.
> - Servidores de aplicações SAP que pertencem a um sistema SAP SID tem de ter suas próprias VMs dedicadas.


## <a name="prepare-the-infrastructure"></a>Preparar a infraestrutura
Para preparar a infraestrutura, pode instalar uma instância do SAP ASCS/SCS adicional com os seguintes parâmetros:

| Nome do parâmetro | Value |
| --- | --- |
| SAP ASCS/SCS SID |pr1-lb-ascs |
| Balanceador de carga interno do SAP DBMS | PR5 |
| Nome de anfitrião virtual de SAP | pr5-sap-cl |
| Endereço IP de anfitrião virtual do SAP ASCS/SCS (endereço IP do Balanceador de carga do Azure adicionais) | 10.0.0.50 |
| Número de instâncias do SAP ASCS/SCS | 50 |
| Porta de sonda do ILB para a instância do SAP ASCS/SCS adicionais | 62350 |

> [!NOTE]
> Para as instâncias de cluster do SAP ASCS/SCS, cada endereço IP requer uma porta de sonda exclusivo. Por exemplo, se um endereço IP num Balanceador de carga interno do Azure utiliza a porta de sonda 62300, outro endereço IP no balanceador de carga que pode utilizar a porta de sonda 62300.
>
>Para nossos propósitos, porque a porta de sonda 62300 já está reservada, estamos a utilizar a porta de sonda 62350.

Pode instalar instâncias adicionais do SAP ASCS/SCS do cluster de WSFC existente com dois nós:

| Função de máquina virtual | Nome de anfitrião de máquina virtual | Endereço IP estático |
| --- | --- | --- |
| nó de cluster 1 para a instância ASCS/SCS |pr1-ascs-0 |10.0.0.10 |
| 2ª nó de cluster para a instância ASCS/SCS |pr1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Criar um nome de anfitrião virtual para a instância do SAP ASCS/SCS em cluster no servidor DNS

Pode criar uma entrada DNS para o nome de anfitrião virtual de instância do ASCS/SCS utilizando os seguintes parâmetros:

| Novo nome de anfitrião virtual SAP ASCS/SCS | Endereço IP associado |
| --- | --- |
|pr5-sap-cl |10.0.0.50 |

O novo nome de anfitrião e endereço IP são apresentados no Gestor de DNS, conforme mostrado na captura de ecrã seguinte:

![Lista de Gestor de DNS, realce a entrada DNS definida a nova SAP ASCS/SCS do cluster virtual nome e endereço de TCP/IP][sap-ha-guide-figure-6004]

O procedimento para criar uma entrada DNS também está descrito em detalhe no principal [guia de elevada disponibilidade SAP NetWeaver em VMs do Windows][sap-ha-guide-9.1.1].

> [!NOTE]
> O novo endereço IP que atribuir ao nome do anfitrião virtual de instância do ASCS/SCS adicional tem de ser o mesmo que o novo endereço IP que atribuiu ao balanceador de carga do Azure de SAP.
>
>Em nosso cenário, o endereço IP é 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>Adicionar um endereço IP a um balanceador de carga interno do Azure existente com o PowerShell

Para criar mais de uma instância do SAP ASCS/SCS no mesmo cluster WSFC, utilize o PowerShell para adicionar um endereço IP a um balanceador de carga interno do Azure existente. Cada endereço IP requer seu próprio regras de balanceamento de carga, a porta de sonda, o conjunto IP Front-end e o conjunto back-end.

O script a seguir adiciona um novo endereço IP a um balanceador de carga existente. Atualize as variáveis do PowerShell para o seu ambiente. O script irá criar regras de balanceamento de carga todas as necessárias para todas as portas de SAP ASCS/SCS.

```powershell

# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure VNet and subnet
$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzureRmLoadBalancer

# Get new updated configuration
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
# Get new updated LP FrontendIP COnfig
$FEConfig = Get-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add new back-end configuration into existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzureRmLoadBalancer

# Get new updated config
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to backend pool
$BEPool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        #start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzureRmLoadBalancer

Write-Host "Successfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
Após a execução do script, os resultados são apresentados no portal do Azure, conforme mostrado na captura de ecrã seguinte:

![Novo conjunto IP de front-end no portal do Azure][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Adicionar discos para máquinas de cluster e configurar o disco de compartilhamento de cluster do SIOS

Tem de adicionar um nova partilha de disco em cluster para cada instância adicional do SAP ASCS/SCS. Para o Windows Server 2012 R2, o disco de compartilhamento de cluster WSFC atualmente em uso é a solução de software do SIOS DataKeeper.

Faça o seguinte:
1. Adicione um disco adicional ou discos do mesmo tamanho (o que precisa da faixa) a cada um de nós do cluster e formatá-los.
2. Configure a replicação de armazenamento com o SIOS DataKeeper.

Este procedimento assume que já tem instalado o SIOS DataKeeper nas máquinas de cluster WSFC. Se ele foi instalado, agora tem de configurar a replicação entre as máquinas. O processo é descrito detalhadamente no principal [guia de elevada disponibilidade SAP NetWeaver em VMs do Windows][sap-ha-guide-8.12.3.3].  

![DataKeeper síncrona de espelhamento para o novo SAP ASCS/SCS partilhar o disco][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>Implementar VMs para servidores de aplicações SAP e o cluster DBMS

Para concluir a preparação de infraestrutura para o sistema SAP segundo, efetue o seguinte:

1. Implementar VMs dedicadas para servidores de aplicações SAP e colocá-los no seu próprio grupo de disponibilidade dedicado.
2. Implementar VMs dedicadas para o cluster do DBMS e colocá-los no seu próprio grupo de disponibilidade dedicado.


## <a name="install-the-second-sap-sid2-netweaver-system"></a>Instalar o sistema SAP NetWeaver de SID2 segundo

O processo completo de instalação de um segundo sistema SAP SID2 é descrito no principal [guia de elevada disponibilidade SAP NetWeaver em VMs do Windows][sap-ha-guide-9].

O procedimento de alto nível é o seguinte:

1. [Instalar o SAP primeiro nó de cluster][sap-ha-guide-9.1.2].  
 Neste passo, estiver a instalar SAP com uma instância do ASCS/SCS de elevada disponibilidade no **o nó de cluster de WSFC existente 1**.

2. [Modificar o perfil SAP da instância do ASCS/SCS][sap-ha-guide-9.1.3].

3. [Configurar uma porta de sonda][sap-ha-guide-9.1.4].  
 Neste passo, está a configurar um recurso de cluster SAP porta de sonda de SAP-SID2-IP com o PowerShell. Execute esta configuração em um de nós do cluster de SAP ASCS/SCS.

4. [Instalar a instância de base de dados] [sap-ha-guia-9.2].  
 Neste passo, estiver instalando o DBMS num cluster WSFC dedicado.

5. [Instalar o segundo nó de cluster] [sap-ha-guia-9.3].  
 Neste passo, que está a instalar SAP com uma instância do ASCS/SCS de elevada disponibilidade no nó de cluster WSFC 2 existente.

6. Abrir portas de Firewall do Windows para a instância do SAP ASCS/SCS e ProbePort.  
 Em ambos os nós de cluster que são utilizados para as instâncias do SAP ASCS/SCS, estará criando todas as portas de Firewall do Windows que são utilizadas pelo SAP ASCS/SCS. Estas portas são listadas na [guia de elevada disponibilidade SAP NetWeaver em VMs do Windows][sap-ha-guide-8.8].  
 Também abra a porta de sonda de Balanceador de carga interno do Azure, que é 62350 em nosso cenário.

7. [Alterar o tipo de início da instância de serviço Windows do SAP ERS][sap-ha-guide-9.4].

8. [Instalar o servidor de principal da aplicação SAP] [ sap-ha-guide-9.5] no novo dedicado a VM.

9. [Instalar o servidor de aplicações adicionais do SAP] [ sap-ha-guide-9.6] no novo dedicado a VM.

10. [Testar a ativação pós-falha de instância do SAP ASCS/SCS e os replicação SIOS][sap-ha-guide-10].

## <a name="next-steps"></a>Passos Seguintes

- [Limites de rede: Azure Resource Manager][networking-limits-azure-resource-manager]
- [Balanceador de carga de vários VIPs para o Azure][load-balancer-multivip-overview]
- [Guia para elevada disponibilidade SAP NetWeaver em VMs do Windows][sap-ha-guide]
