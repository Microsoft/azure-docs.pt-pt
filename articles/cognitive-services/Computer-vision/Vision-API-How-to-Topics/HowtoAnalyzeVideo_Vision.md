---
title: 'Exemplo: Análise de vídeo em tempo real - de imagem digitalizada'
titlesuffix: Azure Cognitive Services
description: Saiba como efetuar uma análise quase em tempo real de fotogramas obtidos de uma transmissão de fluxo de vídeo em direto com a API de Imagem Digitalizada.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 03/21/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: fb684a59362e0f7b6ccdc2ca05fda1b89def2835
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351852"
---
# <a name="how-to-analyze-videos-in-real-time"></a>Como analisar vídeos em tempo real

Este guia demonstrará como efetuar uma análise quase em tempo real de fotogramas obtidos de uma transmissão de fluxo de vídeo em direto. Os componentes básicos num sistema desse tipo são:

- Obter fotogramas de uma origem de vídeo
- Selecionar os fotogramas a analisar
- Enviar estes fotogramas para a API
- Consumir cada resultado de análise que é devolvido da chamada à API

Estes exemplos são escritos em C# e o código pode ser encontrado no GitHub aqui: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>A Abordagem

Existem múltiplas formas de resolver o problema de executar a análise quase em tempo real de transmissões de fluxo de vídeo. Iremos começar por destacar três abordagens para aumentar os níveis de sofisticação.

### <a name="a-simple-approach"></a>Uma Abordagem Simples

O design mais simples para um sistema de análise quase em tempo real é um ciclo infinito, em que obtemos um fotograma em cada iteração, analisamos o fotograma e, em seguida, consumimos o resultado:

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

Se a nossa análise consistisse num algoritmo simples do lado do cliente, essa abordagem seria adequada. No entanto, quando a nossa análise está a acontecer na cloud, uma chamada à API pode demorar vários segundos por causa da latência. Durante esse período de tempo, não estamos a capturar imagens e o nosso thread não faz nada. A nossa taxa de fotogramas máxima é limitada pela latência das chamadas à API.

### <a name="parallelizing-api-calls"></a>Paralelizar Chamadas à API

Embora um ciclo de thread único seja adequado para um algoritmo simples do lado do cliente, não é adequado com a latência das chamadas à API na cloud. A solução para este problema é permitir que as chamadas à API de execução longa sejam executadas em paralelo com a obtenção de fotogramas. No C#, podemos alcançar isto através de um paralelismo baseado em Tarefas. Por exemplo:

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

Essa abordagem inicia cada análise numa Tarefa separada, que pode ser executada em segundo plano enquanto obtemos novos fotogramas. Evita o bloqueio do thread principal enquanto aguarda a devolução de uma chamada à API, mas perdemos algumas das garantias fornecidas pela versão simples – podem ocorrer múltiplas chamadas à API em paralelo e os resultados podem ser devolvidos na ordem errada. Essa abordagem também pode causar vários threads inserir a função de ConsumeResult() simultaneamente, que podem ser perigosos, se a função não é thread-safe. Por fim, este código simples não controla as Tarefas criadas, pelo que as exceções irão desaparecer silenciosamente. Portanto, o ingrediente final a adicionar é um thread de "consumidor" que irá controlar as tarefas de análise, gerar exceções, eliminar tarefas de longa execução e garantir que os resultados são consumidos na ordem correta, um de cada vez.

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

Também temos um thread de consumidor, que retira as tarefas da fila, aguarda que estas terminem e apresenta o resultado ou gera a exceção que foi emitida. Ao utilizar a fila, podemos garantir que os resultados são consumidos um de cada vez, na ordem correta, sem limitar a taxa de fotogramas máxima do sistema.

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

Para começar a utilizar a aplicação o mais rápido possível, implementámos o sistema descrito acima. Pretendemos que seja flexível o suficiente para implementar vários cenários e fácil de utilizar. Para aceder ao código, vá para [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

A biblioteca contém a classe FrameGrabber, que implementa o sistema de consumidor de produtor descrito acima, para processar os quadros de vídeo de uma webcam. O utilizador pode especificar o formato exato da chamada à API e a classe utiliza eventos para permitir que o código de chamada saiba quando um novo fotograma é adquirido ou um novo resultado de análise está disponível.

Para ilustrar algumas das possibilidades, existem duas aplicações de exemplo que utilizam a biblioteca. A primeira é uma aplicação de consola simples. Abaixo está reproduzida uma versão simplificada disto. Obtém os fotogramas da câmara Web predefinida e envia-os para a API Face para deteção facial.

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
            FaceServiceClient faceClient = new FaceServiceClient("<subscription key>");

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

Na maioria dos modos, existirá um atraso visível entre o vídeo em direto à esquerda e a análise visualizada à direita. Este atraso é o tempo necessário para efetuar a chamada à API. A exceção está no modo "EmotionsWithClientFaceDetect", que executa a deteção de rostos localmente no computador cliente usando OpenCV, antes de submeter quaisquer imagens aos serviços cognitivos. Ao fazer isto, podemos visualizar o rosto detetado imediatamente e atualizar as emoções mais tarde quando a chamada à API for devolvida. Isto demonstra a possibilidade de uma abordagem "híbrida", em que pode ser efetuado algum processamento simples no cliente e, em seguida, as APIs Serviços Cognitivos podem ser utilizadas para aumentar as possibilidades com uma análise mais avançada, quando for necessário.

![Captura de ecrã da aplicação LiveCameraSample que mostra uma imagem com etiquetas apresentadas](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integrar num código base

Para começar a utilizar este exemplo, siga estes passos:

1. Obtenha as chaves de API para as APIs de Visão a partir de [Subscrições](https://azure.microsoft.com/try/cognitive-services/). Para a análise de fotogramas de vídeo, as APIs aplicáveis são:
    - [API de Imagem Digitalizada](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [API de Emoções](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [API Face](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Clonar o repositório de GitHub [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)

3. Abra o exemplo no Visual Studio 2015, crie as aplicações de exemplo e execute-as:
    - Para BasicConsoleSample, a chave de Face API é hard-coded diretamente nas [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - Para LiveCameraSample, as chaves devem ser introduzidas no painel Definições da aplicação. Estas permanecerão nas sessões como dados de utilizador.

Quando estiver pronto para integrar, **faça referência à biblioteca VideoFrameAnalyzer a partir dos seus próprios projetos.**

As funcionalidades de compreensão de texto, vídeo, voz ou imagem do VideoFrameAnalyzer utilizam os Serviços Cognitivos do Azure. A Microsoft irá receber as imagens, áudio, vídeo e outros dados que carregar (através desta aplicação) e poderá utilizá-los para melhorar o serviço. Pedimos a sua ajuda para proteger as pessoas cujos dados são enviados para os Serviços Cognitivos do Azure através da sua aplicação.

## <a name="summary"></a>Resumo

Neste guia, aprendeu a executar uma análise quase em tempo real de transmissões de fluxo de vídeo em direto com as APIs Face, de Imagem Digitalizada e de Emoções, e como pode utilizar o nosso código de exemplo para começar a trabalhar. Pode começar a criar uma aplicação com chaves de API gratuitas na [página de inscrição dos Serviços Cognitivos da Microsoft](https://azure.microsoft.com/try/cognitive-services/). 

À vontade fornecer comentários e sugestões na [repositório do GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/), ou para obter mais amplo feedback de API, no nosso [site do UserVoice](https://cognitive.uservoice.com/).

