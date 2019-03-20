---
title: Operações de longa execução de consulta | Documentos da Microsoft
description: Este tópico mostra como consultar as operações de longa execução.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 9a68c4b1-6159-42fe-9439-a3661a90ae03
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 518cc1d55a83d95daec8f22d0dcfc5db23cc2d38
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58173843"
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Fornecimento de transmissão em fluxo em direto com os serviços de multimédia do Azure

## <a name="overview"></a>Descrição geral

Serviços de multimédia do Microsoft Azure oferece APIs que enviam pedidos para serviços de multimédia para iniciar operações (por exemplo: criar, iniciar, parar ou eliminar um canal). Essas operações são de execução longa.

O SDK .NET dos Media Services fornece APIs que enviar a solicitação e aguarde pela conclusão da operação (internamente, as APIs são de consulta para o progresso da operação em alguns intervalos). Por exemplo, quando chama o canal. Start(), o método retorna depois do canal está iniciado. Também pode utilizar a versão assíncrona: await canal. StartAsync() (para obter informações sobre o padrão assíncrono baseado em tarefas, consulte [TOQUE](https://msdn.microsoft.com/library/hh873175\(v=vs.110\).aspx)). "Métodos de consulta" são chamadas de APIs que enviar um pedido de operação e, em seguida, consultar o estado até que a operação esteja concluída. Esses métodos (especialmente a versão de Async) são recomendados para aplicativos cliente sofisticados e/ou serviços com estado.

Existem cenários onde um aplicativo não é possível aguardar por um pedido de http de longa execução e quer consultar o progresso da operação manualmente. Um exemplo típico seria um navegador da interagir com um serviço web sem monitorização de estado: quando o browser pede para criar um canal, o serviço web inicia uma operação de longa execução e retorna a ID de operação para o navegador. O browser, em seguida, pode pedir o web service para obter o estado da operação com base no ID do. O SDK .NET dos Media Services fornece APIs que são úteis para este cenário. Essas APIs são chamadas de "métodos de consulta não".
"Métodos de consulta não" tem o seguinte padrão de nomenclatura: Envie*OperationName*operação (por exemplo, SendCreateOperation). Envie*OperationName*métodos de operação retornar a **IOperation** objeto; retornado objeto contém informações que podem ser utilizadas para controlar a operação. O envio*OperationName*OperationAsync métodos retornam **tarefa<IOperation>**.

Atualmente, as seguintes classes de suportam a métodos de consulta não:  **Canal**, **StreamingEndpoint**, e **programa**.

Para consultar o estado da operação, utilize o **GetOperation** método no **OperationBaseCollection** classe. Utilize os seguintes intervalos para verificar o estado da operação: para **canal** e **StreamingEndpoint** operações, utilize a 30 segundos; para **programa** operações, utilize a 10 segundos.

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET).

## <a name="example"></a>Exemplo

O exemplo seguinte define uma classe chamada **ChannelOperations**. Esta definição de classe pode ser um ponto de partida para a sua definição de classe de serviço web. Para simplificar, os exemplos seguintes utilizam as versões não assíncronas dos métodos.

O exemplo também mostra como o cliente poderá utilizar esta classe.

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

### <a name="the-client-code"></a>O código de cliente

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

