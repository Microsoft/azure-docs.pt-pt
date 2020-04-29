---
title: Configure os ouvintes do grupo de disponibilidade & balanceador de carga (PowerShell)
description: Configure os ouvintes do Grupo de Disponibilidade no modelo Do Gestor de Recursos Azure, utilizando um equilibrador de carga interno com um ou mais endereços IP.
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
ms.openlocfilehash: cabfc84d2bc0c9d08a457e67c0182d7550f04ceb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80668892"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Configure um ou mais sempre em pessoas do grupo de disponibilidade - Gestor de Recursos
Este tópico mostra como:

* Crie um equilíbrio interno de carga para grupos de disponibilidade do Servidor SQL utilizando cmdlets PowerShell.
* Adicione endereços IP adicionais a um equilibrador de carga para mais de um grupo de disponibilidade. 

Um ouvinte de grupo de disponibilidade é um nome de rede virtual a que os clientes se conectam para acesso à base de dados. Nas máquinas virtuais Azure, um equilibrista de carga detém o endereço IP para o ouvinte. O equilibrista de carga direciona o tráfego para a instância do SQL Server que está a ouvir na porta da sonda. Normalmente, um grupo de disponibilidade usa um equilibrador de carga interno. Um equilibrador de carga interna Azure pode acolher um ou muitos endereços IP. Cada endereço IP utiliza uma porta de sonda específica. Este documento mostra como usar o PowerShell para criar um balanceor de carga, ou adicionar endereços IP a um equilíbrio de carga existente para grupos de disponibilidade do Servidor SQL. 

