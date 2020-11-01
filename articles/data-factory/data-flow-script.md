---
title: Script de fluxo de dados de mapeamento
description: Visão geral do código de código de fluxo de dados da Data Factory
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/29/2020
ms.openlocfilehash: 8310c34e06d52dc12af42f8bc33f4a4d7e99d68d
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2020
ms.locfileid: "91598097"
---
# <a name="data-flow-script-dfs"></a>Script de fluxo de dados (DFS)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O script de fluxo de dados (DFS) é o metadados subjacente, semelhante a uma linguagem de codificação, que é usado para executar as transformações que estão incluídas num fluxo de dados de mapeamento. Cada transformação é representada por uma série de propriedades que fornecem a informação necessária para executar o trabalho corretamente. O script é visível e editável a partir de ADF clicando no botão "script" na fita superior do navegador UI.

![Botão de script](media/data-flow/scriptbutton.png "Botão de script")

Por exemplo, `allowSchemaDrift: true,` numa transformação de fonte diz ao serviço para incluir todas as colunas do conjunto de dados de origem no fluxo de dados, mesmo que não estejam incluídas na projeção do esquema.

## <a name="use-cases"></a>Casos de utilização
O DFS é produzido automaticamente pela interface do utilizador. Pode clicar no botão Script para visualizar e personalizar o script. Também pode gerar scripts fora da UI ADF e, em seguida, passar isso para o cmdlet PowerShell. Ao depurar fluxos de dados complexos, poderá ser mais fácil digitalizar o código de script para trás em vez de digitalizar a representação do gráfico de UI dos seus fluxos.

Aqui estão alguns casos de uso de exemplo:
- Produzindo programaticamente muitos fluxos de dados que são bastante semelhantes, ou seja, fluxos de dados "estampagem".
- Expressões complexas que são difíceis de gerir na UI ou que estão a resultar em problemas de validação.
- Depurando e melhor compreendendo vários erros devolvidos durante a execução.

Quando constrói um script de fluxo de dados para utilizar com o PowerShell ou uma API, tem de colapsar o texto formatado numa única linha. Pode manter as abas e as novidades como personagens de fuga. Mas o texto deve ser formatado para caber dentro de uma propriedade JSON. Existe um botão na UI do editor de script na parte inferior que irá formatar o script como uma única linha para si.

![botão Copiar](media/data-flow/copybutton.png "botão Copiar")

## <a name="how-to-add-transforms"></a>Como adicionar transformações
A adição de transformações requer três etapas básicas: adicionar os dados de transformação do núcleo, redirecionar o fluxo de entrada e, em seguida, redirecionar o fluxo de saída. Isto pode ser visto mais fácil num exemplo.
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

Se decidirmos adicionar uma transformação derivada, primeiro precisamos criar o texto de transformação central, que tem uma expressão simples para adicionar uma nova coluna maiúscula `upperCaseTitle` chamada:
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Em seguida, pegamos no DFS existente e adicionamos a transformação:
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

E agora reencaminhamos o fluxo de entrada identificando qual a transformação que queremos que a nova transformação venha atrás (neste `source1` caso,) e copiando o nome do fluxo para a nova transformação:
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

Finalmente identificamos a transformação que queremos vir após esta nova transformação, e substituímos o seu fluxo de entrada (neste caso, `sink1` ) com o nome de fluxo de saída da nossa nova transformação:
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
O DFS é composto por uma série de transformações conectadas, incluindo fontes, pias, e várias outras que podem adicionar novas colunas, filtrar dados, juntar dados, e muito mais. Normalmente, o guião começa com uma ou mais fontes seguidas por muitas transformações e terminando com uma ou mais pias.

Todas as fontes têm a mesma construção básica:
```
source(
  source properties
) ~> source_name
```

Por exemplo, uma fonte simples com três colunas (movieId, título, géneros) seria:
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

Por exemplo, uma simples transformação derivada que leva uma coluna (título) e a substitui com uma versão maiúscula seria a seguinte:
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

## <a name="script-snippets"></a>Recortes de guião

