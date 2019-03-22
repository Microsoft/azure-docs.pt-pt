---
title: Configurar Always On serviços de escuta do grupo de disponibilidade – Microsoft Azure | Documentos da Microsoft
description: Configure serviços de escuta de grupo de disponibilidade no modelo do Azure Resource Manager, utilizando um balanceador de carga interno com um ou mais endereços IP.
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/06/2019
ms.author: mikeray
ms.openlocfilehash: 1b15a3966c6e408fb17655b112c1ec900bd86c5f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57999778"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Configurar um ou mais Always On grupo serviços de escuta disponibilidade - Resource Manager
Este tópico mostra como:

* Crie um balanceador de carga interno para grupos de disponibilidade do SQL Server utilizando cmdlets do PowerShell.
* Adicione endereços IP adicionais a um balanceador de carga para mais de um grupo de disponibilidade. 

Um serviço de escuta do grupo de disponibilidade é um nome de rede virtual que os clientes ligam para acesso de base de dados. Em máquinas virtuais do Azure, um balanceador de carga contém o endereço IP para o serviço de escuta. O Balanceador de carga encaminha o tráfego para a instância do SQL Server que está a escutar na porta de pesquisa. Normalmente, um grupo de disponibilidade utiliza um balanceador de carga interno. Um balanceador de carga interno do Azure pode alojar um ou vários endereços IP. Cada endereço IP usa uma porta de sonda específico. Este documento mostra como utilizar o PowerShell para criar um balanceador de carga ou adicione os endereços IP a um balanceador de carga existente para grupos de disponibilidade do SQL Server. 

