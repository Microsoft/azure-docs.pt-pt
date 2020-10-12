---
title: Analise vídeos em quase tempo real - Visão de Computador
titleSuffix: Azure Cognitive Services
description: Aprenda a realizar análises quase em tempo real em quadros que são retirados de um stream de vídeo ao vivo utilizando a API de Visão de Computador.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: c86b06ff46f1ddc8d22d2ab7ec4bc8620a8c862f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88933176"
---
# <a name="analyze-videos-in-near-real-time"></a>Analise os vídeos em quase tempo real

Este artigo demonstra como realizar análises quase em tempo real em quadros que são retirados de um stream de vídeo ao vivo utilizando a API de Visão de Computador. Os elementos básicos de tal análise são:

- A aquisição de quadros a partir de uma fonte de vídeo.
- Selecionando quais quadros para analisar.
- Submeter estes quadros à API.
- Consumir cada resultado de análise que foi devolvido da chamada da API.

As amostras deste artigo estão escritas em C#. Para aceder ao código, aceda à página de [amostra de análise de quadros de vídeo](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) no GitHub.

## <a name="approaches-to-running-near-real-time-analysis"></a>Abordagens para correr perto da análise em tempo real

Pode resolver o problema de correr perto de análises em tempo real em streams de vídeo usando uma variedade de abordagens. Este artigo descreve três deles, em níveis crescentes de sofisticação.

### <a name="design-an-infinite-loop"></a>Desenhe um loop infinito

O design mais simples para análise quase em tempo real é um loop infinito. Em cada iteração deste loop, pega-se numa moldura, analisa-se e depois consome-se o resultado:

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

Se a sua análise consistisse num algoritmo leve do lado do cliente, esta abordagem seria adequada. No entanto, quando a análise ocorre na nuvem, a latência resultante significa que uma chamada de API pode demorar vários segundos. Durante este tempo, não estás a captar imagens, e o teu fio está essencialmente a não fazer nada. A sua taxa máxima de fotogramas é limitada pela latência das chamadas da API.

### <a name="allow-the-api-calls-to-run-in-parallel"></a>Permitir que as chamadas da API corram em paralelo

Embora um loop simples e de roscado faça sentido para um algoritmo leve do lado do cliente, não se encaixa bem com a latência de uma chamada de API em nuvem. A solução para este problema é permitir que a chamada de API de longa duração seja executada em paralelo com a captura de quadros. Em C#, você poderia fazê-lo usando o paralelismo baseado em tarefas. Por exemplo, pode executar o seguinte código:

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

Com esta abordagem, lança-se cada análise numa tarefa separada. A tarefa pode ser executada em segundo plano enquanto continua a agarrar novos quadros. A abordagem evita bloquear o fio principal enquanto espera pelo retorno de uma chamada da API. No entanto, a abordagem pode apresentar algumas desvantagens:
* Custa-lhe algumas das garantias que a versão simples forneceu. Ou seja, várias chamadas da API podem ocorrer em paralelo, e os resultados podem ser devolvidos na ordem errada. 
* Também pode causar a entrada de vários fios na função ConsumeResult() simultaneamente, o que pode ser perigoso se a função não for segura para os fios. 
* Finalmente, este simples código não acompanha as tarefas que são criadas, por isso as exceções desaparecem silenciosamente. Assim, é necessário adicionar um fio "consumidor" que rastreie as tarefas de análise, levante exceções, mate tarefas de longa duração e garanta que os resultados são consumidos na ordem correta, uma de cada vez.

### <a name="design-a-producer-consumer-system"></a>Conceber um sistema produtor-consumidor

Para a sua abordagem final, projetando um sistema "produtor-consumidor", você constrói um fio de produtor que se parece com o seu loop infinito anteriormente mencionado. No entanto, em vez de consumir os resultados da análise assim que estiverem disponíveis, o produtor simplesmente coloca as tarefas numa fila para os acompanhar.

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

Também cria um fio de consumo, que tira tarefas da fila, espera que terminem, e ou exibe o resultado ou aumenta a exceção que foi lançada. Ao utilizar a fila, pode garantir que os resultados são consumidos um de cada vez, na ordem correta, sem limitar a taxa máxima de fotogramas do sistema.

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

### <a name="get-started-quickly"></a>Começar rapidamente

