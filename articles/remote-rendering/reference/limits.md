---
title: Limitações
description: Limitações de código para funcionalidades SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 6a1a51ee09422607ae1392704add4d49d3367d57
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759052"
---
# <a name="limitations"></a>Limitações

Várias funcionalidades têm tamanho, contagem ou outras limitações.

## <a name="azure-frontend"></a>Azure Frontend

* Total de instâncias AzureFrontend por processo: 16.
* Total de instâncias AzureSession por AzureFrontend: 16.

## <a name="objects"></a>Objetos

* Total de objetos admissíveis de um único tipo (Entidade, CutPlaneComponent, etc.): 16.777.215.
* Total de aviões de corte ativo admissíveis: 8.

## <a name="materials"></a>Materiais

* Total de materiais admissíveis num ativo: 65.535.

## <a name="overall-number-of-polygons"></a>Número global de polígonos

O número admissível de polígonos para todos os modelos carregados depende do tamanho do VM, tal como passado para [a gestão da sessão REST API:](../how-tos/session-rest-api.md#create-a-session)

| Tamanho da VM | Número máximo de polígonos |
|:--------|:------------------|
|padrão| 20 milhões |
|premium| sem limite |


## <a name="platform-limitations"></a>Limitações da plataforma

**Ambiente de trabalho do Windows 10**

* O UWP/x86 é a única plataforma suportada da UWP. O UWP/x64 não é suportado.

**Hololens 2**

* O [render da função de câmara PV](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) não é suportado.
