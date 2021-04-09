---
title: Junte-se à transformação no fluxo de dados de mapeamento
description: Combine dados de duas fontes de dados usando a transformação de união no fluxo de dados de mapeamento da Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.openlocfilehash: ac84ce17f53145ffd85ffa31b6633d8b4b184962
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93042644"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Junte-se à transformação no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilize a transformação de união para combinar dados de duas fontes ou fluxos num fluxo de dados de mapeamento. O fluxo de saída incluirá todas as colunas de ambas as fontes correspondidas com base numa condição de união. 

## <a name="join-types"></a>Junte tipos

Mapear fluxos de dados suporta atualmente cinco tipos diferentes de junção.

### <a name="inner-join"></a>Unidos internos

O interior une apenas linhas de saída que têm valores correspondentes em ambas as tabelas.

### <a name="left-outer"></a>Esquerda Exterior

A junção exterior esquerda retorna todas as linhas do fluxo esquerdo e os registos combinados do fluxo direito. Se uma linha do fluxo esquerdo não tiver correspondência, as colunas de saída do fluxo direito estão definidas para NU. A saída serão as linhas devolvidas por uma junção interna mais as linhas incomparáveis do fluxo esquerdo.

> [!NOTE]
> O motor Spark utilizado pelos fluxos de dados irá ocasionalmente falhar devido a possíveis produtos cartesianos nas suas condições de união. Se isto ocorrer, pode mudar para uma cruz personalizada e introduzir manualmente a sua condição de união. Isto pode resultar num desempenho mais lento nos fluxos de dados, uma vez que o motor de execução pode precisar de calcular todas as linhas de ambos os lados da relação e, em seguida, filtrar linhas.

### <a name="right-outer"></a>Direito Exterior

A junção exterior direita retorna todas as linhas do fluxo direito e os registos combinados do fluxo esquerdo. Se uma linha do fluxo direito não tiver correspondência, as colunas de saída do fluxo esquerdo estão definidas para NU. A saída serão as linhas devolvidas por uma junção interna mais as linhas incomparáveis do fluxo direito.

### <a name="full-outer"></a>Outer completo

Unir completamente todas as colunas e linhas de ambos os lados com valores NULOS para colunas que não são compatíveis.

### <a name="custom-cross-join"></a>Cruz personalizada junta-se

Cruze as saídas o produto transversal dos dois fluxos com base numa condição. Se estiver a usar uma condição que não seja igualdade, especifique uma expressão personalizada como condição de união cruzada. O fluxo de saída será todas as linhas que cumprem a condição de união.

Você pode usar este tipo de junção para não-equi uniões e ```OR``` condições.

Se quiser produzir explicitamente um produto cartesiano completo, utilize a transformação da Coluna Derivada em cada um dos dois fluxos independentes antes da junção para criar uma chave sintética para combinar. Por exemplo, crie uma nova coluna na Coluna Derivada em cada fluxo chamada ```SyntheticKey``` e coloque-a igual a ```1``` . Em seguida, use ```a.SyntheticKey == b.SyntheticKey``` como expressão de união personalizada.

> [!NOTE]
> Certifique-se de incluir pelo menos uma coluna de cada lado da sua relação esquerda e direita numa ligação personalizada. A execução da cruz junta-se a valores estáticos em vez de colunas de cada lado resulta em análises completas de todo o conjunto de dados, fazendo com que o fluxo de dados funcione mal.

## <a name="configuration"></a>Configuração

1. Escolha o fluxo de dados com o qual se junta na entrega do **fluxo certo.**
1. Selecione o **seu tipo de Junção**
1. Escolha quais as colunas-chave que pretende combinar para que se junte à condição. Por predefinição, o fluxo de dados procura a igualdade entre uma coluna em cada fluxo. Para comparar através de um valor calculado, paire sobre a coluna dropdown e **selecione coluna Computada**.

![Junte-se à Transformação](media/data-flow/join.png "Participar")

### <a name="non-equi-joins"></a>Não-equi junta-se

Para utilizar um operador condicional como não é igual (!=) ou superior a (>) nas suas condições de união, altere o recuo do operador entre as duas colunas. As juntas não-equi requerem que pelo menos um dos dois streams seja transmitido através da radiodifusão **fixa** no **separador Otimize.**

