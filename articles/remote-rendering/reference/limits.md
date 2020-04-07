---
title: Limites
description: Limitações de código para funcionalidades SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 6e23c716a965cdf178c9f847900e66c141d5d7eb
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680339"
---
# <a name="limits"></a>Limites

Várias funcionalidades têm limitações de tamanho ou contagem devido a detalhes internos do sistema de funcionamento.

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



