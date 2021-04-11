---
title: Ligar ao GitHub
description: Utilize o GitHub para especificar as referências da sua entidade modelo de dados comuns
author: dcstwh
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: weetok
ms.openlocfilehash: 7461a0332a36509c7bb6dfdd6db5948b056b35a6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222117"
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