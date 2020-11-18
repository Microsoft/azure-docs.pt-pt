---
title: incluir ficheiro
description: incluir ficheiro
services: azure-communication-services
author: dademath
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: a084295aec2cafadd07d47e85a0116a89d37c985
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94816794"
---
Inicie-se com os Serviços de Comunicação Azure utilizando a biblioteca de clientes SMS dos Serviços de Comunicação C# PARA enviar mensagens SMS.

Completar este quickstart incorre num pequeno custo de alguns usd ou menos na sua conta Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Package (NuGet)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- A versão mais recente [.NET Core biblioteca de clientes](https://dotnet.microsoft.com/download/dotnet-core) para o seu sistema operativo.
- Um recurso ativo dos Serviços de Comunicação e cadeia de ligação. [Criar um recurso de Serviços de Comunicação.](../../create-communication-resource.md)
- Um número de telefone por SMS habilitado. [Obter um número de telefone.](../get-phone-number.md)

### <a name="prerequisite-check"></a>Verificação pré-requisito

- Numa janela de terminal ou comando, verifique `dotnet` se a biblioteca do cliente .NET está instalada.
- Para visualizar os números de telefone associados ao seu recurso serviços de comunicação, inscreva-se no [portal Azure,](https://portal.azure.com/)localize o seu recurso de Serviços de Comunicação e abra o separador **números** de telefone a partir do painel de navegação à esquerda.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome `SmsQuickstart` . Este comando cria um projeto simples "Hello World" C# com um único ficheiro de origem: **Program.cs**.

```console
dotnet new console -o SmsQuickstart
```

Mude o seu diretório para a pasta de aplicação recém-criada e use o `dotnet build` comando para compilar a sua aplicação.

```console
cd SmsQuickstart
dotnet build
```

### <a name="install-the-package"></a>Instale o pacote

Enquanto ainda está no diretório de aplicações, instale a biblioteca de clientes Azure Communication Services SMS para pacote .NET utilizando o `dotnet add package` comando.

```console
dotnet add package Azure.Communication.Sms --version 1.0.0-beta.3
```

Adicione uma `using` diretiva ao topo de **Program.cs** para incluir o espaço `Azure.Communication` de nome.

```csharp

using Azure.Communication;
using Azure.Communication.Sms;

```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características da biblioteca de clientes dos Serviços de Comunicação Azure para C#.

| Nome                                       | Descrição                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SmsClient     | Esta classe é necessária para toda a funcionalidade SMS. Você instantaneamente com as suas informações de subscrição, e use-as para enviar mensagens SMS.                           |
| Envios Opções | Esta classe oferece opções para configurar relatórios de entrega. Se enable_delivery_report está definido para True, então um evento será emitido quando a entrega foi bem sucedida |

## <a name="authenticate-the-client"></a>Autenticar o cliente

 Abra **Program.cs** num editor de texto e substitua o corpo do `Main` método por código para inicializar um com `SmsClient` a sua cadeia de ligação. O código abaixo recupera a cadeia de ligação para o recurso a partir de uma variável ambiental chamada `COMMUNICATION_SERVICES_CONNECTION_STRING` . Saiba como gerir a [cadeia de ligação dos recursos.](../../create-communication-resource.md#store-your-connection-string)


```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");

SmsClient smsClient = new SmsClient(connectionString);
```

## <a name="send-an-sms-message"></a>Enviar uma mensagem SMS

Envie uma mensagem SMS chamando o método Enviar. Adicione este código ao fim do `Main` método em **Program.cs:**

```csharp
smsClient.Send(
    from: new PhoneNumber("<leased-phone-number>"),
    to: new PhoneNumber("<to-phone-number>"),
    message: "Hello World via SMS",
    new SendSmsOptions { EnableDeliveryReport = true } // optional
);
```

Deverá substituir `<leased-phone-number>` por um número de telefone por SMS associado ao seu recurso de Serviços de Comunicação e `<to-phone-number>` pelo número de telefone a que deseja enviar uma mensagem.

O `EnableDeliveryReport` parâmetro é um parâmetro opcional que pode utilizar para configurar relatórios de entrega. Isto é útil para cenários em que pretende emitir eventos quando as mensagens SMS são entregues. Consulte o [Punho SMS Events](../handle-sms-events.md) quickstart para configurar relatórios de entrega para as suas mensagens SMS.

## <a name="run-the-code"></a>Executar o código

Executar o pedido do seu diretório de candidaturas com o `dotnet run` comando.

```console
dotnet run
```

## <a name="sample-code"></a>Código de Exemplo

Você pode baixar o aplicativo de amostra do [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/SendSMS)
