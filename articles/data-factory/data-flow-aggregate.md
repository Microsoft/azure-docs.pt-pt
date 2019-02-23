---
title: Mapeamento de transformação de agregação de fluxo de dados de fábrica de dados do Azure
description: Transformação agregado de fluxo de dados de fábrica dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 7b488b243c0520befb6b5470598f460b5a759fed
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56730041"
---
# <a name="azure-data-factory-mapping-data-flow-aggregate-transformation"></a>Mapeamento de transformação de agregação de fluxo de dados de fábrica de dados do Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A transformação de agregação é onde definirá as agregações de colunas em seus fluxos de dados. No construtor de expressões, pode definir os tipos diferentes de agregações (ou seja, SUM, MIN, Máx, CONTAGEM, etc.) e criar um novo campo no resultado que inclui estas agregações com campos de grupo opcional.

![Agregar as opções de transformação](media/data-flow/agg.png "agregar 1")

## <a name="group-by"></a>Agrupar por
(Opcional) Escolha uma cláusula Group by para a agregação e usar o nome de uma coluna existente ou um novo nome. Utilize "Adicionar coluna" adicionar mais cláusulas Agrupar por e clique na caixa de texto junto ao nome da coluna para iniciar o construtor de expressões para selecionar apenas uma coluna existente, a combinação de colunas ou expressões para o agrupamento.

## <a name="the-aggregate-column-tab"></a>O separador de coluna agregada 
(Obrigatório) Escolha o separador de coluna agregada para criar as expressões de agregação. Pode escolher uma coluna existente para substituir o valor com a agregação ou criar um novo campo com o novo nome para a agregação. A expressão que deseja usar para a agregação será introduzida na caixa junto do Seletor de nome de coluna da direita. Clicar na caixa de texto, abrirá o construtor de expressões.

![Agregar as opções de transformação](media/data-flow/agg2.png "agregador")

## <a name="data-preview-in-expression-builder"></a>Pré-visualização de dados no construtor de expressões

No modo de depuração, o construtor de expressões não é possível produzir pré-visualizações de dados com as funções de agregação. Para ver as pré-visualizações de dados para transformações de agregação, feche o construtor de expressões e visualizar o perfil de dados a partir do estruturador de fluxo de dados.
