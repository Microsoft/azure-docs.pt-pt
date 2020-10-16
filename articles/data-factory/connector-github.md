---
title: Ligar ao GitHub
description: Utilize o GitHub para especificar as referências da sua entidade modelo de dados comuns
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 0e17580524d6a67934aed83c6f745583b92e2422
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84771041"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>Utilize o GitHub para ler referências de entidades de modelos de dados comuns

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O conector GitHub na Azure Data Factory é utilizado apenas para receber o esquema de referência da entidade para o formato [Common Data Model](format-common-data-model.md) no fluxo de dados de mapeamento.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado ao GitHub.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **GitHub**. | sim
| userName | Nome de utilizador GitHub | sim |
| palavra-passe | Senha do GitHub | sim |

## <a name="next-steps"></a>Passos Seguintes

Crie um [conjunto de dados de origem](data-flow-source.md) no fluxo de dados de mapeamento.