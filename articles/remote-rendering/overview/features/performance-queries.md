---
title: Consultas de desempenho do lado do servidor
description: Como fazer consultas de desempenho do lado do servidor através de chamadas API
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 57a9f6f11283e020efc25f55f1df473a6cb2d321
ms.sourcegitcommit: 9d9221ba4bfdf8d8294cf56e12344ed05be82843
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2021
ms.locfileid: "98570002"
---
# <a name="server-side-performance-queries"></a>Consultas de desempenho do lado do servidor

Um bom desempenho de renderização no servidor é fundamental para taxas de fotogramas estáveis e uma boa experiência do utilizador. É importante monitorizar cuidadosamente as características de desempenho no servidor e otimizar sempre que necessário. Os dados de desempenho podem ser consultados através de funções dedicadas da API.

O mais impactante para o desempenho da renderização são os dados de entrada do modelo. Pode ajustar os dados de entrada descritos na [configuração da conversão do modelo](../../how-tos/conversion/configure-model-conversion.md).

O desempenho da aplicação do lado do cliente também pode ser um estrangulamento. Para uma análise aprofundada do desempenho do lado do cliente, é recomendado tomar um [:::no-loc text="performance trace":::](../../how-tos/performance-tracing.md) .

## <a name="clientserver-timeline"></a>Linha do tempo do cliente/servidor

Antes de entrar em detalhes sobre os vários valores de latência, vale a pena ter uma olhada nos pontos de sincronização entre cliente e servidor na linha do tempo:

![Cronologia do gasoduto](./media/server-client-timeline.png)

A ilustração mostra como:

* uma *estimativa pose* é iniciada pelo cliente a uma taxa de fotogramas constante de 60-Hz (a cada 16,6 ms)
* o servidor começa então a renderizar, com base na pose
* o servidor envia de volta a imagem de vídeo codificada
* o cliente descodifica a imagem, executa algum trabalho de CPU e GPU em cima dela e, em seguida, exibe a imagem

## <a name="frame-statistics-queries"></a>Consultas estatísticas de quadros

As estatísticas de quadros fornecem algumas informações de alto nível para o último quadro, como a latência. Os dados fornecidos na `FrameStatistics` estrutura são medidos do lado do cliente, pelo que a API é uma chamada sincronizada:

```cs
void QueryFrameData(AzureSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
```

```cpp
void QueryFrameData(ApiHandle<AzureSession> session)
{
    FrameStatistics frameStatistics;
    if (*session->GetGraphicsBinding()->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        // do something with the result
    }
}
```

O objeto recuperado `FrameStatistics` contém os seguintes membros:

| Membro | Explicação |
|:-|:-|
| latênciaPoseToReceive | A latência da câmara representa a estimativa no dispositivo do cliente até que uma moldura do servidor para esta pose esteja totalmente disponível para a aplicação do cliente. Este valor inclui ida e volta de rede, tempo de renderização do servidor, descodificar vídeo e compensação de nervosismo. Consulte **o intervalo 1 na ilustração acima.**|
| latênciaReceiveToPresent | Latência da disponibilidade de um quadro remoto recebido até que a aplicação do cliente ligue para PresentFrame no CPU. Consulte **o intervalo 2 na ilustração acima.**|
| latênciaPresentToDisplay  | Latência de apresentar uma moldura no CPU até que o visor se aceda. Este valor inclui o tempo de GPU do cliente, qualquer buffering de quadro realizado pelo SO, reprojecção de hardware e tempo de verificação do ecrã dependente do dispositivo. Consulte **o intervalo 3 na ilustração acima.**|
| timeSinceLastPresent | O tempo entre chamadas subsequentes para PresentFrame no CPU. Valores superiores à duração do visor (por exemplo, 16,6 ms num dispositivo cliente de 60 Hz) indicam problemas causados pela aplicação do cliente que não termina a sua carga de trabalho de CPU a tempo.|
| videoFramesReceived | O número de fotogramas recebidos do servidor no último segundo. |
| videoFrameReusedCount | Número de fotogramas recebidos no último segundo que foram utilizados no dispositivo mais de uma vez. Valores não nulos indicam que os quadros tiveram de ser reutilizados e reprojados devido ao nervosismo da rede ou ao tempo excessivo de renderização do servidor. |
| videoFramesSkipped | Número de quadros recebidos no último segundo que foram descodificados, mas não mostrados no ecrã porque chegou uma moldura mais recente. Valores não nulos indicam que o nervosismo da rede fez com que vários quadros fossem atrasados e, em seguida, chegassem juntos ao dispositivo cliente numa explosão. |
| videoFramesDiscarded | Muito semelhante ao **videoFramesSkipped**, mas a razão para ser descartado é que uma moldura chegou tão tarde que já nem pode ser correlacionada com qualquer pose pendente. Se isto acontecer, há uma grave contenção na rede.|
| vídeoFrameMinDelta | Quantidade mínima de tempo entre dois quadros consecutivos que chegam durante o último segundo. Juntamente com o videoFrameMaxDelta, esta gama dá uma indicação de nervosismo causada pela rede ou pelo codec de vídeo. |
| videoFrameMaxDelta | Quantidade máxima de tempo entre dois quadros consecutivos que chegam durante o último segundo. Juntamente com o vídeoFrameMinDelta, esta gama dá uma indicação de nervosismo causada pela rede ou pelo codec de vídeo. |

