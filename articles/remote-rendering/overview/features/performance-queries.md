---
title: Consultas de desempenho do lado do servidor
description: Como fazer consultas de desempenho do lado do servidor através de chamadas API
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 9a28dee2d1e6d1355b729a56e8eeb8447e4ed8c8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80682029"
---
# <a name="server-side-performance-queries"></a>Consultas de desempenho do lado do servidor

Um bom desempenho de renderização no servidor é fundamental para taxas de fotogramas estáveis e uma boa experiência do utilizador. É importante monitorizar cuidadosamente as características de desempenho do servidor e otimizar sempre que necessário. Os dados de desempenho podem ser consultados através de funções de API dedicadas.

Mais impactante para o desempenho da renderização são os dados de entrada do modelo. Pode ajustar os dados de entrada conforme descrito na [configuração da conversão do modelo](../../how-tos/conversion/configure-model-conversion.md).

O desempenho da aplicação do lado do cliente também pode ser um estrangulamento. Para uma análise aprofundada do desempenho do cliente, é aconselhável fazer um rastreio de [desempenho.](../../how-tos/performance-tracing.md)

## <a name="clientserver-timeline"></a>Linha do tempo do cliente/servidor

Antes de entrar em detalhes sobre os vários valores de latência, vale a pena dar uma olhada nos pontos de sincronização entre o cliente e o servidor na linha do tempo:

![Linha do tempo do gasoduto](./media/server-client-timeline.png)

A ilustração mostra como:

* uma *estimativa pose* é iniciada pelo cliente a uma taxa de fotogramas constante de 60-Hz (a cada 16,6 ms)
* o servidor, em seguida, começa a renderização, com base na pose
* o servidor envia de volta a imagem de vídeo codificada
* o cliente descodifica a imagem, executa alguns trabalhos de CPU e GPU em cima dela e, em seguida, exibe a imagem

## <a name="frame-statistics-queries"></a>Consultas estatísticas de quadros

As estatísticas de quadros fornecem algumas informações de alto nível para o último quadro, como a latência. Os dados fornecidos `FrameStatistics` na estrutura são medidos do lado do cliente, pelo que a API é uma chamada sincronizada:

