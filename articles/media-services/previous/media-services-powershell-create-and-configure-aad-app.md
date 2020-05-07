---
title: Use a PowerShell para criar uma app Azure AD para aceder à API dos Serviços de Mídia Azure [ Microsoft Docs
description: Aprenda a usar o PowerShell para criar uma aplicação Azure Ative Directory (Azure AD) e instale-a para aceder à API azure Media Services.
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
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 02cc2ddc793906ff79cbb8619a43d54d696b42ed
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594088"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Use a PowerShell para criar uma app Azure AD para usar com a API Azure Media Services

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [a orientação de migração da v2 para a v3](../latest/migrate-from-v2-to-v3.md)

Saiba como usar um script PowerShell para criar uma aplicação e serviço de serviço azure Ative Directory (Azure AD) para aceder aos recursos da Azure Media Services.  

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se não tiver uma conta, comece com um [teste gratuito azure.](https://azure.microsoft.com/pricing/free-trial/) 
- Uma conta dos Media Services. Para mais informações, consulte [Criar uma conta Azure Media Services no portal Azure.](media-services-portal-create-account.md)

- Azure PowerShell. Para mais informações, consulte [Como utilizar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-an-azure-ad-app-by-using-powershell"></a>Crie uma aplicação Azure AD utilizando o PowerShell  

```powershell
Connect-AzAccount
Import-Module Az.Resources
Set-AzContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

Para obter mais informações, veja os artigos seguintes:

- [Utilize o Azure PowerShell para criar um principal de serviço para aceder aos recursos](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [Gerir o Controlo de Acesso baseado em funções utilizando o Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Como configurar manualmente as aplicações daemon utilizando certificados](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)

## <a name="next-steps"></a>Passos seguintes

Começa com [o upload de ficheiros para a sua conta.](media-services-portal-upload-files.md)
