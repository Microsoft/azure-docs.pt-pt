---
title: Atividade variável do apêndice na fábrica de dados Azure
description: Saiba como definir a atividade variável do apêndice para adicionar um valor a uma variável de matriz existente definida num pipeline de Fábrica de Dados
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.date: 10/09/2018
ms.openlocfilehash: 1ca58fc208bb02d137b977e0b18857e8c87a5440
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783851"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Atividade variável do apêndice na fábrica de dados Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Utilize a atividade da Variável do Apêndice para adicionar um valor a uma variável de matriz existente definida num pipeline da Data Factory.

## <a name="type-properties"></a>Tipo de propriedades

Propriedade | Descrição | Necessário
-------- | ----------- | --------
name | Nome da atividade em pipeline | Sim
descrição | Texto descrevendo o que a atividade faz | não
tipo | O tipo de atividade é apendável | sim
valor | Valor literal ou de objeto de expressão de corda usado para anexar em variável especificada | sim
nome variável | Nome da variável que será modificada por atividade, a variável deve ser do tipo 'Matriz' | sim

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre uma atividade de fluxo de controlo relacionada suportada pela Data Factory: 

- [Definir a Atividade Variável](control-flow-set-variable-activity.md)
