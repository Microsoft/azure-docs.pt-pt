---
title: Definir atividade de variável no Azure Data Factory | Microsoft Docs
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
ms.openlocfilehash: cfe6dd63234a7750fe01614d6f1b38bb7cce1adb
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142431"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Definir atividade de variável no Azure Data Factory

Use a atividade definir variável para definir o valor de uma variável existente do tipo cadeia de caracteres, bool ou matriz definida em um pipeline Data Factory.

## <a name="type-properties"></a>Propriedades do tipo

Propriedade | Descrição | Requerido
-------- | ----------- | --------
name | Nome da atividade no pipeline | Sim
description | Texto que descreve o que a atividade faz | não
type | O tipo de atividade é setvariance | sim
value | Literal de cadeia de caracteres ou valor de objeto de expressão usado para definir a variável especificada | sim
variableName | Nome da variável que será definida por esta atividade | sim


## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre uma atividade de fluxo de controle relacionada com suporte pelo Data Factory: 

- [Atividade de acréscimo de variável](control-flow-append-variable-activity.md)
