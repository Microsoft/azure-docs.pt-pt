---
title: Ligar ao GitHub
description: Utilize o GitHub para especificar as referências da sua entidade modelo de dados comuns
author: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 5d555d7bc4d3aae9c016cacbe17b68c30859d99a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372285"
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