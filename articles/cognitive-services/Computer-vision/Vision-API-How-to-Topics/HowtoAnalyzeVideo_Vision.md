---
title: Analise vídeos quase em tempo real Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Saiba como executar uma análise quase em tempo real em quadros que são tirados de um fluxo de vídeo ao vivo usando o API da Pesquisa Visual Computacional.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 18b158b7a4881619b93ab404de67f7bb25f92b6a
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166835"
---
# <a name="analyze-videos-in-near-real-time"></a>Analise vídeos quase em tempo real

Este artigo demonstra como executar uma análise quase em tempo real em quadros que são tirados de um fluxo de vídeo ao vivo usando o API da Pesquisa Visual Computacional. Os elementos básicos de tal análise são:

- Adquirindo quadros de uma fonte de vídeo.
- Selecionando os quadros a serem analisados.
- Enviando esses quadros para a API.
- Consumir cada resultado de análise que é retornado da chamada à API.

Os exemplos neste artigo são escritos em C#. Para acessar o código, vá para a página de [exemplo de análise de quadros de vídeo](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) no github.

## <a name="approaches-to-running-near-real-time-analysis"></a>Abordagens para executar análise quase em tempo real

Você pode resolver o problema de executar uma análise quase em tempo real em fluxos de vídeo usando uma variedade de abordagens. Este artigo descreve três deles, em níveis crescentes de sofisticação.

### <a name="design-an-infinite-loop"></a>Criar um loop infinito

O design mais simples para análise quase em tempo real é um loop infinito. Em cada iteração desse loop, você pega um quadro, analisa-o e, em seguida, consome o resultado:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```

Se sua análise fosse consistir em um algoritmo leve do lado do cliente, essa abordagem seria adequada. No entanto, quando a análise ocorre na nuvem, a latência resultante significa que uma chamada à API pode levar vários segundos. Durante esse tempo, você não está capturando imagens, e seu thread, essencialmente, não faz nada. A taxa máxima de quadros é limitada pela latência das chamadas à API.

### <a name="allow-the-api-calls-to-run-in-parallel"></a>Permitir que as chamadas à API sejam executadas em paralelo

Embora um loop simples, de thread único faça sentido para um algoritmo leve do lado do cliente, ele não se ajusta bem à latência de uma chamada à API de nuvem. A solução para esse problema é permitir que a chamada de API de longa execução seja executada em paralelo com a captura de quadros. No C#, você pode fazer isso usando paralelismo baseado em tarefa. Por exemplo, você pode executar o seguinte código:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () =>
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```

Com essa abordagem, você inicia cada análise em uma tarefa separada. A tarefa pode ser executada em segundo plano enquanto você continua a captar novos quadros. A abordagem evita o bloqueio do thread principal à medida que você aguarda uma chamada à API retornar. No entanto, a abordagem pode apresentar determinadas desvantagens:
* Ele custa a você algumas das garantias que a versão simples fornecida. Ou seja, várias chamadas à API podem ocorrer em paralelo, e os resultados podem ser retornados na ordem errada. 
* Ele também pode fazer com que vários threads insiram a função ConsumeResult () simultaneamente, o que pode ser perigoso se a função não for thread-safe. 
* Por fim, esse código simples não mantém o controle das tarefas que são criadas, portanto, as exceções desaparecem silenciosamente. Portanto, você precisa adicionar um thread de "consumidor" que acompanha as tarefas de análise, gera exceções, elimina tarefas de execução longa e garante que os resultados sejam consumidos na ordem correta, um de cada vez.

### <a name="design-a-producer-consumer-system"></a>Criar um sistema produtor-consumidor

Para sua abordagem final, criando um sistema de "produtor-consumidor", você cria um thread de produtor parecido com o loop infinito mencionado anteriormente. No entanto, em vez de consumir os resultados da análise assim que estiverem disponíveis, o produtor simplesmente coloca as tarefas em uma fila para manter o controle delas.

```csharp
// Queue that will contain the API call tasks.
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();

// Producer thread.
while (true)
{
    // Grab a frame.
    Frame f = GrabFrame();

    // Decide whether to analyze the frame.
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread.
        var analysisTask = Task.Run(async () =>
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }

        // Push the task onto the queue.
        taskQueue.Add(analysisTask);
    }
}
```

Você também cria um thread de consumidor, que retira as tarefas da fila, aguarda que elas sejam concluídas e exibe o resultado ou gera a exceção que foi lançada. Usando a fila, você pode garantir que os resultados sejam consumidos um de cada vez, na ordem correta, sem limitar a taxa máxima de quadros do sistema.

