---
title: Acrescentar atividade variável na fábrica de dados do Azure | Documentos da Microsoft
description: Saiba como configurar a atividade de acrescentar variável para adicionar um valor para uma variável de matriz existente definida no pipeline do Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/09/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: a5efe946000eb00e65d314ae53d7136761e2109d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557227"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Acrescentar atividade variável na fábrica de dados do Azure

Utilize a atividade de acrescentar variável para adicionar um valor para uma variável de matriz existente definida no pipeline do Data Factory.

## <a name="type-properties"></a>Propriedades do tipo

Propriedade | Descrição | Necessário
-------- | ----------- | --------
nome | Nome da atividade no pipeline | Sim
description | Texto que descreve o que faz a atividade | não
tipo | Tipo de atividade é AppendVariable | sim
value | Cadeia de caracteres literal ou expressão de valor de objeto utilizado para acrescentar numa variável especificada | sim
variableName | Nome da variável que será modificada pela atividade, a variável tem de ser do tipo "Array" | sim

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre uma atividade de fluxo de controle relacionados suportada pelo Data Factory: 

- [Atividade de variável de conjunto](control-flow-set-variable-activity.md)
