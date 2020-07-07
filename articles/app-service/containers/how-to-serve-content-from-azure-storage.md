---
title: Sirva o conteúdo do Azure Storage para os contentores Linux
description: Saiba como anexar a partilha de rede personalizada ao seu contentor Linux no Azure App Service. Partilhe ficheiros entre apps, gerencie o conteúdo estático remotamente e aceda localmente, etc.
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: 9a5a38ea32d927f50fb9ddbebe3e1c3533e6fcc0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82625328"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Servir conteúdo do Azure Storage in App Service no Linux

> [!NOTE]
> Este artigo aplica-se aos contentores Linux. Para implementar para recipientes windows personalizados, consulte ficheiros [Configure Azure num recipiente windows no Serviço de Aplicações.](../configure-connect-to-azure-storage.md) O Azure Storage in App Service no Linux é uma funcionalidade **de pré-visualização.** Esta funcionalidade não é **suportada para cenários de produção.**
>

Este guia mostra como anexar o Azure Storage ao Serviço de Aplicações no Linux. Os benefícios incluem conteúdo seguro, portabilidade do conteúdo, armazenamento persistente, acesso a várias aplicações e múltiplos métodos de transferência.

## <a name="prerequisites"></a>Pré-requisitos

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 ou mais tarde).
- Um serviço de aplicações existente [na aplicação Linux.](https://docs.microsoft.com/azure/app-service/containers/)
- Uma [conta de armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- Uma [partilha de ficheiros Azure e um diretório](../../storage/files/storage-how-to-use-files-cli.md).


## <a name="limitations-of-azure-storage-with-app-service"></a>Limitações do Armazenamento Azure com Serviço de Aplicações

- O Azure Storage with App Service está **em pré-visualização** para o Serviço de Aplicações em Linux e Web App para Contentores. Não é **apoiado** para cenários de **produção.**
- O Azure Storage with App Service suporta a montagem **de recipientes Azure Files** (Ler/ Escrever) e **recipientes Azure Blob** (Read Only)
- O Azure Storage with App Service **não suporta** a utilização da configuração de Firewall de **Armazenamento** devido a limitações de infraestrutura.
- O Azure Storage com Serviço de Aplicações permite especificar **até cinco** pontos de montagem por aplicação.
- O Azure Storage montado numa aplicação não é acessível através de pontos finais ftp/FTPs do Serviço de Aplicações. Utilize [o explorador de armazenamento Azure](https://azure.microsoft.com/features/storage-explorer/).
- O Azure Storage não está **incluído** na sua aplicação web e faturado separadamente. Saiba mais sobre [os preços do Azure Storage](https://azure.microsoft.com/pricing/details/storage).

> [!WARNING]
> As configurações do Serviço de Aplicações que utilizam o Azure Blob Storage só serão lidas em fevereiro de 2020. [Saiba mais](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>Configure a sua app com o Azure Storage

Uma vez criada a sua [conta de Armazenamento Azure, partilha de ficheiros e diretório,](#prerequisites)pode agora configurar a sua aplicação com o Azure Storage.

Para montar uma conta de armazenamento num diretório na sua aplicação App Service, utilize o [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) comando. O Tipo de Armazenamento pode ser AzureBlob ou AzureFiles. AzureFiles é usado neste exemplo. A definição do caminho de montagem corresponde à pasta que pretende montar a partir do Azure Storage. Definindo-o para '/' monta todo o Azure Storage.


> [!CAUTION]
> O diretório especificado como o caminho de montagem na sua aplicação web deve estar vazio. Qualquer conteúdo armazenado neste diretório será eliminado quando for adicionado um suporte externo. Se estiver a migrar ficheiros para uma aplicação existente, faça uma cópia de segurança da sua aplicação e do seu conteúdo antes de começar.
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Deve fazer isso para quaisquer outros diretórios que queira estar ligado a uma conta de armazenamento.

## <a name="verify-azure-storage-link-to-the-web-app"></a>Verifique a ligação de armazenamento Azure para a aplicação web

Uma vez que um recipiente de armazenamento esteja ligado a uma aplicação web, pode verificar isso executando o seguinte comando:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>Use o armazenamento Azure em Docker Compose

O Azure Storage pode ser montado com aplicações multi-contentores utilizando o id personalizado. Para ver o nome de identificação personalizada, corra [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list) .

No seu ficheiro *docker-compose.yml,* mapear a `volumes` opção para `custom-id` . Por exemplo:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Passos seguintes

- [Configure aplicativos web no Azure App Service](../configure-common.md).

