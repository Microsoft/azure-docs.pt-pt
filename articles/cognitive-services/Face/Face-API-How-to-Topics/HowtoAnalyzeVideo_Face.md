---
title: 'Exemplo: Análise de vídeo em tempo real-API de Detecção Facial'
titleSuffix: Azure Cognitive Services
description: Utilize a API Face para efetuar uma análise quase em tempo real em fotogramas obtidos a partir de uma transmissão de fluxo de vídeo em direto.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: e2166354fb45d24e117156e917f4da726ee8406f
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114334"
---
# <a name="example-how-to-analyze-videos-in-real-time"></a>Exemplo: Como Analisar Vídeos em Tempo Real

Este guia demonstrará como efetuar uma análise quase em tempo real de fotogramas obtidos de uma transmissão de fluxo de vídeo em direto. Os componentes básicos num sistema desse tipo são:

- Obter fotogramas de uma origem de vídeo
- Selecionar os fotogramas a analisar
- Enviar estes fotogramas para a API
- Consumir cada resultado de análise que é devolvido da chamada à API

Estes exemplos são escritos em C# e o código pode ser encontrado no GitHub aqui: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>A Abordagem

Existem múltiplas formas de resolver o problema de executar a análise quase em tempo real de transmissões de fluxo de vídeo. Iremos começar por destacar três abordagens para aumentar os níveis de sofisticação.

### <a name="a-simple-approach"></a>Uma Abordagem Simples

O design mais simples para um sistema de análise quase em tempo real é um loop infinito, onde cada iteração captura um quadro, analisa-o e, em seguida, consome o resultado:

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

Se a nossa análise consistisse num algoritmo simples do lado do cliente, essa abordagem seria adequada. No entanto, quando a análise acontece na nuvem, a latência envolvida significa que uma chamada à API pode levar vários segundos. Durante esse tempo, não estamos capturando imagens, e nosso thread, essencialmente, não faz nada. A nossa taxa de fotogramas máxima é limitada pela latência das chamadas à API.

### <a name="parallelizing-api-calls"></a>Paralelizar Chamadas à API

Embora um ciclo de thread único seja adequado para um algoritmo simples do lado do cliente, não é adequado com a latência das chamadas à API na cloud. A solução para este problema é permitir que as chamadas à API de execução longa sejam executadas em paralelo com a obtenção de fotogramas. No C#, podemos alcançar isto ao utilizar um paralelismo baseado em Tarefas. Por exemplo:

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

Este código inicia cada análise numa Tarefa separada, que pode ser executada em segundo plano enquanto obtemos novos fotogramas. Com esse método, evitamos o bloqueio do thread principal enquanto aguarda uma chamada à API retornar, mas perdemos algumas das garantias de que a versão simples é fornecida. Várias chamadas à API podem ocorrer em paralelo, e os resultados podem ser retornados na ordem errada. Isso também pode fazer com que vários threads insiram a função ConsumeResult () simultaneamente, o que poderia ser perigoso, se a função não for thread-safe. Por fim, este código simples não controla as Tarefas criadas, pelo que as exceções irão desaparecer silenciosamente. Portanto, a etapa final é adicionar um thread de "consumidor" que controlará as tarefas de análise, gerar exceções, eliminar tarefas de longa execução e garantir que os resultados sejam consumidos na ordem correta.

### <a name="a-producer-consumer-design"></a>Um Design de Produtor e Consumidor

No nosso sistema final de "produtor e consumidor", temos um thread de produtor com um aspeto semelhante ao nosso ciclo infinito anterior. No entanto, em vez de consumir resultados de análise assim que estiverem disponíveis, o produtor coloca simplesmente as tarefas numa fila para as controlar.

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

Também temos um thread de consumidor que retira as tarefas da fila, aguarda que elas sejam concluídas e exibe o resultado ou gera a exceção que foi lançada. Ao utilizar a fila, podemos garantir que os resultados são consumidos um de cada vez, na ordem correta, sem limitar a taxa de fotogramas máxima do sistema.

