---
title: Atividade acrescentar variável no Azure Data Factory
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
ms.openlocfilehash: bcecd3411b6e6e751032f33862a671d8adbca046
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679978"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Atividade acrescentar variável no Azure Data Factory

Use a atividade acrescentar variável para adicionar um valor a uma variável de matriz existente definida em um pipeline Data Factory.

## <a name="type-properties"></a>Propriedades do tipo

Propriedade | Descrição | Necessário
-------- | ----------- | --------
nome | Nome da atividade no pipeline | Sim
descrição | Texto que descreve o que a atividade faz | não
tipo | O tipo de atividade é AppendVariable | sim
valor | Literal de cadeia de caracteres ou valor de objeto de expressão usado para acrescentar à variável especificada | sim
variableName | Nome da variável que será modificada pela atividade, a variável deve ser do tipo ' array ' | sim

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre uma atividade de fluxo de controle relacionada com suporte pelo Data Factory: 

- [Definir atividade variável](control-flow-set-variable-activity.md)
