---
title: Definir variável atividade na fábrica de dados do Azure | Documentos da Microsoft
description: Saiba como utilizar a atividade definir variável para definir o valor de uma variável existente definido no pipeline do Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 71abfdff629f36b278488851b546c7371353a4d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60767970"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Atividade de variável de conjunto de Azure Data Factory

Utilize a atividade definir variável para definir o valor de uma variável existente do tipo cadeia, booleano ou matriz definidos no pipeline do Data Factory.

## <a name="type-properties"></a>Propriedades do tipo

Propriedade | Descrição | Necessário
-------- | ----------- | --------
name | Nome da atividade no pipeline | Sim
description | Texto que descreve o que faz a atividade | não
type | Tipo de atividade é SetVariable | sim
value | Cadeia de caracteres literal ou expressão de valor de objeto usado para definir a variável especificada | sim
variableName | Nome da variável que esta atividade definirá | sim


## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre uma atividade de fluxo de controle relacionados suportada pelo Data Factory: 

- [Acrescentar atividade variável](control-flow-append-variable-activity.md)
