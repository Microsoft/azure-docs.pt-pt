---
title: Limitações
description: Limitações de código para funcionalidades SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 61085a5d28fcd74bbf6a393ddc0731e36094a63f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617503"
---
# <a name="limitations"></a>Limitações

Uma série de funcionalidades têm tamanho, contagem ou outras limitações.

## <a name="azure-frontend"></a>Azure Frontend

* Total de instâncias AzureFrontend por processo: 16.
* Total de instâncias de AzureSsssion por AzureFrontend: 16.

## <a name="objects"></a>Objetos

* Total de objetos admissíveis de um único tipo (Entidade, CutPlaneComponent, etc.): 16.777.215.
* Total admissível de planos de corte ativo: 8.

## <a name="geometry"></a>Geometria

* **Animação:** As animações limitam-se a animar transformações individuais de objetos de jogo. Animações esqueléticas com animações de esfolar ou vértice não são suportadas. As faixas de animação do ficheiro do ativo de origem não são preservadas. Em vez disso, as animações de transformação de objetos têm de ser conduzidas pelo código do cliente.
* **Shaders personalizados:** A autoria de shaders personalizados não é suportada. Só podem ser [utilizados materiais de cor incorporados](../overview/features/color-materials.md) ou [materiais PBR.](../overview/features/pbr-materials.md)
* **Número máximo de materiais distintos** num ativo: 65.535. Para obter mais informações sobre a redução automática da contagem de material, consulte o capítulo [da des duplicação do material.](../how-tos/conversion/configure-model-conversion.md#material-de-duplication)
* **Dimensão máxima de uma única textura:** 16.384 x 16.384. Texturas de origem maiores serão reduzidas em tamanho pelo processo de conversão.

### <a name="overall-number-of-polygons"></a>Número total de polígonos

O número admissível de polígonos para todos os modelos carregados depende do tamanho do VM, tal como transmitido à [gestão da sessão REST API:](../how-tos/session-rest-api.md#create-a-session)

| Tamanho do servidor | Número máximo de polígonos |
|:--------|:------------------|
|padrão| 20 milhões |
|premium| sem limite |

Para obter informações detalhadas sobre esta limitação, consulte o capítulo do tamanho do [servidor.](../reference/vm-sizes.md)

## <a name="platform-limitations"></a>Limitações da plataforma

**Ambiente de trabalho do Windows 10**

* Win32/x64 é a única plataforma Win32 suportada. Win32/x86 não é suportado.

**HoloLens 2**

* A [função de câmara de PV](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) não é suportada.
