---
title: Apêndice Atividade Variável na Fábrica de Dados Azure
description: Saiba como definir a atividade variável do apêndice para adicionar um valor a uma variável de matriz existente definida num pipeline data Factory
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73679978"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Apêndice Atividade Variável na Fábrica de Dados Azure

Utilize a atividade da Variável do Apêndice para adicionar um valor a uma variável de matriz existente definida num pipeline data Factory.

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | Descrição | Necessário
-------- | ----------- | --------
nome | Nome da atividade em pipeline | Sim
descrição | Texto descrevendo o que a atividade faz | não
tipo | Tipo de atividade é AppendVariable | sim
valor | Valor literal de corda ou objeto de expressão usado para anexar em variável especificada | sim
nome variável | Nome da variável que será modificada por atividade, a variável deve ser do tipo 'Array' | sim

## <a name="next-steps"></a>Passos seguintes
Conheça uma atividade de fluxo de controlo relacionada suportada pela Data Factory: 

- [Definir a Atividade Variável](control-flow-set-variable-activity.md)