````c#
void QueryFrameData(AzureSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
````

O objeto `FrameStatistics` recuperado contém os seguintes membros:

| Membro | Explicação |
|:-|:-|
| latênciaPosetoreceber | A latência da câmara posiciona a estimativa do dispositivo cliente até que um quadro de servidor para esta pose esteja totalmente disponível para a aplicação do cliente. Este valor inclui a ida e volta da rede, tempo de renderização do servidor, descodificação de vídeo e compensação de nervosismo. Consulte **o intervalo 1 na ilustração acima.**|
| latênciaReceberToPresent | Latência da disponibilidade de um quadro remoto recebido até que a aplicação do cliente ligue para presentFrame no CPU. |
| latênciaPresentToDisplay  | Latência de apresentar uma moldura no CPU até que o visor se acenda. Este valor inclui o tempo de GPU do cliente, qualquer tampão de quadro realizado pelo SISTEMA, reprojeção de hardware e tempo de visualização dependente do dispositivo. Consulte **o intervalo 2 na ilustração acima.**|
| timeSinceLastPresent | O tempo entre as chamadas subsequentes para presentFrame no CPU. Valores superiores à duração do ecrã (por exemplo, 16,6 ms num dispositivo cliente de 60 Hz) indicam problemas causados pela aplicação do cliente que não terminam a carga de trabalho do CPU a tempo. Consulte **o intervalo 3 na ilustração acima.**|
| vídeoFramesRecebido | O número de quadros recebidos do servidor no último segundo. |
| videoFrameReusedCount | Número de quadros recebidos no último segundo que foram usados no dispositivo mais de uma vez. Os valores não nulos indicam que os quadros tiveram de ser reutilizados e reprojectados devido ao nervosismo da rede ou ao tempo excessivo de renderização do servidor. |
| vídeoFramesSkipped | Número de quadros recebidos no último segundo que foram descodificados, mas não mostrados em exibição porque chegou uma moldura mais recente. Os valores não nulos indicam que o nervosismo da rede fez com que vários quadros fossem atrasados e, em seguida, chegassem juntos ao dispositivo do cliente numa explosão. |
| vídeoFrames Descartados | Muito semelhante ao **vídeoFramesSkipped**, mas a razão para ser descartado é que uma moldura chegou tão tarde que já nem pode ser correlacionada com qualquer pose pendente. Se isto acontecer, há uma forte disputa de rede.|
| vídeoFrameMinDelta | Quantidade mínima de tempo entre dois quadros consecutivos que chegam durante o último segundo. Juntamente com o vídeoFrameMaxDelta, esta gama dá uma indicação de nervosismo causado pela rede ou pelo código de vídeo. |
| vídeoFrameMaxDelta | O tempo máximo entre dois quadros consecutivos que chegam durante o último segundo. Juntamente com o vídeoFrameMinDelta, esta gama dá uma indicação de nervosismo causado pela rede ou pelo código de vídeo. |

A soma de todos os valores de latência é tipicamente muito maior do que o tempo de trabalho disponível a 60 Hz. Isto é OK, porque vários quadros estão em voo em paralelo, e novos pedidos de fotogramas são iniciados à taxa de fotogramas desejada, como mostra a ilustração. No entanto, se a latência se tornar demasiado grande, afeta a qualidade da [reprojecção tardia](../../overview/features/late-stage-reprojection.md)e pode comprometer a experiência global.

`videoFramesReceived`, `videoFrameReusedCount`e `videoFramesDiscarded` pode ser usado para medir o desempenho da rede e do servidor. Se `videoFramesReceived` for `videoFrameReusedCount` baixo e for alto, isto pode indicar congestionamento de rede ou fraco desempenho do servidor. Um `videoFramesDiscarded` valor elevado também indica congestionamento da rede.

Por`timeSinceLastPresent`último, `videoFrameMinDelta`e `videoFrameMaxDelta` dar uma ideia da variação dos quadros de vídeo e das chamadas locais. Alta variação significa taxa de fotogramas inestável.

Nenhum dos valores acima dá indicação clara de latência pura da rede (as setas vermelhas na ilustração), porque `latencyPoseToReceive`a hora exata em que o servidor está ocupado a renderização precisa de ser subtraída do valor da ida e volta . A parte do lado do servidor da latência geral é informação que não está disponível para o cliente. No entanto, o parágrafo seguinte explica como este valor é aproximado `networkLatency` através de entrada adicional do servidor e exposto através do valor.

## <a name="performance-assessment-queries"></a>Consultas de avaliação de desempenho

As consultas de *avaliação* de desempenho fornecem informações mais aprofundadas sobre a carga de trabalho do CPU e da GPU no servidor. Uma vez que os dados são solicitados ao servidor, a consulta de um instantâneo de desempenho segue o padrão asincronizado habitual:

``` cs
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

Ao contrário `FrameStatistics` do objeto, o `PerformanceAssessment` objeto contém informações do lado do servidor:

| Membro | Explicação |
|:-|:-|
| timeCPU | Tempo médio do CPU do servidor por quadro em milissegundos |
| timeGPU | Tempo médio de GPU do servidor por quadro em milissegundos |
| utilizaçãoCPU | Total de utilização do CPU do servidor em percentagem |
| utilizaçãoGPU | Utilização total de GPU do servidor em percentagem |
| memoryCPU | Total de utilização da memória principal do servidor em percentagem |
| memoryGPU | Total de utilização dedicada da memória de vídeo em percentagem da GPU do servidor |
| redeLatency | A latência média da rede de ida e volta em milissegundos. Na ilustração acima, isto corresponde à soma das setas vermelhas. O valor é calculado subtraindo o tempo `latencyPoseToReceive` real `FrameStatistics`de renderização do servidor a partir do valor de . Embora esta aproximação não seja exata, dá alguma indicação da latência da rede, isolada dos valores de latência calculados no cliente. |
| polígonosRenderizados | O número de triângulos renderizados numa só moldura. Este número também inclui os triângulos que são abatidos mais tarde durante a renderização. Isto significa que este número não varia muito entre diferentes posições da câmara, mas o desempenho pode variar drasticamente, dependendo da taxa de abate do triângulo.|

Para ajudá-lo a avaliar os valores, cada porção vem com uma classificação de qualidade como **Great,** **Good**, **Mediocre,** ou **Bad**.
Esta métrica de avaliação fornece uma indicação aproximada da saúde do servidor, mas não deve ser vista como absoluta. Por exemplo, assuma que vê uma pontuação 'medíocre' para o tempo de GPU. Considera-se medíocre porque se aproxima do limite para o orçamento global do prazo. No seu caso, no entanto, pode ser um bom valor, no entanto, porque está a renderizar um modelo complexo.

## <a name="statistics-debug-output"></a>Produção de depuração estatística

A `ARRServiceStats` classe envolve tanto as estatísticas de quadros como as consultas de avaliação de desempenho e fornece uma funcionalidade conveniente para devolver as estatísticas como valores agregados ou como uma cadeia pré-construída. O código seguinte é a forma mais fácil de mostrar estatísticas do lado do servidor na sua aplicação de cliente.

``` cs
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

O código acima preenche a etiqueta de texto com o seguinte texto:

![Saída de cordas ArrServiceStats](./media/arr-service-stats.png)

A `GetStatsString` API forma uma série de todos os valores, mas cada valor `ARRServiceStats` único também pode ser consultado programáticamente a partir da instância.

Há também variantes dos membros, que agregam os valores ao longo do tempo. Consulte os membros `*Avg` `*Max`com `*Total`sufixo, ou . O `FramesUsedForAverage` membro indica quantos quadros foram usados para esta agregação.

## <a name="next-steps"></a>Passos seguintes

* [Criar vestígios de desempenho](../../how-tos/performance-tracing.md)
* [Configurar a conversão do modelo](../../how-tos/conversion/configure-model-conversion.md)
