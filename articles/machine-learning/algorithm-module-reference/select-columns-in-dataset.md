---
title: 'Selecione colunas em conjunto de dados: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Select Columns in Dataset em Azure Machine Learning para escolher um subconjunto de colunas para utilizar em operações a jusante.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 7926d5dd33df5538713f8de152dbbace2bc29864
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90890290"
---
# <a name="select-columns-in-dataset-module"></a>Selecione colunas no módulo dataset

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para escolher um subconjunto de colunas para utilizar em operações a jusante. O módulo não remove fisicamente as colunas do conjunto de dados de origem; em vez disso, cria um subconjunto de colunas, tal como uma visão ou *projeção*de bases de *dados.*

Este módulo é útil quando é necessário limitar as colunas disponíveis para uma operação a jusante, ou se pretende reduzir o tamanho do conjunto de dados removendo colunas não necessárias.

As colunas no conjunto de dados são saídas na mesma ordem que nos dados originais, mesmo que as especifique numa ordem diferente.

## <a name="how-to-use"></a>Como utilizar

Este módulo não tem parâmetros. Utilize o seletor de colunas para escolher as colunas para incluir ou excluir.

### <a name="choose-columns-by-name"></a>Escolha colunas pelo nome

Existem múltiplas opções no módulo para escolher colunas pelo nome: 

+ Filtrar e pesquisar

    Clique na opção **POR NOME.**

    Se tiver ligado um conjunto de dados que já está povoado, deve aparecer uma lista de colunas disponíveis. Se não aparecerem colunas, poderá ter de executar módulos a montante para visualizar a lista de colunas.

    Para filtrar a lista, digite a caixa de pesquisa. Por exemplo, se escrever a letra `w` na caixa de pesquisa, a lista é filtrada para mostrar os nomes das colunas que contêm a letra `w` .

    Selecione colunas e clique no botão de seta direita para mover as colunas selecionadas para a lista no painel direito.

    + Para selecionar uma gama contínua de nomes de colunas, prima **Shift + Click**.
    + Para adicionar colunas individuais à seleção, prima **Ctrl + Click**.

    Clique no botão de marca de verificação para guardar e fechar.

+ Use nomes em combinação com outras regras

    Clique na opção **COM REGRAS.**
    
    Escolha uma regra, como mostrar colunas de um tipo de dados específico.

    Em seguida, clique em colunas individuais desse tipo por nome, para adicioná-las à lista de seleção.

+ Digite ou cole uma lista separada por vírgulas de nomes de colunas

    Se o seu conjunto de dados for amplo, poderá ser mais fácil utilizar índices ou listas de nomes geradas, em vez de selecionar colunas individualmente. Assumindo que preparou a lista com antecedência:

    1. Clique na opção **COM REGRAS.** 
    2. Selecione **Não colunas**, selecione  **Incluir**e, em seguida, clique no interior da caixa de texto com o ponto de exclamação vermelho. 
    3. Cole ou digite uma lista separada por vírgula de nomes de colunas previamente validados. Não é possível guardar o módulo se alguma coluna tiver um nome inválido, por isso certifique-se de verificar previamente os nomes.
    
    Também pode utilizar este método para especificar uma lista de colunas utilizando os seus valores de índice. 

### <a name="choose-by-type"></a>Escolha por tipo

Se utilizar a opção **COM REGRAS,** pode aplicar várias condições nas seleções das colunas. Por exemplo, pode precisar de obter apenas colunas de recurso de um tipo de dados numérico.

A opção **START WITH** determina o seu ponto de partida e é importante para compreender os resultados. 

+ Se selecionar a opção **TODAS AS COLUNAS,** todas as colunas são adicionadas à lista. Em seguida, deve utilizar a opção **Excluir** para *remover* colunas que satisfaçam determinadas condições. 

    Por exemplo, pode começar com todas as colunas e depois remover colunas pelo nome, ou por tipo.

+ Se selecionar a opção **NO COLUMNS,** a lista de colunas começa a ficar vazia. Em seguida, especifique as condições para *adicionar* colunas à lista. 

    Se aplicar várias regras, cada condição é **aditiva.** Por exemplo, digamos que se começa sem colunas e depois adiciona-se uma regra para obter todas as colunas numéricas. No conjunto de dados de preços do Automóvel, isso resulta em 16 colunas. Em seguida, clique no **+** sinal para adicionar uma nova condição e selecione **Inclua todas as funcionalidades**. O conjunto de dados resultante inclui todas as colunas numéricas, além de todas as colunas de funcionalidades, incluindo algumas colunas de características de cordas.

### <a name="choose-by-column-index"></a>Escolha por índice de coluna

O índice da coluna refere-se à ordem da coluna dentro do conjunto de dados original.

+ As colunas são numeradas sequencialmente a partir de 1.  
+ Para obter uma gama de colunas, use um hífen. 
+ Especificações abertas, tais como `1-` ou `-3` não são permitidas.
+ Os valores de índice duplicados (ou nomes de colunas) não são permitidos e podem resultar num erro.

Por exemplo, assumindo que o seu conjunto de dados tem pelo menos oito colunas, pode colar em qualquer um dos seguintes exemplos para devolver várias colunas não contíguas: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

o exemplo final não resulta num erro; no entanto, devolve uma única instância de coluna `4` .



### <a name="change-order-of-columns"></a>Alterar a ordem das colunas

A opção **Permitir duplicar e preservar a ordem da coluna na seleção** começa com uma lista vazia e adiciona colunas que especifica pelo nome ou por índice. Ao contrário de outras opções, que devolvem sempre as colunas na sua "ordem natural", esta opção produz as colunas na ordem que nomeia ou enumera. 

Por exemplo, num conjunto de dados com as colunas Col1, Col2, Col3 e Col4, pode inverter a ordem das colunas e deixar de fora a coluna 2, especificando qualquer uma das seguintes listas:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 