Os snippets de script são código partilhável do Data Flow Script que podes usar para partilhar através dos fluxos de dados. Este vídeo abaixo fala sobre como usar os snippets de script e usar o Data Flow Script para copiar e colar partes do script por trás dos gráficos de fluxo de dados:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>Estatísticas de resumo agregadas
Adicione uma transformação agregada ao seu fluxo de dados chamado "ResumosStats" e, em seguida, cole neste código abaixo para a função agregada no seu script, substituindo as Estatísticas Sumárias existentes. Isto fornecerá um padrão genérico para estatísticas sumárias de perfis de dados.

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
Também pode utilizar a amostra abaixo para contar o número de linhas únicas e o número de linhas distintas nos seus dados. O exemplo abaixo pode ser colado num fluxo de dados com transformação agregada chamada ValueDistAgg. Este exemplo usa uma coluna chamada "título". Certifique-se de que substitui "título" pela coluna de cordas nos seus dados que pretende utilizar para obter contagens de valor.

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>Incluir todas as colunas num agregado
Este é um padrão agregado genérico que demonstra como pode manter as colunas restantes nos metadados de saída quando estiver a construir agregados. Neste caso, usamos a ```first()``` função para escolher o primeiro valor em cada coluna cujo nome não é "filme". Para utilizar isto, crie uma transformação agregada chamada DistinctRows e, em seguida, cole-a no seu script sobre o script agregado de DistinctRows existente.

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>Criar impressão digital de haxixe de linha 
Utilize este código no seu roteiro de fluxo de dados para criar uma nova coluna derivada chamada ```DWhash``` que produz um ```sha1``` haxixe de três colunas.

```
derive(DWhash = sha1(Name,ProductNumber,Color)) ~> DWHash
```

Também pode utilizar este script abaixo para gerar um haxixe de linha utilizando todas as colunas que estão presentes no seu fluxo, sem necessidade de nomear cada coluna:

```
derive(DWhash = sha1(columns())) ~> DWHash
```

### <a name="string_agg-equivalent"></a>String_agg equivalente
Este código funcionará como a função T-SQL ```string_agg()``` e agregará os valores das cordas numa matriz. Em seguida, pode lançar essa matriz numa corda para usar com destinos SQL.

```
source1 aggregate(groupBy(year),
    string_agg = collect(title)) ~> Aggregate1
Aggregate1 derive(string_agg = toString(string_agg)) ~> StringAgg
```

### <a name="count-number-of-updates-upserts-inserts-deletes"></a>Número de atualizações, subidas, inserções, eliminações
Ao utilizar uma transformação alter row, pode querer contar o número de atualizações, atualizações, inserções, eliminações que resultam das suas políticas Alter Row. Adicione uma transformação agregada após a sua linha de alter e cole este Data Flow Script na definição agregada para essas contagens.

```
aggregate(updates = countIf(isUpdate(), 1),
        inserts = countIf(isInsert(), 1),
        upserts = countIf(isUpsert(), 1),
        deletes = countIf(isDelete(),1)) ~> RowCount
```

### <a name="distinct-row-using-all-columns"></a>Linha distinta usando todas as colunas
Este snippet irá adicionar uma nova transformação agregada ao seu fluxo de dados que levará todas as colunas de entrada, gerará um haxixe que é usado para agrupar para eliminar duplicados, em seguida, fornecer a primeira ocorrência de cada duplicado como saída. Não é necessário nomear explicitamente as colunas, elas serão geradas automaticamente a partir do fluxo de dados de entrada.

```
aggregate(groupBy(mycols = sha2(256,columns())),
    each(match(true()), $$ = first($$))) ~> DistinctRows
```

### <a name="check-for-nulls-in-all-columns"></a>Verifique se há NULLs em todas as colunas
Este é um corte que você pode colar no seu fluxo de dados para verificar genericamente todas as suas colunas para valores NUOS. Esta técnica aproveita a deriva do esquema para olhar através de todas as colunas em todas as linhas e usa uma Divisão Condicional para separar as linhas com NULs das linhas sem NULLs. 

```
split(contains(array(columns()),isNull(#item)),
    disjoint: false) ~> LookForNULLs@(hasNULLs, noNULLs)
```

## <a name="next-steps"></a>Passos seguintes

Explore os Fluxos de Dados começando com o artigo de visão geral dos [fluxos de dados](concepts-data-flow-overview.md)
