---
title: Criar um Azure Private Endpoint usando a Azure PowerShell Microsoft Docs
description: Saiba mais sobre a Azure Private Link
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 0c6fc36be101679cea3a770f311005f63c3f0d66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84737381"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Criar um ponto final privado usando a Azure PowerShell
Um Ponto Final Privado é o bloco de construção fundamental para a ligação privada em Azure. Permite que os recursos do Azure, como máquinas virtuais (VMs), comuniquem privadamente com recursos de ligação privada. 

Neste Quickstart, aprenderás a criar um VM numa Rede Virtual Azure, um servidor SQL lógico com um ponto final privado Azure utilizando o Azure PowerShell. Em seguida, pode aceder de forma segura à Base de Dados SQL a partir do VM.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de poder criar os seus recursos, tem de criar um grupo de recursos que acolhe a Rede Virtual e o ponto final privado com o [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *WestUS:*

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>Criar uma Rede Virtual
Nesta secção, cria-se uma rede virtual e uma sub-rede. Em seguida, associe a sub-rede à sua Rede Virtual.

### <a name="create-a-virtual-network"></a>Criar uma Rede Virtual

Crie uma rede Virtual para o seu ponto final privado com [a New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo a seguir cria uma Rede Virtual chamada *MyVirtualNetwork:*
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Adicionar uma Sub-rede

O Azure implementa recursos para uma sub-rede dentro de uma Rede Virtual, por isso é necessário criar uma sub-rede. Crie uma configuração de sub-rede chamada *mySubnet* com [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig). O exemplo a seguir cria uma sub-rede chamada *mySubnet* com a bandeira de política de rede de pontos finais privada definida para **Deficientes**.

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

> [!CAUTION]
> É fácil confundir o `PrivateEndpointNetworkPoliciesFlag` parâmetro com outra bandeira disponível, `PrivateLinkServiceNetworkPoliciesFlag` porque ambas são palavras longas e têm aparência semelhante.  Certifique-se de que está a usar o certo, `PrivateEndpointNetworkPoliciesFlag` .

### <a name="associate-the-subnet-to-the-virtual-network"></a>Associar a Sub-rede à Rede Virtual

Pode escrever a configuração da sub-rede para a Rede Virtual com [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Este comando cria a sub-rede:

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Criar um VM na Rede Virtual com [New-AzVM](/powershell/module/az.compute/new-azvm). Quando se dirige o próximo comando, é-nos solicitado credenciais. Introduza um nome de utilizador e senha para o VM:

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

## <a name="create-a-logical-sql-server"></a>Criar um servidor SQL lógico 

Crie um servidor SQL lógico utilizando o comando New-AzSqlServer. Lembre-se que o nome do seu servidor deve ser único em todo o Azure, por isso substitua o valor do espaço reservado nos parênteses pelo seu próprio valor único:

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

Ponto final privado para o servidor na sua Rede Virtual com [New-AzPrivateLinkConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection): 

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
  -Subnet  $subnet `
  -PrivateLinkServiceConnection $privateEndpointConnection
``` 

## <a name="configure-the-private-dns-zone"></a>Configure a Zona Privada de DNS 
Crie uma Zona Privada de DNS para domínio SQL Database, crie uma ligação de associação com a Rede Virtual e crie um Grupo de Zonas DNS para associar o ponto final privado à Zona Privada de DNS.

```azurepowershell

$zone = New-AzPrivateDnsZone -ResourceGroupName "myResourceGroup" `
  -Name "privatelink.database.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName "myResourceGroup" `
  -ZoneName "privatelink.database.windows.net"`
  -Name "mylink" `
  -VirtualNetworkId $virtualNetwork.Id  

$config = New-AzPrivateDnsZoneConfig -Name "privatelink.database.windows.net" -PrivateDnsZoneId $zone.ResourceId

$privateDnsZoneGroup = New-AzPrivateDnsZoneGroup -ResourceGroupName "myResourceGroup" `
 -PrivateEndpointName "myPrivateEndpoint" -name "MyZoneGroup" -PrivateDnsZoneConfig $config
``` 
  
## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Utilize [o Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) para devolver o endereço IP público de um VM. Este exemplo devolve o endereço IP público do *myVM* VM:

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
Abra um pedido de comando no seu computador local. Executar o comando mstsc. <publicIpAddress>Substitua-o pelo endereço IP público devolvido do último passo: 


> [!NOTE]
> Se estiver a executar estes comandos a partir de uma solicitação PowerShell no seu computador local, e estiver a utilizar a versão 1.0 ou posterior do módulo Az PowerShell, pode continuar nessa interface.
```
mstsc /v:<publicIpAddress>
```

1. Se lhe for pedido, selecione **Ligar**. 
2. Introduza o nome de utilizador e a palavra-passe que especificou ao criar a VM.
  > [!NOTE]
  > Poderá ter de selecionar Mais opções > Utilizar uma conta diferente, para especificar as credenciais que introduziu quando criou o VM. 
  
3. Selecione **OK**. 
4. Pode receber um aviso de certificado. Se o fizer, selecione **Sim** ou **Continue**. 

## <a name="access-sql-database-privately-from-the-vm"></a>Acesso SQL Base de Dados privada a partir do VM

1. No Ambiente de Trabalho Remoto de myVM, abra o PowerShell.
2. Introduza `nslookup myserver.database.windows.net`. Lembre-se de substituir `myserver` pelo nome do servidor SQL.

    Irá receber uma mensagem semelhante a esta:
    
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
    
3. Instale [o SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15).
4. Em **Ligar ao servidor,** insira ou selecione estas informações:

    | Definição | Valor |
    | --- | --- |
    | Tipo de servidor | Motor de Base de Dados |
    | Nome do servidor | myserver.database.windows.net |
    | Nome de utilizador | Introduza o nome de utilizador fornecido durante a criação |
    | Palavra-passe | Introduza a senha fornecida durante a criação |
    | Lembre-se da palavra-passe | Sim |
    
5. Selecione **Ligar**.
6. Procure bases **de dados** a partir do menu esquerdo. 
7. (Opcionalmente) Criar ou consultar informações a partir da minha base de dados.
8. Feche a ligação remota do ambiente de trabalho ao *myVM*. 

## <a name="clean-up-resources"></a>Limpar os recursos 
Quando terminar de usar o ponto final privado, a Base de Dados SQL e o VM, utilize o [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que tem:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o Azure Private Link](private-link-overview.md)
