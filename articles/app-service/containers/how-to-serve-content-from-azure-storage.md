---
title: Fornecer conteúdo do armazenamento do Azure no Linux-serviço de aplicativo
description: Como configurar e fornecer conteúdo do armazenamento do Azure no serviço de Azure App no Linux.
author: msangapu
manager: jeconnoc
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 2/04/2019
ms.author: msangapu
ms.openlocfilehash: 97c03ad294bba1f8a0285fff4595991ca0acc8b5
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018265"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Fornecer conteúdo do armazenamento do Azure no serviço de aplicativo no Linux

Este guia mostra como servir conteúdo estático no serviço de aplicativo no Linux usando o [armazenamento do Azure](/azure/storage/common/storage-introduction). Os benefícios incluem conteúdo protegido, portabilidade de conteúdo, armazenamento persistente, acesso a vários aplicativos e vários métodos de transferência.

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo Web existente (serviço de aplicativo no Linux ou Aplicativo Web para Contêineres).
- [CLI do Azure](/cli/azure/install-azure-cli) (2.0.46 ou posterior).

## <a name="create-azure-storage"></a>Criar armazenamento do Azure

> [!NOTE]
> O armazenamento do Azure é um armazenamento não padrão e cobrado separadamente, não incluído no aplicativo Web.
>
> Traga seu próprio armazenamento não dá suporte ao uso da configuração do firewall de armazenamento devido a limitações de infraestrutura.
>

Crie uma [conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)do Azure Azure.

```azurecli
#Create Storage Account
az storage account create --name <storage_account_name> --resource-group myResourceGroup

#Create Storage Container
az storage container create --name <storage_container_name> --account-name <storage_account_name>
```

## <a name="upload-files-to-azure-storage"></a>Carregar arquivos no armazenamento do Azure

Para carregar um diretório local na conta de armazenamento, use o [`az storage blob upload-batch`](https://docs.microsoft.com/cli/azure/storage/blob?view=azure-cli-latest#az-storage-blob-upload-batch) comando como o exemplo a seguir:

```azurecli
az storage blob upload-batch -d <full_path_to_local_directory> --account-name <storage_account_name> --account-key "<access_key>" -s <source_location_name>
```

## <a name="link-storage-to-your-web-app-preview"></a>Vincular o armazenamento ao seu aplicativo Web (versão prévia)

> [!CAUTION]
> Vincular um diretório existente em um aplicativo Web a uma conta de armazenamento excluirá o conteúdo do diretório. Se você estiver migrando arquivos para um aplicativo existente, faça um backup de seu aplicativo e seu conteúdo antes de começar.
>

Para montar uma conta de armazenamento em um diretório em seu aplicativo do serviço de aplicativo, [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) use o comando. O tipo de armazenamento pode ser AzureBlob ou AzureFiles. Você usa AzureBlob para este contêiner.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureBlob --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Você deve fazer isso para todos os outros diretórios que deseja que sejam vinculados a uma conta de armazenamento.

## <a name="verify"></a>Validar

Depois que um contêiner de armazenamento é vinculado a um aplicativo Web, você pode verificar isso executando o seguinte comando:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-custom-storage-in-docker-compose"></a>Usar armazenamento personalizado no Docker Compose

O armazenamento do Azure pode ser montado com aplicativos de vários contêineres usando a ID personalizada. Para exibir o nome de ID personalizado, execute [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list).

No arquivo *Docker-Compose. yml* , mapeie a `volumes` opção para `custom-id`. Por exemplo:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Passos Seguintes

- [Configurar aplicativos Web no serviço Azure app](../configure-common.md).
