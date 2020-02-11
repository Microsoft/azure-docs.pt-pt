---
title: Adicione armazenamento personalizado (recipiente Windows)
description: Saiba como anexar a partilha personalizada da rede num recipiente personalizado do Windows no Serviço de Aplicações Azure. Partilhe ficheiros entre apps, gerencie conteúdo estática remotamente e aceda localmente, etc.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 64ef4dfe81e6415f1285a74962e2123507715119
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120680"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Configure ficheiros Azure num recipiente do Windows no serviço de aplicações

> [!NOTE]
> Este artigo aplica-se a recipientes windows personalizados. Para ser implantado no Serviço de Aplicações no _Linux,_ consulte [o Serve Content do Armazenamento Azure.](./containers/how-to-serve-content-from-azure-storage.md)
>

Este guia mostra como aceder ao Armazenamento Azure em Recipientes Windows. Apenas as ações de [ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) e [as ações](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) premium são suportadas. Usa ações de ficheiros Azure neste como. Os benefícios incluem conteúdo seguro, portabilidade de conteúdo, acesso a várias aplicações e múltiplos métodos de transferência.

## <a name="prerequisites"></a>Pré-requisitos

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 ou mais tarde).
- [Uma aplicação existente para recipientes windows no Serviço de Aplicações Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Criar partilha de ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Upload de ficheiros para partilha de Ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> O Azure Files é um armazenamento não predefinido e faturado separadamente, não incluído na aplicação web. Não suporta usar a configuração firewall devido a limitações de infraestrutura.
>

## <a name="limitations"></a>Limitações

- O Armazenamento Azure em contentores Windows **encontra-se em pré-visualização** e **não suporta dos** cenários de **produção.**
- O Armazenamento Azure em recipientes Windows suporta a montagem apenas de **recipientes Azure Files** (Ler / Escrever).
- O Armazenamento Azure em contentores Windows não é **suportado** para trazer os seus próprios cenários de código nos planos do Serviço de Aplicações windows.
- O Armazenamento Azure em contentores Windows **não suporta** utilizar a configuração de Firewall de **Armazenamento** devido a limitações de infraestrutura.
- O Armazenamento Azure em recipientes Windows permite especificar **até cinco** pontos de montagem por app.
- O Armazenamento Azure montado numa aplicação não é acessível através de pontos finais ftp/FTPs do Serviço de Aplicação. Utilize o explorador de [armazenamento Azure.](https://azure.microsoft.com/features/storage-explorer/)
- O Azure Storage é faturado de forma independente e **não incluído** na sua aplicação web. Saiba mais sobre os preços do [Armazenamento Azure.](https://azure.microsoft.com/pricing/details/storage)

## <a name="link-storage-to-your-web-app-preview"></a>Link armazenamento para a sua aplicação web (pré-visualização)

 Para montar um Azure Files Share para um diretório na sua aplicação App Service, utiliza o comando [`az webapp config storage-account add`.](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) O Tipo de Armazenamento deve ser AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Deve fazer isto por quaisquer outros diretórios que queira estar ligado a uma partilha de Ficheiros Azure.

## <a name="verify"></a>Verificar

Uma vez que uma partilha de Ficheiros Azure esteja ligada a uma aplicação web, pode verificar isso executando o seguinte comando:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="next-steps"></a>Passos seguintes

- [Migrar uma aplicação ASP.NET para o Serviço de Aplicações Azure utilizando um recipiente Windows (Pré-visualização)](app-service-web-tutorial-windows-containers-custom-fonts.md).
