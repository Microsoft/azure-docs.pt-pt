---
title: Restringir a exfiltração de dados ao Armazenamento Azure - Azure CLI
description: Neste artigo, aprende-se a limitar e restringir a exfiltração de dados de rede virtual aos recursos do Azure Storage com políticas de ponto final de serviço de rede virtual utilizando o Azure CLI.
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
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: e04c23f6f27561c2108c97d4def77361a9c50834
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78253003"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-the-azure-cli"></a>Gerir a exfiltração de dados para contas de Armazenamento Azure com políticas de ponto final do serviço de rede virtual utilizando o Azure CLI

As políticas de endpoint do serviço de rede virtual permitem-lhe aplicar o controlo de acesso nas contas do Armazenamento Do Azure a partir de uma rede virtual sobre pontos finais de serviço. Esta é uma chave para garantir as suas cargas de trabalho, gerir quais as contas de armazenamento permitidas e onde a exfiltração de dados é permitida.
Neste artigo, vai aprender a:

* Crie uma rede virtual e adicione uma subrede.
* Ativar o ponto final de serviço para o Armazenamento Azure.
* Crie duas contas de Armazenamento Azure e permita o acesso à rede a partir da subnet acima criada.
* Crie uma política de ponto final de serviço para permitir o acesso apenas a uma das contas de armazenamento.
* Desloque uma máquina virtual (VM) para a sub-rede.
* Confirme o acesso à conta de armazenamento permitida a partir da subnet.
* Confirme que o acesso é negado à conta de armazenamento não permitida a partir da subnet.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este início rápido requer a execução da versão 2.0.28 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

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
  --subnet-name Private \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Ativar um ponto final de serviço 

Neste exemplo, é criado um ponto final de serviço para a *Microsoft.Storage* para a subnet *Private:* 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.0.0/24 \
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

