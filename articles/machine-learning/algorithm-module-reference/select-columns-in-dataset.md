---
title: 'Selecionar colunas no conjunto de conjuntos: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo selecionar colunas no conjunto de informações em Azure Machine Learning para escolher um subconjunto de colunas a ser usado em operações de downstream.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 714148815fa4ff543ecbf86ab21e8d51fe775543
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153779"
---
# <a name="select-columns-in-dataset-module"></a>Selecionar colunas no módulo DataSet

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para escolher um subconjunto de colunas a ser usado em operações de downstream. O módulo não remove fisicamente as colunas do conjunto de dados de origem; em vez disso, cria um subconjunto de colunas, tal como uma *visão* ou *projeção*da base de dados.

Esse módulo é útil quando você precisa limitar as colunas disponíveis para uma operação downstream ou se deseja reduzir o tamanho do conjunto de um removendo colunas desnecessárias.

As colunas no DataSet são geradas na mesma ordem que nos dados originais, mesmo se você especificá-las em uma ordem diferente.

## <a name="how-to-use"></a>Como utilizar

Este módulo não tem parâmetros. Use o seletor de coluna para escolher as colunas a serem incluídas ou excluídas.

### <a name="choose-columns-by-name"></a>Escolher colunas por nome

Há várias opções no módulo para escolher colunas por nome: 

+ Filtrar e Pesquisar

    Clique na opção **POR NOME.**

    Se você tiver conectado um conjunto de um que já está populado, uma lista de colunas disponíveis deverá aparecer. Se nenhuma coluna aparecer, talvez seja necessário executar módulos upstream para exibir a lista de colunas.

    Para filtrar a lista, digite na caixa de pesquisa. Por exemplo, se escrever a letra `w` na caixa de pesquisa, a lista é filtrada para mostrar os nomes da coluna que contêm a letra `w`.

    Selecione colunas e clique no botão de seta para a direita para mover as colunas selecionadas para a lista no painel à direita.

    + Para selecionar uma gama contínua de nomes de colunas, prima **Shift + Click**.
    + Para adicionar colunas individuais à seleção, prima **Ctrl + Clique**em .

    Clique no botão de marca de seleção para salvar e fechar.

+ Usar nomes em combinação com outras regras

    Clique na opção **COM REGRAS.**
    
    Escolha uma regra, como mostrar colunas de um tipo de dados específico.

    Em seguida, clique em colunas individuais desse tipo por nome para adicioná-las à lista de seleção.

+ Digite ou cole uma lista separada por vírgulas de nomes de coluna

    Se seu conjunto de seus conjuntos de seus for amplo, pode ser mais fácil usar índices ou listas de nomes geradas, em vez de selecionar colunas individualmente. Supondo que você preparou a lista com antecedência:

    1. Clique na opção **COM REGRAS.** 
    2. Selecione **Nenhuma coluna,** selecione **Incluir,** e, em seguida, clique no interior da caixa de texto com a marca de exclamação vermelha. 
    3. Cole ou digite uma lista separada por vírgulas de nomes de coluna validados anteriormente. Você não poderá salvar o módulo se alguma coluna tiver um nome inválido, portanto, certifique-se de verificar os nomes com antecedência.
    
    Você também pode usar esse método para especificar uma lista de colunas usando seus valores de índice. 

### <a name="choose-by-type"></a>Escolher por tipo

Se utilizar a opção **REGRAS COM,** pode aplicar várias condições nas seleções de colunas. Por exemplo, talvez seja necessário obter apenas colunas de recursos de um tipo de dados numérico.

A opção **START WITH** determina o seu ponto de partida e é importante para a compreensão dos resultados. 

+ Se selecionar a opção **TODAS AS COLUNAS,** todas as colunas são adicionadas à lista. Em seguida, deve utilizar a opção **Excluir** para *remover* colunas que satisfaçam determinadas condições. 

    Por exemplo, você pode iniciar com todas as colunas e, em seguida, remover colunas por nome ou por tipo.

+ Se selecionar a opção **NO COLUMNS,** a lista de colunas começa vazia. Em seguida, especifique as condições para *adicionar* colunas à lista. 

    Se aplicar várias regras, cada condição é **aditiva.** Por exemplo, digamos que você inicie sem colunas e, em seguida, adicione uma regra para obter todas as colunas numéricas. No conjunto de linhas de preço do automóvel, isso resulta em 16 colunas. Em seguida, clique no sinal **de+** para adicionar uma nova condição, e selecione Incluir todas **as funcionalidades**. O conjunto de resultados resultante inclui todas as colunas numéricas, além de todas as colunas de recurso, incluindo algumas colunas de recursos de cadeia de caracteres.

### <a name="choose-by-column-index"></a>Escolher por índice de coluna

O índice de coluna refere-se à ordem da coluna dentro do DataSet original.

+ As colunas são numeradas sequencialmente a partir de 1.  
+ Para obter um intervalo de colunas, use um hífen. 
+ Não são permitidas especificações abertas, como `1-` ou `-3`.
+ Valores de índice duplicados (ou nomes de coluna) não são permitidos e podem resultar em um erro.

Por exemplo, supondo que o conjunto de seus conjuntos de seus tem pelo menos oito colunas, você pode colar qualquer um dos seguintes exemplos para retornar várias colunas não contíguas: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

o exemplo final não resulta num erro; no entanto, devolve uma única instância de coluna `4`.



### <a name="change-order-of-columns"></a>Alterar a ordem das colunas

A opção **Permitir duplicados e preservar** a ordem da coluna na seleção começa com uma lista vazia e adiciona colunas que especifica por nome ou por índice. Ao contrário de outras opções, que sempre retornam colunas em sua "ordem natural", essa opção gera as colunas na ordem em que você as nomeou ou lista. 

Por exemplo, em um conjunto de linhas com as colunas Col1, Col2, Col3 e COL4, você pode inverter a ordem das colunas e deixar a coluna 2, especificando uma das seguintes listas:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 