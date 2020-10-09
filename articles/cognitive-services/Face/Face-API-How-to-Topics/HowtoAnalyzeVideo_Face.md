---
title: 'Exemplo: Análise de vídeo em tempo real - Face'
titleSuffix: Azure Cognitive Services
description: Utilize o serviço Face para realizar análises quase em tempo real em quadros retirados de um stream de vídeo ao vivo.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 730946a0c581be4697c0f45c8bdeb1d38f0ca23d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856393"
---
# <a name="example-how-to-analyze-videos-in-real-time"></a>Exemplo: Como Analisar Vídeos em Tempo Real

Este guia demonstrará como efetuar uma análise quase em tempo real de fotogramas obtidos de uma transmissão de fluxo de vídeo em direto. Os componentes básicos num sistema desse tipo são:

- Obter fotogramas de uma origem de vídeo
- Selecionar os fotogramas a analisar
- Enviar estes fotogramas para a API
- Consumir cada resultado de análise que é devolvido da chamada à API

Estas amostras estão escritas em C# e o código pode ser encontrado no GitHub aqui: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) .

## <a name="the-approach"></a>A Abordagem

Existem múltiplas formas de resolver o problema de executar a análise quase em tempo real de transmissões de fluxo de vídeo. Iremos começar por destacar três abordagens para aumentar os níveis de sofisticação.

### <a name="a-simple-approach"></a>Uma Abordagem Simples

O design mais simples para um sistema de análise quase em tempo real é um loop infinito, onde cada iteração agarra uma moldura, analisa-a e, em seguida, consome o resultado:

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

Se a nossa análise consistisse num algoritmo simples do lado do cliente, essa abordagem seria adequada. No entanto, quando a análise acontece na nuvem, a latência envolvida significa que uma chamada de API pode demorar alguns segundos. Durante este tempo, não estamos a captar imagens, e o nosso fio está essencialmente a não fazer nada. A nossa taxa de fotogramas máxima é limitada pela latência das chamadas à API.

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

Este código inicia cada análise numa Tarefa separada, que pode ser executada em segundo plano enquanto obtemos novos fotogramas. Com este método evitamos bloquear o fio principal enquanto esperamos que uma chamada da API regresse, mas perdemos algumas das garantias que a versão simples forneceu. Várias chamadas de API podem ocorrer em paralelo, e os resultados podem ser devolvidos na ordem errada. Isto também pode fazer com que vários threads introduzam a função ConsumeResult() em simultâneo, o que pode ser perigoso se a função não for segura para threads. Por fim, este código simples não controla as Tarefas criadas, pelo que as exceções irão desaparecer silenciosamente. Portanto, o passo final é adicionar um fio "consumidor" que acompanhará as tarefas de análise, levantará exceções, matará tarefas de longa duração e garantirá que os resultados sejam consumidos na ordem correta.

### <a name="a-producer-consumer-design"></a>Um Design de Produtor e Consumidor

No nosso sistema final de "produtor e consumidor", temos um thread de produtor com um aspeto semelhante ao nosso ciclo infinito anterior. No entanto, em vez de consumir resultados de análise assim que estiverem disponíveis, o produtor coloca simplesmente as tarefas numa fila para controlar as mesmas.

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

Temos também um fio condutor que tira as tarefas da fila, espera que terminem, e ou mostra o resultado ou aumenta a exceção que foi lançada. Ao utilizar a fila, podemos garantir que os resultados são consumidos um de cada vez, na ordem correta, sem limitar a taxa de fotogramas máxima do sistema.

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

Para que a sua aplicação esteja a funcionar o mais rapidamente possível, utilizará uma implementação flexível do sistema acima descrito. Para aceder ao código, aceda a [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis) .

A biblioteca contém a classe FrameGrabber, que implementa o sistema de produtor e consumidor debatido acima para processar fotogramas de vídeo de uma câmara Web. O utilizador pode especificar a forma exata da chamada API, e a classe utiliza eventos para informar o código de chamada quando um novo quadro é adquirido ou um novo resultado de análise está disponível.

Para ilustrar algumas das possibilidades, existem duas aplicações de exemplo que utilizam a biblioteca. A primeira é uma simples aplicação de consola, e uma versão simplificada da mesmo é reproduzida abaixo. Agarra os quadros da webcam padrão e submete-os ao serviço Face para deteção facial.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/analyze.cs":::

A segunda aplicação de exemplo é um pouco mais interessante e permite-lhe escolher que API chamar em fotogramas. No lado esquerdo, a aplicação mostra uma pré-visualização do vídeo em direto. No lado direito, mostra o resultado de API mais recente sobreposto num fotograma correspondente.

Na maioria dos modos, existirá um atraso visível entre o vídeo em direto à esquerda e a análise visualizada à direita. Este atraso é o tempo necessário para efetuar a chamada à API. Uma exceção é o modo "EmotionsWithClientFaceDetect", que realiza a deteção facial localmente no computador cliente utilizando o OpenCV, antes de submeter quaisquer imagens aos Serviços Cognitivos. Desta forma, podemos visualizar o rosto detetado imediatamente e, em seguida, atualizar as emoções assim que a chamada da API retornar. Este é um exemplo de uma abordagem "híbrida", onde o cliente pode realizar algum processamento simples, e as APIs de Serviços Cognitivos podem aumentá-lo com uma análise mais avançada quando necessário.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integrar num código base

Para começar a utilizar este exemplo, siga estes passos:

1. Crie uma [conta do Azure](https://azure.microsoft.com/free/cognitive-services/). Se já tem um, pode saltar para o próximo passo.
2. Crie recursos para Visão de Computador e Rosto no portal Azure para obter a sua chave e ponto final. Certifique-se de que seleciona o nível livre (F0) durante a configuração.
   - [Imagem Digitalizada](https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
   - [Rosto](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) Depois de os recursos serem implantados, clique em **Ir ao recurso** para recolher a sua chave e ponto final para cada recurso. 
3. Clone o [repo Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub.
4. Abra a amostra no Visual Studio e construa e execute as aplicações da amostra:
    - Para BasicConsoleSample, a tecla Face é codificada diretamente em [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - Para LiveCameraSample, as chaves devem ser introduzidas no painel Definições da aplicação. Estas permanecerão nas sessões como dados de utilizador.
        

Quando estiver pronto para integrar, **faça referência à biblioteca VideoFrameAnalyzer dos seus próprios projetos.** 

## <a name="summary"></a>Resumo

Neste guia, aprendeu a fazer análises em tempo real em streams de vídeo ao vivo utilizando as APIs de Face, Visão de Computador e Emoção, e como usar o nosso código de amostra para começar.

Sinta-se livre para fornecer feedback e sugestões no [repositório GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) ou, para feedback mais amplo da API, no nosso [site UserVoice](https://cognitive.uservoice.com/).

## <a name="related-topics"></a>Tópicos relacionados
- [Como Detetar Rostos na Imagem](HowtoDetectFacesinImage.md)
