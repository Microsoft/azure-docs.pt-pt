---
title: Configurar ouvintes de grupo de disponibilidade & balanceador de carga (PowerShell)
description: Configure os ouvintes do grupo de disponibilidade no modelo de Azure Resource Manager, usando um balanceador de carga interno com um ou mais endereços IP.
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/06/2019
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 83910c2209b5d3d3d67578ae41afb902bc885171
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037461"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Configurar um ou mais ouvintes de grupo de disponibilidade Always On-Resource Manager
Este tópico mostra como:

* Crie um balanceador de carga interno para SQL Server grupos de disponibilidade usando cmdlets do PowerShell.
* Adicione outros endereços IP a um balanceador de carga para mais de um grupo de disponibilidade. 

Um ouvinte de grupo de disponibilidade é um nome de rede virtual ao qual os clientes se conectam para acesso ao banco de dados. Em máquinas virtuais do Azure, um balanceador de carga mantém o endereço IP para o ouvinte. O balanceador de carga roteia o tráfego para a instância do SQL Server que está escutando na porta de investigação. Normalmente, um grupo de disponibilidade usa um balanceador de carga interno. Um balanceador de carga interno do Azure pode hospedar um ou vários endereços IP. Cada endereço IP usa uma porta de investigação específica. Este documento mostra como usar o PowerShell para criar um balanceador de carga ou adicionar endereços IP a um balanceador de carga existente para SQL Server grupos de disponibilidade. 

