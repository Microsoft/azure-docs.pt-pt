---
title: Restringir o acesso à rede aos recursos paaS - Azure PowerShell
description: Neste artigo, aprende-se a limitar e restringir o acesso à rede aos recursos do Azure, como o Azure Storage e o Azure SQL Database, com pontos finais de serviço de rede virtual utilizando o Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 1d0cf65bb39dbda2b7451c50629ff8949c5507cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185545"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>Restringir o acesso da rede aos recursos PaaS com pontos finais de serviço de rede virtual usando powerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os pontos finais de serviço de rede virtual permitem-lhe limitar o acesso de rede a alguns recursos de serviços do Azure a uma sub-rede de rede virtual. Também pode remover o acesso à Internet aos recursos. Os pontos finais de serviço proporcionam uma ligação direta a partir da sua rede virtual a serviços do Azure suportados, o que lhe permite utilizar o espaço de endereços privados da sua rede virtual para aceder aos serviços do Azure. O tráfego destinado aos recursos do Azure através de pontos finais de serviço permanece sempre na rede backbone do Microsoft Azure. Neste artigo, vai aprender a:

* Criar uma rede virtual com uma sub-rede
* Adicionar uma sub-rede e ativar um ponto final de serviço
* Criar um recurso do Azure e permitir o acesso de rede ao mesmo apenas a partir de uma sub-rede
* Implementar uma máquina virtual (VM) em cada sub-rede
* Confirmar o acesso a um recurso a partir de uma sub-rede
* Confirmar que o acesso é negado a um recurso a partir de uma sub-rede e da Internet

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 1.0.0 ou posterior do módulo PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Antes de criar uma rede virtual, tem de criar um grupo de recursos para a rede virtual, e todos os outros recursos criados neste artigo. Crie um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup:* 

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Criar uma rede virtual com [new-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo seguinte cria uma rede virtual chamada *myVirtualNetwork* com o prefixo de endereço *10.0.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Crie uma configuração de sub-rede com [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). O exemplo seguinte cria uma configuração de sub-rede para uma sub-rede chamada *Public:*

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Crie a subrede na rede virtual escrevendo a configuração da sub-rede para a rede virtual com [set-AzVirtualNetwork:](/powershell/module/az.network/Set-azVirtualNetwork)

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="enable-a-service-endpoint"></a>Ativar um ponto final de serviço

Só pode ativar pontos finais de serviço para serviços que suportam pontos finais de serviço. Consulte os serviços endpoint habilitados para o serviço disponíveis numa localização Azure com [O Get-AzVirtualNetworkAvailableEndpointService](/powershell/module/az.network/get-azvirtualnetworkavailableendpointservice). O exemplo seguinte devolve uma lista de serviços habilitados para serviços disponíveis na região *oriental.* A lista de serviços devolvidos crescerá ao longo do tempo à medida que mais serviços Azure se tornarem ponto final de serviço habilitados.

```azurepowershell-interactive
Get-AzVirtualNetworkAvailableEndpointService -Location eastus | Select Name
```

Crie uma subrede adicional na rede virtual. Neste exemplo, uma subnet chamada *Private* é criada com um ponto final de serviço para *microsoft.Storage*: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-a-subnet"></a>Restringir o acesso de rede a uma sub-rede

Crie regras de segurança do grupo de segurança da rede com [new-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). A seguinte regra permite o acesso de saída aos endereços IP públicos atribuídos ao serviço de Armazenamento Azure: 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

A seguinte regra nega o acesso a todos os endereços IP públicos. A regra anterior anula esta regra, devido à sua maior prioridade, que permite o acesso aos endereços IP públicos do Armazenamento Azure.

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

A seguinte regra permite o tráfego remoto de desktop (RDP) de entrada na subrede a partir de qualquer lugar. As ligações remotas para desktop são permitidas à sub-rede, para que possa confirmar o acesso da rede a um recurso num passo posterior.

```azurepowershell-interactive
$rule3 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

Criar um grupo de segurança de rede com o [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). O exemplo seguinte cria um grupo de segurança de rede chamado *myNsgPrivate*.

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

Associe o grupo de segurança da rede à subnet *a* [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) e, em seguida, escreva a configuração da sub-rede para a rede virtual. O exemplo seguinte associa o grupo de segurança da rede *myNsgPrivate* à subnet *privada:*

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-a-resource"></a>Restringir o acesso de rede a um recurso

Os passos necessários para restringir o acesso de rede a recursos criados através de serviços do Azure ativados para pontos finais de serviço varia de serviço para serviço. Veja a documentação relativa aos serviços individuais para obter os passos específicos dos mesmos. O restante deste artigo inclui medidas para restringir o acesso à rede de uma conta de Armazenamento Azure, como exemplo.

### <a name="create-a-storage-account"></a>Criar uma conta do Storage

Crie uma conta de armazenamento Azure com [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Substitua `<replace-with-your-unique-storage-account-name>` por um nome único em todas as localizações do Azure, entre 3-24 caracteres de comprimento, utilizando apenas números e letras minúsculas.

```azurepowershell-interactive
$storageAcctName = '<replace-with-your-unique-storage-account-name>'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Depois de criada a conta de armazenamento, recupere a chave para a conta de armazenamento numa variável com [Get-AzStorageAccountKey:](/powershell/module/az.storage/get-azstorageaccountkey)

```azurepowershell-interactive
$storageAcctKey = (Get-AzStorageAccountKey `
  -ResourceGroupName myResourceGroup `
  -AccountName $storageAcctName).Value[0]
```

A chave é usada para criar uma partilha de ficheiros num passo posterior. Introduza `$storageAcctKey` e note o valor, pois também terá de o introduzir manualmente num passo posterior quando mapear a partilha de ficheiros para uma unidade num VM.

### <a name="create-a-file-share-in-the-storage-account"></a>Criar uma partilha de ficheiros na conta de Armazenamento

Crie um contexto para a sua conta de armazenamento e chave com [o New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext). O contexto engloba o nome da conta de armazenamento e a chave da conta:

```azurepowershell-interactive
$storageContext = New-AzStorageContext $storageAcctName $storageAcctKey
```

Criar uma partilha de ficheiros com o [New-AzStorageShare:](/powershell/module/az.storage/new-azstorageshare)

$share = New-AzStorageShare my-file-share -Context $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>Negar todo o acesso à rede a uma conta de armazenamento

Por predefinição, as contas de Armazenamento aceitam ligações de rede de clientes em qualquer rede. Para limitar o acesso a redes selecionadas, altere a ação padrão para *Negar* com [Update-AzStorageAccountNetworkRuleSetSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset). Assim que o acesso de rede for negado, a conta de armazenamento não será acessível a partir de nenhuma rede.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Ativar o acesso de rede a partir de uma de sub-rede

Recupere a rede virtual criada com [a Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) e, em seguida, recupere o objeto de subnet privado numa variável com [Get-AzVirtualNetworkSubnetConfig:](/powershell/module/az.network/get-azvirtualnetworksubnetconfig)

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVirtualNetwork" `
  | Get-AzVirtualNetworkSubnetConfig `
  -Name "Private"
```

Permitir o acesso à rede à conta de armazenamento a partir da subnet *privada* com [Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Para testar o acesso de rede a uma conta de Armazenamento, implemente uma VM em cada sub-rede.

### <a name="create-the-first-virtual-machine"></a>Criar a primeira máquina virtual

Crie uma máquina virtual na subnet *pública* com [New-AzVM](/powershell/module/az.compute/new-azvm). Ao executar o comando seguinte, ser-lhe-ão pedidas as credenciais. Os valores que introduzir são configurados, como o nome de utilizador e a palavra-passe para a VM. A opção `-AsJob` cria a VM em segundo plano, para que possa prosseguir para o passo seguinte.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -Name "myVmPublic" `
    -AsJob
```

A saída semelhante à seguinte saída de exemplo é devolvida:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM     
```

### <a name="create-the-second-virtual-machine"></a>Criar a segunda máquina virtual

Criar uma máquina virtual na sub-rede *privada:*

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -Name "myVmPrivate"
```

Leva alguns minutos para azure criar o VM. Não continue até ao passo seguinte até que o Azure termine de criar o VM e desfaça a saída para a PowerShell.

## <a name="confirm-access-to-storage-account"></a>Confirmar o acesso à conta de Armazenamento

Utilize o [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para devolver o endereço IP público de um VM. O exemplo seguinte devolve o endereço IP público do *myVmPrivate* VM:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

No comando seguinte, substitua `<publicIpAddress>` pelo endereço IP público devolvido pelo comando anterior e, em seguida, introduza o seguinte comando:

```powershell
mstsc /v:<publicIpAddress>
```

É criado e transferido um ficheiro do Protocolo do Ambiente de Trabalho Remoto (.rdp) para o seu computador. Abra o ficheiro rdp transferido. Se lhe for pedido, selecione **Ligar**. Introduza o nome de utilizador e a palavra-passe que especificou ao criar a VM. Poderá ter de selecionar **Mais opções** e **Utilizar uma conta diferente** para especificar as credenciais que introduziu quando criou a VM. Selecione **OK**. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber o aviso, selecione **Sim** ou **Continuar** para prosseguir com a ligação.

Na VM *myVmPrivate*, mapeie a partilha de ficheiros do Azure para a unidade Z com o PowerShell. Antes de executar os comandos que se seguem, substitua `<storage-account-key>` e `<storage-account-name>` com valores fornecidos ou recuperados em Criar uma conta de [armazenamento](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

O PowerShell devolve resultados semelhantes à saída de exemplo seguinte:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
```

A partilha de ficheiros do Azure mapeada com êxito para a unidade Z.

Confirme que o VM não tem conectividade de saída com quaisquer outros endereços IP públicos:

```powershell
ping bing.com
```

Não vai receber respostas, porque o grupo de segurança de rede associado à sub-rede *Privada* não permite o acesso de saída a endereços IP públicos que não os atribuídos ao serviço Armazenamento do Azure.

Feche a sessão de ambiente de trabalho remoto para a VM *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Confirmar que o acesso à conta de Armazenamento é negado

Obtenha o endereço IP público do *myVmPublic* VM:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPublic `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

No comando seguinte, substitua `<publicIpAddress>` pelo endereço IP público devolvido pelo comando anterior e, em seguida, introduza o seguinte comando: 

```powershell
mstsc /v:<publicIpAddress>
```

No *myVmPublic* VM, tente mapear a partilha de ficheiros Azure para conduzir Z. Antes de executar os comandos que se seguem, substitua `<storage-account-key>` e `<storage-account-name>` com valores fornecidos ou recuperados em Criar uma conta de [armazenamento](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

O acesso à partilha é negado `New-PSDrive : Access is denied` e recebe um erro. O acesso é negado porque a VM *myVmPublic* é implementada na sub-rede *Pública*. A sub-rede *Pública* não tem um ponto final de serviço ativado para o Armazenamento do Azure e a conta de armazenamento só permite o acesso de rede a partir da sub-rede *Privada*, não da *Público*.

Feche a sessão de ambiente de trabalho remoto para a VM *myVmPublic*.

Do seu computador, tente visualizar as ações do ficheiro na conta de armazenamento com o seguinte comando:

```powershell-interactive
Get-AzStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

O acesso é negado e recebe um *Get-AzStorageFile : O servidor remoto devolveu um erro: (403) Proibido. Código de Estado HTTP: 403 - HTTP Error Message: Este pedido não está autorizado a executar este* erro de operação, porque o seu computador não está na sub-rede *privada* da rede virtual *MyVirtualNetwork.*

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que contém:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, ativou um ponto final de serviço para uma subnet de rede virtual. Aprendeu que os pontos finais de serviço podem ser ativados para recursos implementados com vários serviços do Azure. Criou uma conta de Armazenamento do Azure e limitou o acesso de rede à mesma apenas para os recursos dentro de uma sub-rede de uma rede virtual. Para saber mais sobre os pontos finais de serviço, veja [Descrição geral dos pontos finais de serviço](virtual-network-service-endpoints-overview.md) e [Manage subnets](virtual-network-manage-subnet.md) (Gerir sub-redes).

Se tiver várias redes virtuais na sua conta, poderá pretender ligar duas redes virtuais para que os recursos dentro de ambas possam comunicar entre si. Para saber como, consulte [Connect redes virtuais](tutorial-connect-virtual-networks-powershell.md).
