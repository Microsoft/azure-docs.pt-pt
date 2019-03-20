---
title: Utilizar a CLI do Azure para criar uma aplicação do Azure AD e configurá-lo para aceder à API de serviços de multimédia do Azure | Documentos da Microsoft
description: Este tópico mostra como utilizar a CLI do Azure para criar uma aplicação do Azure AD e configurá-lo para aceder à API de serviços de multimédia do Azure.
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
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 93a77c64e82c39f8f967a3549fafecfae59b194b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993538"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Utilizar a CLI do Azure para criar uma aplicação do Azure AD e configurá-lo para aceder à API de serviços de multimédia 

Este tópico mostra-lhe como utilizar a CLI do Azure para criar uma aplicação do Azure Active Directory (Azure AD) e o principal de serviço para aceder aos recursos de serviços de multimédia do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Para obter mais detalhes, veja [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Uma conta dos Media Services. Para obter mais informações, consulte [criar uma conta de Media Services do Azure com o portal do Azure](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Utilizar o Azure Cloud Shell

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Inicie o Cloud Shell a partir do painel de navegação superior do portal.

    ![Cloud Shell,](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Para obter mais informações, consulte [descrição geral do Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Criar uma aplicação do Azure AD e configurar o acesso à conta de multimédia com a CLI do Azure
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> --password <strong password>
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Por exemplo:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

Neste exemplo, o **âmbito** é o caminho de recurso completo para o suporte de dados conta dos serviços. No entanto, o **âmbito** pode estar em qualquer nível.

Por exemplo, pode ser um dos seguintes níveis:
 
* O **subscrição** nível.
* O **grupo de recursos** nível.
* O **recursos** nível (por exemplo, uma conta de suporte de dados).

Para obter mais informações, consulte [criar um Azure principal de serviço com a CLI do Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)

Consulte também [Manage Role-Based o controlo de acesso com a interface de linha de comandos do Azure](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Passos Seguintes

Introdução ao [carregar ficheiros para a conta](media-services-portal-upload-files.md).