```csharp
// Consumer thread. 
while (true)
{
    // Get the oldest task. 
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Await until the task is completed. 
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

## <a name="implementing-the-solution"></a>Implementar a Solução

### <a name="getting-started"></a>Introdução

Para colocar seu aplicativo em funcionamento o mais rápido possível, você usará uma implementação flexível do sistema descrito acima. Para aceder ao código, vá para [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

A biblioteca contém a classe FrameGrabber, que implementa o sistema produtor-consumidor discutido acima para processar quadros de vídeo de uma webcam. O usuário pode especificar a forma exata da chamada à API e a classe usa eventos para permitir que o código de chamada saiba quando um novo quadro é adquirido ou que um novo resultado de análise está disponível.

Para ilustrar algumas das possibilidades, existem duas aplicações de exemplo que utilizam a biblioteca. O primeiro é um aplicativo de console simples e uma versão simplificada dele é reproduzida abaixo. Obtém os fotogramas da câmara Web predefinida e envia-os para a API Face para deteção facial.

```csharp
using System;
using VideoFrameAnalyzer;
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;
     
namespace VideoFrameConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create grabber, with analysis type Face[]. 
            FrameGrabber<Face[]> grabber = new FrameGrabber<Face[]>();
            
            // Create Face API Client. Insert your Face API key here.
            private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });

            // Set up our Face API call.
            grabber.AnalysisFunction = async frame => return await faceClient.DetectAsync(frame.Image.ToMemoryStream(".jpg"));

            // Set up a listener for when we receive a new result from an API call. 
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.Analysis != null)
                    Console.WriteLine("New result received for frame acquired at {0}. {1} faces detected", e.Frame.Metadata.Timestamp, e.Analysis.Length);
            };
            
            // Tell grabber to call the Face API every 3 seconds.
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for keypress to stop
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();
            
            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```

A segunda aplicação de exemplo é um pouco mais interessante e permite-lhe escolher que API chamar nos fotogramas de vídeo. No lado esquerdo, a aplicação mostra uma pré-visualização do vídeo em direto. No lado direito, mostra o resultado de API mais recente sobreposto num fotograma correspondente.

Na maioria dos modos, existirá um atraso visível entre o vídeo em direto à esquerda e a análise visualizada à direita. Este atraso é o tempo necessário para efetuar a chamada à API. Uma exceção é o modo "EmotionsWithClientFaceDetect", que executa a detecção facial localmente no computador cliente usando OpenCV, antes de enviar qualquer imagem para serviços cognitivas. Dessa forma, podemos Visualizar a face detectada imediatamente e, em seguida, atualizar as emoções quando a chamada à API retornar. Este é um exemplo de uma abordagem "híbrida", em que o cliente pode executar um processamento simples e API de Serviços Cognitivos pode aumentar isso com uma análise mais avançada quando necessário.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integrar num código base

Para começar a utilizar este exemplo, siga estes passos:

1. Obtenha as chaves de API para as APIs de Visão a partir de [Subscrições](https://azure.microsoft.com/try/cognitive-services/). Para a análise de fotogramas de vídeo, as APIs aplicáveis são:
    - [API de Imagem Digitalizada](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [API de Emoções](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [API Face](https://docs.microsoft.com/azure/cognitive-services/face/overview)

2. Clonar o repositório de GitHub [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)

3. Abra o exemplo no Visual Studio 2015 e compile e execute os aplicativos de exemplo:
    - Para BasicConsoleSample, a chave de API de Detecção Facial é embutida em código diretamente em [BasicConsoleSample/Program. cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - Para LiveCameraSample, as chaves devem ser introduzidas no painel Definições da aplicação. Estas permanecerão nas sessões como dados de utilizador.
        

Quando estiver pronto para integrar, **referencie a biblioteca VideoFrameAnalyzer de seus próprios projetos.** 

## <a name="summary"></a>Resumo

Neste guia, você aprendeu a executar a análise quase em tempo real em fluxos de vídeo ao vivo usando as APIs facial, Pesquisa Visual Computacional e emoções e como usar nosso código de exemplo para começar. Você pode começar a criar seu aplicativo com chaves de API gratuitas na [página de inscrição dos serviços cognitivas do Azure](https://azure.microsoft.com/try/cognitive-services/). 

Sinta-se à vontade para fornecer comentários e sugestões no [repositório GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) ou, para obter comentários mais amplos sobre a API, em nosso [site UserVoice](https://cognitive.uservoice.com/).

## <a name="related-topics"></a>Tópicos Relacionados
- [Como Identificar Rostos na Imagem](HowtoIdentifyFacesinImage.md)
- [Como Detetar Rostos na Imagem](HowtoDetectFacesinImage.md)
