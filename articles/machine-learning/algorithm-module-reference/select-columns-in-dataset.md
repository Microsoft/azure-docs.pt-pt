---
title: 'Selecione colunas no conjunto de dados: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar a selecionar colunas no módulo de conjunto de dados no serviço Azure Machine Learning para escolher um subconjunto de colunas para utilizar nas operações de downstream.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: e7407f62bd3401411d56076b298bd8cd134ece62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028099"
---
# <a name="select-columns-in-dataset-module"></a>Selecionar colunas no módulo de conjunto de dados

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para escolher um subconjunto de colunas para utilizar nas operações de downstream. O módulo não remove fisicamente as colunas do conjunto de dados de origem; em vez disso, ele cria um subconjunto de colunas, tal como uma base de dados *view* ou *projeção*.

Este módulo é útil quando tiver de limitar as colunas disponíveis para uma operação de downstream, ou se quiser reduzir o tamanho do conjunto de dados ao remover colunas desnecessárias.

As colunas no conjunto de dados são produzidas pela mesma ordem como os dados originais, mesmo que especifique-os numa ordem diferente.

## <a name="how-to-use"></a>Como utilizar

Este módulo não tem parâmetros. Utilize o Seletor de colunas para escolher as colunas a incluir ou excluir.

### <a name="choose-columns-by-name"></a>Escolher colunas por nome

Existem várias opções no módulo para escolher colunas por nome: 

+ Filtrar e pesquisar

    Clique nas **pelo nome** opção.

    Se tiver ligado um conjunto de dados que já está preenchido, deverá aparecer uma lista de colunas disponíveis. Se não existem colunas aparecerem, precisará de executar os módulos a montante para ver a lista de colunas.

    Para filtrar a lista, escreva na caixa de pesquisa. Por exemplo, se digitar a letra `w` na caixa de pesquisa, a lista está filtrada para mostrar os nomes das colunas que contêm essa letra `w`.

    Selecionar colunas e clique no botão de seta para a direita para mover as colunas selecionadas para a lista no painel da direita.

    + Selecionar um intervalo contínuo de nomes de colunas, prima **Shift + clique**.
    + Para adicionar colunas individuais para a seleção, prima **Ctrl + clique**.

    Clique no botão de marca de verificação para guardar e fechar.

+ Utilize nomes em combinação com outras regras

    Clique nas **com regras** opção.
    
    Escolha uma regra, como mostrar colunas de um tipo de dados específicos.

    Em seguida, clique em colunas individuais desse tipo por nome, adicioná-los à lista de seleção.

+ Escreva ou cole uma lista separada por vírgulas de nomes de colunas

    Se o conjunto de dados é grande, ela poderá ser mais fácil de usar os índices ou gerado listas de nomes, em vez de selecionar colunas individualmente. Partindo do princípio de que está preparado a lista com antecedência:

    1. Clique nas **com regras** opção. 
    2. Selecione **nenhuma coluna**, selecione **inclusão**e, em seguida, clique dentro da caixa de texto com o ponto de exclamação vermelho. 
    3. Colar ou escreva uma lista separada por vírgulas de nomes de colunas anteriormente validados. Não é possível guardar o módulo se qualquer coluna tem um nome inválido, por isso, certifique-se verificar os nomes com antecedência.
    
    Também pode utilizar este método para especificar uma lista de colunas com seus valores de índice. 

### <a name="choose-by-type"></a>Escolha por tipo

Se utilizar o **com regras** opção, pode aplicar várias condições nas seleções de coluna. Por exemplo, poderá ter de obter apenas as colunas de funcionalidades de um tipo de dados numéricos.

O **começar com** opção determina o seu ponto de partida e é importante para compreender os resultados. 

+ Se selecionar a **todas as colunas** opção, todas as colunas são adicionadas à lista. Em seguida, tem de utilizar o **excluir** a opção de *remover* colunas que atendem a determinadas condições. 

    Por exemplo, pode começar com todas as colunas e, em seguida, remover colunas por nome ou por tipo.

+ Se selecionar a **colunas não** opção, a lista de colunas começa vazio. Em seguida, especificar condições para *adicionar* colunas à lista. 

    Se aplicar várias regras, cada condição for **aditiva**. Por exemplo, digamos que comece com nenhuma coluna e, em seguida, adicione uma regra para obter todas as colunas numéricas. No conjunto de dados do preço automóvel, o que resulta em 16 colunas. Em seguida, clique a **+** iniciar sessão para adicionar uma nova condição e selecionar **incluir todos os recursos**. O conjunto de dados resultante inclui todas as colunas numéricas, além de todas as colunas de funcionalidades, incluindo algumas colunas de recurso de cadeia de caracteres.

### <a name="choose-by-column-index"></a>Escolha ao índice de coluna

O índice de coluna refere-se to the order of a coluna no conjunto de dados original.

+ Colunas são numeradas sequencialmente começando em 1.  
+ Para obter um intervalo de colunas, utilize um hífen. 
+ Especificações abertas tal como `1-` ou `-3` não são permitidos.
+ Valores de índice duplicado (ou nomes de colunas) não são permitidos e poderão resultar num erro.

Por exemplo, partindo do princípio de que o conjunto de dados tem colunas, pelo menos, oito, pode colar em qualquer um dos exemplos a seguir para retornar a várias colunas não contíguas: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

o exemplo final não resulta num erro; No entanto, ele retorna uma única instância da coluna `4`.



### <a name="change-order-of-columns"></a>Alterar a ordem das colunas

A opção **permite duplicatas e preservar a ordem das colunas na seleção** começa com uma lista vazia e adiciona as colunas que especificar por nome ou pelo índice. Ao contrário de outras opções, que sempre devolvem colunas, "por ordem natural", esta opção devolve as colunas pela ordem em que o nome ou listá-los. 

Por exemplo, num conjunto de dados com as colunas Col1, Col2, Col3 e Col4, foi possível inverter a ordem das colunas e omitir a coluna 2, ao especificar qualquer uma das seguintes listas:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 