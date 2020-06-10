---
title: Limitações
description: Limitações de código para funcionalidades SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: c35930a93751cc82c3145d414a2a09b3a1e90c53
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84659834"
---
# <a name="limitations"></a>Limitações

Uma série de funcionalidades têm tamanho, contagem ou outras limitações.

## <a name="azure-frontend"></a>Azure Frontend

* Total de instâncias AzureFrontend por processo: 16.
* Total de instâncias de AzureSsssion por AzureFrontend: 16.

## <a name="objects"></a>Objetos

* Total de objetos admissíveis de um único tipo (Entidade, CutPlaneComponent, etc.): 16.777.215.
* Total admissível de planos de corte ativo: 8.

## <a name="materials"></a>Materiais

* Total de materiais admissíveis num ativo: 65.535.

## <a name="overall-number-of-polygons"></a>Número total de polígonos

O número admissível de polígonos para todos os modelos carregados depende do tamanho do VM, tal como transmitido à [gestão da sessão REST API:](../how-tos/session-rest-api.md#create-a-session)

| Tamanho da VM | Número máximo de polígonos |
|:--------|:------------------|
|padrão| 20 milhões |
|premium| sem limite |


## <a name="platform-limitations"></a>Limitações da plataforma

**Ambiente de trabalho do Windows 10**

* UWP/x86 é a única plataforma UWP suportada. UWP/x64 não é suportado.
* Win32/x64 é a única plataforma Win32 suportada. Win32/x86 não é suportado.

**Hololens 2**

* A [função de câmara de PV](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) não é suportada.
