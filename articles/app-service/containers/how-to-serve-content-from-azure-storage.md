---
title: Sirva conteúdo do Armazenamento Azure para os contentores Linux
description: Saiba como anexar a partilha personalizada da rede ao seu recipiente Linux no Serviço de Aplicações Azure. Partilhe ficheiros entre apps, gerencie conteúdo estática remotamente e aceda localmente, etc.
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: 9a5a38ea32d927f50fb9ddbebe3e1c3533e6fcc0
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82625328"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Sirva conteúdo do Azure Storage no Serviço de Aplicações em Linux

> [!NOTE]
> Este artigo aplica-se aos contentores Linux. Para implementar para personalizar os recipientes do Windows, consulte [configure Azure Files num recipiente windows no serviço de aplicações](../configure-connect-to-azure-storage.md). O Azure Storage no Serviço de Aplicações no Linux é uma funcionalidade de **pré-visualização.** Esta funcionalidade não é **suportada para cenários**de produção.
>

Este guia mostra como anexar o Azure Storage ao Serviço de Aplicações no Linux. Os benefícios incluem conteúdo seguro, portabilidade de conteúdo, armazenamento persistente, acesso a várias aplicações e múltiplos métodos de transferência.

## <a name="prerequisites"></a>Pré-requisitos

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 ou mais tarde).
- Um serviço de aplicações existente [na aplicação Linux.](https://docs.microsoft.com/azure/app-service/containers/)
- Uma [conta de armazenamento azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- Uma [partilha de ficheiros Azure e um diretório.](../../storage/files/storage-how-to-use-files-cli.md)


## <a name="limitations-of-azure-storage-with-app-service"></a>Limitações do armazenamento azure com serviço de aplicações

- O Azure Storage com App Service está **em pré-visualização** para o Serviço de Aplicações no Linux e na Web App para contentores. Não é **suportado** para cenários de **produção.**
- O Armazenamento Azure com App Service suporta a montagem de **contentores Azure Files** (Ler/ Escrever) e **os contentores Azure Blob** (Ler Apenas)
- O Azure Storage com o App Service **não suporta** a utilização da configuração de Firewall de **Armazenamento** devido a limitações de infraestrutura.
- O Azure Storage com app Service permite especificar **até cinco** pontos de montagem por app.
- O Armazenamento Azure montado numa aplicação não é acessível através de pontos finais ftp/FTPs do Serviço de Aplicação. Utilize o explorador de [armazenamento Azure.](https://azure.microsoft.com/features/storage-explorer/)
- O Azure Storage não está **incluído** na sua aplicação web e faturado separadamente. Saiba mais sobre os preços do [Armazenamento Azure.](https://azure.microsoft.com/pricing/details/storage)

> [!WARNING]
> As configurações do Serviço de Aplicações utilizando o Armazenamento De Blob Azure tornar-se-ão lidas apenas em fevereiro de 2020. [Mais informações](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>Configure a sua aplicação com armazenamento azure

Depois de ter criado a sua conta De armazenamento, partilha de [ficheiros e diretório,](#prerequisites)agora pode configurar a sua aplicação com o Armazenamento Azure.

Para montar uma conta de armazenamento num diretório na [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) sua aplicação App Service, utiliza o comando. O Tipo de Armazenamento pode ser AzureBlob ou AzureFiles. O AzureFiles é utilizado neste exemplo. A definição do caminho de montagem corresponde à pasta que gostaria de montar a partir do Armazenamento Azure. Defini-lo para '/' monta todo o Armazenamento Azure.


> [!CAUTION]
> O diretório especificado como o caminho de montagem na sua aplicação web deve estar vazio. Qualquer conteúdo armazenado neste diretório será eliminado quando for adicionado um suporte externo. Se estiver a migrar ficheiros para uma aplicação existente, faça uma cópia de segurança da sua aplicação e do seu conteúdo antes de começar.
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Deve fazer isso por quaisquer outros diretórios que queira estar ligado a uma conta de armazenamento.

## <a name="verify-azure-storage-link-to-the-web-app"></a>Verifique o link de armazenamento azure para a aplicação web

Uma vez que um recipiente de armazenamento esteja ligado a uma aplicação web, pode verificar isso executando o seguinte comando:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>Use armazenamento azure em Docker Compose

O Armazenamento Azure pode ser montado com aplicações multi-contentores utilizando o id personalizado. Para ver o nome de [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list)identificação personalizada, corra .

No seu ficheiro *docker-compose.yml,* mapeie a opção `volumes` para `custom-id`. Por exemplo:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Passos seguintes

- [Configure aplicações web no Azure App Service](../configure-common.md).