A soma de todos os valores de latência é tipicamente muito maior do que o tempo de fotogramas disponível a 60 Hz. Isto é OK, porque vários quadros estão em voo em paralelo, e novos pedidos de fotogramas são iniciados à taxa de fotogramas desejada, como mostra a ilustração. No entanto, se a latência se tornar demasiado grande, afeta a qualidade da [reprojecção tardia](../../overview/features/late-stage-reprojection.md)da fase tardia , e pode comprometer a experiência geral.

`videoFramesReceived`, `videoFrameReusedCount` e pode ser usado para medir o desempenho da rede e do `videoFramesDiscarded` servidor. Se `videoFramesReceived` for baixa e for `videoFrameReusedCount` alta, isto pode indicar congestionamento de rede ou fraco desempenho do servidor. Um valor elevado `videoFramesDiscarded` também indica congestionamento de rede.

Por `timeSinceLastPresent` último, `videoFrameMinDelta` e dê uma ideia `videoFrameMaxDelta` da variação dos quadros de vídeo e das chamadas presentes locais. Alta variação significa taxa de fotogramas instáveis.

Nenhum dos valores acima indica claramente a latência da rede pura (as setas vermelhas na ilustração), porque a hora exata em que o servidor está ocupado deve ser subtraída do valor de ida e volta `latencyPoseToReceive` . A parte do lado do servidor da latência geral é informação que não está disponível para o cliente. No entanto, o parágrafo seguinte explica como este valor é aproximadado através de entrada adicional do servidor e exposto através do `networkLatency` valor.

## <a name="performance-assessment-queries"></a>Consultas de avaliação de desempenho

As consultas de *avaliação* de desempenho fornecem informações mais aprofundadas sobre a carga de trabalho do CPU e da GPU no servidor. Uma vez que os dados são solicitados a partir do servidor, a consulta de um instantâneo de desempenho segue o padrão async habitual:

```cs
PerformanceAssessmentAsync _assessmentQuery = null;

void QueryPerformanceAssessment(AzureSession session)
{
    _assessmentQuery = session.Actions.QueryServerPerformanceAssessmentAsync();
    _assessmentQuery.Completed += (PerformanceAssessmentAsync res) =>
    {
        // do something with the result:
        PerformanceAssessment result = res.Result;
        // ...

        _assessmentQuery = null;
    };
}
```

```cpp
void QueryPerformanceAssessment(ApiHandle<AzureSession> session)
{
    ApiHandle<PerformanceAssessmentAsync> assessmentQuery = *session->Actions()->QueryServerPerformanceAssessmentAsync();
    assessmentQuery->Completed([] (ApiHandle<PerformanceAssessmentAsync> res)
    {
        // do something with the result:
        PerformanceAssessment result = res->GetResult();

        // ...

    });
}
```

