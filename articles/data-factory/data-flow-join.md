---
title: Junte-se à transformação no fluxo de dados de mapeamento
description: Combine dados de duas fontes de dados usando a transformação de adesão no fluxo de dados de mapeamento da Fábrica de Dados da Azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/02/2020
ms.openlocfilehash: 5c388dd2b3e4f40fbf2ed75cf3f1b8ab31aee394
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606409"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Junte-se à transformação no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilize a transformação de união para combinar dados de duas fontes ou streams num fluxo de dados de mapeamento. O fluxo de saída incluirá todas as colunas de ambas as fontes correspondidas com base numa condição de adesão. 

## <a name="join-types"></a>Juntar tipos

Os fluxos de dados de mapeamento suportam atualmente cinco tipos diferentes de união.

### <a name="inner-join"></a>União Interior

Interior une apenas linhas de saída que têm valores correspondentes em ambas as tabelas.

### <a name="left-outer"></a>Exterior esquerdo

A união exterior esquerda devolve todas as linhas do fluxo esquerdo e corresponde aos registos do fluxo direito. Se uma linha do fluxo esquerdo não tiver correspondência, as colunas de saída do fluxo direito estão definidas para NULO. A saída serão as linhas devolvidas por uma junta interior mais as linhas incomparáveis do fluxo esquerdo.

> [!NOTE]
> O motor Spark utilizado pelos fluxos de dados irá ocasionalmente falhar devido a possíveis produtos cartesianos nas suas condições de adesão. Se isto ocorrer, pode mudar para uma junta de cruz personalizada e introduzir manualmente a sua condição de união. Isto pode resultar num desempenho mais lento nos fluxos de dados, uma vez que o motor de execução pode ter de calcular todas as linhas de ambos os lados da relação e, em seguida, filtrar as linhas.

### <a name="right-outer"></a>Right Outer

A união exterior direita devolve todas as linhas do fluxo direito e corresponde aos registos do fluxo esquerdo. Se uma linha do fluxo direito não tiver correspondência, as colunas de saída do fluxo esquerdo estão definidas para NULO. A saída serão as linhas devolvidas por uma junta interior mais as linhas incomparáveis do fluxo certo.

### <a name="full-outer"></a>Full Outer

Adere seleções exteriores completas a todas as colunas e linhas de ambos os lados com valores NULOs para colunas que não são compatíveis.

### <a name="custom-cross-join"></a>Cruz personalizada junta

Cruze as saídas do produto transversal dos dois fluxos com base numa condição. Se estiver a usar uma condição que não seja igualdade, especifique uma expressão personalizada como condição de união cruzada. O fluxo de saída será todas as linhas que cumprem a condição de união.

Pode utilizar este tipo de união para ```OR``` uniões e condições não-equi.

Se quiser produzir explicitamente um produto cartesiano completo, utilize a transformação da Coluna Derivada em cada um dos dois fluxos independentes antes da união para criar uma chave sintética para combinar. Por exemplo, criar uma nova coluna em ```SyntheticKey``` Coluna Derivada ```1```em cada fluxo chamado e defini-lo igual a . Em ```a.SyntheticKey == b.SyntheticKey``` seguida, use como expressão de adesão personalizada.

> [!NOTE]
> Certifique-se de incluir pelo menos uma coluna de cada lado da sua relação esquerda e direita numa cruz personalizada. Executar a cruz junta-se a valores estáticos em vez de colunas de cada lado resulta em exames completos de todo o conjunto de dados, fazendo com que o fluxo de dados seja executado mal.

## <a name="configuration"></a>Configuração

1. Escolha com que fluxo de dados se vai juntar no **dropdown de fluxo certo.**
1. Selecione o seu **tipo De juntar**
1. Escolha quais as colunas-chave que pretende combinar para se juntar às condições. Por padrão, o fluxo de dados procura a igualdade entre uma coluna em cada fluxo. Para comparar através de um valor computado, paire sobre a coluna dropdown e **selecione coluna Computed**.

![Junte-se à transformação](media/data-flow/join.png "Associar")

## <a name="optimizing-join-performance"></a>Otimizar o desempenho de juntar

Ao contrário da fusão em ferramentas como o SSIS, a transformação de adesão não é uma operação de fusão obrigatória. As chaves de juntar não requerem triagem. A operação de adesão ocorre com base na operação de adesão ideal em Spark, seja na difusão ou na adesão do lado do mapa.

![Junte-se ao otimizar de transformação](media/data-flow/joinoptimize.png "Junte-se à Otimização")

Se um ou ambos os fluxos de dados encaixarem na memória do nó do trabalhador, otimize ainda mais o seu desempenho, permitindo a **Transmissão** no separador otimizado. Também pode repartipartição dos seus dados sobre a operação de adesão de modo a que se encaixe melhor na memória por trabalhador.

## <a name="self-join"></a>Auto-juntar-se

Para se juntar a um fluxo de dados consigo mesmo, alia um fluxo existente com uma transformação selecionada. Crie um novo ramo clicando no ícone plus ao lado de uma transformação e selecionando **novo ramo**. Adicione uma transformação selecionada para alias o fluxo original. Adicione uma transformação de join e escolha o fluxo original como o **fluxo esquerdo** e a transformação selecionada como o **fluxo direito**.

![Auto-juntar-se](media/data-flow/selfjoin.png "Auto-juntar-se")

## <a name="testing-join-conditions"></a>Testar condições de adesão

Ao testar as transformações de união com a pré-visualização de dados no modo de depuração, utilize um pequeno conjunto de dados conhecidos. Ao recolher amostras de um grande conjunto de dados, não é possível prever quais as linhas e chaves que serão lidas para testes. O resultado não é determinístico, o que significa que as suas condições de adesão podem não devolver quaisquer fósforos.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>Exemplo de adesão interior

O exemplo abaixo é `JoinMatchedData` uma transformação `TripData` de `TripFare`união chamada que toma fluxo esquerdo e fluxo direito .  A condição de `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` união é `hack_license`a `medallion` `vendor_id`expressão `pickup_datetime` que retorna verdadeira se as colunas em cada stream corresponderem. O `joinType` `'inner'`é. Estamos a permitir a transmissão apenas `broadcast` no `'left'`fluxo esquerdo, por isso tem valor.

Na Fábrica de Dados UX, esta transformação parece a imagem abaixo:

![Junte-se ao exemplo](media/data-flow/join-script1.png "Junte-se ao exemplo")

O script de fluxo de dados para esta transformação está no corte abaixo:

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

### <a name="custom-cross-join-example"></a>Exemplo de junta de cruz personalizada

O exemplo abaixo é `JoiningColumns` uma transformação `LeftStream` de `RightStream`união chamada que toma fluxo esquerdo e fluxo direito . Esta transformação acolhe dois fluxos e une `leftstreamcolumn` todas as `rightstreamcolumn`linhas onde a coluna é maior que a coluna. O `joinType` `cross`é. A radiodifusão `broadcast` não `'none'`está ativada tem valor.

Na Fábrica de Dados UX, esta transformação parece a imagem abaixo:

![Junte-se ao exemplo](media/data-flow/join-script2.png "Junte-se ao exemplo")

O script de fluxo de dados para esta transformação está no corte abaixo:

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
