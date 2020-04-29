---
title: 'Selecione Colunas no Conjunto de Dados: Referência do Módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar as Colunas Select no módulo Dataset no Azure Machine Learning para escolher um subconjunto de colunas para utilizar em operações a jusante.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 714148815fa4ff543ecbf86ab21e8d51fe775543
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77153779"
---
# <a name="select-columns-in-dataset-module"></a>Selecione Colunas no módulo Dataset

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para escolher um subconjunto de colunas para utilizar em operações a jusante. O módulo não remove fisicamente as colunas do conjunto de dados de origem; em vez disso, cria um subconjunto de colunas, tal como uma *visão* ou *projeção*da base de dados.

Este módulo é útil quando é necessário limitar as colunas disponíveis para uma operação a jusante, ou se pretender reduzir o tamanho do conjunto de dados removendo colunas desnecessárias.

As colunas no conjunto de dados são saídas na mesma ordem que nos dados originais, mesmo que as especifique numa ordem diferente.

## <a name="how-to-use"></a>Como utilizar

Este módulo não tem parâmetros. Utilize o seletor de colunas para escolher as colunas para incluir ou excluir.

### <a name="choose-columns-by-name"></a>Escolha colunas pelo nome

Existem várias opções no módulo para escolher colunas pelo nome: 

+ Filtrar e pesquisar

    Clique na opção **POR NOME.**

    Se tiver ligado um conjunto de dados já povoado, deve aparecer uma lista de colunas disponíveis. Se não aparecerem colunas, poderá ser necessário executar módulos a montante para ver a lista de colunas.

    Para filtrar a lista, digite na caixa de pesquisa. Por exemplo, se escrever `w` a letra na caixa de pesquisa, a lista é `w`filtrada para mostrar os nomes da coluna que contêm a letra .

    Selecione colunas e clique no botão de seta direita para mover as colunas selecionadas para a lista no painel da direita.

    + Para selecionar uma gama contínua de nomes de colunas, prima **Shift + Click**.
    + Para adicionar colunas individuais à seleção, prima **Ctrl + Clique**em .

    Clique no botão checkmark para guardar e fechar.

+ Use nomes em combinação com outras regras

    Clique na opção **COM REGRAS.**
    
    Escolha uma regra, como mostrar colunas de um tipo de dados específico.

    Em seguida, clique em colunas individuais desse tipo por nome, para adicioná-las à lista de seleção.

+ Digite ou pasta uma lista separada de vírinas de nomes de colunas

    Se o seu conjunto de dados for amplo, pode ser mais fácil usar índices ou listas de nomes gerados, em vez de selecionar colunas individualmente. Assumindo que preparou a lista com antecedência:

    1. Clique na opção **COM REGRAS.** 
    2. Selecione **Nenhuma coluna,** selecione **Incluir,** e, em seguida, clique no interior da caixa de texto com a marca de exclamação vermelha. 
    3. Colar ou escrever uma lista separada de vírina de nomes de colunas previamente validados. Não é possível guardar o módulo se alguma coluna tiver um nome inválido, por isso certifique-se de verificar previamente os nomes.
    
    Também pode utilizar este método para especificar uma lista de colunas utilizando os seus valores de índice. 

### <a name="choose-by-type"></a>Escolha por tipo

Se utilizar a opção **REGRAS COM,** pode aplicar várias condições nas seleções de colunas. Por exemplo, pode ser necessário obter apenas colunas de recurso de um tipo de dados numéricos.

A opção **START WITH** determina o seu ponto de partida e é importante para a compreensão dos resultados. 

+ Se selecionar a opção **TODAS AS COLUNAS,** todas as colunas são adicionadas à lista. Em seguida, deve utilizar a opção **Excluir** para *remover* colunas que satisfaçam determinadas condições. 

    Por exemplo, pode começar com todas as colunas e, em seguida, remover colunas pelo nome, ou por tipo.

+ Se selecionar a opção **NO COLUMNS,** a lista de colunas começa vazia. Em seguida, especifique as condições para *adicionar* colunas à lista. 

    Se aplicar várias regras, cada condição é **aditiva.** Por exemplo, digamos que começas sem colunas e depois adicionas uma regra para obter todas as colunas numéricas. No conjunto de dados de preços do Automóvel, que resulta em 16 colunas. Em seguida, **+** clique no sinal para adicionar uma nova condição e selecione **Incluir todas as funcionalidades**. O conjunto de dados resultante inclui todas as colunas numéricas, além de todas as colunas de recurso, incluindo algumas colunas de recurso de cadeia.

### <a name="choose-by-column-index"></a>Escolha por índice de coluna

O índice da coluna refere-se à ordem da coluna dentro do conjunto de dados original.

+ As colunas são numeradas sequencialmente a partir de 1.  
+ Para obter uma gama de colunas, use um hífen. 
+ Especificações abertas, `1-` tais `-3` como ou não, são permitidas.
+ Não são permitidos valores de índice duplicados (ou nomes de colunas) e podem resultar num erro.

Por exemplo, assumindo que o seu conjunto de dados tem pelo menos oito colunas, pode colar em qualquer um dos seguintes exemplos para devolver várias colunas não contíguas: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

o exemplo final não resulta num erro; no entanto, devolve uma `4`única instância de coluna.



### <a name="change-order-of-columns"></a>Alterar a ordem das colunas

A opção **Permitir duplicados e preservar** a ordem da coluna na seleção começa com uma lista vazia e adiciona colunas que especifica por nome ou por índice. Ao contrário de outras opções, que devolvem sempre colunas na sua "ordem natural", esta opção produz as colunas na ordem que as refere ou enumera. 

Por exemplo, num conjunto de dados com as colunas Col1, Col2, Col3 e Col4, pode inverter a ordem das colunas e deixar de fora a coluna 2, especificando qualquer uma das seguintes listas:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 