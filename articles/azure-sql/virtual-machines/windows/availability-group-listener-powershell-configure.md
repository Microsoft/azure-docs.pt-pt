---
title: Configure os ouvintes do grupo de disponibilidade e o equilibrador de carga (PowerShell)
description: Configure os ouvintes do Grupo disponibilidade no modelo Azure Resource Manager, utilizando um equilibrador de carga interno com um ou mais endereços IP.
services: virtual-machines
documentationcenter: na
author: MashaMSFT
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/06/2019
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: cb55274800b239cf0e1e942647ae0c65b321b862
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790054"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Configure um ou mais Sempre Na disponibilidade de ouvintes do grupo - Gestor de Recursos

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este documento mostra-lhe como utilizar o PowerShell para fazer uma das seguintes tarefas:
- criar um equilibrador de carga
- adicionar endereços IP a um balanceador de carga existente para grupos de disponibilidade de servidor SQL.

Um ouvinte de grupo de disponibilidade é um nome de rede virtual a que os clientes se conectam para acesso à base de dados. Nas Máquinas Virtuais Azure, um equilibrador de carga detém o endereço IP para o ouvinte. O equilibrador de carga encaminha o tráfego para o caso do SQL Server que está a ouvir na porta da sonda. Normalmente, um grupo de disponibilidade usa um equilibrador de carga interno. Um equilibrador interno de carga Azure pode hospedar um ou muitos endereços IP. Cada endereço IP utiliza uma porta de sonda específica. 

A capacidade de atribuir vários endereços IP a um equilibrador de carga interno é nova para o Azure e só está disponível no modelo de Gestor de Recursos. Para completar esta tarefa, precisa de ter um grupo de disponibilidade de servidor SQL implantado em Máquinas Virtuais Azure no modelo Gestor de Recursos. Ambas as máquinas virtuais SQL Server devem pertencer ao mesmo conjunto de disponibilidade. Pode utilizar o modelo da [Microsoft](./availability-group-quickstart-template-configure.md) para criar automaticamente o grupo de disponibilidade no Azure Resource Manager. Este modelo cria automaticamente o grupo de disponibilidade, incluindo o balançador de carga interno para si. Se preferir, pode [configurar manualmente um grupo de disponibilidade Always On](availability-group-manually-configure-tutorial.md).

Para completar os passos deste artigo, os seus grupos de disponibilidade precisam de ser configurados.  

Os tópicos relacionados incluem:

