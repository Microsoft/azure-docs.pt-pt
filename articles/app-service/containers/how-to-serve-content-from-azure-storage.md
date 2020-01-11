---
title: Fornecer conteúdo do armazenamento do Azure para contêineres do Linux
description: Saiba como anexar um compartilhamento de rede personalizado ao seu contêiner do Linux no serviço Azure App. Compartilhar arquivos entre aplicativos, gerenciar conteúdo estático remotamente e acessar localmente, etc.
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: 0a1e811787a43be76f94b13a6ec9886510c47d1d
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75866962"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Fornecer conteúdo do armazenamento do Azure no serviço de aplicativo no Linux

Este guia mostra como anexar o armazenamento do Azure ao serviço de aplicativo no Linux. Os benefícios incluem conteúdo protegido, portabilidade de conteúdo, armazenamento persistente, acesso a vários aplicativos e vários métodos de transferência.


> [!IMPORTANT]
> O armazenamento do Azure no serviço de aplicativo no Linux é um recurso de **Visualização** . **Não há suporte para esse recurso em cenários de produção**.
>

## <a name="prerequisites"></a>Pré-requisitos

- [CLI do Azure](/cli/azure/install-azure-cli) (2.0.46 ou posterior).
- Um [serviço de aplicativo existente no aplicativo Linux](https://docs.microsoft.com/azure/app-service/containers/).
- Uma [conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- Um [compartilhamento de arquivos do Azure e um diretório](https://docs.microsoft.com/azure/storage/common/storage-azure-cli#create-and-manage-file-shares).


## <a name="limitations-of-azure-storage-with-app-service"></a>Limitações do armazenamento do Azure com o serviço de aplicativo

- O armazenamento do Azure com o serviço de aplicativo está **em versão prévia** para o serviço de aplicativo no Linux e aplicativo Web para contêineres. **Não há suporte** para **cenários de produção**.
- O armazenamento do Azure com o serviço de aplicativo dá suporte à montagem de **contêineres de arquivos do Azure** (leitura/gravação) e **contêineres de blob do Azure**
- O armazenamento do Azure com o serviço de aplicativo **não dá suporte** ao uso da configuração de **Firewall de armazenamento** devido a limitações de infraestrutura.
- O armazenamento do Azure com o serviço de aplicativo permite especificar **até cinco** pontos de montagem por aplicativo.
- O armazenamento do Azure **não está incluído** no seu aplicativo Web e é cobrado separadamente. Saiba mais sobre os [preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage).

> [!WARNING]
> As configurações do serviço de aplicativo usando o armazenamento de BLOBs do Azure serão lidas somente em fevereiro de 2020. [Saiba mais](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>Configurar seu aplicativo com o armazenamento do Azure

Depois de criar sua [conta de armazenamento do Azure, o compartilhamento de arquivos e o diretório](#prerequisites), agora você pode configurar seu aplicativo com o armazenamento do Azure.

Para montar uma conta de armazenamento em um diretório em seu aplicativo do serviço de aplicativo, use o comando [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) . O tipo de armazenamento pode ser AzureBlob ou AzureFiles. AzureFiles é usado neste exemplo.


> [!CAUTION]
> O diretório especificado como o caminho de montagem em seu aplicativo Web deve estar vazio. Qualquer conteúdo armazenado nesse diretório será excluído quando uma montagem externa for adicionada. Se você estiver migrando arquivos para um aplicativo existente, faça um backup de seu aplicativo e seu conteúdo antes de começar.
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Você deve fazer isso para todos os outros diretórios que deseja que sejam vinculados a uma conta de armazenamento.

## <a name="verify-azure-storage-link-to-the-web-app"></a>Verificar o link do armazenamento do Azure para o aplicativo Web

Depois que um contêiner de armazenamento é vinculado a um aplicativo Web, você pode verificar isso executando o seguinte comando:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>Usar o armazenamento do Azure no Docker Compose

O armazenamento do Azure pode ser montado com aplicativos de vários contêineres usando a ID personalizada. Para exibir o nome de ID personalizado, execute [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list).

No arquivo *Docker-Compose. yml* , mapeie a opção `volumes` para `custom-id`. Por exemplo:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Passos seguintes

- [Configurar aplicativos Web no serviço Azure app](../configure-common.md).

