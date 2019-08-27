---
title: Use CLI do Azure para criar um aplicativo do Azure AD e configurá-lo para acessar a API dos serviços de mídia do Azure | Microsoft Docs
description: Este tópico mostra como usar o CLI do Azure para criar um aplicativo do Azure AD e configurá-lo para acessar a API dos serviços de mídia do Azure.
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
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035801"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Use CLI do Azure para criar um aplicativo do Azure AD e configurá-lo para acessar a API dos serviços de mídia 

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Veja a versão mais recente, [Serviços de Multimédia v3](https://docs.microsoft.com/azure/media-services/latest/). Além disso, consulte [diretrizes de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)

Este tópico mostra como usar o CLI do Azure para criar um aplicativo do Azure Active Directory (Azure AD) e uma entidade de serviço para acessar os recursos dos serviços de mídia do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Para obter mais detalhes, veja [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Uma conta dos Media Services. Para obter mais informações, consulte [criar uma conta dos serviços de mídia do Azure usando o portal do Azure](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Usar o Azure Cloud Shell

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Inicie o Cloud Shell no painel de navegação superior do Portal.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Para obter mais informações, consulte [visão geral do Azure cloud Shell](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Criar um aplicativo do Azure AD e configurar o acesso à conta de mídia com CLI do Azure
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> 
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Por exemplo:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

Neste exemplo, o **escopo** é o caminho completo do recurso para a conta dos serviços de mídia. No entanto, o **escopo** pode estar em qualquer nível.

Por exemplo, pode ser um dos seguintes níveis:
 
* O nível da **assinatura** .
* O nível do **grupo de recursos** .
* O nível de **recurso** (por exemplo, uma conta de mídia).

Para obter mais informações, consulte [criar uma entidade de serviço do Azure com o CLI do Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)

Consulte também [gerenciar o controle de acesso baseado em função com a interface de linha de comando do Azure](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Passos seguintes

Comece a [carregar arquivos em sua conta](media-services-portal-upload-files.md).
