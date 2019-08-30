---
title: Atividade de acréscimo de variável no Azure Data Factory | Microsoft Docs
description: Saiba como definir a atividade de variável de acréscimo para adicionar um valor a uma variável de matriz existente definida em um pipeline de Data Factory
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
ms.openlocfilehash: 24808c9ed4bda5e8d3b0ce23ab93bc59eb260374
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141731"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Atividade acrescentar variável no Azure Data Factory

Use a atividade acrescentar variável para adicionar um valor a uma variável de matriz existente definida em um pipeline Data Factory.

## <a name="type-properties"></a>Propriedades do tipo

Propriedade | Descrição | Requerido
-------- | ----------- | --------
name | Nome da atividade no pipeline | Sim
description | Texto que descreve o que a atividade faz | não
type | O tipo de atividade é AppendVariable | sim
value | Literal de cadeia de caracteres ou valor de objeto de expressão usado para acrescentar à variável especificada | sim
variableName | Nome da variável que será modificada pela atividade, a variável deve ser do tipo ' array ' | sim

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre uma atividade de fluxo de controle relacionada com suporte pelo Data Factory: 

- [Definir atividade variável](control-flow-set-variable-activity.md)
