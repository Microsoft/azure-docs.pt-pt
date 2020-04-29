---
title: Restringir a exfiltração de dados ao Armazenamento Azure - Azure PowerShell
description: Neste artigo, aprende-se a limitar e restringir a exfiltração de dados de rede virtual aos recursos do Azure Storage com políticas de ponto final de serviço de rede virtual utilizando o Azure PowerShell.
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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: 673431e2ddfc9a641bb1c640891daac79350cb3a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78253029"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-azure-powershell"></a>Gerir a exfiltração de dados para contas de Armazenamento Azure com políticas de endpoint de serviço de rede virtual usando o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

As políticas de endpoint do serviço de rede virtual permitem-lhe aplicar o controlo de acesso nas contas do Armazenamento Do Azure a partir de uma rede virtual sobre pontos finais de serviço. Esta é uma chave para garantir as suas cargas de trabalho, gerir quais as contas de armazenamento permitidas e onde a exfiltração de dados é permitida.
Neste artigo, vai aprender a:

* Crie uma rede virtual
* Adicione uma sub-rede e ative o ponto final do serviço para o Armazenamento Azure.
* Crie duas contas de Armazenamento Azure e permita o acesso à rede a partir da subnet acima criada.
* Crie uma política de ponto final de serviço para permitir o acesso apenas a uma das contas de armazenamento.
* Desloque uma máquina virtual (VM) para a sub-rede.
* Confirme o acesso à conta de armazenamento permitida a partir da subnet.
* Confirme que o acesso é negado à conta de armazenamento não permitida a partir da subnet.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 1.0.0 ou posterior do módulo PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Antes de criar uma rede virtual, tem de criar um grupo de recursos para a rede virtual, e todos os outros recursos criados neste artigo. Crie um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup:* 

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS
```

Criar uma rede virtual com [new-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo seguinte cria uma rede virtual chamada *myVirtualNetwork* com o prefixo de endereço *10.0.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

## <a name="enable-a-service-endpoint"></a>Ativar um ponto final de serviço

Crie uma sub-rede na rede virtual. Neste exemplo, uma subnet chamada *Private* é criada com um ponto final de serviço para *microsoft.Storage*: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-the-subnet"></a>Restringir o acesso à rede para a subnet

Crie regras de segurança do grupo de segurança da rede com [new-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). A seguinte regra permite o acesso de saída aos endereços IP públicos atribuídos ao serviço de Armazenamento Azure: 

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

A seguinte regra nega o acesso a todos os endereços IP públicos. A regra anterior anula esta regra, devido à sua maior prioridade, que permite o acesso aos endereços IP públicos do Armazenamento Azure.

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
  -AddressPrefix 10.0.0.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Restringir o acesso à rede às contas de Armazenamento Azure

Os passos necessários para restringir o acesso de rede a recursos criados através de serviços do Azure ativados para pontos finais de serviço varia de serviço para serviço. Veja a documentação relativa aos serviços individuais para obter os passos específicos dos mesmos. O restante deste artigo inclui medidas para restringir o acesso à rede de uma conta de Armazenamento Azure, como exemplo.

### <a name="create-two-storage-accounts"></a>Criar duas contas de armazenamento

Crie uma conta de armazenamento Azure com [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

```azurepowershell-interactive
$storageAcctName1 = 'allowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName1 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Depois de criada a conta de armazenamento, recupere a chave para a conta de armazenamento numa variável com [Get-AzStorageAccountKey:](/powershell/module/az.storage/get-azstorageaccountkey)

```azurepowershell-interactive
$storageAcctKey1 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName1).Value[0]
```

A chave é usada para criar uma partilha de ficheiros num passo posterior. Introduza `$storageAcctKey` e note o valor, pois também terá de o introduzir manualmente num passo posterior quando mapear a partilha de ficheiros para uma unidade num VM.

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

### <a name="create-a-file-share-in-each-of-the-storage-account"></a>Criar uma parte de arquivo em cada uma das contas de armazenamento

Crie um contexto para a sua conta de armazenamento e chave com [o New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext). O contexto engloba o nome da conta de armazenamento e a chave da conta:

```azurepowershell-interactive
$storageContext1 = New-AzStorageContext $storageAcctName1 $storageAcctKey1

$storageContext2 = New-AzStorageContext $storageAcctName2 $storageAcctKey2
```

Criar uma partilha de ficheiros com o [New-AzStorageShare:](/powershell/module/az.storage/new-azstorageshare)

```azurepowershell-interactive
$share1 = New-AzStorageShare my-file-share -Context $storageContext1

$share2 = New-AzStorageShare my-file-share -Context $storageContext2
```

### <a name="deny-all-network-access-to-a-storage-accounts"></a>Negar todo o acesso da rede a contas de armazenamento

