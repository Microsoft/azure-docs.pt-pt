---
title: Gestão do gráfico de fluxo de dados de mapeamento
description: Como gerir e editar eficazmente o gráfico de fluxo de dados de mapeamento
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2020
ms.openlocfilehash: 0cdad47123d69ca7cee468c5bb0cea3268d73bfe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89420116"
---
# <a name="managing-the-mapping-data-flow-graph"></a>Gestão do gráfico de fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Os fluxos de dados de mapeamento são da autoria usando uma superfície de design conhecido como o gráfico de fluxo de dados. No gráfico, a lógica de transformação é construída da esquerda para a direita e os fluxos de dados adicionais são adicionados de cima para baixo. Para adicionar uma nova transformação, selecione o sinal de mais no canto inferior direito de uma transformação existente.

![Telas](media/data-flow/canvas2.png "Telas")

À medida que os fluxos de dados se advogem mais complexos, utilize os seguintes mecanismos para navegar e gerir eficazmente o gráfico de fluxo de dados. 

## <a name="moving-transformations"></a>Transformações em movimento

No mapeamento dos fluxos de dados, um conjunto de lógicas de transformação conectadas é conhecido como um **fluxo**. O campo **de fluxo de entrada** dita qual o fluxo de dados que alimenta a transformação atual. Cada transformação tem um ou dois fluxos de entrada dependendo da sua função e representa um fluxo de saída. O esquema de saída dos fluxos de entrada determina quais os metadados da coluna que podem ser referenciados pela transformação atual.

![Nó de movimento](media/data-flow/move-nodes.png "nó movimento")

Ao contrário da tela do gasoduto, as transformações do fluxo de dados não são editadas usando um modelo de arrasto e queda. Para alterar o fluxo de entrada ou "mover" uma transformação, escolha um valor diferente da queda do **fluxo de entrada.** Quando o fizeres, todas as transformações a jusante vão mover-se ao lado da transformação editada. O gráfico atualiza-se automaticamente para mostrar o novo fluxo lógico. Se alterar o fluxo de entrada para uma transformação que já tem transformação a jusante, será criado um novo ramo ou fluxo de dados paralelo. Saiba mais sobre [novos ramos no fluxo de dados de mapeamento.](data-flow-new-branch.md)

## <a name="hide-graph-and-show-graph"></a>Esconda gráfico e mostre gráfico

Ao editar a sua transformação, pode expandir o painel de configuração para ocupar toda a tela, escondendo o gráfico. Clique no chevron virado para cima localizado no lado direito da tela.

![Ocultar gráfico](media/data-flow/hide-graph.png "esconder gráfico")

Quando o gráfico está escondido, pode mover-se entre transformações dentro de um fluxo clicando **em Seguinte** ou **Anterior**. Clique no chevron virado para baixo para mostrar o gráfico.

![Mostrar gráfico](media/data-flow/show-graph.png "mostrar gráfico")

## <a name="searching-for-transformations"></a>Procurando transformações

Para encontrar rapidamente uma transformação no seu gráfico, clique no ícone **'Procurar'** acima da definição de zoom.

![Pesquisa](media/data-flow/search-1.png "Gráfico de pesquisa")

Você pode pesquisar por nome de transformação ou descrição para localizar uma transformação.

![Pesquisa](media/data-flow/search-2.png "Gráfico de pesquisa")

## <a name="hide-reference-nodes"></a>Ocultar os nosdes de referência

Se o seu fluxo de dados tiver alguma junção, procura, existe ou transformações sindicais, o fluxo de dados mostra nós de referência para todos os fluxos de entrada. Se desejar minimizar a quantidade de espaço vertical tomado, pode minimizar os seus nós de referência. Para tal, clique corretamente na tela e selecione **Ocultar nós de referência**.

![Ocultar os nosdes de referência](media/data-flow/hide-reference-nodes.png "Ocultar os nosdes de referência")

## <a name="next-steps"></a>Passos seguintes

Depois de completar a lógica de fluxo de dados, ligue o [modo de depurar](concepts-data-flow-debug-mode.md) e teste-o numa pré-visualização de dados.
