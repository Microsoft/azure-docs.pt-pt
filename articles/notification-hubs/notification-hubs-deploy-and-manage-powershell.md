---
title: Implementar e Gerir Hubs de Notificação com o PowerShell
description: Como criar e gerir os Hubs de notificação com o PowerShell para automação
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 4dbbaeea736dd46478ad9992201ea28bd7bfc2ba
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57855508"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Implementar e gerir os hubs de notificação com o PowerShell

## <a name="overview"></a>Descrição geral

Este artigo mostra-lhe como utilizar a criar e gerir Notification Hubs do Azure com o PowerShell. As seguintes tarefas de automatização comuns são mostradas neste artigo.

- Criar um Hub de notificação
- Definir credenciais

Se também tem de criar um novo namespace de barramento de serviço para os hubs de notificação, veja [gerir o Service Bus com o PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Gerir os Hubs de notificação não é suportado diretamente por cmdlets incluídos com o Azure PowerShell. É a melhor abordagem do PowerShell referenciar o assembly de Microsoft.Azure.NotificationHubs.dll. O assembly é distribuído com o [pacote NuGet de Hubs de notificação do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. O Azure é uma plataforma baseada na subscrição. Para obter mais informações sobre como obter uma subscrição, veja [opções de compra], [ofertas para membros], ou [versão de avaliação gratuita].
- Um computador com o Azure PowerShell. Para obter instruções, consulte [instalar e configurar o Azure PowerShell].
- Uma compreensão geral de scripts do PowerShell, pacotes NuGet e o .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Inclusão de uma referência ao assembly do .NET do Service Bus

Gerir os Hubs de notificação do Azure ainda não está incluído com os cmdlets do PowerShell do Azure PowerShell. Para aprovisionar os hubs de notificação, pode utilizar o cliente .NET fornecido a [pacote NuGet de Hubs de notificação do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Primeiro, certifique-se de que o seu script pode localizar o **Microsoft.Azure.NotificationHubs.dll** assembly, que é instalado como um pacote do NuGet num projeto do Visual Studio. Para que seja flexível, o script executa estes passos:

1. Determina o caminho em que foi invocado.
2. Atravessa o caminho até encontrar uma pasta denominada `packages`. Esta pasta é criada quando instala os pacotes de NuGet para projetos do Visual Studio.
3. Pesquisas de recursivamente a `packages` pasta para um assembly denominado `Microsoft.Azure.NotificationHubs.dll`.
4. Referencia a assemblagem para que os tipos estão disponíveis para utilização posterior.

Eis como estes passos são implementados num script do PowerShell:

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

## <a name="create-the-namespacemanager-class"></a>Criar o `NamespaceManager` classe

Para aprovisionar os Hubs de notificação, crie uma instância do [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx) classe do SDK.

Pode utilizar o [Get-AzureSBAuthorizationRule] cmdlet incluído com o Azure PowerShell para obter uma regra de autorização é utilizada para fornecer uma cadeia de ligação. Uma referência para o `NamespaceManager` instância é armazenada no `$NamespaceManager` variável. `$NamespaceManager` é utilizado para aprovisionar um hub de notificação.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>Aprovisionamento de um novo Hub de notificação

Para aprovisionar um novo hub de notificação, utilize o [.NET API dos Hubs de notificação de].

Nesta parte do script, defina quatro variáveis locais.

1. `$Namespace`: Defina esta opção para o nome do espaço de nomes onde pretende criar um hub de notificação.
2. `$Path`: Defina este caminho para o nome do novo hub de notificação.  Por exemplo, "MyHub".
3. `$WnsPackageSid`: Defina esta opção para o SID para a sua aplicação do Windows do pacote do [Windows Dev Center](https://developer.microsoft.com/en-us/windows).
4. `$WnsSecretkey`: Defina esta opção para a chave secreta para a aplicação Windows a partir da [Windows Dev Center](https://developer.microsoft.com/en-us/windows).

Estas variáveis são utilizadas para ligar ao seu espaço de nomes e criar um novo Hub de notificação configurado para processar as notificações de serviços de notificação Windows (WNS) com as credenciais WNS para uma aplicação do Windows. Para obter informações sobre como obter o pacote do SID e consulte chave secreta, o [introdução aos Hubs de notificação](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) tutorial.

- O fragmento de script utiliza o `NamespaceManager` objeto a verificar para ver se o Hub de notificação identificado pelo `$Path` existe.
- Se não existir, o script cria `NotificationHubDescription` com o WNS credenciais e transmite-o para o `NamespaceManager` classe `CreateNotificationHub` método.

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
- [Como criar filas, tópicos e subscrições através de um script do PowerShell do Service Bus](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Como criar um espaço de nomes do Service Bus e um Hub de eventos utilizando um script do PowerShell](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Alguns scripts prontas a utilizar também estão disponíveis para download:

- [Scripts do PowerShell do Service Bus](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[Opções de compra]: https://azure.microsoft.com/pricing/purchase-options/
[Ofertas para membros]: https://azure.microsoft.com/pricing/member-offers/
[Versão de avaliação gratuita]: https://azure.microsoft.com/pricing/free-trial/
[instalar e configurar o Azure PowerShell]: /powershell/azureps-cmdlets-docs.
[.NET API dos Hubs de notificação de]: https://docs.microsoft.com/dotnet/api/overview/azure/notification-hubs?view=azure-dotnet
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbnamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule
