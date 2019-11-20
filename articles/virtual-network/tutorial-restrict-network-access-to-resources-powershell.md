---
title: Restringir o acesso à rede para recursos de PaaS-Azure PowerShell
description: Neste artigo, você aprende a limitar e restringir o acesso à rede para recursos do Azure, como o armazenamento do Azure e o banco de dados SQL do Azure, com pontos de extremidade de serviço de rede virtual usando Azure PowerShell.
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
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185545"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>Restringir o acesso à rede para recursos de PaaS com pontos de extremidade de serviço de rede virtual usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os pontos finais de serviço de rede virtual permitem-lhe limitar o acesso de rede a alguns recursos de serviços do Azure a uma sub-rede de rede virtual. Também pode remover o acesso à Internet aos recursos. Os pontos finais de serviço proporcionam uma ligação direta a partir da sua rede virtual a serviços do Azure suportados, o que lhe permite utilizar o espaço de endereços privados da sua rede virtual para aceder aos serviços do Azure. O tráfego destinado aos recursos do Azure através de pontos finais de serviço permanece sempre na rede backbone do Microsoft Azure. Neste artigo, vai aprender a:

* Criar uma rede virtual com uma sub-rede
* Adicionar uma sub-rede e ativar um ponto final de serviço
* Criar um recurso do Azure e permitir o acesso de rede ao mesmo apenas a partir de uma sub-rede
* Implementar uma máquina virtual (VM) em cada sub-rede
* Confirmar o acesso a um recurso a partir de uma sub-rede
* Confirmar que o acesso é negado a um recurso a partir de uma sub-rede e da Internet

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o Azure PowerShell módulo versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Antes de criar uma rede virtual, você precisa criar um grupo de recursos para a rede virtual e todos os outros recursos criados neste artigo. Crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos chamado *MyResource*Group: 

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Crie uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo a seguir cria uma rede virtual chamada *myVirtualNetwork* com o prefixo de endereço *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Crie uma configuração de sub-rede com [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). O exemplo a seguir cria uma configuração de sub-rede para uma sub-rede chamada *Public*:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Crie a sub-rede na rede virtual gravando a configuração de sub-rede na rede virtual com [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork):

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="enable-a-service-endpoint"></a>Ativar um ponto final de serviço

Você pode habilitar pontos de extremidade de serviço somente para serviços que dão suporte a pontos de extremidade de serviço. Exiba serviços habilitados para ponto de extremidade de serviço disponíveis em um local do Azure com [Get-AzVirtualNetworkAvailableEndpointService](/powershell/module/az.network/get-azvirtualnetworkavailableendpointservice). O exemplo a seguir retorna uma lista de serviços habilitados para ponto de extremidade de serviço disponíveis na região *lesteus* . A lista de serviços retornada crescerá ao longo do tempo enquanto mais serviços do Azure se tornarem ponto de extremidade de serviço habilitado.

```azurepowershell-interactive
Get-AzVirtualNetworkAvailableEndpointService -Location eastus | Select Name
```

Crie uma sub-rede adicional na rede virtual. Neste exemplo, uma sub-rede chamada *Private* é criada com um ponto de extremidade de serviço para *Microsoft. Storage*: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-a-subnet"></a>Restringir o acesso de rede a uma sub-rede

Crie regras de segurança de grupo de segurança de rede com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). A regra a seguir permite o acesso de saída aos endereços IP públicos atribuídos ao serviço de armazenamento do Azure: 

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

A regra a seguir nega o acesso a todos os endereços IP públicos. A regra anterior substitui essa regra, devido à sua prioridade mais alta, que permite o acesso aos endereços IP públicos do armazenamento do Azure.

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

A regra a seguir permite o tráfego de protocolo RDP (RDP) de entrada para a sub-rede de qualquer lugar. Conexões de área de trabalho remota são permitidas para a sub-rede, para que você possa confirmar o acesso à rede para um recurso em uma etapa posterior.

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

Crie um grupo de segurança de rede com [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). O exemplo a seguir cria um grupo de segurança de rede chamado *myNsgPrivate*.

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

Associe o grupo de segurança de rede à sub-rede *privada* com [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) e, em seguida, grave a configuração de sub-rede na rede virtual. O exemplo a seguir associa o grupo de segurança de rede *myNsgPrivate* à sub-rede *privada* :

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

