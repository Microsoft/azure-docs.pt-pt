---
title: Composição de Depuração
description: Visão geral dos efeitos de renderização do lado do servidor
author: jumeder
ms.author: jumeder
ms.date: 04/09/2020
ms.topic: article
ms.openlocfilehash: dc07b20340b852eadeb7c93e5cef2ed2092b3641
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758661"
---
# <a name="debug-rendering"></a>Composição de Depuração

A API de renderização de depuração fornece uma gama de opções globais para alterar a renderização do lado do servidor com diferentes efeitos de depuração.

## <a name="available-debug-rendering-effects"></a>Efeitos de renderização de depuração disponíveis

|Definição                          | Efeito                               |
|---------------------------------|:-------------------------------------|
|Contador de quadros                    | Torna uma sobreposição de texto no canto superior esquerdo da moldura. O texto mostra o ID do quadro do servidor atual, que é continuamente incrementado como receitas de renderização. |
|Contagem de polígonos                    | Torna uma sobreposição de texto no canto superior esquerdo da moldura. O texto mostra a quantidade atualmente renderizada de polígonos, o mesmo valor que consultado por [consultas de desempenho do lado do Servidor](performance-queries.md)| 
|Armação de arame                        | Se ativado, toda a geometria do objeto carregada no servidor será renderizada no modo de wireframe. Só as bordas dos polígonos serão raspadas neste modo. |

O seguinte código permite estes efeitos de depuração:

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

![Renderização de depuração](./media/debug-rendering.png)

> [!NOTE]
> Todos os efeitos de renderização de depuração são configurações globais que afetam todo o quadro.

## <a name="use-cases"></a>Casos de utilização

A API de renderização de depuração destina-se a tarefas simples de depuração, como verificar se a ligação de serviço está realmente a funcionar corretamente. As opções de renderização de texto afetam diretamente os quadros de vídeo a jusante. Permitir-lhes verificar se novos quadros são recebidos e descodificados corretamente por vídeo.

No entanto, os efeitos fornecidos não dão qualquer introspeção detalhada na saúde do serviço. As consultas de desempenho do lado do Servidor são [recomendadas](performance-queries.md) para este caso de utilização.

## <a name="performance-considerations"></a>Considerações de desempenho

* Permitir as sobreposições de texto incorre pouco ou nada sobre a sobrecarga de desempenho.
* Ativar o modo de armação electrónica incorre numa sobrecarga de desempenho não trivial, embora possa variar dependendo da cena. Para cenas complexas, este modo pode fazer com que a taxa de fotogramas desça abaixo do objetivo de 60-Hz.

## <a name="next-steps"></a>Passos seguintes

* [Modos de composição](../../concepts/rendering-modes.md)
* [Consultas de desempenho do lado do servidor](performance-queries.md)
