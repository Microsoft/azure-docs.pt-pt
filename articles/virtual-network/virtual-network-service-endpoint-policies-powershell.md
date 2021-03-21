---
title: Restringir a exfiltração de dados ao Azure Storage - Azure PowerShell
description: Neste artigo, aprende-se a limitar e restringir a exfiltração de dados de rede virtual aos recursos de armazenamento de Azure com políticas de ponto final de serviço de rede virtual utilizando o Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
manager: narayan
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: d8e300c9be8f3e59dc9443bf1f1806e4228992ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790398"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-azure-powershell"></a>Gerir a exfiltração de dados para contas de armazenamento de Azure com políticas de ponto final de serviço de rede virtual utilizando a Azure PowerShell

As políticas de ponto final do serviço de rede virtual permitem-lhe aplicar o controlo de acesso nas contas de Armazenamento Azure a partir de uma rede virtual sobre pontos finais de serviço. Esta é uma chave para garantir as suas cargas de trabalho, gerir as contas de armazenamento permitidas e onde a exfiltração de dados é permitida.
Neste artigo, vai aprender a:

* Crie uma rede virtual
* Adicione uma sub-rede e ative o ponto final de serviço para o Armazenamento Azure.
* Crie duas contas de Armazenamento Azure e permita o acesso à rede a partir da sub-rede criada acima.
* Crie uma política de ponto final de serviço que permita o acesso apenas a uma das contas de armazenamento.
* Insaem uma máquina virtual (VM) na sub-rede.
* Confirme o acesso à conta de armazenamento permitida a partir da sub-rede.
* Confirme que o acesso é negado à conta de armazenamento não autorizada a partir da sub-rede.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 1.0.0 ou mais tarde do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Antes de criar uma rede virtual, tem de criar um grupo de recursos para a rede virtual, e todos os outros recursos criados neste artigo. Criar um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup:* 

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS
```

Criar uma rede virtual com [a New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo a seguir cria uma rede virtual chamada *myVirtualNetwork* com o prefixo de endereço *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

## <a name="enable-a-service-endpoint"></a>Ativar um ponto final de serviço

Criar uma sub-rede na rede virtual. Neste exemplo, uma sub-rede chamada *Private* é criada com um ponto final de serviço para *Microsoft.Storage*: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-the-subnet"></a>Restringir o acesso à rede para a sub-rede

Criar regras de segurança do grupo de segurança de rede com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). A seguinte regra permite o acesso de saída aos endereços IP públicos atribuídos ao serviço de Armazenamento Azure: 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

A seguinte regra nega o acesso a todos os endereços IP públicos. A regra anterior substitui esta regra, devido à sua maior prioridade, que permite o acesso aos endereços IP públicos do Azure Storage.

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

A seguinte regra permite que o tráfego de ambiente de trabalho remoto (RDP) entre na sub-rede a partir de qualquer lugar. As ligações remotas de ambiente de trabalho são permitidas na sub-rede, para que possa confirmar o acesso à rede a um recurso num passo posterior.

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

Criar um grupo de segurança de rede com [o New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). O exemplo a seguir cria um grupo de segurança de rede chamado *myNsgPrivate*.

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

Associe o grupo de segurança da rede à sub-rede *Privada* com [a Set-AzVirtualNetSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) e, em seguida, escreva a configuração da sub-rede para a rede virtual. O exemplo a seguir associa o grupo de segurança da rede *myNsgPrivate* à sub-rede *Privada:*

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Restringir o acesso à rede às contas de Armazenamento Azure

Os passos necessários para restringir o acesso de rede a recursos criados através de serviços do Azure ativados para pontos finais de serviço varia de serviço para serviço. Veja a documentação relativa aos serviços individuais para obter os passos específicos dos mesmos. O restante deste artigo inclui medidas para restringir o acesso à rede para uma conta de Armazenamento Azure, como exemplo.

### <a name="create-two-storage-accounts"></a>Criar duas contas de armazenamento

Crie uma conta de armazenamento Azure com [o New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

```azurepowershell-interactive
$storageAcctName1 = 'allowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName1 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Após a criação da conta de armazenamento, recupere a chave para a conta de armazenamento numa variável com [o Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey):

