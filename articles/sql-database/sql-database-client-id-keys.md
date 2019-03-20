---
title: Obter os valores para a autenticação da aplicação - base de dados SQL do Azure | Documentos da Microsoft
description: Crie um principal de serviço para aceder à base de dados SQL a partir do código.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 1d60e875b12f02c957ebd6259eb0e7267f23ee51
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57897610"
---
# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>Obter os valores necessários para autenticar uma aplicação para aceder à base de dados SQL a partir do código

Para criar e gerir a base de dados SQL a partir do código tem de registar a sua aplicação no domínio do Azure Active Directory (AAD) na subscrição em que os recursos do Azure foram criados.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Criar um principal de serviço para aceder aos recursos de uma aplicação

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é suportado pelo SQL Database do Azure, mas todo o desenvolvimento futuro é para o módulo de Az.Sql. Para estes cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

O seguinte script do PowerShell cria a aplicação do Active Directory (AD) e o principal de serviço que é necessário para autenticar a nossa aplicação C#. O script produz os valores de necessários para a amostra do C# precedente. Para obter informações detalhadas, consulte [Utilize o Azure PowerShell para criar um principal de serviço para aceder aos recursos](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

    # Sign in to Azure.
    Connect-AzAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create an AAD app
    $azureAdApplication = New-AzADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




## <a name="see-also"></a>Consulte também
* [Criar uma base de dados SQL com c#](sql-database-get-started-csharp.md)
* [Ligar à base de dados SQL com a autenticação do Azure Active Directory](sql-database-aad-authentication.md)