Ao contrário do `FrameStatistics` objeto, o `PerformanceAssessment` objeto contém informações do lado do servidor:

| Membro | Explicação |
|:-|:-|
| timeCPU | Tempo médio do CPU do servidor por quadro em milissegundos |
| timeGPU | Tempo médio de GPU do servidor por quadro em milissegundos |
| utilizaçãoCPU | Utilização total do CPU do servidor em por cento |
| utilização GPU | Utilização total do GPU do servidor em por cento |
| memoryCPU | Total da utilização da memória principal do servidor em por cento |
| memóriaGPU | Utilização total de memória de vídeo dedicada em percentagem da GPU do servidor |
| networkLatency | A latência média aproximada da rede de ida e volta em milissegundos. Na ilustração acima, isto corresponde à soma das setas vermelhas. O valor é calculado subtraindo o tempo real de renderização do servidor a partir do `latencyPoseToReceive` valor de `FrameStatistics` . Embora esta aproximação não seja exata, dá alguma indicação da latência da rede, isolada dos valores de latência calculados no cliente. |
| poligonizador | O número de triângulos renderizados num quadro. Este número também inclui os triângulos que são abatidos mais tarde durante a renderização. Isto significa que este número não varia muito em diferentes posições da câmara, mas o desempenho pode variar drasticamente, dependendo da taxa de abate do triângulo.|

Para ajudá-lo a avaliar os valores, cada porção vem com uma classificação de qualidade como **Great,** **Good,** **Medíocre,** ou **Bad.**
Esta métrica de avaliação fornece uma indicação aproximada da saúde do servidor, mas não deve ser visto como absoluto. Por exemplo, assuma que vê uma pontuação 'medíocre' para o tempo da GPU. Considera-se medíocre porque se aproxima do limite para o orçamento global do prazo de tempo. No seu caso, no entanto, pode ser um bom valor, porque está a tornar um modelo complexo.

## <a name="statistics-debug-output"></a>Produção de depurar estatísticas

A classe `ARRServiceStats` é uma classe C# que envolve tanto as estatísticas de quadros como as consultas de avaliação de desempenho e fornece uma funcionalidade conveniente para devolver estatísticas como valores agregados ou como uma corda pré-construída. O código a seguir é a forma mais fácil de mostrar estatísticas do lado do servidor na aplicação do seu cliente.

```cs
ARRServiceStats _stats = null;

void OnConnect()
{
    _stats = new ARRServiceStats();
}

void OnDisconnect()
{
    _stats = null;
}

void Update()
{
    if (_stats != null)
    {
        // update once a frame to retrieve new information and build average values
        _stats.Update(Service.CurrentActiveSession);

        // retrieve a string with relevant stats information
        InfoLabel.text = _stats.GetStatsString();
    }
}
```

O código acima povoa a etiqueta de texto com o seguinte texto:

![Saída de cadeia arrServiceStats](./media/arr-service-stats.png)

A `GetStatsString` API forma uma cadeia de todos os valores, mas cada valor único também pode ser consultado programáticamente a partir do `ARRServiceStats` caso.

Há também variantes dos membros, que agregam os valores ao longo do tempo. Consulte os membros com sufixo, `*Avg` `*Max` ou `*Total` . O membro `FramesUsedForAverage` indica quantos quadros foram usados para esta agregação.

## <a name="api-documentation"></a>Documentação da API

* [C# RemoteManager.QueryServerPerformanceAssessmentAsync()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.queryserverperformanceassessmentasync)
* [C++ RemoteManager::QueryServerPerformanceAssessmentAsync()](/cpp/api/remote-rendering/remotemanager#queryserverperformanceassessmentasync)

## <a name="next-steps"></a>Passos seguintes

* [Criar traços de desempenho](../../how-tos/performance-tracing.md)
* [Configurar a conversão do modelo](../../how-tos/conversion/configure-model-conversion.md)