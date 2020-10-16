---
title: Adicionar armazenamento Azure (recipiente)
description: Saiba como anexar a partilha de rede personalizada numa aplicação contentorizada no Azure App Service. Partilhe ficheiros entre apps, gerencie o conteúdo estático remotamente e aceda localmente, etc.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: d435a33ba45daf2c8a6a42e51c3e0d58f3abc23b
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057761"
---
# <a name="access-azure-storage-as-a-network-share-from-a-container-in-app-service"></a>Aceder ao Armazenamento do Microsoft Azure como uma partilha de rede de um contentor no Serviço de Aplicações

::: zone pivot="container-windows"

Este guia mostra como anexar os Ficheiros de Armazenamento Azure como uma partilha de rede a um contentor de janelas no Serviço de Aplicações. Apenas [a Azure Files Shares](../storage/files/storage-how-to-use-files-cli.md) e Premium Files [Shares](../storage/files/storage-how-to-create-premium-fileshare.md) são suportadas. Os benefícios incluem conteúdo seguro, portabilidade do conteúdo, acesso a várias aplicações e múltiplos métodos de transferência.

::: zone-end

::: zone pivot="container-linux"

Este guia mostra como ligar o Azure Storage a um Serviço de Aplicações para contentores Linux. Os benefícios incluem conteúdo seguro, portabilidade do conteúdo, armazenamento persistente, acesso a várias aplicações e múltiplos métodos de transferência.

::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

::: zone pivot="container-windows"

- [Uma aplicação existente do Windows Container no Azure App Service](quickstart-custom-container.md)
- [Criar partilha de ficheiros Azure](../storage/files/storage-how-to-use-files-cli.md)
- [Faça upload de ficheiros para a partilha de ficheiros Azure](../storage/files/storage-files-deployment-guide.md)

::: zone-end

::: zone pivot="container-linux"

- Um serviço de aplicações existente [na aplicação Linux.](index.yml)
- Uma [conta de armazenamento Azure](../storage/common/storage-account-create.md?tabs=azure-cli)
- Uma [partilha de ficheiros Azure e um diretório](../storage/files/storage-how-to-use-files-cli.md).

::: zone-end

> [!NOTE]
> O Azure Files é um armazenamento não padrão e faturado separadamente, não incluído na aplicação web. Não suporta a utilização da configuração firewall devido a limitações de infraestrutura.
>

## <a name="limitations"></a>Limitações

::: zone pivot="container-windows"

- O Azure Storage in App Service está **em pré-visualização** e **não é suportado** para **cenários de produção**.
- A Azure Storage in App Service não é atualmente **suportada** para trazer os seus próprios cenários de código (aplicações windows não contentorizadas).
- O Azure Storage in App Service **não suporta** a utilização da configuração de Firewall de **Armazenamento** devido a limitações de infraestrutura.
- O Azure Storage com Serviço de Aplicações permite especificar **até cinco** pontos de montagem por aplicação.
- O Azure Storage montado numa aplicação não é acessível através de pontos finais ftp/FTPs do Serviço de Aplicações. Utilize [o Explorador de Armazenamento Azure](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

::: zone pivot="container-linux"

- O Azure Storage in App Service está **em pré-visualização** para o Serviço de Aplicações em Linux e Web App para Contentores. Não é **apoiado** para cenários de **produção.**
- O Azure Storage in App Service suporta a montagem **de recipientes Azure Files** (Ler/ Escrever) e **recipientes Azure Blob** (Read Only)
- O Azure Storage in App Service permite especificar **até cinco** pontos de montagem por aplicação.
- O Azure Storage montado numa aplicação não é acessível através de pontos finais ftp/FTPs do Serviço de Aplicações. Utilize [o Explorador de Armazenamento Azure](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

## <a name="link-storage-to-your-app"></a>Ligue o armazenamento à sua aplicação

::: zone pivot="container-windows"

Uma vez criada a sua [conta de Armazenamento Azure, partilha de ficheiros e diretório,](#prerequisites)pode agora configurar a sua aplicação com o Azure Storage.

Para montar um Azure Files Share para um diretório na sua aplicação App Service, utilize o [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) comando. O tipo de armazenamento deve ser AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory of form c:<directory name> >
```

Deve fazê-lo para quaisquer outros diretórios que pretenda estar ligado a uma partilha da Azure Files.

::: zone-end

::: zone pivot="container-linux"

Uma vez criada a sua [conta de Armazenamento Azure, partilha de ficheiros e diretório,](#prerequisites)pode agora configurar a sua aplicação com o Azure Storage.

Para montar uma conta de armazenamento num diretório na sua aplicação App Service, utilize o [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) comando. O Tipo de Armazenamento pode ser AzureBlob ou AzureFiles. AzureFiles é usado neste exemplo. A regulação do caminho de montagem corresponde à pasta no interior do recipiente que pretende montar no Azure Storage. Defini-lo para '/' monta todo o recipiente para o Azure Storage.


> [!CAUTION]
> O diretório especificado como o caminho de montagem na sua aplicação web deve estar vazio. Qualquer conteúdo armazenado neste diretório será eliminado quando for adicionado um suporte externo. Se estiver a migrar ficheiros para uma aplicação existente, faça uma cópia de segurança da sua aplicação e do seu conteúdo antes de começar.
>

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

Deve fazer isso para quaisquer outros diretórios que queira estar ligado a uma conta de armazenamento.

::: zone-end

## <a name="verify-linked-storage"></a>Verificar armazenamento ligado

Uma vez que a partilha esteja ligada à aplicação, pode verificar isso executando o seguinte comando:

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

## <a name="next-steps"></a>Passos seguintes

::: zone pivot="container-windows"

- [Migrar o software personalizado para o Azure App Service utilizando um recipiente personalizado.](tutorial-custom-container.md?pivots=container-windows)

::: zone-end

::: zone pivot="container-linux"

- [Configure um recipiente personalizado.](configure-custom-container.md?pivots=platform-linux)

::: zone-end
