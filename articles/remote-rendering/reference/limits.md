---
title: Limitações
description: Limitações de código para funcionalidades SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b6e501cdc1d5091a27d86406c6919587bedb261a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417696"
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

* A implantação "PC Autónomo" da Unidade não é apoiada. Use o UWP em vez disso.
* O UWP/x86 é a única plataforma suportada da UWP. O UWP/x64 não é suportado.

**Hololens 2**

* O [render da função de câmara PV](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) não é suportado.
