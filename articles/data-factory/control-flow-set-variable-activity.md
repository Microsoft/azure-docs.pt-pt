---
title: Definir atividade de variável no Azure Data Factory
description: Saiba como usar a atividade definir variável para definir o valor de uma variável existente definida em um pipeline Data Factory
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
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930648"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Definir atividade de variável no Azure Data Factory

Use a atividade definir variável para definir o valor de uma variável existente do tipo cadeia de caracteres, bool ou matriz definida em um pipeline Data Factory.

## <a name="type-properties"></a>Propriedades do tipo

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
nome | Nome da atividade no pipeline | Sim
descrição | Texto que descreve o que a atividade faz | não
tipo | O tipo de atividade é setvariance | sim
valor | Literal de cadeia de caracteres ou valor de objeto de expressão usado para definir a variável especificada | sim
variableName | Nome da variável que será definida por esta atividade | sim


## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre uma atividade de fluxo de controle relacionada com suporte pelo Data Factory: 

- [Atividade de acréscimo de variável](control-flow-append-variable-activity.md)