Por predefinição, as contas de Armazenamento aceitam ligações de rede de clientes em qualquer rede. Para limitar o acesso a redes selecionadas, altere a ação padrão para *Negar* com [Update-AzStorageAccountNetworkRuleSetSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset). Assim que o acesso de rede for negado, a conta de armazenamento não será acessível a partir de nenhuma rede.

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

### <a name="enable-network-access-only-from-the-vnet-subnet"></a>Ativar o acesso à rede apenas a partir da subnet VNet

Recupere a rede virtual criada com [a Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) e, em seguida, recupere o objeto de subnet privado numa variável com [Get-AzVirtualNetworkSubnetConfig:](/powershell/module/az.network/get-azvirtualnetworksubnetconfig)

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Name myVirtualNetwork `
  | Get-AzVirtualNetworkSubnetConfig -Name Private
```

Permitir o acesso à rede à conta de armazenamento a partir da subnet *privada* com [Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig).

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

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Aplicar política para permitir o acesso a conta de armazenamento válida

Para garantir que os utilizadores da rede virtual só podem aceder às contas de Armazenamento Azure que são seguras e permitidas, pode criar uma política de ponto final de Serviço com a lista de contas de armazenamento permitidas na definição. Esta política é então aplicada à subnet de rede virtual que está ligada ao armazenamento através de pontos finais de serviço.

### <a name="create-a-service-endpoint-policy"></a>Criar uma política de ponto final de serviço

Esta secção cria a definição de política com a lista de recursos permitidos para acesso sobre o ponto final do serviço

Recuperar o ID do recurso para a primeira conta de armazenamento (permitida) 

```azurepowershell-interactive
$resourceId = (Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name $storageAcctName1).id
```

Criar a definição de política para permitir o recurso acima

```azurepowershell-interactive
$policyDefinition = New-AzServiceEndpointPolicyDefinition -Name mypolicydefinition `
  -Description "Service Endpoint Policy Definition" `
  -Service "Microsoft.Storage" `
  -ServiceResource $resourceId
```

Criar a política de ponto final do serviço utilizando a definição de política criada acima

```azurepowershell-interactive
$sepolicy = New-AzServiceEndpointPolicy -ResourceGroupName myresourcegroup `
  -Name mysepolicy -Location EastUS
  -ServiceEndpointPolicyDefinition $policyDefinition
```

### <a name="associate-the-service-endpoint-policy-to-the-virtual-network-subnet"></a>Associar a política de ponto final do serviço à subnet da rede virtual

Depois de criar a política de ponto final de serviço, irá associá-la à subnet-alvo com a configuração de ponto final de serviço para armazenamento Azure.

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

Para testar o acesso da rede a uma conta de armazenamento, implante um VM na sub-rede.

Crie uma máquina virtual na subnet *privada* com [New-AzVM](/powershell/module/az.compute/new-azvm). Ao executar o comando seguinte, ser-lhe-ão pedidas as credenciais. Os valores que introduzir são configurados, como o nome de utilizador e a palavra-passe para a VM. A opção `-AsJob` cria a VM em segundo plano, para que possa prosseguir para o passo seguinte.

```azurepowershell-interactive
New-AzVm -ResourceGroupName myresourcegroup `
  -Location "East US" `
  -VirtualNetworkName myVirtualNetwork `
  -SubnetName Private `
  -Name "myVMPrivate" -AsJob
```

A saída semelhante à seguinte saída de exemplo é devolvida:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="confirm-access-to-the-allowed-storage-account"></a>Confirme o acesso à conta de armazenamento *permitida*

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

No *myVmPrivate* VM, mapeie a parte de ficheiro Azure da conta de armazenamento permitida para conduzir z usando powerShell. 

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

### <a name="confirm-access-is-denied-to-non-allowed-storage-account"></a>Confirmar que o acesso é negado a conta de armazenamento *não permitida*

No mesmo *myVmPrivate* VM, tente mapear a partilha de ficheiros Azure para conduzir X. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\notallowedaccount", $acctKey
New-PSDrive -Name X -PSProvider FileSystem -Root "\\notallowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

O acesso à partilha é negado `New-PSDrive : Access is denied` e recebe um erro. O acesso é negado porque a conta de armazenamento *não é permitida* não está na lista de recursos permitidos na política de ponto final do serviço. 

Feche a sessão de ambiente de trabalho remoto para a VM *myVmPublic*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que contém:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aplicou uma política de ponto final de serviço sobre um ponto final de serviço de rede virtual Azure para o Armazenamento Azure. Criou contas de Armazenamento Azure e acesso limitado à rede apenas a determinadas contas de armazenamento (e assim negou outras) a partir de uma subnet de rede virtual. Para saber mais sobre as políticas de ponto final de serviço, consulte a visão geral das políticas de [pontos finais](virtual-network-service-endpoint-policies-overview.md)do Serviço.
