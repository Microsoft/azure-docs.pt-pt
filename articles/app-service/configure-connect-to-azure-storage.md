---
title: Adicionar armazenamento personalizado (recipiente Windows)
description: Saiba como anexar a partilha de rede personalizada num recipiente personalizado do Windows no Azure App Service. Partilhe ficheiros entre apps, gerencie o conteúdo estático remotamente e aceda localmente, etc.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 64ef4dfe81e6415f1285a74962e2123507715119
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77120680"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Configurar ficheiros Azure num recipiente do Windows no Serviço de Aplicações

> [!NOTE]
> Este artigo aplica-se a recipientes Windows personalizados. Para implementar no Serviço de Aplicações no _Linux,_ consulte [o Conteúdo do Azure.](./containers/how-to-serve-content-from-azure-storage.md)
>

Este guia mostra como aceder ao Azure Storage em Recipientes Windows. Apenas [a Azure Files Shares](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) e Premium Files [Shares](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) são suportadas. Utiliza ações da Azure Files nesta forma de fazer. Os benefícios incluem conteúdo seguro, portabilidade do conteúdo, acesso a várias aplicações e múltiplos métodos de transferência.

## <a name="prerequisites"></a>Pré-requisitos

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 ou mais tarde).
- [Uma aplicação existente do Windows Container no Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Criar partilha de ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Faça upload de ficheiros para a partilha de ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> O Azure Files é um armazenamento não padrão e faturado separadamente, não incluído na aplicação web. Não suporta a utilização da configuração firewall devido a limitações de infraestrutura.
>

## <a name="limitations"></a>Limitações

- O Azure Storage em recipientes Windows está **em pré-visualização** e **não é suportado** para **cenários de produção**.
- O Azure Storage em recipientes Windows suporta a montagem de **recipientes Azure Files** (Ler/ Escrever) apenas.
- O Azure Storage em contentores windows não é atualmente **suportado** para trazer os seus próprios cenários de código nos planos do Windows App Service.
- O Azure Storage em recipientes Windows **não suporta** a utilização da configuração de Firewall de **Armazenamento** devido a limitações de infraestrutura.
- O Azure Storage em recipientes Windows permite especificar **até cinco** pontos de montagem por aplicação.
- O Azure Storage montado numa aplicação não é acessível através de pontos finais ftp/FTPs do Serviço de Aplicações. Utilize [o explorador de armazenamento Azure](https://azure.microsoft.com/features/storage-explorer/).
- O Azure Storage é faturado de forma independente e **não incluído** na sua aplicação web. Saiba mais sobre [os preços do Azure Storage](https://azure.microsoft.com/pricing/details/storage).

## <a name="link-storage-to-your-web-app-preview"></a>Ligue o armazenamento à sua aplicação web (pré-visualização)

 Para montar um Azure Files Share para um diretório na sua aplicação App Service, utilize o [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) comando. O tipo de armazenamento deve ser AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Deve fazê-lo para quaisquer outros diretórios que pretenda estar ligado a uma partilha da Azure Files.

## <a name="verify"></a>Verificar

Uma vez que uma partilha de Ficheiros Azure esteja ligada a uma aplicação web, pode verificar isso executando o seguinte comando:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="next-steps"></a>Próximos passos

- [Migrar uma aplicação ASP.NET para o Azure App Service utilizando um recipiente Windows (Preview)](app-service-web-tutorial-windows-containers-custom-fonts.md).