A capacidade de atribuir vários endereços IP a um equilibrista interno de carga é nova para o Azure e só está disponível no modelo De gestor de recursos. Para completar esta tarefa, é necessário ter um grupo de disponibilidade do SQL Server implantado em máquinas virtuais Azure no modelo Derecursos Manager. Ambas as máquinas virtuais do SQL Server devem pertencer ao mesmo conjunto de disponibilidade. Pode utilizar o modelo da [Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para criar automaticamente o grupo de disponibilidade no Gestor de Recursos Do Azure. Este modelo cria automaticamente o grupo de disponibilidade, incluindo o equilibrador interno de carga para si. Se preferir, pode [configurar manualmente um grupo Sempre On disponibilidade](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Este tópico requer que os seus grupos de disponibilidade já estejam configurados.  

Os tópicos relacionados incluem:

* [Configure Grupos de Disponibilidade AlwaysOn em Azure VM (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Configurar uma ligação VNet a VNet com o Azure Resource Manager e o PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>Verifique a versão PowerShell

Os exemplos deste artigo são testados utilizando a versão 5.4.1 do módulo Azure PowerShell.

Verifique se o seu módulo PowerShell é 5.4.1 ou mais tarde.

Consulte [A instalação do módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="configure-the-windows-firewall"></a>Configure a Firewall do Windows

Configure a Firewall do Windows para permitir o acesso ao Servidor SQL. As regras de firewall permitem ligações TCP às portas utilizadas pela instância SQL Server e pela sonda ouvinte. Para obter instruções detalhadas, consulte [Configurar uma firewall do Windows para acesso](https://msdn.microsoft.com/library/ms175043.aspx#Anchor_1)ao motor de base de dados . Crie uma regra de entrada para a porta SQL Server e para a porta da sonda.

Se estiver a restringir o acesso a um Grupo de Segurança da Rede Azure, certifique-se de que as regras de licenciamento incluem os endereços IP VM do Servidor SQL e os endereços IP flutuantes do balanceor de carga para o ouvinte AG e o endereço IP do núcleo de cluster, se aplicável.

## <a name="determine-the-load-balancer-sku-required"></a>Determine o equilíbrior de carga SKU necessário

[O equilíbrio de carga Azure](../../../load-balancer/load-balancer-overview.md) está disponível em 2 SKUs: Basic & Standard. Recomenda-se o equilíbrio de carga padrão. Se as máquinas virtuais estiverem num conjunto de disponibilidade, é permitido um equilíbrio de carga básico. Se as máquinas virtuais estiverem numa zona de disponibilidade, é necessário um equilíbrio de carga padrão. O equilíbrio de carga padrão requer que todos os endereços IP VM utilizem endereços IP padrão.

O [modelo](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) atual da Microsoft para um grupo de disponibilidade utiliza um equilíbrio de carga básico com endereços IP básicos.

   > [!NOTE]
   > Terá de configurar um [ponto final](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network) de serviço se utilizar um balanceor de carga padrão e armazenamento azure para a testemunha em nuvem. 


Os exemplos deste artigo especificam um equilibrador de carga padrão. Nos exemplos, o `-sku Standard`guião inclui .

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

Para criar um equilíbrio de `-sku Standard` carga básico, retire da linha que cria o equilibrador de carga. Por exemplo:

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Exemplo Script: Criar um equilibrador de carga interna com powerShell

> [!NOTE]
> Se criou o seu grupo de disponibilidade com o modelo da [Microsoft,](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)o equilibrador de carga interna já foi criado.

O seguinte script PowerShell cria um equilibrador de carga interno, configura as regras de equilíbrio de carga e define um endereço IP para o equilibrador de carga. Para executar o script, abra o Windows PowerShell ISE e colhe o script no painel script. Utilize `Connect-AzAccount` para iniciar sessão na PowerShell. Se tiver várias subscrições `Select-AzSubscription` do Azure, utilize para definir a subscrição. 

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

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a><a name="Add-IP"></a>Script exemplo: Adicione um endereço IP a um equilibrador de carga existente com powerShell
Para utilizar mais do que um grupo de disponibilidade, adicione um endereço IP adicional ao equilibrador de carga. Cada endereço IP requer a sua própria regra de equilíbrio de carga, porta de sonda e porta frontal.

A porta frontal é a porta que as aplicações utilizam para ligar à instância do Servidor SQL. Os endereços IP para diferentes grupos de disponibilidade podem usar a mesma porta frontal.

> [!NOTE]
> Para os grupos de disponibilidade do Servidor SQL, cada endereço IP requer uma porta de sonda específica. Por exemplo, se um endereço IP num equilibrador de carga utilizar a porta de sonda 59999, nenhum outro endereço IP nesse equilíbrio de carga pode utilizar a porta de sonda 59999.

* Para obter informações sobre os limites do equilíbrio de carga, consulte **IP frontal privado por balanceador** de carga sob limites de rede - Gestor de Recursos [Azure](../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Para obter informações sobre os limites do grupo de disponibilidade, consulte [Restrições (Grupos de Disponibilidade)](https://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

O seguinte script adiciona um novo endereço IP a um equilibrador de carga existente. O ILB utiliza a porta do ouvinte para a porta frontal de equilíbrio de carga. Esta porta pode ser a porta que o SQL Server está a ouvir. Para os casos predefinidos do Servidor SQL, a porta é 1433. A regra de equilíbrio de carga para um grupo de disponibilidade requer um IP flutuante (retorno do servidor direto) para que a porta traseira seja a mesma que a porta frontal. Atualize as variáveis para o seu ambiente. 

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

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>Desloque a porta de escuta no Estúdio de Gestão de Servidores SQL

1. Lance o Estúdio de Gestão de Servidores SQL e ligue-se à réplica primária.

1. Navegue para **sempre On High Availability** | **Availability Groups** | **Disponibilidade Grupo sintetmente Ouvintes**do Grupo . 

1. Agora deve ver o nome do ouvinte que criou no Failover Cluster Manager. Clique no nome do ouvinte e clique em **Propriedades**.

1. Na caixa **do Porto,** especifique o número de porta para o ouvinte do grupo de disponibilidade utilizando o $EndpointPort utilizado anteriormente (1433 foi o padrão), em seguida, clique em **OK**.

## <a name="test-the-connection-to-the-listener"></a>Testar a ligação ao ouvinte

Para testar a ligação:

1. RDP para um Servidor SQL que está na mesma rede virtual, mas não possui a réplica. Este pode ser o outro Servidor SQL no cluster.

1. Utilize um utilitário **sqlcmd** para testar a ligação. Por exemplo, o seguinte script estabelece uma ligação **sqlcmd** à réplica primária através do ouvinte com autenticação Windows:
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    Se o ouvinte estiver a utilizar uma porta diferente da porta predefinida (1433), especifique a porta na cadeia de ligação. Por exemplo, o seguinte comando sqlcmd conecta-se a um ouvinte na porta 1435: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

A ligação SQLCMD liga-se automaticamente a qualquer instância do SQL Server que acolhe a réplica primária. 

> [!NOTE]
> Certifique-se de que a porta que especifica está aberta na firewall de ambos os Servidores SQL. Ambos os servidores requerem uma regra de entrada para a porta TCP que utiliza. Consulte [a Regra de Adicionar ou Editar firewall](https://technet.microsoft.com/library/cc753558.aspx) para obter mais informações. 
> 
> 

## <a name="guidelines-and-limitations"></a>Orientações e limitações
Note as seguintes orientações sobre o ouvinte do grupo de disponibilidade em Azure utilizando o equilíbrio interno de carga:

* Com um equilibrador de carga interna, você só acede ao ouvinte dentro da mesma rede virtual.

* Se estiver a restringir o acesso a um Grupo de Segurança da Rede Azure, certifique-se de que as regras de licenciamento incluem os endereços IP VM do Servidor SQL e os endereços IP flutuantes do balanceor de carga para o ouvinte AG e o endereço IP do núcleo de cluster, se aplicável.

* Crie um ponto final de serviço ao utilizar um equilíbrio de carga padrão com o Armazenamento Azure para a testemunha em nuvem. Para mais informações, consulte [o acesso do Grant a partir de uma rede virtual.](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)

## <a name="for-more-information"></a>Para obter mais informações:
Para mais informações, consulte [configure Always On availability group em Azure VM manualmente](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell
Utilize os seguintes cmdlets PowerShell para criar um equilíbrio interno de carga para máquinas virtuais Azure.

* [O New-AzLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) cria um equilibrador de carga. 
* [O New-AzLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) cria uma configuração IP frontal para um equilíbrio de carga. 
* [O New-AzLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) cria uma configuração de regra para um equilibrador de carga. 
* [New-AzLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) cria uma configuração de piscina de endereço de backend para um equilibrador de carga. 
* [O New-AzLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) cria uma configuração de sonda para um equilibrador de carga.
* [Remove-AzLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) remove um equilibrador de carga de um grupo de recursos Azure.
