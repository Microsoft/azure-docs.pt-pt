---
title: Limitações
description: Limitações de código para funcionalidades SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: e6b12c2bac4a9732f868f6a6ac3491ef993f54c3
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90976562"
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

* Total de materiais admissíveis num ativo: 65.535. Consulte [a des duplicação de materiais](../how-tos/conversion/configure-model-conversion.md#material-de-duplication) para obter mais informações.
* Dimensão máxima de uma única textura: 16.384 x 16.384. Texturas de origem maiores serão reduzidas pelo processo de conversão.

## <a name="overall-number-of-polygons"></a>Número total de polígonos

O número admissível de polígonos para todos os modelos carregados depende do tamanho do VM, tal como transmitido à [gestão da sessão REST API:](../how-tos/session-rest-api.md#create-a-session)

| Tamanho do servidor | Número máximo de polígonos |
|:--------|:------------------|
|padrão| 20 milhões |
|premium| sem limite |

Consulte o capítulo [do tamanho do servidor](../reference/vm-sizes.md) para obter informações mais detalhadas sobre esta limitação.

## <a name="platform-limitations"></a>Limitações da plataforma

**Ambiente de trabalho do Windows 10**

* Win32/x64 é a única plataforma Win32 suportada. Win32/x86 não é suportado.

**Hololens 2**

* A [função de câmara de PV](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) não é suportada.