Para ajudar a pôr a sua app a funcionar o mais rapidamente possível, implementámos o sistema que está descrito na secção anterior. Pretende-se ser flexível o suficiente para acomodar muitos cenários, ao mesmo tempo que é fácil de usar. Para aceder ao código, aceda à página de [amostra de análise de quadros de vídeo](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) no GitHub.

A biblioteca contém a `FrameGrabber` classe, que implementa o sistema de consumo de produtores-consumidores previamente discutido para processar quadros de vídeo a partir de uma webcam. Os utilizadores podem especificar a forma exata da chamada API, e a classe utiliza eventos para informar o código de chamada quando um novo quadro é adquirido, ou quando um novo resultado de análise está disponível.

Para ilustrar algumas das possibilidades, fornecemos duas aplicações de amostra que usam a biblioteca. 

A primeira aplicação de amostra é uma simples aplicação de consola que agarra os quadros da webcam padrão e depois as submete ao serviço Face para deteção facial. Uma versão simplificada da aplicação é reproduzida no seguinte código:

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

A segunda aplicação de amostra é um pouco mais interessante. Permite-lhe escolher qual a API a ligar nos quadros de vídeo. No lado esquerdo, a aplicação mostra uma pré-visualização do vídeo ao vivo. À direita, sobrepõe-se ao resultado mais recente da API no quadro correspondente.

Na maioria dos modos, há um atraso visível entre o vídeo ao vivo à esquerda e a análise visualizada à direita. Este atraso é o tempo que leva para fazer a chamada da API. Uma exceção encontra-se no modo "EmotionsWithClientFaceDetect", que realiza a deteção facial localmente no computador cliente, utilizando o OpenCV antes de submeter quaisquer imagens aos Serviços Cognitivos Azure. 

Utilizando esta abordagem, pode visualizar o rosto detetado imediatamente. Em seguida, pode atualizar as emoções mais tarde, após o retorno da chamada da API. Isto demonstra a possibilidade de uma abordagem "híbrida". Ou seja, algum processamento simples pode ser realizado no cliente, e então as APIs de Serviços Cognitivos podem ser usadas para aumentar este processamento com uma análise mais avançada quando necessário.

![A app LiveCameraSample exibindo uma imagem com etiquetas](../../Video/Images/FramebyFrame.jpg)

### <a name="integrate-the-samples-into-your-codebase"></a>Integre as amostras na sua base de código

Para começar com esta amostra, faça o seguinte:

1. Crie uma [conta do Azure](https://azure.microsoft.com/free/cognitive-services/). Se já tem um, pode saltar para o próximo passo.
2. Crie recursos para Visão de Computador e Rosto no portal Azure para obter a sua chave e ponto final. Certifique-se de que seleciona o nível livre (F0) durante a configuração.
   - [Imagem Digitalizada](https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
   - [Rosto](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) Depois de os recursos serem implantados, clique em **Ir ao recurso** para recolher a sua chave e ponto final para cada recurso. 
3. Clone o [repo Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub.
4. Abra a amostra no Visual Studio 2015 ou mais tarde e, em seguida, construa e execute as aplicações da amostra:
    - Para BasicConsoleSample, a tecla Face é codificada diretamente em [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - Para liveCameraSample, introduza as teclas no painel **de Definições** da aplicação. As teclas são persistiu em todas as sessões como dados do utilizador.

Quando estiver pronto para integrar as amostras, consulte a biblioteca VideoFrameAnalyzer dos seus próprios projetos.

As capacidades de compreensão de imagens, vozes, vídeos e textos do VideoFrameAnalyzer utilizam os Serviços Cognitivos Azure. A Microsoft recebe as imagens, áudio, vídeo e outros dados que carrega (através desta aplicação) e pode usá-las para fins de melhoria de serviço. Pedimos a sua ajuda para proteger as pessoas cujos dados são enviados para os Serviços Cognitivos do Azure através da sua aplicação.

## <a name="summary"></a>Resumo

Neste artigo, aprendeu a executar quase análises em tempo real em streams de vídeo ao vivo utilizando os serviços face e visão de computador. Também aprendeste como podes usar o nosso código de amostra para começar.

Sinta-se livre para fornecer feedback e sugestões no [repositório GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/). Para fornecer feedback mais amplo da API, aceda ao nosso [site UserVoice](https://cognitive.uservoice.com/).

