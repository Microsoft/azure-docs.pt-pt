---
title: Implementar e configurar firewall Azure usando Azure PowerShell
description: Neste artigo, aprende-se a implantar e configurar a Azure Firewall utilizando o Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 08/28/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: c720d7c261421ade9dfce01f0b116123dcab1e55
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89071708"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-powershell"></a>Implementar e configurar firewall Azure usando Azure PowerShell

Controlar o acesso de rede de saída é uma parte importante de um plano de segurança de rede geral. Por exemplo, pode querer limitar o acesso a sites web. Ou, pode querer limitar os endereços IP de saída e portas que podem ser acedidos.

Uma forma de controlar o acesso de rede de saída a partir de uma sub-rede do Azure é com a Azure Firewall. Com a Azure Firewall, pode configurar:

* Regras da aplicação que definem nomes de domínio completamente qualificado (FQDNs) que podem ser acedidos a partir de uma sub-rede.
* Regras de rede que definem o endereço de origem, o protocolo, a porta de destino e o endereço de destino.

O tráfego de rede está sujeito às regras de firewall configuradas quando encaminha o tráfego de rede para a firewall como o gateway padrão de sub-rede.

Para este artigo, cria-se um VNet único simplificado com três sub-redes para fácil implementação. Para implantações de produção, [recomenda-se](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) um modelo de hub e spoke, onde a firewall está no seu próprio VNet. Os servidores de carga de trabalho estão em VNets espreitados na mesma região com uma ou mais sub-redes.

* **AzureFirewallSubnet** - a firewall está nesta sub-rede.
* **Workload-SN** - o servidor de carga de trabalho está nesta sub-rede. O tráfego de rede desta sub-rede passa pela firewall.
* **Jump-SN** - o servidor “de ligação” está nesta sub-rede. O servidor de ligação tem um endereço IP público ao qual pode ligar com o Ambiente de Trabalho Remoto. A partir daí, pode ligar (com outro Ambiente de Trabalho Remoto) ao servidor de carga de trabalho.

