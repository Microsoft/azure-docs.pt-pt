---
title: Ligar ao GitHub
description: Utilize o GitHub para especificar as referências da sua entidade modelo de dados comuns
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 3ad666a477595b8367b388c4343df8aaed561a87
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84435444"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>Utilize o GitHub para ler referências de entidades de modelos de dados comuns

O conector GitHub na Azure Data Factory é utilizado apenas para receber o esquema de referência da entidade para o formato [Common Data Model](format-common-data-model.md) no fluxo de dados de mapeamento.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado ao GitHub.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **GitHub**. | sim
| userName | Nome de utilizador GitHub | sim |
| palavra-passe | Senha do GitHub | sim |

## <a name="next-steps"></a>Passos Seguintes

Crie um [conjunto de dados de origem](data-flow-source.md) no fluxo de dados de mapeamento.