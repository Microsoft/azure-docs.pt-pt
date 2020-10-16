---
title: Restringir a exfiltração de dados ao Azure Storage - Azure CLI
description: Neste artigo, aprende-se a limitar e restringir a exfiltração de dados de rede virtual aos recursos de armazenamento de Azure com políticas de ponto final de serviço de rede virtual utilizando o CLI Azure.
services: virtual-network
documentationcenter: virtual-network
author: rdhillon
manager: ''
editor: ''
tags: azure-resource-manager
Customer intent: I want only specific Azure Storage account to be allowed access from a virtual network subnet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: 53f10996a7f15e32261f151600163f41df4e58ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666794"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-the-azure-cli"></a>Gerir a exfiltração de dados para contas de armazenamento de Azure com políticas de ponto final de serviço de rede virtual utilizando o Azure CLI

As políticas de ponto final do serviço de rede virtual permitem-lhe aplicar o controlo de acesso nas contas de Armazenamento Azure a partir de uma rede virtual sobre pontos finais de serviço. Esta é uma chave para garantir as suas cargas de trabalho, gerir as contas de armazenamento permitidas e onde a exfiltração de dados é permitida.
Neste artigo, vai aprender a:

* Crie uma rede virtual e adicione uma sub-rede.
* Ativar o ponto final do serviço para o Armazenamento Azure.
* Crie duas contas de Armazenamento Azure e permita o acesso à rede a partir da sub-rede criada acima.
* Crie uma política de ponto final de serviço que permita o acesso apenas a uma das contas de armazenamento.
* Insaem uma máquina virtual (VM) na sub-rede.
* Confirme o acesso à conta de armazenamento permitida a partir da sub-rede.
* Confirme que o acesso é negado à conta de armazenamento não autorizada a partir da sub-rede.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este início rápido requer a execução da versão 2.0.28 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli). 

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Antes de criar uma rede virtual, tem de criar um grupo de recursos para a rede virtual, e todos os outros recursos criados neste artigo. Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Crie uma rede virtual com uma sub-rede com [a criação de vnet de rede az](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Private \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Ativar um ponto final de serviço 

Neste exemplo, é criado um ponto final de serviço para o *Microsoft.Storage* para a sub-rede *Private:* 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.0.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Restringir o acesso de rede a uma sub-rede

Criar um grupo de segurança de rede com [a criação de nsg de rede Az](/cli/azure/network/nsg). O exemplo a seguir cria um grupo de segurança de rede chamado *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Associe o grupo de segurança da rede à sub-rede *Privada* com [a atualização da sub-rede de rede Az.](/cli/azure/network/vnet/subnet) O exemplo a seguir associa o grupo de segurança da rede *myNsgPrivate* à sub-rede *Privada:*

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Criar regras de segurança com [a regra az rede nsg criar](/cli/azure/network/nsg/rule). A regra que se segue permite o acesso de saída aos endereços IP públicos atribuídos ao serviço de Armazenamento Azure: 

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

Cada grupo de segurança de rede contém várias [regras de segurança predefinidos.](security-overview.md#default-security-rules) A regra que segue sobrepõe-se a uma regra de segurança predefinido que permite o acesso de saída a todos os endereços IP públicos. A `destination-address-prefix "Internet"` opção nega o acesso de saída a todos os endereços IP públicos. A regra anterior substitui esta regra, devido à sua maior prioridade, que permite o acesso aos endereços IP públicos do Azure Storage.

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

A seguinte regra permite que o tráfego SSH entre na sub-rede a partir de qualquer lugar. A regra substitui uma regra de segurança predefinida que nega todo o tráfego de entrada a partir da Internet. O SSH é permitido à sub-rede para que a conectividade possa ser testada num passo posterior.

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

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Restringir o acesso à rede às contas de Armazenamento Azure

Esta secção lista etapas para restringir o acesso à rede de uma conta de Armazenamento Azure a partir da sub-rede dada numa rede Virtual através do ponto final de serviço.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie duas contas de armazenamento Azure com [a criação de conta de armazenamento AZ](/cli/azure/storage/account).

```azurecli-interactive
storageAcctName1="allowedstorageacc"

az storage account create \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2

storageAcctName2="notallowedstorageacc"

az storage account create \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Após a criação das contas de armazenamento, recupere a cadeia de ligação para as contas de armazenamento numa variável com [az armazenamento show-connection-string](/cli/azure/storage/account). A cadeia de ligação é utilizada para criar uma partilha de ficheiros num passo posterior.

```azurecli-interactive
saConnectionString1=$(az storage account show-connection-string \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)

saConnectionString2=$(az storage account show-connection-string \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Veja o conteúdo da variável e note o valor da **ContaKey** devolvido na saída, porque é usado num passo posterior.

```azurecli-interactive
echo $saConnectionString1

echo $saConnectionString2
```

### <a name="create-a-file-share-in-the-storage-account"></a>Criar uma partilha de ficheiros na conta de Armazenamento

Crie uma partilha de ficheiros na conta de armazenamento com [a az share create](/cli/azure/storage/share). Numa etapa posterior, esta partilha de ficheiros é montada para confirmar o acesso à rede ao mesmo.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString1 > /dev/null

az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString2 > /dev/null
```

### <a name="deny-all-network-access-to-the-storage-account"></a>Negar todo o acesso à rede à conta de armazenamento

Por predefinição, as contas de Armazenamento aceitam ligações de rede de clientes em qualquer rede. Para limitar o acesso a redes selecionadas, altere a ação padrão para *Negar* com [a atualização da conta de armazenamento AZ](/cli/azure/storage/account). Assim que o acesso de rede for negado, a conta de armazenamento não será acessível a partir de nenhuma rede.

```azurecli-interactive
az storage account update \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --default-action Deny

az storage account update \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-virtual-network-subnet"></a>Permitir o acesso à rede a partir da sub-rede de rede virtual

Permitir o acesso à rede à conta de armazenamento a partir da sub-rede *Privada* com [a regra da rede de armazenamento Az add](/cli/azure/storage/account/network-rule).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName1 \
  --vnet-name myVirtualNetwork \
  --subnet Private

az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName2 \
  --vnet-name myVirtualNetwork \
  --subnet Private
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Aplicar a política para permitir o acesso a conta de armazenamento válido

As políticas de Endpoint do serviço Azure só estão disponíveis para o Armazenamento Azure. Assim, vamos permitir o Ponto Final de Serviço para *o Microsoft.Storage* nesta sub-rede para esta configuração de exemplo.

As políticas de ponto final de serviço são aplicadas sobre os pontos finais do serviço. Começaremos por criar uma política de ponto final de serviço. Em seguida, criaremos as definições políticas ao abrigo desta política para que as contas de armazenamento do Azure sejam aprovadas para esta sub-rede

Criar uma política de ponto final de serviço

```azurecli-interactive
az network service-endpoint policy create \
  --resource-group myResourceGroup \
  --name mysepolicy \
  --location eastus
```

Guarde o recurso URI para a conta de armazenamento permitida numa variável. Antes de executar o comando abaixo, *\<your-subscription-id>* substitua-o pelo valor real do seu ID de subscrição.

```azurecli-interactive
$serviceResourceId="/subscriptions/<your-subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/allowedstorageacc"
```

Crie & adicionar uma definição de política para permitir a conta de armazenamento Azure acima na política de ponto final de serviço

```azurecli-interactive
az network service-endpoint policy-definition create \
  --resource-group myResourceGroup \
  --policy-name mysepolicy \
  --name mypolicydefinition \
  --service "Microsoft.Storage" \
  --service-resources $serviceResourceId
```

E atualize a sub-rede de rede virtual para associar-lhe a política de ponto final de serviço criada no passo anterior

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --service-endpoints Microsoft.Storage \
  --service-endpoint-policy mysepolicy
```

## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Validar a restrição de acesso às contas de Armazenamento Azure

### <a name="create-the-virtual-machine"></a>Criar a máquina virtual

Para testar o acesso à rede a uma conta de armazenamento, implante um VM na sub-rede.

Criar um VM na sub-rede *privada* com [criação az vm](/cli/azure/vm). Se as chaves SSH ainda não existirem numa localização de chaves predefinida, o comando cria-as. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

A criação da VM demora alguns minutos. Após a criação, tome nota do **públicoIpAddress** na saída devolvida. Este endereço é utilizado para aceder ao VM a partir da internet num passo posterior.

### <a name="confirm-access-to-storage-account"></a>Confirmar o acesso à conta de Armazenamento

SSH no *myVmPrivate* VM. *\<publicIpAddress>* Substitua-o pelo endereço IP público do seu *VM myVmPrivate.*

```bash 
ssh <publicIpAddress>
```

Criar uma pasta para um ponto de montagem:

```bash
sudo mkdir /mnt/MyAzureFileShare1
```

Monte a partilha de ficheiros Azure para o diretório que criou. Antes de executar o comando abaixo, *\<storage-account-key>* substitua-o pelo valor da *AccountKey* de **$saConnectionString 1**.

```bash
sudo mount --types cifs //allowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare1 --options vers=3.0,username=allowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Recebes o `user@myVmPrivate:~$` pedido. A partilha de ficheiros Azure montada com sucesso para */mnt/MyAzureFileShare*.

### <a name="confirm-access-is-denied-to-storage-account"></a>Confirmar que o acesso à conta de Armazenamento é negado

A partir do mesmo VM *myVmPrivate,* crie um diretório para um ponto de montagem:

```bash
sudo mkdir /mnt/MyAzureFileShare2
```

Tente montar a parte de ficheiros Azure da conta de armazenamento *notallowedstorageaccc* para o diretório que criou. Este artigo pressupõe que implementou a versão mais recente do Ubuntu. Se estiver a utilizar versões anteriores do Ubuntu, consulte [o Mount on Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter instruções adicionais sobre a montagem de partilhas de ficheiros. 

Antes de executar o comando abaixo, *\<storage-account-key>* substitua o valor da *AccountKey* de **$saConnectionString 2**.

```bash
sudo mount --types cifs //notallowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare2 --options vers=3.0,username=notallowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

O acesso é negado e você recebe um `mount error(13): Permission denied` erro, porque esta conta de armazenamento não está na lista de permitir a política de ponto final de serviço que aplicamos na sub-rede. 

Saia da sessão SSH para o *myVmPublic* VM.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, utilize [o grupo AZ para remover](/cli/azure) o grupo de recursos e todos os recursos que contém.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aplicou uma política de ponto final de serviço sobre um ponto final de serviço de rede virtual Azure ao Azure Storage. Criou contas de Armazenamento Azure e acesso limitado à rede a determinadas contas de armazenamento (e assim negou outras) a partir de uma sub-rede de rede virtual. Para saber mais sobre as políticas de ponto final de serviço, consulte [a visão geral das políticas de pontos finais](virtual-network-service-endpoint-policies-overview.md)do Serviço .
