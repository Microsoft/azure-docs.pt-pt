---
title: Restringir o acesso à rede para recursos de PaaS-CLI do Azure
description: Neste artigo, você aprende a limitar e restringir o acesso à rede para recursos do Azure, como o armazenamento do Azure e o banco de dados SQL do Azure, com pontos de extremidade de serviço de rede virtual usando o CLI do Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: f2dcc714bc9052dd51f114e24f0b9bd74b87480c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186393"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Restringir o acesso à rede para recursos de PaaS com pontos de extremidade de serviço de rede virtual usando o CLI do Azure

Os pontos finais de serviço de rede virtual permitem-lhe limitar o acesso de rede a alguns recursos de serviços do Azure a uma sub-rede de rede virtual. Também pode remover o acesso à Internet aos recursos. Os pontos finais de serviço proporcionam uma ligação direta a partir da sua rede virtual a serviços do Azure suportados, o que lhe permite utilizar o espaço de endereços privados da sua rede virtual para aceder aos serviços do Azure. O tráfego destinado aos recursos do Azure através de pontos finais de serviço permanece sempre na rede backbone do Microsoft Azure. Neste artigo, vai aprender a:

* Criar uma rede virtual com uma sub-rede
* Adicionar uma sub-rede e ativar um ponto final de serviço
* Criar um recurso do Azure e permitir o acesso de rede ao mesmo apenas a partir de uma sub-rede
* Implementar uma máquina virtual (VM) em cada sub-rede
* Confirmar o acesso a um recurso a partir de uma sub-rede
* Confirmar que o acesso é negado a um recurso a partir de uma sub-rede e da Internet

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este início rápido requer a execução da versão 2.0.28 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Antes de criar uma rede virtual, você precisa criar um grupo de recursos para a rede virtual e todos os outros recursos criados neste artigo. Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Crie uma rede virtual com uma sub-rede com [AZ Network vnet Create](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Ativar um ponto final de serviço 

Você pode habilitar pontos de extremidade de serviço somente para serviços que dão suporte a pontos de extremidade de serviço. Exiba serviços habilitados para ponto de extremidade de serviço disponíveis em um local do Azure com [AZ Network vnet List-Endpoint-Services](/cli/azure/network/vnet). O exemplo a seguir retorna uma lista de serviços habilitados para ponto de extremidade de serviço disponíveis na região *lesteus* . A lista de serviços retornada crescerá ao longo do tempo, já que mais serviços do Azure tornam o ponto de extremidade de serviço habilitado.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Crie uma sub-rede adicional na rede virtual com [AZ Network vnet subnet Create](/cli/azure/network/vnet/subnet). Neste exemplo, um ponto de extremidade de serviço para *Microsoft. Storage* é criado para a sub-rede: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Restringir o acesso de rede a uma sub-rede

Crie um grupo de segurança de rede com [AZ Network NSG Create](/cli/azure/network/nsg). O exemplo a seguir cria um grupo de segurança de rede chamado *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Associe o grupo de segurança de rede à sub-rede *privada* com [AZ Network vnet subnet Update](/cli/azure/network/vnet/subnet). O exemplo a seguir associa o grupo de segurança de rede *myNsgPrivate* à sub-rede *privada* :

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Crie regras de segurança com [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule). A regra a seguir permite o acesso de saída aos endereços IP públicos atribuídos ao serviço de armazenamento do Azure: 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"
```

Cada grupo de segurança de rede contém várias [regras de segurança padrão](security-overview.md#default-security-rules). A regra a seguir substitui uma regra de segurança padrão que permite o acesso de saída a todos os endereços IP públicos. A opção `destination-address-prefix "Internet"` nega o acesso de saída a todos os endereços IP públicos. A regra anterior substitui essa regra, devido à sua prioridade mais alta, que permite o acesso aos endereços IP públicos do armazenamento do Azure.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"
```

A regra a seguir permite o tráfego SSH de entrada para a sub-rede de qualquer lugar. A regra substitui uma regra de segurança predefinida que nega todo o tráfego de entrada a partir da Internet. O SSH é permitido para a sub-rede para que a conectividade possa ser testada em uma etapa posterior.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-a-resource"></a>Restringir o acesso de rede a um recurso

Os passos necessários para restringir o acesso de rede a recursos criados através de serviços do Azure ativados para pontos finais de serviço varia de serviço para serviço. Veja a documentação relativa aos serviços individuais para obter os passos específicos dos mesmos. O restante deste artigo inclui etapas para restringir o acesso à rede para uma conta de armazenamento do Azure, como um exemplo.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma conta de armazenamento do Azure com [AZ Storage Account Create](/cli/azure/storage/account). Substitua `<replace-with-your-unique-storage-account-name>` por um nome que seja exclusivo em todos os locais do Azure, entre 3-24 caracteres de comprimento, usando apenas números e letras minúsculas.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Depois que a conta de armazenamento for criada, recupere a cadeia de conexão para a conta de armazenamento em uma variável com [AZ Storage Account show-Connection-String](/cli/azure/storage/account). A cadeia de conexão é usada para criar um compartilhamento de arquivos em uma etapa posterior.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Exiba o conteúdo da variável e anote o valor de **AccountKey** retornado na saída, pois ele é usado em uma etapa posterior.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Criar uma partilha de ficheiros na conta de Armazenamento

Crie um compartilhamento de arquivos na conta de armazenamento com [AZ Storage share Create](/cli/azure/storage/share). Em uma etapa posterior, esse compartilhamento de arquivos é montado para confirmar o acesso à rede a ele.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Negar todo o acesso à rede para uma conta de armazenamento

Por predefinição, as contas de Armazenamento aceitam ligações de rede de clientes em qualquer rede. Para limitar o acesso às redes selecionadas, altere a ação padrão para *negar* com [AZ Storage Account Update](/cli/azure/storage/account). Quando o acesso à rede for negado, a conta de armazenamento não poderá ser acessada de nenhuma rede.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Ativar o acesso de rede a partir de uma de sub-rede

Permita o acesso à rede para a conta de armazenamento da sub-rede *privada* com [AZ Storage Account Network-regra Add](/cli/azure/storage/account/network-rule).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName \
  --vnet-name myVirtualNetwork \
  --subnet Private
```
## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Para testar o acesso de rede a uma conta de Armazenamento, implemente uma VM em cada sub-rede.

### <a name="create-the-first-virtual-machine"></a>Criar a primeira máquina virtual

Crie uma VM na sub-rede *pública* com [AZ VM Create](/cli/azure/vm). Se as chaves SSH ainda não existirem numa localização de chaves predefinida, o comando cria-as. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

A criação da VM demora alguns minutos. Depois que a VM é criada, a CLI do Azure mostra informações semelhantes ao exemplo a seguir: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPublic",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Anote o **publicIpAddress** na saída retornada. Esse endereço é usado para acessar a VM da Internet em uma etapa posterior.

### <a name="create-the-second-virtual-machine"></a>Criar a segunda máquina virtual

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

A criação da VM demora alguns minutos. Após a criação, anote o **publicIpAddress** na saída retornada. Esse endereço é usado para acessar a VM da Internet em uma etapa posterior.

## <a name="confirm-access-to-storage-account"></a>Confirmar o acesso à conta de Armazenamento

SSH na VM *myVmPrivate* . Substitua *\<publicIpAddress >* pelo endereço IP público da VM *myVmPrivate* .

```bash 
ssh <publicIpAddress>
```

Crie uma pasta para um ponto de montagem:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Monte o compartilhamento de arquivos do Azure no diretório que você criou. Antes de executar o comando a seguir, substitua `<storage-account-name>` pelo nome da conta e `<storage-account-key>` com a chave que você recuperou em [criar uma conta de armazenamento](#create-a-storage-account).

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Você recebe o prompt de `user@myVmPrivate:~$`. O compartilhamento de arquivos do Azure foi montado com êxito no */mnt/MyAzureFileShare*.

Confirme se a VM não tem conectividade de saída com nenhum outro endereço IP público:

```bash
ping bing.com -c 4
```

Não vai receber respostas, porque o grupo de segurança de rede associado à sub-rede *Privada* não permite o acesso de saída a endereços IP públicos que não os atribuídos ao serviço Armazenamento do Azure.

Saia da sessão SSH para a VM *myVmPrivate* .

## <a name="confirm-access-is-denied-to-storage-account"></a>Confirmar que o acesso à conta de Armazenamento é negado

Use o comando a seguir para criar uma sessão SSH com a VM *myVmPublic* . Substitua `<publicIpAddress>` pelo endereço IP público da VM *myVmPublic* : 

```bash 
ssh <publicIpAddress>
```

Crie um diretório para um ponto de montagem:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Tente montar o compartilhamento de arquivos do Azure para o diretório que você criou. Este artigo pressupõe que você implantou a versão mais recente do Ubuntu. Se você estiver usando versões anteriores do Ubuntu, consulte [montar no Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter instruções adicionais sobre como montar compartilhamentos de arquivos. Antes de executar o comando a seguir, substitua `<storage-account-name>` pelo nome da conta e `<storage-account-key>` com a chave que você recuperou em [criar uma conta de armazenamento](#create-a-storage-account):

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

O acesso é negado e você recebe um erro de `mount error(13): Permission denied`, porque a VM *myVmPublic* é implantada na sub-rede *pública* . A sub-rede *Pública* não tem um ponto final de serviço ativado para o Armazenamento do Azure e a conta de armazenamento só permite o acesso de rede a partir da sub-rede *Privada*, não da *Público*.

Saia da sessão SSH para a VM *myVmPublic* .

Em seu computador, tente exibir os compartilhamentos em sua conta de armazenamento com [AZ Storage share List](/cli/azure/storage/share?view=azure-cli-latest). Substitua `<account-name>` e `<account-key>` com o nome da conta de armazenamento e a chave de [criar uma conta de armazenamento](#create-a-storage-account):

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

O acesso é negado e você recebe que *esta solicitação não está autorizada a executar esse* erro de operação, pois o computador não está na sub-rede *privada* da rede virtual *MyVirtualNetwork* .

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, use [AZ Group Delete](/cli/azure) para remover o grupo de recursos e todos os recursos que ele contém.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você habilitou um ponto de extremidade de serviço para uma sub-rede de rede virtual. Aprendeu que os pontos finais de serviço podem ser ativados para recursos implementados com vários serviços do Azure. Criou uma conta de Armazenamento do Azure e limitou o acesso de rede à mesma apenas para os recursos dentro de uma sub-rede de uma rede virtual. Para saber mais sobre os pontos finais de serviço, veja [Descrição geral dos pontos finais de serviço](virtual-network-service-endpoints-overview.md) e [Manage subnets](virtual-network-manage-subnet.md) (Gerir sub-redes).

Se tiver várias redes virtuais na sua conta, poderá pretender ligar duas redes virtuais para que os recursos dentro de ambas possam comunicar entre si. Para saber como, consulte [conectar redes virtuais](tutorial-connect-virtual-networks-cli.md).
