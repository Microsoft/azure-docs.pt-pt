---
title: Criar um Ponto Final Privado Azure utilizando o Azure PowerShell. Microsoft Docs
description: Saiba mais sobre o Link Privado Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 60032677594537f1e7791b7108eebd5d4cfad5b4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75430350"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Criar um ponto final privado usando o Azure PowerShell
Um Private Endpoint é o bloco de construção fundamental para ligação privada em Azure. Permite que os recursos azure, como as Máquinas Virtuais (VMs), comuniquem privadamente com recursos de ligação privada. 

Neste Quickstart, você aprenderá a criar um VM numa Rede Virtual Azure, um Servidor de Base de Dados SQL com um ponto final privado Azure usando o Azure PowerShell. Em seguida, pode aceder de forma segura ao Servidor de Base de Dados SQL a partir do VM.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de poder criar os seus recursos, tem de criar um grupo de recursos que acolhe a Rede Virtual e o ponto final privado com o [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* na localização *WestUS:*

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>Criar uma Rede Virtual
Nesta secção, cria-se uma rede virtual e uma sub-rede. Em seguida, associa a subnet à sua Rede Virtual.

### <a name="create-a-virtual-network"></a>Criar uma Rede Virtual

Crie uma rede Virtual para o seu ponto final privado com [new-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo seguinte cria uma Rede Virtual chamada *MyVirtualNetwork:*
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Adicione uma Subnet

O Azure descoloca recursos numa subrede dentro de uma Rede Virtual, pelo que precisa de criar uma subrede. Crie uma configuração de sub-rede chamada *mySubnet* com [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig). O exemplo seguinte cria uma subnet chamada *mySubnet* com a bandeira de política de rede de pontofinal privado definida para **deficientes**.

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

> [!CAUTION]
> É fácil confundir o `PrivateEndpointNetworkPoliciesFlag` parâmetro com outra bandeira `PrivateLinkServiceNetworkPoliciesFlag`disponível, porque ambas são palavras longas e têm aparência semelhante.  Certifique-se de que está `PrivateEndpointNetworkPoliciesFlag`a usar o certo, .

### <a name="associate-the-subnet-to-the-virtual-network"></a>Associar a Subnet à Rede Virtual

Pode escrever a configuração da sub-rede para a Rede Virtual com [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Este comando cria a sub-rede:

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie um VM na Rede Virtual com [New-AzVM](/powershell/module/az.compute/new-azvm). Quando diriges o próximo comando, és solicitado por credenciais. Introduza um nome de utilizador e uma palavra-passe para o VM:

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

A `-AsJob` opção cria o VM em segundo plano. Pode continuar até ao próximo passo.

Quando o Azure começar a criar o VM em segundo plano, terás algo assim de volta:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

## <a name="create-a-sql-database-server"></a>Criar um servidor de base de dados SQL 

Crie um Servidor de Base de Dados SQL utilizando o comando New-AzSqlServer. Lembre-se que o nome do seu servidor de base de dados SQL deve ser único em todo o Azure, por isso substitua o valor do espaço reservado em parênteses pelo seu próprio valor único:

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

Ponto final privado para o Servidor de Base de Dados SQL na sua Rede Virtual com [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection): 

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

## <a name="configure-the-private-dns-zone"></a>Configure a Zona Privada dNS 
Criar uma zona Privada de DNS para o domínio do Servidor de Base de Dados SQL e criar uma ligação de associação com a rede virtual: 

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

Utilize o [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) para devolver o endereço IP público de um VM. Este exemplo devolve o endereço IP público do *myVM* VM:

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
Abra um pedido de comando no seu computador local. Comande o comando do MSTSC. Substitua <publicIpAddress> pelo endereço IP público devolvido do último passo: 


> [!NOTE]
> Se tiver estado a executar estes comandos a partir de um pedido powerShell no seu computador local, e estiver a utilizar a versão 1.0 ou mais posterior do módulo Az PowerShell, pode continuar nessa interface.
```
mstsc /v:<publicIpAddress>
```

1. Se lhe for pedido, selecione **Ligar**. 
2. Introduza o nome de utilizador e a palavra-passe que especificou ao criar a VM.
  > [!NOTE]
  > Poderá ter de selecionar Mais opções > Utilize uma conta diferente, para especificar as credenciais que inseriu quando criou o VM. 
  
3. Selecione **OK**. 
4. Pode receber um aviso de certificado. Se o fizer, selecione **Sim** ou **Continue**. 

## <a name="access-sql-database-server-privately-from-the-vm"></a>Aceda ao Servidor de Base de Dados SQL em privado a partir do VM

1. No Ambiente de Trabalho Remoto do myVM, abra a PowerShell.
2. Introduza `nslookup myserver.database.windows.net`. 

    Receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Instale o Estúdio de Gestão de Servidores SQL
4. No Connect ao servidor, introduza ou selecione estas informações: Configurar o tipo de servidor de valor Selecione o motor base de dados.
      Nome do servidor Selecione myserver.database.windows.net nome de utilizador Introduza um nome de utilizador fornecido durante a criação.
      Password Introduza uma palavra-passe fornecida durante a criação.
      Lembre-se de palavra-passe Selecione Sim.
5. Selecione Ligar.
6. Consulte as Bases de Dados do menu esquerdo. 
7. (Opcionalmente) Criar ou consultar informações a partir da minha base de dados
8. Feche a ligação remota de ambiente de trabalho ao *myVM*. 

## <a name="clean-up-resources"></a>Limpar recursos 
Quando terminar usando o ponto final privado, o servidor de base de dados SQL e o VM, utilize [o Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que tem:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o Link Privado Azure](private-link-overview.md)
