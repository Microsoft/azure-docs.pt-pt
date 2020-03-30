---
title: Mapeando o script de fluxo de dados
description: Visão geral do código de código de fluxo de dados da Data Factory por trás do idioma
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 92421125ecb5f4336922c6e6b4508fcdaf92be6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246403"
---
# <a name="data-flow-script-dfs"></a>Script de fluxo de dados (DFS)

O script de fluxo de dados (DFS) é o metadados subjacente, semelhante a uma linguagem de codificação, que é usado para executar as transformações que estão incluídas num fluxo de dados de mapeamento. Cada transformação é representada por uma série de propriedades que fornecem a informação necessária para executar o trabalho corretamente. O script é visível e editável a partir da ADF clicando no botão "script" na fita superior do UI do navegador.

![Botão script](media/data-flow/scriptbutton.png "Botão script")

Por exemplo, `allowSchemaDrift: true,` numa transformação de fonte diz ao serviço para incluir todas as colunas do conjunto de dados de origem no fluxo de dados, mesmo que não estejam incluídas na projeção do esquema.

## <a name="use-cases"></a>Casos de utilização
O DFS é produzido automaticamente pela interface do utilizador. Pode clicar no botão Script para visualizar e personalizar o script. Também pode gerar scripts fora da UI ADF e depois passá-lo para o cmdlet PowerShell. Ao depurar fluxos complexos de dados, poderá ser mais fácil digitalizar o código do script por trás em vez de digitalizar a representação do gráfico uI dos seus fluxos.

Aqui estão alguns casos de utilização de exemplo:
- Produção programaticamente de muitos fluxos de dados que são bastante semelhantes, ou seja, fluxos de dados de "eliminação".
- Expressões complexas que são difíceis de gerir na UI ou que estão a resultar em problemas de validação.
- Depuração e melhor compreensão de vários erros devolvidos durante a execução.

Quando constrói um script de fluxo de dados para utilizar com powerShell ou uma API, tem de colapsar o texto formatado numa única linha. Pode sapateado e novas linhas como personagens de fuga. Mas o texto deve ser formatado para caber dentro de uma propriedade DaJ. Há um botão no UI do editor de scripts na parte inferior que irá formatar o script como uma única linha para si.

![botão Copiar](media/data-flow/copybutton.png "botão Copiar")

## <a name="how-to-add-transforms"></a>Como adicionar transformações
A adição de transformações requer três passos básicos: adicionar os dados de transformação do núcleo, reencaminhar o fluxo de entrada e, em seguida, reencaminhar o fluxo de saída. Isto pode ser visto mais fácil num exemplo.
Digamos que começamos com uma fonte simples para afundar o fluxo de dados como o seguinte:

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Se decidirmos adicionar uma transformação derivada, primeiro precisamos criar o texto de transformação `upperCaseTitle`do núcleo, que tem uma expressão simples para adicionar uma nova coluna maiúscula chamada:
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Depois, pegamos no DFS existente e adicionamos a transformação:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

E agora redirecionamos o fluxo de entrada identificando que transformação `source1`queremos que a nova transformação venha depois (neste caso, ) e copiando o nome do fluxo para a nova transformação:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Finalmente identificamos a transformação que queremos vir depois desta nova transformação, e substituímos o seu fluxo de entrada (neste caso, `sink1`) com o nome de fluxo de saída da nossa nova transformação:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>Fundamentos do DFS
O DFS é composto por uma série de transformações conectadas, incluindo fontes, pias e várias outras que podem adicionar novas colunas, filtrar dados, juntar dados, e muito mais. Normalmente, o guião começa com uma ou mais fontes seguidas de muitas transformações e terminando com um ou mais sumidouros.

Todas as fontes têm a mesma construção básica:
```
source(
  source properties
) ~> source_name
```

Por exemplo, uma fonte simples com três colunas (movieId, title, genres) seria:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Todas as transformações que não as fontes têm a mesma construção básica:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Por exemplo, uma transformação simples derivada que leva uma coluna (título) e a substitui com uma versão maiúscula seria a seguinte:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

E uma pia sem esquema seria simplesmente:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>Script snippets

### <a name="aggregated-summary-stats"></a>Estatísticas sumárias agregadas
Adicione uma transformação agregada ao seu fluxo de dados chamado "SumárioS" e, em seguida, colá-lo neste código abaixo para a função agregada no seu script, substituindo os Resumos existentes. Isto fornecerá um padrão genérico para estatísticas sumárias de perfil de dados.

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
Também pode utilizar a amostra abaixo para contar o número de linhas únicas e o número de linhas distintas nos seus dados. O exemplo abaixo pode ser colado num fluxo de dados com transformação agregada chamada ValueDistAgg. Este exemplo usa uma coluna chamada "título". Certifique-se de substituir o "título" pela coluna de cordas nos seus dados que pretende utilizar para obter contagens de valor.

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>Incluir todas as colunas num agregado
Este é um padrão agregado genérico que demonstra como pode manter as colunas restantes nos seus metadados de saída quando está a construir agregados. Neste caso, usamos ```first()``` a função para escolher o primeiro valor em cada coluna cujo nome não é "filme". Para usá-lo, crie uma transformação agregada chamada DistinctRows e, em seguida, colá-lo no seu script sobre o topo do script agregado DistinctRows existente.

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>Criar impressão digital hash row 
Utilize este código no seu script de fluxo ```DWhash``` de dados ```sha1``` para criar uma nova coluna derivada chamada que produz um haxixe de três colunas.

```
derive(DWhash = sha1(Name,ProductNumber,Color))
```

## <a name="next-steps"></a>Passos seguintes

Explore os Fluxos de Dados começando com os [fluxos](concepts-data-flow-overview.md) de dados artigo de visão geral
