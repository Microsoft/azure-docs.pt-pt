---
title: Criar um ponto de extremidade privado do Azure usando Azure PowerShell | Microsoft Docs
description: Saiba mais sobre o link privado do Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 60032677594537f1e7791b7108eebd5d4cfad5b4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430350"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Criar um ponto de extremidade privado usando Azure PowerShell
Um ponto de extremidade privado é o bloco de construção fundamental para o link privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem de forma privada com recursos de link privado. 

Neste guia de início rápido, você aprenderá a criar uma VM em uma rede virtual do Azure, um servidor de banco de dados SQL com um ponto de extremidade privado do Azure usando Azure PowerShell. Em seguida, você pode acessar com segurança o servidor do banco de dados SQL da VM.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de poder criar seus recursos, você deve criar um grupo de recursos que hospede a rede virtual e o ponto de extremidade privado com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos chamado *MyResource* Group no local *westus* :

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>Criar uma Rede Virtual
Nesta seção, você criará uma rede virtual e uma sub-rede. Em seguida, associe a sub-rede à sua rede virtual.

### <a name="create-a-virtual-network"></a>Criar uma Rede Virtual

Crie uma rede virtual para seu ponto de extremidade privado com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo a seguir cria uma rede virtual chamada *MyVirtualNetwork*:
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

O Azure implanta recursos em uma sub-rede em uma rede virtual, portanto, você precisa criar uma sub-rede. Crie uma configuração de sub-rede chamada *mysubnet* com [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig). O exemplo a seguir cria uma sub-rede chamada *mysubnet* com o sinalizador de política de rede do ponto de extremidade privado definido como **desabilitado**.

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

> [!CAUTION]
> É fácil confundir o parâmetro `PrivateEndpointNetworkPoliciesFlag` com outro sinalizador disponível, `PrivateLinkServiceNetworkPoliciesFlag`, pois eles são palavras longas e têm aparência semelhante.  Verifique se você está usando o correto, `PrivateEndpointNetworkPoliciesFlag`.

### <a name="associate-the-subnet-to-the-virtual-network"></a>Associar a sub-rede à rede virtual

Você pode gravar a configuração de sub-rede na rede virtual com [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Este comando cria a sub-rede:

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma VM na rede virtual com [New-AzVM](/powershell/module/az.compute/new-azvm). Quando você executar o próximo comando, suas credenciais serão solicitadas. Insira um nome de usuário e uma senha para a VM:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "westcentralus" `
    -VirtualNetworkName "MyVirtualNetwork" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389 `
    -AsJob  
```

A opção `-AsJob` cria a VM em segundo plano. Você pode continuar para a próxima etapa.

Quando o Azure começar a criar a VM em segundo plano, você obterá algo assim:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

## <a name="create-a-sql-database-server"></a>Criar um servidor de banco de dados SQL 

Crie um servidor de banco de dados SQL usando o comando New-AzSqlServer. Lembre-se de que o nome do seu servidor do banco de dados SQL deve ser exclusivo no Azure, portanto, substitua o valor do espaço reservado entre colchetes com seu próprio valor exclusivo:

```azurepowershell-interactive
$adminSqlLogin = "SqlAdmin"
$password = "ChangeYourAdminPassword1"

$server = New-AzSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver" `
    -Location "WestCentralUS" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminSqlLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

New-AzSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver"`
    -DatabaseName "myda"`
    -RequestedServiceObjectiveName "S0" `
    -SampleName "AdventureWorksLT"
```

## <a name="create-a-private-endpoint"></a>Criar um Ponto Final Privado

Ponto de extremidade privado para o servidor do banco de dados SQL em sua rede virtual com [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection): 

```azurepowershell

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" `
  -PrivateLinkServiceId $server.ResourceId `
  -GroupId "sqlServer" 
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  "myResourceGroup" -Name "MyVirtualNetwork"  
 
$subnet = $virtualNetwork `
  | Select -ExpandProperty subnets `
  | Where-Object  {$_.Name -eq 'mysubnet'}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName "myResourceGroup" `
  -Name "myPrivateEndpoint" `
  -Location "westcentralus" `
  -Subnet  $subnet`
  -PrivateLinkServiceConnection $privateEndpointConnection
``` 

## <a name="configure-the-private-dns-zone"></a>Configurar a zona de DNS privado 
Crie uma zona DNS privada para o domínio do servidor do banco de dados SQL e crie um link de associação com a rede virtual: 

```azurepowershell

$zone = New-AzPrivateDnsZone -ResourceGroupName "myResourceGroup" `
  -Name "privatelink.database.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName "myResourceGroup" `
  -ZoneName "privatelink.database.windows.net"`
  -Name "mylink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.database.windows.net"  `
-ResourceGroupName "myResourceGroup" -Ttl 600 `
-PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
} 
} 
``` 
  
## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Use [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) para retornar o endereço IP público de uma VM. Este exemplo retorna o endereço IP público da VM *myVM* :

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
Abra um prompt de comando no computador local. Execute o comando mstsc. Substitua <publicIpAddress> pelo endereço IP público retornado da última etapa: 


> [!NOTE]
> Se você estiver executando esses comandos em um prompt do PowerShell no computador local e estiver usando o módulo AZ PowerShell versão 1,0 ou posterior, poderá continuar nessa interface.
```
mstsc /v:<publicIpAddress>
```

1. Se lhe for pedido, selecione **Ligar**. 
2. Introduza o nome de utilizador e a palavra-passe que especificou ao criar a VM.
  > [!NOTE]
  > Talvez seja necessário selecionar mais escolhas > usar uma conta diferente, para especificar as credenciais inseridas quando você criou a VM. 
  
3. Selecione **OK**. 
4. Você pode receber um aviso de certificado. Se você fizer isso, selecione **Sim** ou **continuar**. 

## <a name="access-sql-database-server-privately-from-the-vm"></a>Acessar o servidor do banco de dados SQL privadamente da VM

1. No Área de Trabalho Remota do myVM, abra o PowerShell.
2. Introduza `nslookup myserver.database.windows.net`. 

    Você receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Instalar SQL Server Management Studio
4. Em conectar ao servidor, insira ou selecione estas informações: configuração tipo de servidor de valor selecione Mecanismo de Banco de Dados.
      Nome do servidor selecione nome de usuário myserver.database.windows.net digite um nome de usuário fornecido durante a criação.
      Senha Insira uma senha fornecida durante a criação.
      Lembrar senha selecione Sim.
5. Selecione conectar.
6. Procurar bancos de dados no menu à esquerda. 
7. Opcionalmente Criar ou consultar informações de MyDatabase
8. Feche a conexão de área de trabalho remota para *myVM*. 

## <a name="clean-up-resources"></a>Limpar recursos 
Quando você terminar de usar o ponto de extremidade privado, o servidor de banco de dados SQL e a VM, use [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele tem:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre o [link privado do Azure](private-link-overview.md)