A capacidade de atribuir vários endereços IP a um balanceador de carga interno está familiarizada com o Azure e só está disponível no modelo do Resource Manager. Para concluir essa tarefa, tem de ter um grupo de disponibilidade do SQL Server implementado em máquinas virtuais do Azure no modelo do Resource Manager. Ambas as máquinas virtuais do SQL Server têm de pertencer ao mesmo conjunto de disponibilidade. Pode utilizar o [modelo de Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para criar automaticamente o grupo de disponibilidade no Azure Resource Manager. Este modelo cria automaticamente o grupo de disponibilidade, incluindo o Balanceador de carga interno para. Se preferir, pode [configurar manualmente um grupo de disponibilidade Always On](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Este tópico requer que os seus grupos de disponibilidade já estão configurados.  

Tópicos relacionados incluem:

* [Configurar grupos de Disponibilidade AlwaysOn em VMS do Azure (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Configurar uma ligação VNet a VNet com o Azure Resource Manager e PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>Verificar a versão do PowerShell

Os exemplos neste artigo são testados com o Azure PowerShell versão 5.4.1 do módulo.

Certifique-se de que o módulo do PowerShell é 5.4.1 ou posterior.

Ver [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="configure-the-windows-firewall"></a>Configurar a Firewall do Windows

Configure a Firewall do Windows para permitir o acesso do SQL Server. As regras de firewall permitam ligações de TCP para o uso de portas por instância do SQL Server e a sonda de serviço de escuta. Para obter instruções detalhadas, consulte [configurar uma Firewall do Windows para acesso ao motor de base de dados](https://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Crie uma regra de entrada para a porta do SQL Server e para a porta de sonda.

Se estiver a restrição do acesso com um grupo de segurança de rede do Azure, certifique-se de que as regras de permissão incluem os endereços de IP da VM do SQL Server back-end e o Balanceador de carga IP flutuante endereços para o serviço de escuta de AG e o endereço IP de núcleo de cluster, se aplicável.

## <a name="determine-the-load-balancer-sku-required"></a>Determinar o Balanceador de carga SKU necessário

[Balanceador de carga do Azure](../../../load-balancer/load-balancer-overview.md) está disponível em 2 SKUs: Básico e Standard. Recomenda-se o Balanceador de carga standard. Se as máquinas virtuais num conjunto de disponibilidade, o Balanceador de carga básico é permitida. Balanceador de carga Standard requer que todos os endereços IP da VM utilizarem endereços IP padrão.

O atual [modelo de Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para um disponibilidade grupo utiliza um balanceador de carga básico com endereços IP básicos.

Os exemplos neste artigo, especifique um balanceador de carga standard. Nos exemplos, o script inclui `-sku Standard`.

```PowerShell
$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

Para criar um balanceador de carga básico, remover `-sku Standard` partir da linha que cria o Balanceador de carga. Por exemplo:

```PowerShell
$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Script de exemplo: Criar um balanceador de carga interno com o PowerShell

> [!NOTE]
> Se tiver criado o seu grupo de disponibilidade com o [modelo de Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md), o Balanceador de carga interno já foi criado.

O seguinte script do PowerShell cria um balanceador de carga interno, configura a regras de balanceamento de carga e define um endereço IP do Balanceador de carga. Para executar o script, abra o ISE do Windows PowerShell e cole o script no painel de Script. Utilize `Connect-AzAccount` para iniciar sessão no PowerShell. Se tiver várias subscrições do Azure, utilize `Select-AzSubscription ` para definir a subscrição. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the front-end configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the back-end configuration

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzNetworkInterface -NetworkInterface $NIC
        start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="Add-IP"></a> Script de exemplo: Adicionar um endereço IP a um balanceador de carga existente com o PowerShell
Para utilizar mais de um grupo de disponibilidade, adicione um endereço IP adicional para o Balanceador de carga. Cada endereço IP requer seu próprio regra, a porta de sonda e a porta de front-de balanceamento de carga.

A porta de front-end é a porta que aplicações utilizam para ligar à instância do SQL Server. Endereços IP para grupos de disponibilidade diferente, podem utilizar a mesma porta de front-end.

> [!NOTE]
> Para grupos de disponibilidade do SQL Server, cada endereço IP requer uma porta de sonda específico. Por exemplo, se um endereço IP num Balanceador de carga utiliza a porta de sonda 59999, outros endereços IP no balanceador de carga que podem utilizar a porta de sonda 59999.

* Para obter informações sobre os limites de Balanceador de carga, veja **IP de front-end privado por Balanceador de carga** sob [limites de rede - Azure Resource Manager](../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Para obter informações sobre os limites do grupo de disponibilidade, consulte [restrições (grupos de disponibilidade)](https://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

O script a seguir adiciona um novo endereço IP a um balanceador de carga existente. O ILB utiliza a porta do serviço de escuta para a porta de front-end de balanceamento de carga. Esta porta pode ser a porta que o SQL Server está a escutar. Para as instâncias de padrão do SQL Server, a porta é 1433. Regra para um grupo de disponibilidade de balanceamento de carga requer um IP flutuante (devolução direta do servidor), para a porta de back-end é o mesmo que a porta de front-end. Atualize as variáveis para o seu ambiente. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzLoadBalancer 

$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzLoadBalancer   
```

## <a name="configure-the-listener"></a>Configurar o serviço de escuta

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>Defina a porta de serviço de escuta no SQL Server Management Studio

1. Inicie o SQL Server Management Studio e ligue-se para a réplica primária.

1. Navegue para **AlwaysOn elevada disponibilidade** | **grupos de disponibilidade** | **serviços de escuta do grupo de disponibilidade**. 

1. Agora, deverá ver o nome do serviço de escuta que criou no Gestor de clusters de ativação pós-falha. O nome do serviço de escuta com o botão direito e clique em **propriedades**.

1. Na **porta** caixa, especifique o número de porta para o serviço de escuta do grupo de disponibilidade com o $EndpointPort que utilizou anteriormente (era a predefinição de 1433), em seguida, clique em **OK**.

## <a name="test-the-connection-to-the-listener"></a>Testar a ligação ao serviço de escuta

Para testar a ligação:

1. RDP para um SQL Server que está na mesma rede virtual, mas não é proprietário a réplica. Isso pode ser outro servidor SQL no cluster.

1. Uso **sqlcmd** utilitário para testar a ligação. Por exemplo, o seguinte script estabelece uma **sqlcmd** ligação para a réplica primária por meio do serviço de escuta com a autenticação do Windows:
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    Se o serviço de escuta é utilizar uma porta diferente da predefinição da porta (1433), especifique a porta na cadeia de ligação. Por exemplo, o seguinte comando sqlcmd liga a um serviço de escuta na porta 1435: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

A ligação de SQLCMD liga-se automaticamente para qualquer instância do SQL Server aloja a réplica primária. 

> [!NOTE]
> Certifique-se de que a porta especificada está aberta na firewall de ambos os servidores SQL. Ambos os servidores exigem uma regra de entrada para a porta TCP que utiliza. Ver [adicionar ou Editar regra de Firewall](https://technet.microsoft.com/library/cc753558.aspx) para obter mais informações. 
> 
> 

## <a name="guidelines-and-limitations"></a>Diretrizes e limitações
Tenha em atenção as seguintes diretrizes no serviço de escuta de grupo de disponibilidade no Azure utilizando o interno de Balanceador de carga:

* Com um balanceador de carga interno, acessar apenas o serviço de escuta de dentro da mesma rede virtual.

* Se estiver a restrição do acesso com um grupo de segurança de rede do Azure, certifique-se de que as regras de permissão incluem os endereços de IP da VM do SQL Server back-end e o Balanceador de carga IP flutuante endereços para o serviço de escuta de AG e o endereço IP de núcleo de cluster, se aplicável.

## <a name="for-more-information"></a>Para obter mais informações:
Para obter mais informações, consulte [grupo de disponibilidade configurar Always On na VM do Azure manualmente](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell
Utilize os seguintes cmdlets do PowerShell para criar um balanceador de carga interno para máquinas virtuais do Azure.

* [Novo AzLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) cria um balanceador de carga. 
* [Novo AzLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) cria uma configuração de IP Front-end para um balanceador de carga. 
* [Novo AzLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) cria uma configuração de regra para um balanceador de carga. 
* [Novo AzLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) cria uma configuração de conjuntos de endereços de back-end para um balanceador de carga. 
* [Novo AzLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) cria uma configuração de pesquisa para um balanceador de carga.
* [Remove-AzLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) remove um balanceador de carga a partir de um grupo de recursos do Azure.
