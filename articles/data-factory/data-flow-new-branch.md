---
title: Várias ramificações no fluxo de dados de mapeamento
description: Replicando fluxos de dados no fluxo de dados de mapeamento com várias ramificações
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 71fb9f1ba9952be0e6b3910dd1079aa6d3c0482d
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834525"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Criando uma nova ramificação no fluxo de dados de mapeamento

Adicione uma nova ramificação para fazer vários conjuntos de operações e transformações no mesmo fluxo de dados. A adição de uma nova ramificação é útil quando você deseja usar a mesma origem para vários coletores ou para unir dados automaticamente.

Uma nova ramificação pode ser adicionada da lista de transformação semelhante a outras transformações. A **nova ramificação** só estará disponível como uma ação quando houver uma transformação existente após a transformação que você está tentando ramificar.

![Adicionando uma nova ramificação](media/data-flow/new-branch2.png "Adicionando uma nova ramificação")

No exemplo abaixo, o fluxo de dados está lendo dados de viagem de táxi. A saída agregada por dia e fornecedor é necessária. Em vez de criar dois fluxos de dados separados que lêem da mesma fonte, uma nova ramificação pode ser adicionada. Dessa forma, ambas as agregações podem ser executadas como parte do mesmo fluxo de dados. 

![Adicionando uma nova ramificação](media/data-flow/new-branch.png "Adicionando uma nova ramificação")
