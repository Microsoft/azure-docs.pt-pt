---
title: Definir atividade variável na fábrica de dados azure
description: Saiba como utilizar a atividade variável definida para definir o valor de uma variável existente definida num pipeline data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/10/2018
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: 88500ecbc56b34551a0cbd3ca94727ba4bbcda9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930648"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Definir atividade variável na fábrica de dados azure

Utilize a atividade Variável definida para definir o valor de uma variável existente de cadeia de tipo, bool ou Matriz definida num pipeline data Factory.

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | Descrição | Necessário
-------- | ----------- | --------
nome | Nome da atividade em pipeline | Sim
descrição | Texto descrevendo o que a atividade faz | não
tipo | Tipo de atividade é SetVariable | sim
valor | Valor literal de corda ou objeto de expressão usado para definir variável especificada | sim
nome variável | Nome da variável que será definida por esta atividade | sim


## <a name="next-steps"></a>Passos seguintes
Conheça uma atividade de fluxo de controlo relacionada suportada pela Data Factory: 

- [Acrescentar a Atividade Variável](control-flow-append-variable-activity.md)
