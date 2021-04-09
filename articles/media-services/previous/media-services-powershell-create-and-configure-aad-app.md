---
title: Use o PowerShell para criar uma aplicação AD Azure para aceder à Azure Media Services API | Microsoft Docs
description: Aprenda a usar o PowerShell para criar uma aplicação Azure Ative Directory (Azure AD) e instale-a para aceder à AZure Media Services API.
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
ms.openlocfilehash: d16c2bbb16a19e5cb22b2b2b0378880ec9aa48b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009662"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Use o PowerShell para criar uma app AD Azure para usar com a Azure Media Services API

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Saiba como usar um script PowerShell para criar uma aplicação e um diretor de serviço Azure Ative (Azure AD) para aceder aos recursos do Azure Media Services.  

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se não tem conta, comece com um [julgamento gratuito do Azure.](https://azure.microsoft.com/pricing/free-trial/) 
- Uma conta dos Media Services. Para mais informações, consulte [Criar uma conta Azure Media Services no portal Azure](media-services-portal-create-account.md).

- Azure PowerShell. Para obter mais informações, [consulte Como utilizar a Azure PowerShell](/powershell/azure/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-an-azure-ad-app-by-using-powershell"></a>Crie uma aplicação AD AZure utilizando o PowerShell  

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

Para obter mais informações, veja os seguintes artigos:

- [Utilize o Azure PowerShell para criar um principal de serviço para aceder aos recursos](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [Utilizar o Azure PowerShell para adicionar ou remover atribuições de funções do Azure](../../role-based-access-control/role-assignments-powershell.md)
- [Como configurar manualmente aplicações daemon usando certificados](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)

## <a name="next-steps"></a>Passos seguintes

Começa com [o upload de ficheiros para a tua conta.](media-services-portal-upload-files.md)
