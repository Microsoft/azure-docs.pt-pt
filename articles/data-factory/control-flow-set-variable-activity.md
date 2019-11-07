---
title: Definir atividade de variável no Azure Data Factory
description: Saiba como usar a atividade definir variável para definir o valor de uma variável existente definida em um pipeline Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2018
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: 2589e56d3170b235f31d07f902eb75c749e8573f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679305"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Definir atividade de variável no Azure Data Factory

Use a atividade definir variável para definir o valor de uma variável existente do tipo cadeia de caracteres, bool ou matriz definida em um pipeline Data Factory.

## <a name="type-properties"></a>Propriedades do tipo

Propriedade | Descrição | Necessário
-------- | ----------- | --------
nome | Nome da atividade no pipeline | Sim
descrição | Texto que descreve o que a atividade faz | não
tipo | O tipo de atividade é setvariance | sim
valor | Literal de cadeia de caracteres ou valor de objeto de expressão usado para definir a variável especificada | sim
variableName | Nome da variável que será definida por esta atividade | sim


## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre uma atividade de fluxo de controle relacionada com suporte pelo Data Factory: 

- [Atividade de acréscimo de variável](control-flow-append-variable-activity.md)
