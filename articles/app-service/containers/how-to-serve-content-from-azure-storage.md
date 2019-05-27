---
title: Ofereça conteúdo a partir do armazenamento do Azure no Linux - serviço de aplicações
description: Como configurar e servir conteúdo a partir do armazenamento do Azure no serviço de aplicações do Azure no Linux.
author: msangapu
manager: jeconnoc
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 2/04/2019
ms.author: msangapu-msft
ms.openlocfilehash: 6b4e145a693aabbf1a00d732e2fd602e7c887a03
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956022"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Ofereça conteúdo a partir do armazenamento do Azure no serviço de aplicações no Linux

Este guia mostra como servir conteúdo estático no serviço de aplicações no Linux utilizando [armazenamento do Azure](/azure/storage/common/storage-introduction). Os benefícios incluem segura conteúda, o conteúdo portabilidade, acesso a várias aplicações e vários métodos de transferência. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma aplicação web existente (serviço de aplicações no Linux ou aplicação Web para contentores).
- [CLI do Azure](/cli/azure/install-azure-cli) (2.0.46 ou posterior).

## <a name="create-azure-storage"></a>Criar o armazenamento do Azure

> [!NOTE]
> O armazenamento do Azure é o armazenamento de não-padrão e faturados em separado, não incluídas com a aplicação web.
>

Criar do Azure [conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli).

```azurecli
#Create Storage Account
az storage account create --name <storage_account_name> --resource-group myResourceGroup

#Create Storage Container
az storage container create --name <storage_container_name> --account-name <storage_account_name>
```

## <a name="upload-files-to-azure-storage"></a>Carregar ficheiros para o armazenamento do Azure

Para carregar um diretório local para a conta de armazenamento, utilize o [ `az storage blob upload-batch` ](https://docs.microsoft.com/cli/azure/storage/blob?view=azure-cli-latest#az-storage-blob-upload-batch) comando semelhante ao seguinte exemplo:

```azurecli
az storage blob upload-batch -d <full_path_to_local_directory> --account-name <storage_account_name> --account-key "<access_key>" -s <source_location_name>
```

## <a name="link-storage-to-your-web-app-preview"></a>Armazenamento de ligação à sua aplicação web (pré-visualização)

> [!CAUTION]
> Ligar um diretório existente numa aplicação web a uma conta de armazenamento, irá eliminar o conteúdo do diretório. Se estiver migrando arquivos para uma aplicação existente, faça uma cópia de segurança da sua aplicação e o respetivo conteúdo antes de começar.
>

Para montar uma conta de armazenamento para um diretório na sua aplicação de serviço de aplicações, utilize o [ `az webapp config storage-account add` ](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) comando. Tipo de armazenamento pode ser AzureBlob ou AzureFiles. Utilize o AzureBlob para esse contentor.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureBlob --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Deve fazê-lo para outros diretórios que pretende ser ligada a uma conta de armazenamento.

## <a name="verify"></a>Validar

Depois de um contentor de armazenamento está ligado a uma aplicação web, pode verificar isto, executando o seguinte comando:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-custom-storage-in-docker-compose"></a>Utilizar o armazenamento personalizado no Docker Compose

O armazenamento do Azure pode ser montado aplicações de vários contentores com o id de personalizada. Para ver o nome do id de personalizada, execute [ `az webapp config storage-account list --name <app_name> --resource-group <resource_group>` ](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list).

No seu *docker-Compose* de arquivos, mapear os `volumes` a opção de `custom-id`. Por exemplo:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Passos Seguintes

- [Configurar aplicações web no App Service do Azure](../configure-common.md).
