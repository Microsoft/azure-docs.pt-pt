---
title: Implementar e gerir os centros de notificação utilizando o PowerShell
description: Como criar e gerir os Centros de Notificação utilizando o PowerShell para automação
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
ms.openlocfilehash: 4534584144f54618d7f3dd39cf5e40bc0464fb21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102454990"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Implementar e gerir os centros de notificação utilizando o PowerShell

## <a name="overview"></a>Descrição Geral

Este artigo mostra-lhe como utilizar Criar e Gerir os Centros de Notificação Azure utilizando o PowerShell. Neste artigo são apresentadas as seguintes tarefas comuns de automação.

- Criar um centro de notificação
- Definir credenciais

Se também precisar de criar um novo espaço de nome de ônibus de serviço para os seus centros de notificação, consulte [Manage Service Bus com PowerShell](../service-bus-messaging/service-bus-manage-with-ps.md).

O Managing Notifications Hubs não é suportado diretamente pelos cmdlets incluídos na Azure PowerShell. A melhor abordagem da PowerShell é fazer referência à montagem Microsoft.Azure.NotificationHubs.dll. O conjunto é distribuído com o [pacote NuGet dos Hubs de Notificação do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. A Azure é uma plataforma baseada em subscrições. Para obter mais informações sobre a obtenção de uma subscrição, consulte [Opções de Compra,] [Ofertas de Membros]ou [Teste Gratuito.]
- Um computador com a Azure PowerShell. Para obter instruções, consulte [instalar e configurar a Azure PowerShell].
- Uma compreensão geral dos scripts PowerShell, pacotes NuGet e o Quadro .NET.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Incluindo uma referência à montagem .NET para Service Bus

A gestão dos hubs de notificação do Azure ainda não está incluída com os cmdlets PowerShell em Azure PowerShell. Para fornecer centros de notificação, pode utilizar o cliente .NET fornecido no [pacote NuGet do Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Em primeiro lugar, certifique-se de que o seu script pode localizar o conjunto **Microsoft.Azure.NotificationHubs.dll,** que está instalado como um pacote NuGet num projeto do Visual Studio. Para ser flexível, o script executa estes passos:

1. Determina o caminho em que foi invocado.
2. Percorre o caminho até encontrar uma pasta chamada `packages` . Esta pasta é criada quando instala pacotes NuGet para projetos de Estúdio Visual.
3. Procura novamente a `packages` pasta por um conjunto denominado `Microsoft.Azure.NotificationHubs.dll` .
4. Refere o conjunto de modo a que os tipos estejam disponíveis para utilização posterior.

Eis como estes passos são implementados num script PowerShell:

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

Para providenciar Centros de Notificação, crie uma instância da classe [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) a partir do SDK.

Pode utilizar o [cmdlet Get-AzureSBAuthorizationRule] incluído com a Azure PowerShell para recuperar uma regra de autorização que é usada para fornecer uma cadeia de ligação. Uma referência ao `NamespaceManager` caso é armazenada na `$NamespaceManager` variável. `$NamespaceManager` é utilizado para a provisionar um centro de notificação.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>Provisionamento de um novo centro de notificação

Para obter um novo centro de notificação, utilize a [API .NET para centros de notificação].

Nesta parte do guião, cria-se quatro variáveis locais.

1. `$Namespace`: Desa esta atenção ao nome do espaço de nome onde pretende criar um centro de notificação.
2. `$Path`: Definir este caminho para o nome do novo centro de notificação.  Por exemplo, "MyHub".
3. `$WnsPackageSid`: Desaprova isto no pacote SID para a sua Aplicação Windows a partir do [Windows Dev Center](https://developer.microsoft.com/en-us/windows).
4. `$WnsSecretkey`: Desaprova isto na chave secreta da sua aplicação para o Windows a partir do [Windows Dev Center](https://developer.microsoft.com/en-us/windows).

Estas variáveis são usadas para ligar ao seu espaço de nome e criar um novo Centro de Notificações configurado para lidar com notificações do Windows Notification Services (WNS) com credenciais WNS para uma App Windows. Para obter informações sobre a obtenção do pacote SID e chave secreta ver, o tutorial De Início com Centros de [Notificação.](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)

- O corte de script usa o `NamespaceManager` objeto para verificar se o Centro de Notificação identificado `$Path` existe.
- Se não existir, o script cria `NotificationHubDescription` com credenciais WNS e passa-o para o método de `NamespaceManager` `CreateNotificationHub` classe.

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

- [Gerir o Barramento de Serviço com o PowerShell](../service-bus-messaging/service-bus-manage-with-ps.md)
- [Como criar filas, tópicos e subscrições de Service Bus usando um script PowerShell](/archive/blogs/paolos/how-to-create-service-bus-queues-topics-and-subscriptions-using-a-powershell-script)
- [Como criar um espaço de nome de ônibus de serviço e um centro de eventos usando um script PowerShell](/archive/blogs/paolos/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script)

Alguns scripts prontos também estão disponíveis para download:

- [Scripts powershell de ônibus de serviço](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[Opções de Compra]: https://azure.microsoft.com/pricing/purchase-options/
[Ofertas de Membros]: https://azure.microsoft.com/pricing/member-offers/
[Avaliação Gratuita]: https://azure.microsoft.com/pricing/free-trial/
[Instalar e configurar o Azure PowerShell]: /powershell/azure/
[.NET API para centros de notificação]: /dotnet/api/overview/azure/notification-hubs
[Get-AzureSBNamespace]: /powershell/module/servicemanagement/azure.service/get-azuresbnamespace
[New-AzureSBNamespace]: /powershell/module/servicemanagement/azure.service/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: /powershell/module/servicemanagement/azure.service/get-azuresbauthorizationrule
