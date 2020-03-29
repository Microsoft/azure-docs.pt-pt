---
title: Sondagens operações de longa duração Microsoft Docs
description: A Azure Media Services oferece APIs que enviam pedidos aos Serviços de Media para iniciar operações (por exemplo, criar, iniciar, parar ou apagar um canal), estas operações são de longa duração. Este tópico mostra como fazer sondagens de longa duração.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 43d9a6adc935010eab6e5e52d73f2019c8afcf5f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887187"
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Entrega de streaming ao vivo com serviços de mídia azure

## <a name="overview"></a>Descrição geral

O Microsoft Azure Media Services oferece APIs que enviam pedidos aos Serviços de Media para iniciar operações (por exemplo: criar, iniciar, parar ou apagar um canal). Estas operações são de longa data.

O Media Services .NET SDK fornece APIs que enviam o pedido e aguardam que a operação esteja concluída (internamente, as APIs estão a votar para o progresso da operação em alguns intervalos). Por exemplo, quando liga para o canal. Início(), o método retorna após o início do canal. Também pode utilizar a versão assíncrona: aguarde o canal. StartAsync() (para informações sobre o Padrão Assíncrono baseado em Tarefas, ver [TAP](https://msdn.microsoft.com/library/hh873175\(v=vs.110\).aspx)). As APIs que enviam um pedido de operação e depois fazem sondagem para o estado até que a operação esteja concluída são chamadas de "métodos de sondagem". Estes métodos (especialmente a versão Async) são recomendados para aplicações de clientes ricos e/ou serviços imponentes.

Há cenários em que uma candidatura não pode esperar por um pedido de http de longa duração e quer fazer sondagens para o progresso da operação manualmente. Um exemplo típico seria um navegador interagindo com um serviço web apátrida: quando o navegador solicita a criação de um canal, o serviço web inicia uma operação de longa duração e devolve o ID de operação ao navegador. O navegador poderia então pedir ao serviço web para obter o estado de operação com base no ID. O Media Services .NET SDK fornece APIs que são úteis para este cenário. Estas APIs são chamadas de "métodos não-eleitorais".
Os "métodos não-eleitorais" têm o seguinte padrão de nomeação: Enviar Operação*Operação Nome*(por exemplo, Operação EnviarCriar). Enviar*Métodos*de operação Operação Operação Devolva o objeto **IOperação;** o objeto devolvido contém informações que podem ser usadas para rastrear a operação. Os*métodos OperationName OperationAsync*devolvem **a tarefa\<IOperação>**.

Atualmente, as seguintes classes apoiam métodos de não-sondagem: **Canal,** **StreamingEndpoint**, e **Programa**.

Para fazer uma sondagem sobre o estado de funcionamento, utilize o método **GetOperation** na classe **OperationBaseCollection.** Utilize os seguintes intervalos para verificar o estado de funcionamento: para as operações **do Canal** e **do StreamingEndpoint,** utilize 30 segundos; para operações de **programa,** utilize 10 segundos.

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET).

## <a name="example"></a>Exemplo

O exemplo seguinte define uma classe chamada **ChannelOperations**. Esta definição de classe pode ser um ponto de partida para a definição da sua classe de serviço web. Para a simplicidade, os seguintes exemplos utilizam as versões não-assinizadas dos métodos.

O exemplo também mostra como o cliente pode usar esta classe.

### <a name="channeloperations-class-definition"></a>Definição de classe ChannelOperations

```csharp
using Microsoft.WindowsAzure.MediaServices.Client;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.Net;

/// <summary> 
/// The ChannelOperations class only implements 
/// the Channel’s creation operation. 
/// </summary> 
public class ChannelOperations
{
    // Read values from the App.config file.
    private static readonly string _AADTenantDomain =
        ConfigurationManager.AppSettings["AMSAADTenantDomain"];
    private static readonly string _RESTAPIEndpoint =
        ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
    private static readonly string _AMSClientId =
        ConfigurationManager.AppSettings["AMSClientId"];
    private static readonly string _AMSClientSecret =
        ConfigurationManager.AppSettings["AMSClientSecret"];

    // Field for service context.
    private static CloudMediaContext _context = null;

    public ChannelOperations()
    {
        AzureAdTokenCredentials tokenCredentials = 
            new AzureAdTokenCredentials(_AADTenantDomain,
                new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                AzureEnvironments.AzureCloudEnvironment);

        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
    }

    /// <summary>  
    /// Initiates the creation of a new channel.  
    /// </summary>  
    /// <param name="channelName">Name to be given to the new channel</param>  
    /// <returns>  
    /// Operation Id for the long running operation being executed by Media Services. 
    /// Use this operation Id to poll for the channel creation status. 
    /// </returns> 
    public string StartChannelCreation(string channelName)
    {
        var operation = _context.Channels.SendCreateOperation(
            new ChannelCreationOptions
            {
                Name = channelName,
                Input = CreateChannelInput(),
                Preview = CreateChannelPreview(),
                Output = CreateChannelOutput()
            });

        return operation.Id;
    }

    /// <summary> 
    /// Checks if the operation has been completed. 
    /// If the operation succeeded, the created channel Id is returned in the out parameter.
    /// </summary> 
    /// <param name="operationId">The operation Id.</param> 
    /// <param name="channel">
    /// If the operation succeeded, 
    /// the created channel Id is returned in the out parameter.</param>
    /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
    public bool IsCompleted(string operationId, out string channelId)
    {
        IOperation operation = _context.Operations.GetOperation(operationId);
        bool completed = false;

        channelId = null;

        switch (operation.State)
        {
            case OperationState.Failed:
                // Handle the failure. 
                // For example, throw an exception. 
                // Use the following information in the exception: operationId, operation.ErrorMessage.
                break;
            case OperationState.Succeeded:
                completed = true;
                channelId = operation.TargetEntityId;
                break;
            case OperationState.InProgress:
                completed = false;
                break;
        }
        return completed;
    }

    private static ChannelInput CreateChannelInput()
    {
        return new ChannelInput
        {
            StreamingProtocol = StreamingProtocol.RTMP,
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelInput001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelPreview CreateChannelPreview()
    {
        return new ChannelPreview
        {
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelPreview001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelOutput CreateChannelOutput()
    {
        return new ChannelOutput
        {
            Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
        };
    }
}
```

### <a name="the-client-code"></a>O código do cliente

```csharp
ChannelOperations channelOperations = new ChannelOperations();
string opId = channelOperations.StartChannelCreation("MyChannel001");

string channelId = null;
bool isCompleted = false;

while (isCompleted == false)
{
    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
    isCompleted = channelOperations.IsCompleted(opId, out channelId);
}

// If we got here, we should have the newly created channel id.
Console.WriteLine(channelId);
```

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

