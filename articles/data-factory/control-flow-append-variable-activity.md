---
title: Atividade variável do apêndice na fábrica de dados Azure
description: Saiba como definir a atividade variável do apêndice para adicionar um valor a uma variável de matriz existente definida num pipeline de Fábrica de Dados
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.date: 10/09/2018
ms.openlocfilehash: 5d74cd0fcd524f00d79eb3fbab386c602a413766
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81414201"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Atividade variável do apêndice na fábrica de dados Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Utilize a atividade da Variável do Apêndice para adicionar um valor a uma variável de matriz existente definida num pipeline da Data Factory.

## <a name="type-properties"></a>Tipo de propriedades

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
name | Nome da atividade em pipeline | Sim
descrição | Texto descrevendo o que a atividade faz | não
tipo | O tipo de atividade é apendável | sim
valor | Valor literal ou de objeto de expressão de corda usado para anexar em variável especificada | sim
nome variável | Nome da variável que será modificada por atividade, a variável deve ser do tipo 'Matriz' | sim

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre uma atividade de fluxo de controlo relacionada suportada pela Data Factory: 

- [Definir a Atividade Variável](control-flow-set-variable-activity.md)