```csharp
// Consumer thread.
while (true)
{
    // Get the oldest task.
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Wait until the task is completed.
    var output = await analysisTask;

    // Consume the exception or result.
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implement-the-solution"></a>Implementar a solução

### <a name="get-started-quickly"></a>Início rápido

Para ajudar a colocar seu aplicativo em funcionamento o mais rápido possível, implementamos o sistema descrito na seção anterior. Ele se destina a ser flexível o suficiente para acomodar muitos cenários, embora seja fácil de usar. Para acessar o código, vá para a página de [exemplo de análise de quadros de vídeo](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) no github.

A biblioteca contém a classe `FrameGrabber`, que implementa o sistema do consumidor produtor discutido anteriormente para processar quadros de vídeo de uma webcam. Os usuários podem especificar a forma exata da chamada à API, e a classe usa eventos para permitir que o código de chamada saiba quando um novo quadro é adquirido ou quando um novo resultado de análise está disponível.

Para ilustrar algumas das possibilidades, fornecemos dois aplicativos de exemplo que usam a biblioteca. 

O primeiro aplicativo de exemplo é um aplicativo de console simples que captura quadros da webcam padrão e os envia para o serviço de face para detecção facial. Uma versão simplificada do aplicativo é reproduzida no código a seguir:

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;
using VideoFrameAnalyzer;

namespace BasicConsoleSample
{
    internal class Program
    {
        const string ApiKey = "<your API key>";
        const string Endpoint = "https://<your API region>.api.cognitive.microsoft.com";

        private static async Task Main(string[] args)
        {
            // Create grabber.
            FrameGrabber<DetectedFace[]> grabber = new FrameGrabber<DetectedFace[]>();

            // Create Face Client.
            FaceClient faceClient = new FaceClient(new ApiKeyServiceClientCredentials(ApiKey))
            {
                Endpoint = Endpoint
            };

            // Set up a listener for when we acquire a new frame.
            grabber.NewFrameProvided += (s, e) =>
            {
                Console.WriteLine($"New frame acquired at {e.Frame.Metadata.Timestamp}");
            };

            // Set up a Face API call.
            grabber.AnalysisFunction = async frame =>
            {
                Console.WriteLine($"Submitting frame acquired at {frame.Metadata.Timestamp}");
                // Encode image and submit to Face service.
                return (await faceClient.Face.DetectWithStreamAsync(frame.Image.ToMemoryStream(".jpg"))).ToArray();
            };

            // Set up a listener for when we receive a new result from an API call.
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.TimedOut)
                    Console.WriteLine("API call timed out.");
                else if (e.Exception != null)
                    Console.WriteLine("API call threw an exception.");
                else
                    Console.WriteLine($"New result received for frame acquired at {e.Frame.Metadata.Timestamp}. {e.Analysis.Length} faces detected");
            };

            // Tell grabber when to call the API.
            // See also TriggerAnalysisOnPredicate
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running in the background.
            await grabber.StartProcessingCameraAsync();

            // Wait for key press to stop.
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();

            // Stop, blocking until done.
            await grabber.StopProcessingAsync();
        }
    }
}
```

O segundo aplicativo de exemplo é um pouco mais interessante. Ele permite que você escolha qual API deve ser chamada nos quadros de vídeo. No lado esquerdo, o aplicativo mostra uma visualização do vídeo ao vivo. À direita, ele sobrepõe o resultado da API mais recente no quadro correspondente.

Na maioria dos modos, há um atraso visível entre o vídeo ao vivo à esquerda e a análise visualizada à direita. Esse atraso é o tempo necessário para fazer a chamada à API. Uma exceção está no modo "EmotionsWithClientFaceDetect", que executa a detecção facial localmente no computador cliente usando OpenCV antes de enviar qualquer imagem para os serviços cognitivas do Azure. 

Usando essa abordagem, você pode visualizar a face detectada imediatamente. Em seguida, você pode atualizar as emoções mais tarde, depois que a chamada à API retorna. Isso demonstra a possibilidade de uma abordagem "híbrida". Ou seja, um processamento simples pode ser executado no cliente e, em seguida, API de Serviços Cognitivos pode ser usado para aumentar esse processamento com uma análise mais avançada quando necessário.

![O aplicativo LiveCameraSample exibindo uma imagem com marcas](../../Video/Images/FramebyFrame.jpg)

### <a name="integrate-the-samples-into-your-codebase"></a>Integre os exemplos à sua base de código

Para começar a usar este exemplo, faça o seguinte:

1. Obtenha as chaves de API para as APIs de Visão a partir de [Subscrições](https://azure.microsoft.com/try/cognitive-services/). Para a análise de quadros de vídeo, os serviços aplicáveis são:
    - [Imagem Digitalizada](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Face](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Clone o repositório GitHub [cognitiva-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) .

3. Abra o exemplo no Visual Studio 2015 ou posterior e, em seguida, compile e execute os aplicativos de exemplo:
    - Para BasicConsoleSample, a chave de face é embutida em código diretamente em [BasicConsoleSample/Program. cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - Para LiveCameraSample, insira as chaves no painel **configurações** do aplicativo. As chaves são mantidas entre as sessões como dados do usuário.

Quando estiver pronto para integrar os exemplos, faça referência à biblioteca VideoFrameAnalyzer de seus próprios projetos.

Os recursos de imagem, voz, vídeo e texto-entendendo do VideoFrameAnalyzer usam os serviços cognitivas do Azure. A Microsoft recebe imagens, áudio, vídeo e outros dados que você carrega (por meio deste aplicativo) e pode usá-los para fins de aprimoramento de serviço. Pedimos a sua ajuda para proteger as pessoas cujos dados são enviados para os Serviços Cognitivos do Azure através da sua aplicação.

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu como executar uma análise quase em tempo real em fluxos de vídeo ao vivo usando os serviços de face e Pesquisa Visual Computacional. Você também aprendeu como é possível usar nosso código de exemplo para começar. Para começar a criar seu aplicativo usando as chaves de API gratuitas, vá para a [página de inscrição dos serviços cognitivas do Azure](https://azure.microsoft.com/try/cognitive-services/).

Sinta-se à vontade para fornecer comentários e sugestões no [repositório GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/). Para fornecer comentários mais amplos sobre a API, acesse nosso [site UserVoice](https://cognitive.uservoice.com/).

