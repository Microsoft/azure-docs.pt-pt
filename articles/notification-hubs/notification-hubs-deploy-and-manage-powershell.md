---
title: Implementar e Gerir Hubs de Notificação com o PowerShell
description: Como criar e gerenciar hubs de notificação usando o PowerShell para automação
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 5af920249000cabbc63f0c9ab453738450875172
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213413"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Implantar e gerenciar hubs de notificação usando o PowerShell

## <a name="overview"></a>Descrição geral

Este artigo mostra como usar criar e gerenciar hubs de notificação do Azure usando o PowerShell. As tarefas comuns de automação a seguir são mostradas neste artigo.

- Criar um hub de notificação
- Definir credenciais

Se você também precisar criar um novo namespace do barramento de serviço para seus hubs de notificação, consulte [gerenciar o barramento de serviço com o PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Não há suporte para o gerenciamento de hubs de notificações diretamente pelos cmdlets incluídos com Azure PowerShell. A melhor abordagem do PowerShell é fazer referência ao assembly Microsoft. Azure. NotificationHubs. dll. O assembly é distribuído com o [hubs de notificações do Microsoft Azure pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. O Azure é uma plataforma baseada na subscrição. Para obter mais informações sobre como obter uma subscrição, veja [opções de compra], [Ofertas de membros], ou [Avaliação gratuita].
- Um computador com Azure PowerShell. Para obter instruções, consulte [instalar e configurar o Azure PowerShell].
- Uma compreensão geral dos scripts do PowerShell, dos pacotes NuGet e do .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Incluindo uma referência ao assembly .NET para o barramento de serviço

O gerenciamento de hubs de notificação do Azure ainda não está incluído com os cmdlets do PowerShell no Azure PowerShell. Para provisionar hubs de notificação, você pode usar o cliente .NET fornecido no [pacote hubs de notificações do Microsoft Azure NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Primeiro, verifique se o seu script pode localizar o assembly **Microsoft. Azure. NotificationHubs. dll** , que é instalado como um pacote NuGet em um projeto do Visual Studio. Para ser flexível, o script executa estas etapas:

1. Determina o caminho no qual ele foi invocado.
2. Percorre o caminho até encontrar uma pasta chamada `packages`. Essa pasta é criada quando você instala pacotes NuGet para projetos do Visual Studio.
3. Pesquisa recursivamente a `packages` pasta em busca de um `Microsoft.Azure.NotificationHubs.dll`assembly denominado.
4. Faz referência ao assembly para que os tipos estejam disponíveis para uso posterior.

Veja como essas etapas são implementadas em um script do PowerShell:

``` powershell
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>Criar a `NamespaceManager` classe

Para provisionar hubs de notificação, crie uma instância da classe [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager?view=azure-dotnet) do SDK.

Você pode usar o cmdlet [Get-AzureSBAuthorizationRule] incluído com Azure PowerShell para recuperar uma regra de autorização que é usada para fornecer uma cadeia de conexão. Uma referência à `NamespaceManager` instância é armazenada `$NamespaceManager` na variável. `$NamespaceManager`é usado para provisionar um hub de notificação.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>Provisionando um novo hub de notificação

Para aprovisionar um novo hub de notificação, utilize o [.NET API dos Hubs de notificação de].

Nesta parte do script, você configura quatro variáveis locais.

1. `$Namespace`: Defina isso para o nome do namespace no qual você deseja criar um hub de notificação.
2. `$Path`: Defina esse caminho para o nome do novo hub de notificação.  Por exemplo, "MyHub".
3. `$WnsPackageSid`: Defina isso para o SID do pacote para seu aplicativo do Windows no [centro de desenvolvimento do Windows](https://developer.microsoft.com/en-us/windows).
4. `$WnsSecretkey`: Defina isso para a chave secreta para seu aplicativo do Windows no [centro de desenvolvimento do Windows](https://developer.microsoft.com/en-us/windows).

Essas variáveis são usadas para se conectar ao namespace e criar um novo hub de notificação configurado para manipular notificações do Windows Notification Services (WNS) com credenciais WNS para um aplicativo do Windows. Para obter informações sobre como obter o SID do pacote e a chave secreta, consulte o tutorial [introdução com hubs de notificação](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .

- O trecho de script usa `NamespaceManager` o objeto para verificar se o Hub de notificação identificado por `$Path` existe.
- Se não existir, o script criará `NotificationHubDescription` com credenciais WNS e a passará para o método de `NamespaceManager` classe `CreateNotificationHub` .

``` powershell
$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```

## <a name="additional-resources"></a>Recursos Adicionais

- [Gerir o Service Bus com o PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
- [Como criar filas, tópicos e assinaturas do barramento de serviço usando um script do PowerShell](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Como criar um namespace do barramento de serviço e um hub de eventos usando um script do PowerShell](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Alguns scripts prontos também estão disponíveis para download:

- [Scripts do PowerShell do barramento de serviço](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[Opções de compra]: https://azure.microsoft.com/pricing/purchase-options/
[Ofertas de membros]: https://azure.microsoft.com/pricing/member-offers/
[Avaliação gratuita]: https://azure.microsoft.com/pricing/free-trial/
[instalar e configurar o Azure PowerShell]: /powershell/azureps-cmdlets-docs.
[.NET API dos Hubs de notificação de]: https://docs.microsoft.com/dotnet/api/overview/azure/notification-hubs?view=azure-dotnet
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbnamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule
