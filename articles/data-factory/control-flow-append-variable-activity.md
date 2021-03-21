---
title: Atividade variável do apêndice na fábrica de dados Azure
description: Saiba como definir a atividade variável do apêndice para adicionar um valor a uma variável de matriz existente definida num pipeline de Fábrica de Dados
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.date: 10/09/2018
ms.openlocfilehash: 5a9ed44e05c371460ae3ceab721f2236f6ec7fd6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383419"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Atividade variável do apêndice na fábrica de dados Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Utilize a atividade da Variável do Apêndice para adicionar um valor a uma variável de matriz existente definida num pipeline da Data Factory.

## <a name="type-properties"></a>Tipo de propriedades

Propriedade | Descrição | Necessário
-------- | ----------- | --------
name | Nome da atividade em pipeline | Yes
descrição | Texto descrevendo o que a atividade faz | não
tipo | O tipo de atividade é apendável | sim
valor | Valor literal ou de objeto de expressão de corda usado para anexar em variável especificada | sim
nome variável | Nome da variável que será modificada por atividade, a variável deve ser do tipo 'Matriz' | sim

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre uma atividade de fluxo de controlo relacionada suportada pela Data Factory: 

- [Definir a Atividade Variável](control-flow-set-variable-activity.md)