```azurepowershell-interactive
$storageAcctKey1 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName1).Value[0]
```

A chave é usada para criar uma partilha de ficheiros num passo posterior. Introduza `$storageAcctKey` e note o valor, pois também terá de inseri-lo manualmente num passo posterior quando mapear a partilha de ficheiros para uma unidade num VM.

Repita agora os passos acima para criar uma segunda conta de armazenamento.

```azurepowershell-interactive
$storageAcctName2 = 'notallowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName2 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Também recupere a chave da conta de armazenamento desta conta para usar mais tarde para criar uma partilha de ficheiros.

```azurepowershell-interactive
$storageAcctKey2 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName2).Value[0]
```

### <a name="create-a-file-share-in-each-of-the-storage-account"></a>Criar uma partilha de ficheiros em cada uma das contas de armazenamento

Crie um contexto para a sua conta de armazenamento e chave com [o New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext). O contexto encapsula o nome da conta de armazenamento e a chave da conta:

```azurepowershell-interactive
$storageContext1 = New-AzStorageContext $storageAcctName1 $storageAcctKey1

$storageContext2 = New-AzStorageContext $storageAcctName2 $storageAcctKey2
```

Crie uma partilha de ficheiros com [o New-AzStorageShare:](/powershell/module/az.storage/new-azstorageshare)

```azurepowershell-interactive
$share1 = New-AzStorageShare my-file-share -Context $storageContext1

$share2 = New-AzStorageShare my-file-share -Context $storageContext2
```

### <a name="deny-all-network-access-to-a-storage-accounts"></a>Negar todo o acesso à rede a uma conta de armazenamento

Por predefinição, as contas de Armazenamento aceitam ligações de rede de clientes em qualquer rede. Para limitar o acesso a redes selecionadas, altere a ação predefinida para *Negar* com [Actualização-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset). Assim que o acesso de rede for negado, a conta de armazenamento não será acessível a partir de nenhuma rede.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -DefaultAction Deny

Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -DefaultAction Deny
```

### <a name="enable-network-access-only-from-the-vnet-subnet"></a>Permitir o acesso à rede apenas a partir da sub-rede VNet

Recupere a rede virtual criada com [a Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) e, em seguida, recupere o objeto de sub-rede privada numa variável com [Get-AzVirtualNetworkSubnetConfig:](/powershell/module/az.network/get-azvirtualnetworksubnetconfig)

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Name myVirtualNetwork `
  | Get-AzVirtualNetworkSubnetConfig -Name Private
```

