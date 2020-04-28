---
title: Utilize o Azure CLI para criar uma aplicação Azure AD e configurá-la para aceder à API dos Serviços de Mídia Azure [ API] Microsoft Docs
description: Este tópico mostra como usar o Azure CLI para criar uma aplicação Azure AD e configurá-la para aceder à API azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: juliako
ms.openlocfilehash: f136fb666e93adc0fe92aee014e3da9a37bbd6aa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "70035801"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Utilize o Azure CLI para criar uma aplicação Azure AD e configurá-la para aceder à API dos Serviços de Media 

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [a orientação de migração da v2 para a v3](../latest/migrate-from-v2-to-v3.md)

Este tópico mostra-lhe como usar o Azure CLI para criar uma aplicação e serviço de serviço azure Ative Directory (Azure AD) para aceder aos recursos da Azure Media Services. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Para mais detalhes, consulte [o teste gratuito do Azure.](https://azure.microsoft.com/pricing/free-trial/) 
- Uma conta dos Media Services. Para mais informações, consulte [Criar uma conta Azure Media Services utilizando o portal Azure](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Use a casca de nuvem azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Lance a Cloud Shell a partir do painel de navegação superior do portal.

    ![Cloud Shell,](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Para mais informações, consulte [a visão geral da Casca de Nuvem Azure](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Crie uma aplicação Azure AD e configure o acesso à conta de mídia com o Azure CLI
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> 
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Por exemplo:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

Neste exemplo, o **âmbito** é o caminho completo dos recursos para a conta de serviços de comunicação social. No entanto, o **âmbito** pode estar em qualquer nível.

Por exemplo, pode ser um dos seguintes níveis:
 
* O nível de **subscrição.**
* O nível do **grupo de recursos.**
* O nível de **recursos** (por exemplo, uma conta Media).

Para mais informações, consulte Criar um diretor de [serviço Azure com o Azure CLI](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)

Consulte também a gestão do [Controlo de Acesso baseado em funções com a interface de linha de comando Azure](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Passos seguintes

Começa com [o upload de ficheiros para a sua conta.](media-services-portal-upload-files.md)
