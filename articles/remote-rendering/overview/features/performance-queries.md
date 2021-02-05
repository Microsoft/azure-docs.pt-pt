---
title: Consultas de desempenho do lado do servidor
description: Como fazer consultas de desempenho do lado do servidor através de chamadas API
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 30b8104a9596f0b32f731c507b513b204f5d1acd
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594100"
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
void QueryFrameData(RenderingSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
```

```cpp
void QueryFrameData(ApiHandle<RenderingSession> session)
{
    FrameStatistics frameStatistics;
    if (session->GetGraphicsBinding()->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        // do something with the result
    }
}
```

O objeto recuperado `FrameStatistics` contém os seguintes membros:

| Membro | Explicação |
|:-|:-|
| LatênciaPoseToReceive | A latência da câmara representa a estimativa no dispositivo do cliente até que uma moldura do servidor para esta pose esteja totalmente disponível para a aplicação do cliente. Este valor inclui ida e volta de rede, tempo de renderização do servidor, descodificar vídeo e compensação de nervosismo. Consulte **o intervalo 1 na ilustração acima.**|
| LatênciaReceiveToPresent | Latência da disponibilidade de um quadro remoto recebido até que a aplicação do cliente ligue para PresentFrame no CPU. Consulte **o intervalo 2 na ilustração acima.**|
| LatencyPresentToDisplay  | Latência de apresentar uma moldura no CPU até que o visor se aceda. Este valor inclui o tempo de GPU do cliente, qualquer buffering de quadro realizado pelo SO, reprojecção de hardware e tempo de verificação do ecrã dependente do dispositivo. Consulte **o intervalo 3 na ilustração acima.**|
| TimeSinceLastPresent | O tempo entre chamadas subsequentes para PresentFrame no CPU. Valores superiores à duração do visor (por exemplo, 16,6 ms num dispositivo cliente de 60 Hz) indicam problemas causados pela aplicação do cliente que não termina a sua carga de trabalho de CPU a tempo.|
| VídeoFramesVido | O número de fotogramas recebidos do servidor no último segundo. |
| VideoFrameReusedCount | Número de fotogramas recebidos no último segundo que foram utilizados no dispositivo mais de uma vez. Valores não nulos indicam que os quadros tiveram de ser reutilizados e reprojados devido ao nervosismo da rede ou ao tempo excessivo de renderização do servidor. |
| VideoFramesSkipped | Número de quadros recebidos no último segundo que foram descodificados, mas não mostrados no ecrã porque chegou uma moldura mais recente. Valores não nulos indicam que o nervosismo da rede fez com que vários quadros fossem atrasados e, em seguida, chegassem juntos ao dispositivo cliente numa explosão. |
| VideoFramesDiscarded | Muito semelhante ao **VideoFramesSkipped**, mas a razão para ser descartado é que uma moldura chegou tão tarde que já nem pode ser correlacionada com qualquer pose pendente. Se esta devoluções acontecer, há uma grave contenção da rede.|
| VídeoFrameMinDelta | Quantidade mínima de tempo entre dois quadros consecutivos que chegam durante o último segundo. Juntamente com o VideoFrameMaxDelta, esta gama dá uma indicação de nervosismo causada pela rede ou pelo codec de vídeo. |
| VídeoFrameMaxDelta | Quantidade máxima de tempo entre dois quadros consecutivos que chegam durante o último segundo. Juntamente com o VideoFrameMinDelta, esta gama dá uma indicação de nervosismo causada pela rede ou pelo codec de vídeo. |

A soma de todos os valores de latência é tipicamente muito maior do que o tempo de fotogramas disponível a 60 Hz. Isto é OK, porque vários quadros estão em voo em paralelo, e novos pedidos de fotogramas são iniciados à taxa de fotogramas desejada, como mostra a ilustração. No entanto, se a latência se tornar demasiado grande, afeta a qualidade da [reprojecção tardia](../../overview/features/late-stage-reprojection.md)da fase tardia , e pode comprometer a experiência geral.

`VideoFramesReceived`, `VideoFrameReusedCount` e pode ser usado para medir o desempenho da rede e do `VideoFramesDiscarded` servidor. Uma combinação de um `VideoFramesReceived` baixo valor e um valor elevado pode indicar o `VideoFrameReusedCount` congestionamento da rede ou o fraco desempenho do servidor. Um valor elevado `VideoFramesDiscarded` também indica congestionamento de rede.

Por `TimeSinceLastPresent` último, `VideoFrameMinDelta` e dê uma ideia `VideoFrameMaxDelta` da variação dos quadros de vídeo e das chamadas presentes locais. Alta variação significa taxa de fotogramas instáveis.

Nenhum dos valores acima indica claramente a latência da rede pura (as setas vermelhas na ilustração), porque a hora exata em que o servidor está ocupado deve ser subtraída do valor de ida e volta `LatencyPoseToReceive` . A parte do lado do servidor da latência geral é informação que não está disponível para o cliente. No entanto, o parágrafo seguinte explica como este valor é aproximadado através de entrada adicional do servidor e exposto através do `NetworkLatency` valor.

## <a name="performance-assessment-queries"></a>Consultas de avaliação de desempenho

As consultas de *avaliação* de desempenho fornecem informações mais aprofundadas sobre a carga de trabalho do CPU e da GPU no servidor. Uma vez que os dados são solicitados a partir do servidor, a consulta de um instantâneo de desempenho segue o padrão async habitual:

```cs
async void QueryPerformanceAssessment(RenderingSession session)
{
    try
    {
        PerformanceAssessment result = await session.Connection.QueryServerPerformanceAssessmentAsync();
        // do something with result...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void QueryPerformanceAssessment(ApiHandle<RenderingSession> session)
{
    session->Connection()->QueryServerPerformanceAssessmentAsync([](Status status, PerformanceAssessment result) {
        if (status == Status::OK)
        {
            // do something with result...
        }
    });
}
```

Ao contrário do `FrameStatistics` objeto, o `PerformanceAssessment` objeto contém informações do lado do servidor:

| Membro | Explicação |
|:-|:-|
| TimeCPU | Tempo médio do CPU do servidor por quadro em milissegundos |
| TimeGPU | Tempo médio de GPU do servidor por quadro em milissegundos |
| UtilizaçãoCPU | Utilização total do CPU do servidor em por cento |
| UtilizaçãoGPU | Utilização total do GPU do servidor em por cento |
| MemoryCPU | Total da utilização da memória principal do servidor em por cento |
| MemoryGPU | Utilização total de memória de vídeo dedicada em percentagem da GPU do servidor |
| Latência de rede | A latência média aproximada da rede de ida e volta em milissegundos. Na ilustração acima, este valor corresponde à soma das setas vermelhas. O valor é calculado subtraindo o tempo real de renderização do servidor a partir do `LatencyPoseToReceive` valor de `FrameStatistics` . Embora esta aproximação não seja exata, dá alguma indicação da latência da rede, isolada dos valores de latência calculados no cliente. |
| Poligonizado | O número de triângulos renderizados num quadro. Este número também inclui os triângulos que são abatidos mais tarde durante a renderização. Isto significa que este número não varia muito em diferentes posições da câmara, mas o desempenho pode variar drasticamente, dependendo da taxa de abate do triângulo.|

Para ajudá-lo a avaliar os valores, cada porção vem com uma classificação de qualidade como **Great,** **Good,** **Medíocre,** ou **Bad.**
Esta métrica de avaliação fornece uma indicação aproximada da saúde do servidor, mas não deve ser visto como absoluto. Por exemplo, assuma que vê uma pontuação 'medíocre' para o tempo da GPU. Considera-se medíocre porque se aproxima do limite para o orçamento global do prazo de tempo. No seu caso, no entanto, pode ser um bom valor, porque está a tornar um modelo complexo.

## <a name="statistics-debug-output"></a>Produção de depurar estatísticas

A classe `ServiceStatistics` é uma classe C# que envolve tanto as estatísticas de quadros como as consultas de avaliação de desempenho e fornece uma funcionalidade conveniente para devolver estatísticas como valores agregados ou como uma corda pré-construída. O código a seguir é a forma mais fácil de mostrar estatísticas do lado do servidor na aplicação do seu cliente.

```cs
ServiceStatistics _stats = null;

void OnConnect()
{
    _stats = new ServiceStatistics();
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

A `GetStatsString` API forma uma cadeia de todos os valores, mas cada valor único também pode ser consultado programáticamente a partir do `ServiceStatistics` caso.

Há também variantes dos membros, que agregam os valores ao longo do tempo. Consulte os membros com sufixo, `*Avg` `*Max` ou `*Total` . O membro `FramesUsedForAverage` indica quantos quadros foram usados para esta agregação.

## <a name="api-documentation"></a>Documentação da API

* [C# RenderingConnection.QueryServerPerformanceAssessmentAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.queryserverperformanceassessmentasync)
* [C++ RenderingConnection::QueryServerPerformanceAssessmentAsync()](/cpp/api/remote-rendering/renderingconnection#queryserverperformanceassessmentasync)

## <a name="next-steps"></a>Passos seguintes

* [Criar traços de desempenho](../../how-tos/performance-tracing.md)
* [Configurar a conversão do modelo](../../how-tos/conversion/configure-model-conversion.md)