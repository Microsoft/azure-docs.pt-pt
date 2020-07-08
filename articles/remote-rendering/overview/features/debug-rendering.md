---
title: Composição de Depuração
description: Visão geral dos efeitos de depuragem do lado do servidor
author: jumeder
ms.author: jumeder
ms.date: 06/15/2020
ms.topic: article
ms.openlocfilehash: aa6e6dced3dfd32896489db2ed76704304dbc745
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565444"
---
# <a name="debug-rendering"></a>Composição de Depuração

A API de renderização de depurg fornece um leque de opções globais para alterar a renderização do lado do servidor com diferentes efeitos de depurar.

## <a name="available-debug-rendering-effects"></a>Efeitos de renderização de depurações disponíveis

|Definição                          | Efeito                               |
|---------------------------------|:-------------------------------------|
|Contador de quadros                    | Torna uma sobreposição de texto no canto superior esquerdo da armação. O texto mostra o ID do quadro do lado do servidor atual, que é continuamente incrementado como receitas de renderização. |
|Contagem de polígono                    | Torna uma sobreposição de texto no canto superior esquerdo da armação. O texto mostra a quantidade atualmente renderizada de polígonos, o mesmo valor que as [consultas de desempenho do lado do Servidor](performance-queries.md)| 
|Wireframe                        | Se ativado, toda a geometria do objeto carregada no servidor será renderizada no modo de wireframe. Apenas as bordas dos polígonos serão rasterizadas neste modo. |

O seguinte código permite estes efeitos de depurações:

```cs
void EnableDebugRenderingEffects(AzureSession session, bool highlight)
{
    DebugRenderingSettings settings = session.Actions.DebugRenderingSettings;

    // Enable frame counter text overlay on the server side rendering
    settings.RenderFrameCount = true;

    // Enable polygon count text overlay on the server side rendering
    settings.RenderPolygonCount = true;

    // Enable wireframe rendering of object geometry on the server
    settings.RenderWireframe = true;
}
```

```cpp
void EnableDebugRenderingEffects(ApiHandle<AzureSession> session, bool highlight)
{
    ApiHandle<DebugRenderingSettings> settings = *session->Actions()->DebugRenderingSettings();

    // Enable frame counter text overlay on the server side rendering
    settings->RenderFrameCount(true);

    // Enable polygon count text overlay on the server side rendering
    settings->RenderPolygonCount(true);

    // Enable wireframe rendering of object geometry on the server
    settings->RenderWireframe(true);
}
```

![Renderização de depurante](./media/debug-rendering.png)

> [!NOTE]
> Todos os efeitos de renderização de depurg são configurações globais que afetam todo o quadro.

## <a name="use-cases"></a>Casos de utilização

A API de renderização de depurante destina-se a tarefas simples de depurar, como verificar se a ligação de serviço está realmente a funcionar corretamente. As opções de renderização de texto afetam diretamente os quadros de vídeo transmitidos abaixo. Ativar-lhes verifica se novos quadros são recebidos e descodificados corretamente.

No entanto, os efeitos fornecidos não dão qualquer introspeção detalhada à saúde do serviço. Recomenda-se que as consultas de desempenho do lado do Servidor sejam [recomendadas](performance-queries.md) para este caso de utilização.

## <a name="performance-considerations"></a>Considerações de desempenho

* Permitir que as sobreposições de texto incorre pouco ou nada sobre a sobrecarga de desempenho.
* Ativar o modo de estrutura de arame incorre numa sobrecarga de desempenho não trivial, embora possa variar dependendo da cena. Para cenas complexas, este modo pode fazer com que a taxa de fotogramas baixe abaixo do alvo de 60-Hz.

## <a name="next-steps"></a>Próximos passos

* [Modos de composição](../../concepts/rendering-modes.md)
* [Consultas de desempenho do lado do servidor](performance-queries.md)
