---
title: Configurar o armazenamento utilizando ficheiros do Azure
description: Como configurar e ligar aos ficheiros do Azure num contentor do Windows no serviço de aplicações.
author: msangapu-msft
manager: gwallace
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu-msft
ms.openlocfilehash: ea6555e8459b8f372a73d7f943e9a96523d4c791
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789049"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Configurar os ficheiros do Azure num contentor do Windows no serviço de aplicações

> [!NOTE]
> Este artigo aplica-se para os contentores do Windows personalizadas. Para implementar um serviço de aplicações no _Linux_, consulte [servir conteúdo a partir do armazenamento do Azure](./containers/how-to-serve-content-from-azure-storage.md).
>

Este guia mostra como acessar o armazenamento do Azure nos contentores do Windows. Apenas [partilhas de ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) e [partilhas de ficheiros de Premium](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) são suportados. Utilizar partilhas de ficheiros do Azure nesta explicação de procedimento. Os benefícios incluem segura conteúda, o conteúdo portabilidade, acesso a várias aplicações e vários métodos de transferência.

## <a name="prerequisites"></a>Pré-requisitos

- [CLI do Azure](/cli/azure/install-azure-cli) (2.0.46 ou posterior).
- [Uma aplicação de contentor do Windows existente no serviço de aplicações do Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Criar partilha de ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Carregar ficheiros para a partilha de ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Os ficheiros do Azure é o armazenamento de não-padrão e faturados em separado, não incluídas com a aplicação web. Não suporta a utilização de configuração da Firewall devido a limitações de infraestrutura.
>

## <a name="link-storage-to-your-web-app-preview"></a>Armazenamento de ligação à sua aplicação web (pré-visualização)

 Para montar uma partilha de ficheiros do Azure para um diretório na sua aplicação de serviço de aplicações, utilize o [ `az webapp config storage-account add` ](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) comando. Tipo de armazenamento tem de ser AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Deve fazê-lo para partilham de outros diretórios que pretende ser ligada a um serviço ficheiros do Azure.

## <a name="verify"></a>Verificar

Depois de uma partilha de ficheiros do Azure está ligada a uma aplicação web, pode verificar isto, executando o seguinte comando:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```


## <a name="next-steps"></a>Passos Seguintes

- [Migrar uma aplicação ASP.NET para o serviço de aplicações do Azure com um contentor do Windows (pré-visualização)](app-service-web-tutorial-windows-containers-custom-fonts.md).