Os passos necessários para restringir o acesso de rede a recursos criados através de serviços do Azure ativados para pontos finais de serviço varia de serviço para serviço. Veja a documentação relativa aos serviços individuais para obter os passos específicos dos mesmos. O restante deste artigo inclui etapas para restringir o acesso à rede para uma conta de armazenamento do Azure, como um exemplo.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma conta de armazenamento do Azure com [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Substitua `<replace-with-your-unique-storage-account-name>` por um nome que seja exclusivo em todos os locais do Azure, entre 3-24 caracteres de comprimento, usando apenas números e letras minúsculas.

```azurepowershell-interactive
$storageAcctName = '<replace-with-your-unique-storage-account-name>'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Depois que a conta de armazenamento for criada, recupere a chave da conta de armazenamento em uma variável com [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey):

```azurepowershell-interactive
$storageAcctKey = (Get-AzStorageAccountKey `
  -ResourceGroupName myResourceGroup `
  -AccountName $storageAcctName).Value[0]
```

A chave é usada para criar um compartilhamento de arquivos em uma etapa posterior. Insira `$storageAcctKey` e observe o valor, pois você também precisará inseri-lo manualmente em uma etapa posterior ao mapear o compartilhamento de arquivos para uma unidade em uma VM.

### <a name="create-a-file-share-in-the-storage-account"></a>Criar uma partilha de ficheiros na conta de Armazenamento

Crie um contexto para sua conta de armazenamento e a chave com [New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext). O contexto encapsula o nome da conta de armazenamento e a chave de conta:

```azurepowershell-interactive
$storageContext = New-AzStorageContext $storageAcctName $storageAcctKey
```

Crie um compartilhamento de arquivos com [New-AzStorageShare](/powershell/module/az.storage/new-azstorageshare):

$share = New-AzStorageShare my-file-share -Context $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>Negar todo o acesso à rede para uma conta de armazenamento

Por predefinição, as contas de Armazenamento aceitam ligações de rede de clientes em qualquer rede. Para limitar o acesso às redes selecionadas, altere a ação padrão para *negar* com [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset). Quando o acesso à rede for negado, a conta de armazenamento não poderá ser acessada de nenhuma rede.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Ativar o acesso de rede a partir de uma de sub-rede

Recupere a rede virtual criada com [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) e, em seguida, recupere o objeto de sub-rede privada em uma variável com [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVirtualNetwork" `
  | Get-AzVirtualNetworkSubnetConfig `
  -Name "Private"
```

Permita o acesso à rede para a conta de armazenamento da sub-rede *privada* com [Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Para testar o acesso de rede a uma conta de Armazenamento, implemente uma VM em cada sub-rede.

### <a name="create-the-first-virtual-machine"></a>Criar a primeira máquina virtual

Crie uma máquina virtual na sub-rede *pública* com [New-AzVM](/powershell/module/az.compute/new-azvm). Ao executar o comando seguinte, ser-lhe-ão pedidas as credenciais. Os valores que introduzir são configurados, como o nome de utilizador e a palavra-passe para a VM. A opção `-AsJob` cria a VM em segundo plano, para que possa prosseguir para o passo seguinte.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -Name "myVmPublic" `
    -AsJob
```

Uma saída semelhante à seguinte saída de exemplo é retornada:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM     
```

### <a name="create-the-second-virtual-machine"></a>Criar a segunda máquina virtual

Crie uma máquina virtual na sub-rede *privada* :

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -Name "myVmPrivate"
```

Leva alguns minutos para que o Azure crie a VM. Não continue na próxima etapa até que o Azure termine de criar a VM e retorne a saída para o PowerShell.

## <a name="confirm-access-to-storage-account"></a>Confirmar o acesso à conta de Armazenamento

Use [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para retornar o endereço IP público de uma VM. O exemplo a seguir retorna o endereço IP público da VM *myVmPrivate* :

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

Na VM *myVmPrivate*, mapeie a partilha de ficheiros do Azure para a unidade Z com o PowerShell. Antes de executar os comandos a seguir, substitua `<storage-account-key>` e `<storage-account-name>` com os valores fornecidos ou recuperados em [criar uma conta de armazenamento](#create-a-storage-account).

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

Confirme se a VM não tem conectividade de saída com nenhum outro endereço IP público:

```powershell
ping bing.com
```

Não vai receber respostas, porque o grupo de segurança de rede associado à sub-rede *Privada* não permite o acesso de saída a endereços IP públicos que não os atribuídos ao serviço Armazenamento do Azure.

Feche a sessão de ambiente de trabalho remoto para a VM *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Confirmar que o acesso à conta de Armazenamento é negado

Obtenha o endereço IP público da VM *myVmPublic* :

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

Na VM *myVmPublic* , tente mapear o compartilhamento de arquivos do Azure para a unidade Z. Antes de executar os comandos a seguir, substitua `<storage-account-key>` e `<storage-account-name>` com os valores fornecidos ou recuperados em [criar uma conta de armazenamento](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

O acesso ao compartilhamento é negado e você recebe um erro de `New-PSDrive : Access is denied`. O acesso é negado porque a VM *myVmPublic* é implementada na sub-rede *Pública*. A sub-rede *Pública* não tem um ponto final de serviço ativado para o Armazenamento do Azure e a conta de armazenamento só permite o acesso de rede a partir da sub-rede *Privada*, não da *Público*.

Feche a sessão de ambiente de trabalho remoto para a VM *myVmPublic*.

Em seu computador, tente exibir os compartilhamentos de arquivos na conta de armazenamento com o seguinte comando:

```powershell-interactive
Get-AzStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

O acesso é negado e você recebe um *Get-AzStorageFile: o servidor remoto retornou um erro: (403) proibido. Código de status HTTP: 403-mensagem de erro de HTTP: esta solicitação não está autorizada a executar esse* erro de operação, pois o computador não está na sub-rede *privada* da rede virtual *MyVirtualNetwork* .

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você habilitou um ponto de extremidade de serviço para uma sub-rede de rede virtual. Aprendeu que os pontos finais de serviço podem ser ativados para recursos implementados com vários serviços do Azure. Criou uma conta de Armazenamento do Azure e limitou o acesso de rede à mesma apenas para os recursos dentro de uma sub-rede de uma rede virtual. Para saber mais sobre os pontos finais de serviço, veja [Descrição geral dos pontos finais de serviço](virtual-network-service-endpoints-overview.md) e [Manage subnets](virtual-network-manage-subnet.md) (Gerir sub-redes).

Se tiver várias redes virtuais na sua conta, poderá pretender ligar duas redes virtuais para que os recursos dentro de ambas possam comunicar entre si. Para saber como, consulte [conectar redes virtuais](tutorial-connect-virtual-networks-powershell.md).
