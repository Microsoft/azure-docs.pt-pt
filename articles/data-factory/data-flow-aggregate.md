---
title: Agregar transformação no mapeamento de fluxo de dados - Azure Data Factory | Documentos da Microsoft
description: Aprenda a agregar dados em escala no Azure Data Factory com a transformação do fluxo agregado da dados do mapeamento.
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 21135b26d4bc840b3fcb091e675e5e6bd24d8548
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312137"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Transformação de agregação no mapeamento de fluxo de dados 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A transformação de agregação é onde definirá as agregações de colunas em seus fluxos de dados. Com o construtor de expressões, pode definir tipos diferentes de agregações, como soma, MIN, MAX e COUNT que pode ser agrupado por colunas calculadas ou existentes.

## <a name="group-by"></a>Agrupar por
Selecione uma coluna existente ou crie uma nova coluna calculada para utilizar como um grupo por cláusula para a agregação. Para utilizar uma coluna existente, selecione a coluna pretendida na lista pendente. Para criar uma nova coluna calculada, coloque o cursor sobre a cláusula e clique em 'Coluna calculada'. Esta ação abre o [construtor de expressões de fluxo de dados](concepts-data-flow-expression-builder.md). Depois de criar a coluna calculada, introduza o nome de coluna de saída em campo "Nome como". Se desejar adicionar um grupo adicional através da cláusula, coloque o cursor sobre uma cláusula existente e clique em "+".

![Grupo de transformação de agregação por configurações](media/data-flow/agg.png "grupo de transformação de agregação pelas definições")

> [!NOTE]
> Um grupo por cláusula é opcional numa transformação de agregação.

## <a name="aggregate-column"></a>Coluna agregada 
Escolha o separador "Agregados" para criar expressões de agregação. Pode escolher uma coluna existente e substitua o valor com a agregação ou criar um novo campo com um novo nome. A expressão de agregação é introduzida na caixa junto do Seletor de nome de coluna da direita. Para editar a expressão, clique na caixa de texto para abrir o construtor de expressões. Para adicionar uma agregação adicional, coloque o cursor sobre uma expressão existente e clique em '+' para criar uma nova coluna de agregação ou [padrão da coluna](concepts-data-flow-column-pattern.md).

![Agregar as definições de agregação de transformação](media/data-flow/agg2.png "agregar as definições de agregação de transformação")

> [!NOTE]
> Cada expressão de agregação tem de conter pelo menos uma função de agregação.

> [!NOTE]
> No modo de depuração, o construtor de expressões não é possível produzir pré-visualizações de dados com as funções de agregação. Para ver as pré-visualizações de dados para transformações de agregação, feche o construtor de expressões e ver os dados através do separador de pré-visualização de dados.

## <a name="next-steps"></a>Passos Seguintes

-Definir a agregação baseado no Windows utilizando o [transformação de janela](data-flow-window.md)