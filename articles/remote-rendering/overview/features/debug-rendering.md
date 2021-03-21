---
title: Composição de Depuração
description: Visão geral dos efeitos de depuragem do lado do servidor
author: jumeder
ms.author: jumeder
ms.date: 06/15/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f6d79f41843069fe6cafe1fa1358ac6c1aab12e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99591740"
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
void EnableDebugRenderingEffects(RenderingSession session, bool highlight)
{
    DebugRenderingSettings settings = session.Connection.DebugRenderingSettings;

    // Enable frame counter text overlay on the server side rendering
    settings.RenderFrameCount = true;

    // Enable polygon count text overlay on the server side rendering
    settings.RenderPolygonCount = true;

    // Enable wireframe rendering of object geometry on the server
    settings.RenderWireframe = true;
}
```

```cpp
void EnableDebugRenderingEffects(ApiHandle<RenderingSession> session, bool highlight)
{
    ApiHandle<DebugRenderingSettings> settings = session->Connection()->GetDebugRenderingSettings();

    // Enable frame counter text overlay on the server side rendering
    settings->SetRenderFrameCount(true);

    // Enable polygon count text overlay on the server side rendering
    settings->SetRenderPolygonCount(true);

    // Enable wireframe rendering of object geometry on the server
    settings->SetRenderWireframe(true);
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

## <a name="api-documentation"></a>Documentação da API

* [C++ RenderingConnection::D ebugRenderingSettings()](/cpp/api/remote-rendering/renderingconnection#debugrenderingsettings)

## <a name="next-steps"></a>Passos seguintes

* [Modos de composição](../../concepts/rendering-modes.md)
* [Consultas de desempenho do lado do servidor](performance-queries.md)