---
title: Use o Azure CLI para criar uma aplicação AD Azure e configurá-la para aceder à Azure Media Services API | Microsoft Docs
description: Este tópico mostra como usar o Azure CLI para criar uma app AD Azure e configurá-la para aceder à Azure Media Services API.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 6517cc8224a1a4e2e3319fde52fb621e4f7ab564
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016938"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Use o Azure CLI para criar uma app AD Azure e configurá-la para aceder à API dos Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Este tópico mostra-lhe como usar o CLI Azure para criar uma aplicação e um diretor de serviço Azure Ative (Azure AD) para aceder aos recursos da Azure Media Services. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Para mais detalhes, consulte [o julgamento gratuito do Azure.](https://azure.microsoft.com/pricing/free-trial/) 
- Uma conta dos Media Services. Para mais informações, consulte [Criar uma conta Azure Media Services utilizando o portal Azure](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Use a concha da nuvem Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Lance a Cloud Shell a partir do painel de navegação superior do portal.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Para mais informações, consulte [a visão geral da Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Criar uma app AD AZure e configurar o acesso à conta de media com o Azure CLI
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> 
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Por exemplo:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

Neste exemplo, o âmbito é o **caminho** completo dos recursos para a conta dos serviços de comunicação social. No entanto, o **âmbito** pode ser a qualquer nível.

Por exemplo, pode ser um dos seguintes níveis:
 
* O nível **de subscrição.**
* O nível **do grupo de recursos.**
* O nível **de recursos** (por exemplo, uma conta Media).

Para mais informações, consulte [Criar um diretor de serviço Azure com o CLI Azure](/cli/azure/create-an-azure-service-principal-azure-cli)

Consulte também [adicionar ou remover atribuições de funções Azure utilizando O Azure CLI](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Passos seguintes

Começa com [o upload de ficheiros para a tua conta.](media-services-portal-upload-files.md)