![Tutorial de infraestrutura de rede](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Neste artigo, vai aprender a:


* Configurar um ambiente de rede de teste
* Implementar uma firewall
* Criar uma rota predefinida
* Configure uma regra de aplicação para permitir o acesso a www.google.com
* Configurar uma regra de rede para permitir o acesso aos servidores DNS externos
* Testar a firewall

Se preferir, pode concluir este procedimento utilizando o [portal Azure](tutorial-firewall-deploy-portal.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Este procedimento requer que você execute o PowerShell localmente. Deve ter o módulo Azure PowerShell instalado. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](https://docs.microsoft.com/powershell/azure/install-Az-ps). Depois de verificar a versão do PowerShell, execute `Connect-AzAccount` para criar uma ligação ao Azure.

## <a name="set-up-the-network"></a>Configurar a rede

Em primeiro lugar, crie um grupo de recursos para conter os recursos necessários para implementar a firewall. Em seguida, crie uma VNet, sub-redes e servidores de teste.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O grupo de recursos contém todos os recursos para a implantação.

```azurepowershell
New-AzResourceGroup -Name Test-FW-RG -Location "East US"
```

### <a name="create-a-vnet"></a>Criar uma VNet

Esta rede virtual tem três sub-redes:

> [!NOTE]
> O tamanho da sub-rede AzureFirewallSubnet é /26. Para obter mais informações sobre o tamanho da sub-rede, consulte [a Azure Firewall FAQ](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name AzureFirewallSubnet -AddressPrefix 10.0.1.0/26
$Worksub = New-AzVirtualNetworkSubnetConfig -Name Workload-SN -AddressPrefix 10.0.2.0/24
$Jumpsub = New-AzVirtualNetworkSubnetConfig -Name Jump-SN -AddressPrefix 10.0.3.0/24
```
Agora, crie a rede virtual:

```azurepowershell
$testVnet = New-AzVirtualNetwork -Name Test-FW-VN -ResourceGroupName Test-FW-RG `
-Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $FWsub, $Worksub, $Jumpsub
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Agora, crie as máquinas virtuais de ligação e de carga de trabalho, e coloque-as nas sub-redes adequadas.
Quando lhe for pedido, escreva um nome de utilizador e palavra-passe para a máquina virtual.

Crie a máquina virtual Srv-Jump.

```azurepowershell
New-AzVm `
    -ResourceGroupName Test-FW-RG `
    -Name "Srv-Jump" `
    -Location "East US" `
    -VirtualNetworkName Test-FW-VN `
    -SubnetName Jump-SN `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

Crie uma máquina virtual de carga de trabalho sem endereço IP público.
Quando lhe for pedido, escreva um nome de utilizador e palavra-passe para a máquina virtual.

```azurepowershell
#Create the NIC
$NIC = New-AzNetworkInterface -Name Srv-work -ResourceGroupName Test-FW-RG `
 -Location "East US" -Subnetid $testVnet.Subnets[1].Id 

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName Srv-Work -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Srv-Work -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName Test-FW-RG -Location "East US" -VM $VirtualMachine -Verbose
```

## <a name="deploy-the-firewall"></a>Implementar a firewall

Agora, insi(implantado a firewall na rede virtual.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName Test-FW-RG `
  -Location "East US" -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name Test-FW01 -ResourceGroupName Test-FW-RG -Location "East US" -VirtualNetworkName Test-FW-VN -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP
```

Anote o endereço IP privado. Vai utilizá-lo mais tarde quando criar a rota predefinida.

## <a name="create-a-default-route"></a>Criar uma rota predefinida

Criar uma tabela, com propagação de rota BGP desativada

```azurepowershell
$routeTableDG = New-AzRouteTable `
  -Name Firewall-rt-table `
  -ResourceGroupName Test-FW-RG `
  -location "East US" `
  -DisableBgpRoutePropagation

#Create a route
 Add-AzRouteConfig `
  -Name "DG-Route" `
  -RouteTable $routeTableDG `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $testVnet `
  -Name Workload-SN `
  -AddressPrefix 10.0.2.0/24 `
  -RouteTable $routeTableDG | Set-AzVirtualNetwork
```

## <a name="configure-an-application-rule"></a>Configurar uma regra de aplicação

A regra da aplicação permite o acesso de saída a www.google.com.

```azurepowershell
$AppRule1 = New-AzFirewallApplicationRule -Name Allow-Google -SourceAddress 10.0.2.0/24 `
  -Protocol http, https -TargetFqdn www.google.com

$AppRuleCollection = New-AzFirewallApplicationRuleCollection -Name App-Coll01 `
  -Priority 200 -ActionType Allow -Rule $AppRule1

$Azfw.ApplicationRuleCollections.Add($AppRuleCollection)

Set-AzFirewall -AzureFirewall $Azfw
```

O Azure Firewall inclui uma coleção de regras incorporadas para os FQDNs de infraestrutura que são permitidos por predefinição. Estes FQDNs são específicos da plataforma e não podem ser utilizados para outros fins. Para obter mais informações, veja [FQDNs de Infraestrutura](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Configurar uma regra de rede

A regra da rede permite o acesso de saída a dois endereços IP na porta 53 (DNS).

```azurepowershell
$NetRule1 = New-AzFirewallNetworkRule -Name "Allow-DNS" -Protocol UDP -SourceAddress 10.0.2.0/24 `
   -DestinationAddress 209.244.0.3,209.244.0.4 -DestinationPort 53

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 200 `
   -Rule $NetRule1 -ActionType "Allow"

$Azfw.NetworkRuleCollections.Add($NetRuleCollection)

Set-AzFirewall -AzureFirewall $Azfw
```

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Alterar o endereço DNS primário e secundário para a interface de rede **Srv-Work**

Para efeitos de teste neste procedimento, configuure os endereços DNS primários e secundários do servidor. Isto não é um requisito geral da Azure Firewall.

```azurepowershell
$NIC.DnsSettings.DnsServers.Add("209.244.0.3")
$NIC.DnsSettings.DnsServers.Add("209.244.0.4")
$NIC | Set-AzNetworkInterface
```

## <a name="test-the-firewall"></a>Testar a firewall

Agora, teste a firewall para confirmar que funciona como esperado.

1. Note o endereço IP privado para a máquina virtual **Srv-Work:**

   ```
   $NIC.IpConfigurations.PrivateIpAddress
   ```

1. Ligue um ambiente de trabalho remoto à máquina virtual **Srv-Jump** e inscreva-se. A partir daí, abra uma ligação de ambiente de trabalho remoto ao endereço IP privado **Srv-Work** e inscreva-se.

3. No **SRV-Work,** abra uma janela PowerShell e execute os seguintes comandos:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Ambos os comandos devem responder, mostrando que as suas consultas de DNS estão a passar pela firewall.

1. Execute os seguintes comandos:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   Os `www.google.com` pedidos devem ter sucesso, e os `www.microsoft.com` pedidos devem falhar. Isto demonstra que as suas regras de firewall estão a funcionar como esperado.

Então agora verificaste que as regras da firewall estão a funcionar:

* Pode resolver nomes DNS com o servidor DNS externo configurado.
* Pode navegar para o único FQDN permitido, mas não para quaisquer outros.

## <a name="clean-up-resources"></a>Limpar os recursos

Pode manter os seus recursos de firewall para o próximo tutorial, ou se já não for necessário, eliminar o grupo de recursos **Test-FW-RG** para eliminar todos os recursos relacionados com firewall:

```azurepowershell
Remove-AzResourceGroup -Name Test-FW-RG
```

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: monitorizar registos do Azure Firewall](./tutorial-diagnostics.md)