Permitir o acesso à rede à conta de armazenamento a partir da sub-rede *Privada* com [Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -VirtualNetworkResourceId $privateSubnet.Id

Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Aplicar a política para permitir o acesso a conta de armazenamento válido

Para garantir que os utilizadores da rede virtual só podem aceder às contas de Armazenamento Azure que são seguras e permitidas, pode criar uma política de ponto final de Serviço com a lista de contas de armazenamento permitidas na definição. Esta política é então aplicada à sub-rede de rede virtual que está ligada ao armazenamento através de pontos finais de serviço.

### <a name="create-a-service-endpoint-policy"></a>Criar uma política de ponto final de serviço

Esta secção cria a definição de política com a lista de recursos permitidos para acesso sobre o ponto final do serviço

Recupere o ID do recurso para a primeira conta de armazenamento (permitida) 

```azurepowershell-interactive
$resourceId = (Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name $storageAcctName1).id
```

Crie a definição de política para permitir o recurso acima

```azurepowershell-interactive
$policyDefinition = New-AzServiceEndpointPolicyDefinition -Name mypolicydefinition `
  -Description "Service Endpoint Policy Definition" `
  -Service "Microsoft.Storage" `
  -ServiceResource $resourceId
```

Crie a política de ponto final de serviço utilizando a definição de política acima criada

```azurepowershell-interactive
$sepolicy = New-AzServiceEndpointPolicy -ResourceGroupName myresourcegroup `
  -Name mysepolicy -Location EastUS
  -ServiceEndpointPolicyDefinition $policyDefinition
```

### <a name="associate-the-service-endpoint-policy-to-the-virtual-network-subnet"></a>Associar a política de ponto final de serviço à sub-rede de rede virtual

Depois de criar a política de ponto final de serviço, irá associá-la à sub-rede-alvo com a configuração do ponto final de serviço para o Azure Storage.

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -NetworkSecurityGroup $nsg `
  -ServiceEndpoint Microsoft.Storage `
  -ServiceEndpointPolicy $sepolicy

$virtualNetwork | Set-AzVirtualNetwork
```
## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Validar a restrição de acesso às contas de Armazenamento Azure

### <a name="deploy-the-virtual-machine"></a>Implementar a máquina virtual

Para testar o acesso à rede a uma conta de armazenamento, implante um VM na sub-rede.

Crie uma máquina virtual na sub-rede *Privada* com [New-AzVM](/powershell/module/az.compute/new-azvm). Ao executar o comando seguinte, ser-lhe-ão pedidas as credenciais. Os valores que introduzir são configurados, como o nome de utilizador e a palavra-passe para a VM. A opção `-AsJob` cria a VM em segundo plano, para que possa prosseguir para o passo seguinte.

```azurepowershell-interactive
New-AzVm -ResourceGroupName myresourcegroup `
  -Location "East US" `
  -VirtualNetworkName myVirtualNetwork `
  -SubnetName Private `
  -Name "myVMPrivate" -AsJob
```

A saída semelhante à saída de exemplo a seguir é devolvida:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="confirm-access-to-the-allowed-storage-account"></a>Confirme o acesso à conta de armazenamento *permitida*

Utilize [o Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para devolver o endereço IP público de um VM. O exemplo a seguir devolve o endereço IP público do *myVmPrivate* VM:

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

No *myVmPrivate* VM, mapear a partilha de ficheiros Azure da conta de armazenamento permitida para conduzir Z usando PowerShell. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList ("Azure\allowedaccount"), $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\allowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

O PowerShell devolve resultados semelhantes à saída de exemplo seguinte:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\allowedaccount.file.core.windows.net\my-f...
```

A partilha de ficheiros do Azure mapeada com êxito para a unidade Z.

Feche a sessão de ambiente de trabalho remoto para a VM *myVmPrivate*.

### <a name="confirm-access-is-denied-to-non-allowed-storage-account"></a>Confirmar o acesso é negado a conta de armazenamento *não permitida*

No mesmo *myVmPrivate* VM, tente mapear a partilha de ficheiros Azure para dirigir X. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\notallowedaccount", $acctKey
New-PSDrive -Name X -PSProvider FileSystem -Root "\\notallowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

O acesso à partilha é negado e recebes um `New-PSDrive : Access is denied` erro. O acesso é negado porque a conta de conta *de* armazenamento não está na lista de recursos permitidos na política de ponto final de serviço. 

Feche a sessão de ambiente de trabalho remoto para a VM *myVmPublic*.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, pode utilizar [o Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que contém:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aplicou uma política de ponto final de serviço sobre um ponto final de serviço de rede virtual Azure ao Azure Storage. Criou contas de Armazenamento Azure e acesso limitado à rede a determinadas contas de armazenamento (e assim negou outras) a partir de uma sub-rede de rede virtual. Para saber mais sobre as políticas de ponto final de serviço, consulte [a visão geral das políticas de pontos finais](virtual-network-service-endpoint-policies-overview.md)do Serviço .