Cada grupo de segurança da rede contém várias regras de [segurança padrão](security-overview.md#default-security-rules). A regra que segue substitui uma regra de segurança padrão que permite o acesso de saída a todos os endereços IP públicos. A opção `destination-address-prefix "Internet"` nega o acesso de saída a todos os endereços IP públicos. A regra anterior anula esta regra, devido à sua maior prioridade, que permite o acesso aos endereços IP públicos do Armazenamento Azure.

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

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Restringir o acesso à rede às contas de Armazenamento Azure

Esta secção enumera etapas para restringir o acesso à rede de uma conta de Armazenamento Azure a partir da subnet dada numa rede Virtual através de ponto final de serviço.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie duas contas de armazenamento Azure com a criação de uma conta de [armazenamento Az.](/cli/azure/storage/account)

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

Após a criação das contas de armazenamento, recupere a cadeia de ligação para as contas de armazenamento numa variável com conta de [armazenamento az mostrar-string de conexão](/cli/azure/storage/account). A cadeia de ligação é usada para criar uma partilha de ficheiros num passo posterior.

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

<a name="account-key"></a>Veja o conteúdo da variável e note o valor do **AccountKey** devolvido na saída, porque é usado num passo posterior.

```azurecli-interactive
echo $saConnectionString1

echo $saConnectionString2
```

### <a name="create-a-file-share-in-the-storage-account"></a>Criar uma partilha de ficheiros na conta de Armazenamento

Crie uma parte de arquivo na conta de armazenamento com a criação de partilha de [armazenamento az](/cli/azure/storage/share). Num passo posterior, esta partilha de ficheiros é montada para confirmar o acesso à rede.

```azurecli-interactive
az storage share create \
  --name my-file-share1 \
  --quota 2048 \
  --connection-string $saConnectionString1 > /dev/null

az storage share create \
  --name my-file-share2 \
  --quota 2048 \
  --connection-string $saConnectionString2 > /dev/null
```

### <a name="deny-all-network-access-to-the-storage-account"></a>Negar todo o acesso da rede à conta de armazenamento

Por predefinição, as contas de Armazenamento aceitam ligações de rede de clientes em qualquer rede. Para limitar o acesso a redes selecionadas, altere a ação padrão para *Negar* com a atualização da conta de [armazenamento az](/cli/azure/storage/account). Uma vez negado o acesso à rede, a conta de armazenamento não é acessível a partir de nenhuma rede.

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

### <a name="enable-network-access-from-virtual-network-subnet"></a>Ativar o acesso à rede a partir de subnet de rede virtual

Permitir o acesso da rede à conta de armazenamento a partir da subnet *privada* com adição de regras de rede de arma de [armazenamento az](/cli/azure/storage/account/network-rule).

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

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Aplicar política para permitir o acesso a conta de armazenamento válida

As políticas do Azure Service Endpoint só estão disponíveis para o Armazenamento Azure. Portanto, vamos ativar o Ponto Final do Serviço para *a Microsoft.Storage* nesta subnet para esta configuração por exemplo.

As políticas de ponto final de serviço são aplicadas sobre os pontos finais do serviço. Começaremos por criar uma política de ponto final de serviço. Criaremos então as definições políticas ao abrigo desta política para as contas de Armazenamento Azure para serem listadas de branco para esta subrede

Criar uma política de ponto final de serviço

```azurecli-interactive
az network service-endpoint policy create \
  --resource-group myResourceGroup \
  --name mysepolicy \
  --location eastus
```

Guarde o recurso URI para a conta de armazenamento permitida numa variável. Antes de executar o comando abaixo, substitua *\<sua subscrição-id>* com o valor real do seu ID de subscrição.

```azurecli-interactive
$serviceResourceId="/subscriptions/<your-subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/allowedstorageacc"
```

Criar & adicionar uma definição de política para permitir a conta de Armazenamento Azure acima à política de ponto final de serviço

```azurecli-interactive
az network service-endpoint policy-definition create \
  --resource-group myResourceGroup \
  --policy-name mysepolicy \
  --name mypolicydefinition \
  --service "Microsoft.Storage" \
  --service-resources $serviceResourceId
```

E atualizar a subnet da rede virtual para associar-lhe a política de ponto final de serviço criada no passo anterior

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

Para testar o acesso da rede a uma conta de armazenamento, desloque um VM para a subnet.

Crie um VM na subnet *privada* com [az vm criar](/cli/azure/vm). Se as chaves SSH ainda não existirem numa localização de chaves predefinida, o comando cria-as. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.

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

### <a name="confirm-access-to-storage-account"></a>Confirmar o acesso à conta de Armazenamento

SSH no *myVmPrivate* VM. Substitua *\<publicaIpAddress>* com o endereço IP público do seu *myVmPrivate* VM.

```bash 
ssh <publicIpAddress>
```

Crie uma pasta para um ponto de montagem:

```bash
sudo mkdir /mnt/MyAzureFileShare1
```

Monte a partilha de ficheiros Azure para o diretório que criou. Antes de executar o comando abaixo, substitua *\<chave&chave de armazenamento>* com o valor do *AccountKey* a partir de **$saConnectionString1**.

```bash
sudo mount --types cifs //allowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare1 --options vers=3.0,username=allowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Recebes o `user@myVmPrivate:~$` pronta. A partilha de ficheiros Azure montada com sucesso para */mnt/MyAzureFileShare*.

### <a name="confirm-access-is-denied-to-storage-account"></a>Confirmar que o acesso à conta de Armazenamento é negado

A partir do mesmo *VM myVmPrivate,* crie um diretório para um ponto de montagem:

```bash
sudo mkdir /mnt/MyAzureFileShare2
```

Tente montar a partilha de ficheiros Azure a partir da conta de armazenamento *não permitida storageacc* para o diretório que criou. Este artigo assume que implementou a versão mais recente de Ubuntu. Se estiver a utilizar versões anteriores de Ubuntu, consulte [o Mount on Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter instruções adicionais sobre a montagem de partilhas de ficheiros. 

Antes de executar o comando abaixo, substitua\<chave *de conta de armazenamento>* com o valor do *AccountKey* a partir de **$saConnectionString2**.

```bash
sudo mount --types cifs //notallowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare2 --options vers=3.0,username=notallowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

O acesso é negado e recebe um erro `mount error(13): Permission denied`, porque esta conta de armazenamento não está na lista de autorização da política de ponto final de serviço que aplicamos à subnet. 

Saia da sessão SSH para o *myVmPublic* VM.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, utilize o [grupo Az eliminar](/cli/azure) para remover o grupo de recursos e todos os recursos que contém.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aplicou uma política de ponto final de serviço sobre um ponto final de serviço de rede virtual Azure para o Armazenamento Azure. Criou contas de Armazenamento Azure e acesso limitado à rede apenas a determinadas contas de armazenamento (e assim negou outras) a partir de uma subnet de rede virtual. Para saber mais sobre as políticas de ponto final de serviço, consulte a visão geral das políticas de [pontos finais](virtual-network-service-endpoint-policies-overview.md)do Serviço.
