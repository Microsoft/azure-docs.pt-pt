---
title: Restringir o acesso à rede a recursos de PaaS - CLI do Azure | Documentos da Microsoft
description: Neste artigo, irá aprender a limitar e restringir o acesso à rede para recursos do Azure, como o armazenamento do Azure e base de dados SQL do Azure, com pontos finais de serviço de rede virtual com a CLI do Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
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
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 4d93cfe78159fdf4ef3c34e8f80732603b701538
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521687"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Restringir o acesso à rede a recursos de PaaS com pontos finais de serviço de rede virtual com a CLI do Azure

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

Antes de criar uma rede virtual, terá de criar um grupo de recursos para a rede virtual e todos os outros recursos criados neste artigo. Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Criar uma rede virtual com uma sub-rede com [vnet de rede de az criar](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Ativar um ponto final de serviço 

Pode ativar pontos finais de serviço apenas para serviços que suportam os pontos finais de serviço. Ver serviços de ativado o ponto final de serviço disponíveis na localização do Azure com [az network vnet-endpoint-serviços de lista](/cli/azure/network/vnet). O exemplo seguinte devolve uma lista de serviços de capacidade de ponto final de serviço disponíveis na *eastus* região. A lista de serviços devolvidos irá aumentar ao longo do tempo, à medida que mais serviços do Azure forem ponto final de serviço ativado.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Criar uma sub-rede adicional na rede virtual com [criar a sub-rede de vnet de rede de az](/cli/azure/network/vnet/subnet). Neste exemplo, um ponto final de serviço para *Microsoft. Storage* é criado para a sub-rede: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Restringir o acesso de rede a uma sub-rede

Crie um grupo de segurança de rede com [nsg de rede de az criar](/cli/azure/network/nsg). O exemplo seguinte cria um grupo de segurança de rede com o nome *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Associar o grupo de segurança de rede para o *privada* sub-rede com [atualização de sub-rede de vnet de rede de az](/cli/azure/network/vnet/subnet). O exemplo seguinte associa a *myNsgPrivate* grupo de segurança de rede para o *privada* sub-rede:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Criar regras de segurança com [criar regra de nsg de rede de az](/cli/azure/network/nsg/rule). A regra que se segue permite acesso de saída para os endereços IP públicos atribuídos ao serviço do armazenamento do Azure: 

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

Cada grupo de segurança de rede contém vários [regras de segurança predefinidas](security-overview.md#default-security-rules). A regra que se segue substitui uma regra de segurança predefinida que permite acesso de saída para todos os endereços IP públicos. O `destination-address-prefix "Internet"` opção nega o acesso de saída a endereços IP públicos de tudo. A regra anterior substitui esta regra, devido a sua prioridade superior, o que permite o acesso para os endereços IP públicos do armazenamento do Azure.

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

A seguinte regra permite o tráfego SSH de entrada para a sub-rede de qualquer lugar. A regra substitui uma regra de segurança predefinida que nega todo o tráfego de entrada a partir da Internet. SSH é permitido para a sub-rede, para que a conectividade possa ser testada num passo posterior.

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

Os passos necessários para restringir o acesso de rede a recursos criados através de serviços do Azure ativados para pontos finais de serviço varia de serviço para serviço. Veja a documentação relativa aos serviços individuais para obter os passos específicos dos mesmos. O restante deste artigo inclui os passos para restringir o acesso de rede para uma conta de armazenamento do Azure, como exemplo.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma conta de armazenamento do Azure com [criar conta de armazenamento az](/cli/azure/storage/account). Substitua `<replace-with-your-unique-storage-account-name>` com um nome que seja exclusivo em todas as localizações do Azure, entre 3 e 24 carateres de comprimento, a utilizar apenas números e letras minúsculas.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Depois de criar a conta de armazenamento, obter a cadeia de ligação para a conta de armazenamento numa variável com [armazenamento az account show-connection-string](/cli/azure/storage/account). A cadeia de ligação é utilizada para criar uma partilha de ficheiros num passo posterior.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Exibir o conteúdo da variável e anote o valor para **AccountKey** devolvido no resultado, porque é utilizado num passo posterior.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Criar uma partilha de ficheiros na conta de Armazenamento

Criar uma partilha de ficheiros na conta de armazenamento com [criar partilha de armazenamento az](/cli/azure/storage/share). Num passo posterior, esta partilha de ficheiros está montada para confirmar o acesso de rede ao mesmo.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Negar todos os acessos de rede para uma conta de armazenamento

Por predefinição, as contas de Armazenamento aceitam ligações de rede de clientes em qualquer rede. Para limitar o acesso a redes selecionadas, altere a ação predefinida para *negar* com [atualização da conta de armazenamento az](/cli/azure/storage/account). Quando é negado o acesso à rede, a conta de armazenamento não está acessível a partir de qualquer rede.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Ativar o acesso de rede a partir de uma de sub-rede

Permitir o acesso à rede para a conta de armazenamento do *privada* sub-rede com [adicione a conta de armazenamento az rede-rule](/cli/azure/storage/account/network-rule).

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

Criar uma VM no *pública* sub-rede com [az vm criar](/cli/azure/vm). Se as chaves SSH ainda não existirem numa localização de chaves predefinida, o comando cria-as. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

A criação da VM demora alguns minutos. Depois da VM é criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo: 

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

Anote o **publicIpAddress** no resultado retornado. Este endereço é utilizado para aceder à VM a partir da internet num passo posterior.

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

A criação da VM demora alguns minutos. Após a criação, anote o **publicIpAddress** no resultado devolvido. Este endereço é utilizado para aceder à VM a partir da internet num passo posterior.

## <a name="confirm-access-to-storage-account"></a>Confirmar o acesso à conta de Armazenamento

SSH para o *myVmPrivate* VM. Substitua  *\<publicIpAddress >* com o endereço IP público do seu *myVmPrivate* VM.

```bash 
ssh <publicIpAddress>
```

Crie uma pasta para um ponto de montagem:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Monte a partilha de ficheiros do Azure para o diretório que criou. Antes de executar o seguinte comando, substitua `<storage-account-name>` com o nome da conta e `<storage-account-key>` com a chave que obteve na [criar uma conta de armazenamento](#create-a-storage-account).

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Recebe o `user@myVmPrivate:~$` prompt. A partilha de ficheiros do Azure montada com êxito para */mnt/MyAzureFileShare*.

Confirme que a VM tem sem conectividade de saída para outros endereços IP públicos:

```bash
ping bing.com -c 4
```

Não vai receber respostas, porque o grupo de segurança de rede associado à sub-rede *Privada* não permite o acesso de saída a endereços IP públicos que não os atribuídos ao serviço Armazenamento do Azure.

Saia da sessão SSH para o *myVmPrivate* VM.

## <a name="confirm-access-is-denied-to-storage-account"></a>Confirmar que o acesso à conta de Armazenamento é negado

Utilize o seguinte comando para criar uma sessão SSH com o *myVmPublic* VM. Substitua `<publicIpAddress>` com o endereço IP público do seu *myVmPublic* VM: 

```bash 
ssh <publicIpAddress>
```

Crie um diretório para um ponto de montagem:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Tentativa para montar a partilha de ficheiros do Azure para o diretório que criou. Este artigo pressupõe que implantou a versão mais recente do Ubuntu. Se estiver a utilizar versões anteriores do Ubuntu, veja [montar no Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter instruções adicionais sobre como montar partilhas de ficheiros. Antes de executar o seguinte comando, substitua `<storage-account-name>` com o nome da conta e `<storage-account-key>` com a chave que obteve na [criar uma conta de armazenamento](#create-a-storage-account):

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Acesso negado e recebe um `mount error(13): Permission denied` erro, porque o *myVmPublic* VM for implementada no *pública* sub-rede. A sub-rede *Pública* não tem um ponto final de serviço ativado para o Armazenamento do Azure e a conta de armazenamento só permite o acesso de rede a partir da sub-rede *Privada*, não da *Público*.

Saia da sessão SSH para o *myVmPublic* VM.

Do seu computador, tentar visualizar as partilhas na sua conta de armazenamento com [lista de partilha de armazenamento az](/cli/azure/storage/share?view=azure-cli-latest). Substitua `<account-name>` e `<account-key>` com o nome de conta de armazenamento e a chave de [criar uma conta de armazenamento](#create-a-storage-account):

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

Acesso negado e recebe um *este pedido não está autorizado a efetuar esta operação* erro, porque não se encontra em seu computador o *privada* sub-rede do *MyVirtualNetwork* rede virtual.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, utilize [eliminação do grupo de az](/cli/azure) para remover o grupo de recursos e todos os recursos nele contidos.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, ativou um ponto final de serviço para uma sub-rede de rede virtual. Aprendeu que os pontos finais de serviço podem ser ativados para recursos implementados com vários serviços do Azure. Criou uma conta de Armazenamento do Azure e limitou o acesso de rede à mesma apenas para os recursos dentro de uma sub-rede de uma rede virtual. Para saber mais sobre os pontos finais de serviço, veja [Descrição geral dos pontos finais de serviço](virtual-network-service-endpoints-overview.md) e [Manage subnets](virtual-network-manage-subnet.md) (Gerir sub-redes).

Se tiver várias redes virtuais na sua conta, poderá pretender ligar duas redes virtuais para que os recursos dentro de ambas possam comunicar entre si. Para saber como, veja [ligar redes virtuais](tutorial-connect-virtual-networks-cli.md).