A capacidade de atribuir vários endereços IP a um balanceador de carga interno é nova no Azure e só está disponível no modelo do Resource Manager. Para concluir essa tarefa, você precisa ter um grupo de disponibilidade SQL Server implantado em máquinas virtuais do Azure no modelo do Resource Manager. Ambas as máquinas virtuais SQL Server devem pertencer ao mesmo conjunto de disponibilidade. Você pode usar o [modelo da Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para criar automaticamente o grupo de disponibilidade no Azure Resource Manager. Esse modelo cria automaticamente o grupo de disponibilidade, incluindo o balanceador de carga interno para você. Se preferir, você pode [configurar manualmente um grupo de disponibilidade Always on](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Este tópico requer que seus grupos de disponibilidade já estejam configurados.  

Os tópicos relacionados incluem:

* [Configurar Grupos de Disponibilidade AlwaysOn na VM do Azure (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Configurar uma conexão de VNet para VNet usando o Azure Resource Manager e o PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>Verificar a versão do PowerShell

Os exemplos neste artigo são testados usando Azure PowerShell versão do módulo 5.4.1.

Verifique se o módulo do PowerShell é 5.4.1 ou posterior.

Consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="configure-the-windows-firewall"></a>Configurar o Firewall do Windows

Configure o Firewall do Windows para permitir acesso SQL Server. As regras de firewall permitem conexões TCP com as portas usadas pela instância do SQL Server e a investigação do ouvinte. Para obter instruções detalhadas, consulte [configurar um firewall do Windows para acesso mecanismo de banco de dados](https://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Crie uma regra de entrada para a porta de SQL Server e para a porta de investigação.

Se você estiver restringindo o acesso com um grupo de segurança de rede do Azure, verifique se as regras de permissão incluem os endereços IP de VM de back-end SQL Server e os endereços IP flutuantes do balanceador de carga para o ouvinte AG e o endereço IP principal do cluster, se aplicável.

## <a name="determine-the-load-balancer-sku-required"></a>Determinar o SKU do balanceador de carga necessário

O [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) está disponível em 2 SKUs: Basic & Standard. O balanceador de carga padrão é recomendado. Se as máquinas virtuais estiverem em um conjunto de disponibilidade, o Load Balancer básico será permitido. O balanceador de carga padrão requer que todos os endereços IP da VM usem endereços IP padrão.

O modelo atual da [Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para um grupo de disponibilidade usa um balanceador de carga básico com endereços IP básicos.

Os exemplos neste artigo especificam um balanceador de carga padrão. Nos exemplos, o script inclui `-sku Standard`.

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

Para criar um balanceador de carga básico, remova `-sku Standard` da linha que cria o balanceador de carga. Por exemplo:

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Script de exemplo: criar um balanceador de carga interno com o PowerShell

> [!NOTE]
> Se você criou seu grupo de disponibilidade com o [modelo da Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md), o balanceador de carga interno já foi criado.

O script do PowerShell a seguir cria um balanceador de carga interno, configura as regras de balanceamento de carga e define um endereço IP para o balanceador de carga. Para executar o script, abra ISE do Windows PowerShell e cole o script no painel de script. Use `Connect-AzAccount` para fazer logon no PowerShell. Se você tiver várias assinaturas do Azure, use `Select-AzSubscription` para definir a assinatura. 

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

## <a name="Add-IP"></a>Script de exemplo: adicionar um endereço IP a um balanceador de carga existente com o PowerShell
Para usar mais de um grupo de disponibilidade, adicione um endereço IP adicional ao balanceador de carga. Cada endereço IP requer sua própria regra de balanceamento de carga, porta de investigação e porta frontal.

A porta de front-end é a porta que os aplicativos usam para se conectar à instância de SQL Server. Os endereços IP para diferentes grupos de disponibilidade podem usar a mesma porta de front-end.

> [!NOTE]
> Para SQL Server grupos de disponibilidade, cada endereço IP requer uma porta de investigação específica. Por exemplo, se um endereço IP em um balanceador de carga usar a porta de investigação 59999, nenhum outro endereços IP nesse balanceador de carga poderá usar a porta de investigação 59999.

* Para obter informações sobre limites de balanceador de carga, consulte **IP de front-end privado por balanceador de carga** em [limites de rede-Azure Resource Manager](../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Para obter informações sobre os limites do grupo de disponibilidade, consulte [restrições (grupos de disponibilidade)](https://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

O script a seguir adiciona um novo endereço IP a um balanceador de carga existente. O ILB usa a porta do ouvinte para a porta de front-end de balanceamento de carga. Essa porta pode ser a porta na qual SQL Server está escutando. Para instâncias padrão do SQL Server, a porta é 1433. A regra de balanceamento de carga para um grupo de disponibilidade requer um IP flutuante (retorno de servidor direto) para que a porta de back-end seja a mesma que a porta de front-end. Atualize as variáveis para seu ambiente. 

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

## <a name="configure-the-listener"></a>Configurar o ouvinte

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>Definir a porta do ouvinte no SQL Server Management Studio

1. Inicie o SQL Server Management Studio e conecte-se à réplica primária.

1. Navegue até **alta disponibilidade AlwaysOn** | **grupos de disponibilidade** | **ouvintes do grupo de disponibilidade**. 

1. Agora você deve ver o nome do ouvinte que você criou em Gerenciador de Cluster de Failover. Clique com o botão direito do mouse no nome do ouvinte e clique em **Propriedades**.

1. Na caixa **porta** , especifique o número da porta para o ouvinte do grupo de disponibilidade usando o $EndpointPort usado anteriormente (1433 era o padrão) e clique em **OK**.

## <a name="test-the-connection-to-the-listener"></a>Testar a conexão com o ouvinte

Para testar a conexão:

1. RDP para um SQL Server que está na mesma rede virtual, mas não possui a réplica. Isso pode ser o outro SQL Server no cluster.

1. Use o utilitário **sqlcmd** para testar a conexão. Por exemplo, o script a seguir estabelece uma conexão **sqlcmd** com a réplica primária por meio do ouvinte com autenticação do Windows:
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    Se o ouvinte estiver usando uma porta diferente da porta padrão (1433), especifique a porta na cadeia de conexão. Por exemplo, o comando sqlcmd a seguir se conecta a um ouvinte na porta 1435: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

A conexão SQLCMD se conecta automaticamente a qualquer instância do SQL Server hospeda a réplica primária. 

> [!NOTE]
> Verifique se a porta que você especificou está aberta no firewall de ambos os SQL Servers. Ambos os servidores exigem uma regra de entrada para a porta TCP que você usa. Consulte [Adicionar ou editar regra de firewall](https://technet.microsoft.com/library/cc753558.aspx) para obter mais informações. 
> 
> 

## <a name="guidelines-and-limitations"></a>Diretrizes e limitações
Observe as seguintes diretrizes no ouvinte do grupo de disponibilidade no Azure usando o balanceador de carga interno:

* Com um balanceador de carga interno, você só acessa o ouvinte de dentro da mesma rede virtual.

* Se você estiver restringindo o acesso com um grupo de segurança de rede do Azure, verifique se as regras de permissão incluem os endereços IP de VM de back-end SQL Server e os endereços IP flutuantes do balanceador de carga para o ouvinte AG e o endereço IP principal do cluster, se aplicável.

## <a name="for-more-information"></a>Para obter mais informações:
Para obter mais informações, consulte [Configurar o grupo de disponibilidade Always on na VM do Azure manualmente](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell
Use os seguintes cmdlets do PowerShell para criar um balanceador de carga interno para máquinas virtuais do Azure.

* [New-AzLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) cria um balanceador de carga. 
* [New-AzLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) cria uma configuração de IP de front-end para um balanceador de carga. 
* [New-AzLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) cria uma configuração de regra para um balanceador de carga. 
* [New-AzLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) cria uma configuração de pool de endereços de back-end para um balanceador de carga. 
* [New-AzLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) cria uma configuração de investigação para um balanceador de carga.
* Remove [-AzLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) remove um balanceador de carga de um grupo de recursos do Azure.
