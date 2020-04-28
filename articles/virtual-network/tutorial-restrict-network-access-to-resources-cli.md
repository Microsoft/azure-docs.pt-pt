---
title: Restringir o acesso à rede aos recursos paaS - Azure CLI
description: Neste artigo, aprende-se a limitar e restringir o acesso à rede aos recursos do Azure, como o Azure Storage e o Azure SQL Database, com pontos finais de serviço de rede virtual utilizando o Azure CLI.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74186393"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Restringir o acesso à rede aos recursos PaaS com pontos finais de serviço de rede virtual utilizando o Azure CLI

Os pontos finais de serviço de rede virtual permitem-lhe limitar o acesso de rede a alguns recursos de serviços do Azure a uma sub-rede de rede virtual. Também pode remover o acesso à Internet aos recursos. Os pontos finais de serviço proporcionam uma ligação direta a partir da sua rede virtual a serviços do Azure suportados, o que lhe permite utilizar o espaço de endereços privados da sua rede virtual para aceder aos serviços do Azure. O tráfego destinado aos recursos do Azure através de pontos finais de serviço permanece sempre na rede backbone do Microsoft Azure. Neste artigo, vai aprender a:

* Criar uma rede virtual com uma sub-rede
* Adicionar uma sub-rede e ativar um ponto final de serviço
* Criar um recurso do Azure e permitir o acesso de rede ao mesmo apenas a partir de uma sub-rede
* Implementar uma máquina virtual (VM) em cada sub-rede
* Confirmar o acesso a um recurso a partir de uma sub-rede
* Confirmar que o acesso é negado a um recurso a partir de uma sub-rede e da Internet

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este início rápido requer a execução da versão 2.0.28 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli). 

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Antes de criar uma rede virtual, tem de criar um grupo de recursos para a rede virtual, e todos os outros recursos criados neste artigo. Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Criar uma rede virtual com uma subnet com a [z network vnet criar](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Ativar um ponto final de serviço 

Só pode ativar pontos finais de serviço para serviços que suportam pontos finais de serviço. Consulte os serviços endpoint habilitados para o serviço disponíveis numa localização Azure com [serviços finais de lista az network vnet](/cli/azure/network/vnet). O exemplo seguinte devolve uma lista de serviços habilitados para serviços disponíveis na região *oriental.* A lista de serviços devolvidos crescerá ao longo do tempo, à medida que mais serviços Azure se tornarem ponto final de serviço habilitados.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Crie uma subrede adicional na rede virtual com a criação de [subnet de rede az](/cli/azure/network/vnet/subnet). Neste exemplo, é criado um ponto final de serviço para a *Microsoft.O armazenamento* é criado para a sub-rede: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Restringir o acesso de rede a uma sub-rede

Criar um grupo de segurança de rede com [a rede az nsg criar](/cli/azure/network/nsg). O exemplo seguinte cria um grupo de segurança de rede chamado *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Associe o grupo de segurança da rede à subnet *privada* com a atualização de subnet da [rede az](/cli/azure/network/vnet/subnet). O exemplo seguinte associa o grupo de segurança da rede *myNsgPrivate* à subnet *privada:*

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Criar regras de segurança com [a regra nsg da rede Az criar](/cli/azure/network/nsg/rule). A regra que se segue permite o acesso de saída aos endereços IP públicos atribuídos ao serviço de Armazenamento Azure: 

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

Cada grupo de segurança da rede contém várias regras de [segurança padrão](security-overview.md#default-security-rules). A regra que segue substitui uma regra de segurança padrão que permite o acesso de saída a todos os endereços IP públicos. A `destination-address-prefix "Internet"` opção nega o acesso de saída a todos os endereços IP públicos. A regra anterior anula esta regra, devido à sua maior prioridade, que permite o acesso aos endereços IP públicos do Armazenamento Azure.

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

A regra que se segue permite o tráfego de SSH a caminho da subrede a partir de qualquer lugar. A regra substitui uma regra de segurança predefinida que nega todo o tráfego de entrada a partir da Internet. O SSH é permitido à subneta para que a conectividade possa ser testada num passo posterior.

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

Os passos necessários para restringir o acesso de rede a recursos criados através de serviços do Azure ativados para pontos finais de serviço varia de serviço para serviço. Veja a documentação relativa aos serviços individuais para obter os passos específicos dos mesmos. O restante deste artigo inclui medidas para restringir o acesso à rede de uma conta de Armazenamento Azure, como exemplo.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma conta de armazenamento Azure com a criação de uma conta de [armazenamento Az.](/cli/azure/storage/account) Substitua `<replace-with-your-unique-storage-account-name>` por um nome único em todas as localizações do Azure, entre 3-24 caracteres de comprimento, utilizando apenas números e letras minúsculas.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Após a criação da conta de armazenamento, recupere a cadeia de ligação para a conta de armazenamento numa variável com [conta de armazenamento az mostrar-string de conexão](/cli/azure/storage/account). A cadeia de ligação é usada para criar uma partilha de ficheiros num passo posterior.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Veja o conteúdo da variável e note o valor do **AccountKey** devolvido na saída, porque é usado num passo posterior.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Criar uma partilha de ficheiros na conta de Armazenamento

Crie uma parte de arquivo na conta de armazenamento com a criação de partilha de [armazenamento az](/cli/azure/storage/share). Num passo posterior, esta partilha de ficheiros é montada para confirmar o acesso à rede.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Negar todo o acesso à rede a uma conta de armazenamento

Por predefinição, as contas de Armazenamento aceitam ligações de rede de clientes em qualquer rede. Para limitar o acesso a redes selecionadas, altere a ação padrão para *Negar* com a atualização da conta de [armazenamento az](/cli/azure/storage/account). Assim que o acesso de rede for negado, a conta de armazenamento não será acessível a partir de nenhuma rede.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Ativar o acesso de rede a partir de uma de sub-rede

Permitir o acesso da rede à conta de armazenamento a partir da subnet *privada* com adição de regras de rede de arma de [armazenamento az](/cli/azure/storage/account/network-rule).

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

Crie um VM na subnet *pública* com [az vm criar](/cli/azure/vm). Se as chaves SSH ainda não existirem numa localização de chaves predefinida, o comando cria-as. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

A criação da VM demora alguns minutos. Após a criação do VM, o Azure CLI mostra informações semelhantes ao seguinte exemplo: 

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

Tome nota do **publicIpAddress** na saída devolvida. Este endereço é utilizado para aceder ao VM a partir da internet num passo posterior.

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

A criação da VM demora alguns minutos. Após a criação, tome nota do **publicIpAddress** na saída devolvida. Este endereço é utilizado para aceder ao VM a partir da internet num passo posterior.

## <a name="confirm-access-to-storage-account"></a>Confirmar o acesso à conta de Armazenamento

SSH no *myVmPrivate* VM. Substitua * \<o publicIpAddress>* pelo endereço IP público do seu *myVmPrivate* VM.

```bash 
ssh <publicIpAddress>
```

Crie uma pasta para um ponto de montagem:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Monte a partilha de ficheiros Azure para o diretório que criou. Antes de executar o `<storage-account-name>` seguinte comando, `<storage-account-key>` substitua-o pelo nome da conta e pela chave que recuperou na [Create a storage account](#create-a-storage-account).

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Recebe supor. `user@myVmPrivate:~$` A partilha de ficheiros Azure montada com sucesso para */mnt/MyAzureFileShare*.

Confirme que o VM não tem conectividade de saída com quaisquer outros endereços IP públicos:

```bash
ping bing.com -c 4
```

Não vai receber respostas, porque o grupo de segurança de rede associado à sub-rede *Privada* não permite o acesso de saída a endereços IP públicos que não os atribuídos ao serviço Armazenamento do Azure.

Saia da sessão SSH para o *myVmPrivate* VM.

## <a name="confirm-access-is-denied-to-storage-account"></a>Confirmar que o acesso à conta de Armazenamento é negado

Utilize o seguinte comando para criar uma sessão SSH com o *myVmPublic* VM. Substitua `<publicIpAddress>` pelo endereço IP público do seu *myVmPublic* VM: 

```bash 
ssh <publicIpAddress>
```

Crie um diretório para um ponto de montagem:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Tente montar a partilha de ficheiros Azure para o diretório que criou. Este artigo assume que implementou a versão mais recente de Ubuntu. Se estiver a utilizar versões anteriores de Ubuntu, consulte [o Mount on Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter instruções adicionais sobre a montagem de partilhas de ficheiros. Antes de executar o `<storage-account-name>` seguinte comando, `<storage-account-key>` substitua-o pelo nome da conta e pela chave que recuperou na [Create a storage account:](#create-a-storage-account)

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

O acesso é negado `mount error(13): Permission denied` e recebe um erro, porque o *myVmPublic* VM está implantado na subnet *pública.* A sub-rede *Pública* não tem um ponto final de serviço ativado para o Armazenamento do Azure e a conta de armazenamento só permite o acesso de rede a partir da sub-rede *Privada*, não da *Público*.

Saia da sessão SSH para o *myVmPublic* VM.

Do seu computador, tente ver as ações da sua conta de armazenamento com alista de ações de [armazenamento az](/cli/azure/storage/share?view=azure-cli-latest). `<account-name>` Substitua `<account-key>` e com o nome da conta de armazenamento e chave de Criar uma conta de [armazenamento:](#create-a-storage-account)

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

O acesso é negado e recebe um Este pedido não está autorizado a executar este erro de *operação,* porque o seu computador não está na subnet *privada* da rede virtual *MyVirtualNetwork.*

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, utilize o [grupo Az eliminar](/cli/azure) para remover o grupo de recursos e todos os recursos que contém.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, ativou um ponto final de serviço para uma subnet de rede virtual. Aprendeu que os pontos finais de serviço podem ser ativados para recursos implementados com vários serviços do Azure. Criou uma conta de Armazenamento do Azure e limitou o acesso de rede à mesma apenas para os recursos dentro de uma sub-rede de uma rede virtual. Para saber mais sobre os pontos finais de serviço, veja [Descrição geral dos pontos finais de serviço](virtual-network-service-endpoints-overview.md) e [Manage subnets](virtual-network-manage-subnet.md) (Gerir sub-redes).

Se tiver várias redes virtuais na sua conta, poderá pretender ligar duas redes virtuais para que os recursos dentro de ambas possam comunicar entre si. Para saber como, consulte [Connect redes virtuais](tutorial-connect-virtual-networks-cli.md).