![Não-equi aderir](media/data-flow/non-equi-join.png "Não-equi aderir")

## <a name="optimizing-join-performance"></a>Otimizar o desempenho de junção

Ao contrário da fusão em ferramentas como a SSIS, a transformação de junção não é uma operação de fusão obrigatória. As chaves de junção não requerem separação. A operação de junção ocorre com base na operação de ligação ideal em Spark, seja transmitida ou de cartão.

![Junte-se à transformação otimizar](media/data-flow/joinoptimize.png "Junte-se à otimização")

Em juntas, procura e transformação existente, se um ou ambos os fluxos de dados se encaixarem na memória do nó do trabalhador, pode otimizar o desempenho permitindo a **radiodifusão.** Por predefinição, o motor de faísca decidirá automaticamente se transmite ou não um dos lados. Para escolher manualmente qual lado transmitir, selecione **Fixo**.

Não é aconselhável desativar a transmissão através da opção **Off,** a menos que as suas junções estejam a ter erros de tempo.

## <a name="self-join"></a>Self-Join

Para se juntar a um fluxo de dados consigo mesmo, pseudónimo de um fluxo existente com uma transformação selecionada. Crie um novo ramo clicando no ícone plus ao lado de uma transformação e selecione **Novo ramo**. Adicione uma transformação selecionada ao pseudónimo do fluxo original. Adicione uma transformação de união e escolha o fluxo original como **o fluxo esquerdo** e a transformação selecionada como o fluxo **direito**.

![União de auto-unir-se](media/data-flow/selfjoin.png "União de auto-unir-se")

## <a name="testing-join-conditions"></a>Testes juntam condições de união

Ao testar as transformações de junção com pré-visualização de dados no modo depuror, utilize um pequeno conjunto de dados conhecidos. Ao recolher amostras de um conjunto de dados grande, não é possível prever quais linhas e teclas serão lidas para testes. O resultado não é determinístico, o que significa que as suas condições de união podem não devolver quaisquer partidas.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Syntax

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>Exemplo de união interior

O exemplo abaixo é uma transformação de união nomeada `JoinMatchedData` que toma o fluxo esquerdo e o fluxo `TripData` `TripFare` direito.  A condição de união é a expressão `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` que retorna verdadeira se as `hack_license` `medallion` `vendor_id` colunas e `pickup_datetime` colunas em cada fluxo corresponderem. O `joinType` `'inner'` é. Estamos a permitir a transmissão apenas no fluxo esquerdo, por isso `broadcast` tem `'left'` valor.

No Data Factory UX, esta transformação parece a imagem abaixo:

![O screenshot mostra a transformação com o separador 'Configurações de Junção' selecionado e um tipo de 'Unir'.](media/data-flow/join-script1.png "Junte-se ao exemplo")

O roteiro do fluxo de dados para esta transformação está no snippet abaixo:

```
TripData, TripFare
    join(
        hack_license == { hack_license}
        && TripData@medallion == TripFare@medallion
        && vendor_id == { vendor_id}
        && pickup_datetime == { pickup_datetime},
        joinType:'inner',
        broadcast: 'left'
    )~> JoinMatchedData
```

### <a name="custom-cross-join-example"></a>Cruz personalizada junta-se exemplo

O exemplo abaixo é uma transformação de união nomeada `JoiningColumns` que toma o fluxo esquerdo e o fluxo `LeftStream` `RightStream` direito. Esta transformação toma dois riachos e une todas as linhas onde a coluna `leftstreamcolumn` é maior do que a `rightstreamcolumn` coluna. O `joinType` `cross` é. A radiodifusão não está ativada `broadcast` tem `'none'` valor.

No Data Factory UX, esta transformação parece a imagem abaixo:

![O screenshot mostra a transformação com o separador 'Configurações de Junção' selecionado e um tipo de 'Unir') (cruz).](media/data-flow/join-script2.png "Junte-se ao exemplo")

O roteiro do fluxo de dados para esta transformação está no snippet abaixo:

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>Passos seguintes

Depois de juntar dados, crie uma [coluna derivada](data-flow-derived-column.md) e [afunde](data-flow-sink.md) os seus dados numa loja de dados de destino.