* [Configure grupos de disponibilidade alwayson em Azure VM (GUI)](availability-group-manually-configure-tutorial.md)   
* [Configurar uma ligação VNet a VNet com o Azure Resource Manager e o PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>Verificar a versão PowerShell

Os exemplos deste artigo são testados utilizando a versão 5.4.1 do módulo Azure PowerShell.

Verifique se o seu módulo PowerShell é 5.4.1 ou mais tarde.

Consulte [a instalação do módulo Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="configure-the-windows-firewall"></a>Configure a Firewall do Windows

Configure o Firewall do Windows para permitir o acesso ao SQL Server. As regras de firewall permitem ligações TCP às portas utilizadas pela instância SQL Server e pela sonda do ouvinte. Para obter instruções detalhadas, consulte [configurar uma firewall do Windows para acesso ao motor de base de dados](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access#Anchor_1). Crie uma regra de entrada para a porta SQL Server e para a porta da sonda.

Se estiver a restringir o acesso a um Grupo de Segurança da Rede Azure, certifique-se de que as regras de licenciamento incluem os endereços IP do servidor VM do backend SQL e os endereços IP flutuantes do balançador de carga para o ouvinte AG e o endereço IP do núcleo do cluster, se aplicável.

## <a name="determine-the-load-balancer-sku-required"></a>Determinar o equilibrador de carga que o SKU necessitava

[O equilibrador de carga Azure](../../../load-balancer/load-balancer-overview.md) está disponível em dois SKUs: Basic & Standard. Recomenda-se o equilibrador de carga padrão. Se as máquinas virtuais estiverem num conjunto de disponibilidade, é permitido o balanceador de carga básico. Se as máquinas virtuais estiverem numa zona de disponibilidade, é necessário um balanceador de carga padrão. O balanceador de carga padrão requer que todos os endereços IP VM utilizem endereços IP padrão.

O [modelo](./availability-group-quickstart-template-configure.md) atual da Microsoft para um grupo de disponibilidade utiliza um equilibrador de carga básico com endereços IP básicos.

   > [!NOTE]
   > Terá de configurar um [ponto final de serviço](../../../storage/common/storage-network-security.md?toc=%252fazure%252fvirtual-network%252ftoc.json#grant-access-from-a-virtual-network) se utilizar um equilibrador de carga padrão e um armazenamento Azure para a testemunha em nuvem. 
   > 

Os exemplos deste artigo especificam um balanceador de carga padrão. Nos exemplos, o script inclui `-sku Standard` .

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

Para criar um equilibrador de carga básico, `-sku Standard` retire da linha que cria o equilibrador de carga. Por exemplo:

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Script exemplo: Criar um equilibrador de carga interno com PowerShell

> [!NOTE]
> Se criou o seu grupo de disponibilidade com o [modelo Microsoft,](./availability-group-quickstart-template-configure.md)o balançador de carga interno já foi criado.

O seguinte script PowerShell cria um equilibrador de carga interno, configura as regras de equilíbrio de carga e define um endereço IP para o equilibrador de carga. Para executar o script, abra o Windows PowerShell ISE e, em seguida, cole o script no painel script. Use `Connect-AzAccount` para iniciar sessão no PowerShell. Se tiver várias subscrições do Azure, utilize `Select-AzSubscription` para definir a subscrição. 

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

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a><a name="Add-IP"></a> Script de exemplo: Adicione um endereço IP a um equilibrador de carga existente com PowerShell

Para utilizar mais de um grupo de disponibilidade, adicione um endereço IP adicional ao balançador de carga. Cada endereço IP requer a sua própria regra de equilíbrio de carga, porta de sonda e porta frontal.

A porta frontal é a porta que as aplicações utilizam para ligar à instância do SQL Server. Endereços IP para diferentes grupos de disponibilidade podem usar a mesma porta frontal.

> [!NOTE]
> Para grupos de disponibilidade do SQL Server, cada endereço IP requer uma porta de sonda específica. Por exemplo, se um endereço IP num balanceador de carga utilizar a porta de sonda 59999, nenhum outro endereço IP nesse balanceador de carga pode utilizar a porta de sonda 59999.

* Para obter informações sobre os limites do balançador de carga, consulte **IP frontal privado por balançador** de carga em [Limites de Rede - Gestor de Recursos Azure](../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Para obter informações sobre os limites do grupo de disponibilidade, consulte [Restrições (Grupos de Disponibilidade)](/sql/database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability#RestrictionsAG).

O seguinte script adiciona um novo endereço IP a um equilibrador de carga existente. O ILB utiliza a porta de escuta para a porta frontal de equilíbrio de carga. Esta porta pode ser a porta que o SQL Server está a ouvir. Para casos predefinidos do SQL Server, a porta é 1433. A regra de equilíbrio de carga para um grupo de disponibilidade requer um IP flutuante (retorno do servidor direto) de modo que a porta traseira é a mesma que a porta frontal. Atualize as variáveis para o seu ambiente. 

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

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>Definir a porta do ouvinte no SQL Server Management Studio

1. Lance o SQL Server Management Studio e ligue-se à réplica primária.

1. Navigate to **AlwaysOn High Availability**  >  **Availability Groups**  >  **Availability Group Listeners** . 

1. Deve agora ver o nome do ouvinte que criou no Failover Cluster Manager. Clique com o botão direito no nome do ouvinte e selecione **Propriedades** .

1. Na caixa **de porta,** especifique o número da porta para o ouvinte do grupo de disponibilidade utilizando o $EndpointPort que usou anteriormente (1433 foi o predefinido), então selecione **OK** .

## <a name="test-the-connection-to-the-listener"></a>Teste a ligação ao ouvinte

Para testar a ligação:

1. Utilize o Protocolo de Ambiente de Trabalho Remoto (RDP) para ligar a um SqL Server que está na mesma rede virtual, mas que não possui a réplica. Pode ser o outro SQL Server no cluster.

1. Utilize **serviços sqlcmd** para testar a ligação. Por exemplo, o seguinte script estabelece uma ligação **sqlcmd** à réplica primária através do ouvinte com autenticação do Windows:
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    Se o ouvinte estiver a utilizar uma porta diferente da porta padrão (1433), especifique a porta na cadeia de ligação. Por exemplo, o seguinte comando sqlcmd liga-se a um ouvinte na porta 1435: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

A ligação SQLCMD liga-se automaticamente a qualquer instância do SQL Server que acolhe a réplica primária. 

> [!NOTE]
> Certifique-se de que a porta especificada está aberta na firewall de ambos os Servidores SQL. Ambos os servidores requerem uma regra de entrada para a porta TCP que utiliza. Para obter mais informações, consulte [adicionar ou editar a regra de Firewall](/previous-versions/orphan-topics/ws.11/cc753558(v=ws.11)). 
> 

## <a name="guidelines-and-limitations"></a>Diretrizes e limitações

Note as seguintes diretrizes sobre o ouvinte do grupo de disponibilidade em Azure utilizando o equilibrador interno de carga:

* Com um equilibrador de carga interno, só acede ao ouvinte dentro da mesma rede virtual.

* Se estiver a restringir o acesso a um Grupo de Segurança da Rede Azure, certifique-se de que as regras de licenciamento incluem:
  - Os endereços IP do servidor sql backend
  - Os endereços IP flutuantes do balançador de carga para o ouvinte AG
  - O endereço IP do núcleo do cluster, se aplicável.

* Crie um ponto final de serviço quando utilizar um equilibrador de carga padrão com o Azure Storage para a testemunha em nuvem. Para obter mais informações, consulte [o acesso do Grant a partir de uma rede virtual.](../../../storage/common/storage-network-security.md?toc=%252fazure%252fvirtual-network%252ftoc.json#grant-access-from-a-virtual-network)

## <a name="for-more-information"></a>Para obter mais informações:

Para obter mais informações, consulte [o grupo Configure Always On availability em Azure VM manualmente](availability-group-manually-configure-tutorial.md).

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell

Utilize os seguintes cmdlets PowerShell para criar um equilibrador de carga interno para máquinas virtuais Azure.

* [O Novo AzLoadBalancer](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancer) cria um equilibrador de carga. 
* [New-AzLoadBalancerFrontendIpConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerFrontendIpConfig) cria uma configuração IP frontal para um equilibrador de carga. 
* [New-AzLoadBalancerRuleConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerRuleConfig) cria uma configuração de regra para um equilibrador de carga. 
* [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerBackendAddressPoolConfig) cria uma configuração de pool de endereço de backend para um equilibrador de carga. 
* [New-AzLoadBalancerProbeConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerProbeConfig) cria uma configuração de sonda para um equilibrador de carga.
* [Remove-AzLoadBalancer](/powershell/module/Azurerm.Network/Remove-AzureRmLoadBalancer) remove um equilibrador de carga de um grupo de recursos Azure.