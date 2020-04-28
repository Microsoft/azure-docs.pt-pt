---
title: Implementar e gerir centros de notificação usando powerShell
description: Como criar e gerir centros de notificação usando powerShell para automação
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
ms.openlocfilehash: 863fdb445cce41f0fe4cbee63a3d6198c0a79339
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76264649"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Implementar e gerir centros de notificação usando o PowerShell

## <a name="overview"></a>Descrição geral

Este artigo mostra-lhe como usar Hubs de Notificação Create e Manage Azure usando powerShell. Neste artigo são apresentadas as seguintes tarefas comuns de automação.

- Criar um Centro de Notificação
- Definir credenciais

Se também precisa de criar um novo espaço de nome de autocarro de serviço para os seus centros de notificação, consulte [Manage Service Bus com PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Gerir os Centros de Notificações não é suportado diretamente pelos cmdlets incluídos com o Azure PowerShell. A melhor abordagem da PowerShell é fazer referência ao conjunto Microsoft.Azure.NotificationHubs.dll. O conjunto é distribuído com o [pacote NuGet do Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. O Azure é uma plataforma baseada em subscrições. Para obter mais informações sobre a obtenção de uma subscrição, consulte Opções de [Compra,] [Ofertas de Membros]ou [Teste Gratuito.]
- Um computador com Azure PowerShell. Para obter instruções, consulte [Instalar e configurar o Azure PowerShell].
- Uma compreensão geral dos scripts PowerShell, pacotes NuGet e o .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Incluindo uma referência à montagem .NET para ônibus de serviço

Gerir os Hubs de Notificação Azure ainda não está incluído com os cmdlets PowerShell em Azure PowerShell. Para fornecer centros de notificação, pode utilizar o cliente .NET fornecido no pacote NuGet microsoft [Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Em primeiro lugar, certifique-se de que o seu script pode localizar o conjunto **Microsoft.Azure.NotificationHubs.dll,** que está instalado como um pacote NuGet num projeto do Estúdio Visual. Para ser flexível, o guião executa estes passos:

1. Determina o caminho em que foi invocado.
2. Percorre o caminho até encontrar `packages`uma pasta chamada . Esta pasta é criada quando instala pacotes NuGet para projetos do Estúdio Visual.
3. Procura recursivamente a `packages` pasta para `Microsoft.Azure.NotificationHubs.dll`um conjunto chamado .
4. Refere o conjunto de modo a que os tipos estejam disponíveis para posterior utilização.

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

## <a name="create-the-namespacemanager-class"></a>Criar `NamespaceManager` a classe

Para fornecer Centros de Notificação, crie uma instância da classe [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager?view=azure-dotnet) a partir do SDK.

Pode utilizar o [cmdlet Get-AzureSBAuthorizationRule] incluído com o Azure PowerShell para recuperar uma regra de autorização que é usada para fornecer uma cadeia de ligação. Uma referência `NamespaceManager` à instância é `$NamespaceManager` armazenada na variável. `$NamespaceManager`é utilizado para fornecer um centro de notificação.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>Provisionamento de um novo Centro de Notificação

Para fornecer um novo centro de notificação, utilize a [API .NET para Centros]de Notificação .

Nesta parte do guião, criaste quatro variáveis locais.

1. `$Namespace`: Desloque-o ao nome do espaço de nome onde pretende criar um centro de notificação.
2. `$Path`: Desloque este caminho para o nome do novo centro de notificação.  Por exemplo, "MyHub".
3. `$WnsPackageSid`: Detete isto no pacote SID para a sua aplicação windows a partir do [Windows Dev Center](https://developer.microsoft.com/en-us/windows).
4. `$WnsSecretkey`: Detete isto na chave secreta da sua aplicação Windows a partir do [Windows Dev Center](https://developer.microsoft.com/en-us/windows).

Estas variáveis são usadas para se conectarem ao seu espaço de nome e criarem um novo Centro de Notificação configurado para lidar com notificações dos Serviços de Notificação do Windows (WNS) com credenciais WNS para uma Aplicação Windows. Para obter informações sobre a obtenção do pacote SID e chave secreta ver, o [tutorial Getting Started with Notification Hubs.](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)

- O script snippet `NamespaceManager` usa o objeto para verificar se `$Path` o Centro de Notificação identificado existe.
- Se não existir, o `NotificationHubDescription` script cria com credenciais `NamespaceManager` WNS e passa-o para o método de classe. `CreateNotificationHub`

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

- [Gerir o Barramento de Serviço com o PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
- [Como criar filas de ônibus de serviço, tópicos e subscrições usando um script PowerShell](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Como criar um espaço de nome de ônibus de serviço e um hub de eventousando um script PowerShell](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Alguns scripts prontos também estão disponíveis para download:

- [Scripts PowerShell de ônibus de serviço](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[Opções de Compra]: https://azure.microsoft.com/pricing/purchase-options/
[Ofertas de Membros]: https://azure.microsoft.com/pricing/member-offers/
[Julgamento Gratuito]: https://azure.microsoft.com/pricing/free-trial/
[Instalar e configurar o Azure PowerShell]: /powershell/azureps-cmdlets-docs
[.NET API para Centros de Notificação]: https://docs.microsoft.com/dotnet/api/overview/azure/notification-hubs?view=azure-dotnet
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbnamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule
