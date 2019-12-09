---
title: JSON no fluxo de dados de mapeamento
description: O fluxo de dados de mapeamento de Azure Data Factory tem recursos internos para lidar com documentos JSON com hierarquias
author: kromerm
ms.author: makromer
ms.review: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/30/2019
ms.openlocfilehash: 153c7a1003c68526c960644bebcc4800e92edc3c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928334"
---
# <a name="mapping-data-flow-json-handling"></a>Mapeando manipulação de JSON de fluxo de dados

## <a name="creating-json-structures-in-derived-column"></a>Criando estruturas JSON na coluna derivada

Você pode adicionar uma coluna complexa ao fluxo de dados por meio do construtor de expressões de coluna derivada. Na transformação coluna derivada, adicione uma nova coluna e abra o construtor de expressões clicando na caixa azul. Para tornar uma coluna complexa, você pode inserir a estrutura JSON manualmente ou usar a UX para adicionar Subcolunas de forma interativa.

### <a name="using-the-expression-builder-ux"></a>Usando a UX do construtor de expressões

No painel do lado do esquema de saída, passe o mouse sobre uma coluna e clique no ícone de adição. Selecione **Adicionar subcoluna** para tornar a coluna um tipo complexo.

![Adicionar subcoluna](media/data-flow/addsubcolumn.png "Adicionar subcoluna")

Você pode adicionar colunas e Subcolunas adicionais da mesma maneira. Para cada campo não complexo, uma expressão pode ser adicionada no editor de expressão à direita.

![Coluna complexa](media/data-flow/complexcolumn.png "Coluna complexa")

### <a name="entering-the-json-structure-manually"></a>Inserindo a estrutura JSON manualmente

Para adicionar manualmente uma estrutura JSON, adicione uma nova coluna e insira a expressão no editor. A expressão segue o seguinte formato geral:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Se essa expressão foi inserida para uma coluna chamada "complexColumn", ela seria gravada no coletor como o JSON a seguir:

```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Exemplo de script manual para a definição hierárquica completa
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="source-format-options"></a>Opções de formato de origem

O uso de um conjunto de dados JSON como uma origem em seu Flow permite definir cinco configurações adicionais. Essas configurações podem ser encontradas sob o acordeão de **configurações JSON** na guia **Opções de origem** .  

![Configurações de JSON](media/data-flow/json-settings.png "Definições de JSON")

### <a name="default"></a>Predefinição

Por padrão, os dados JSON são lidos no formato a seguir.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>Documento único

Se **um único documento** estiver selecionado, o mapeamento de fluxos de dados lerá um documento JSON de cada arquivo. 

``` json
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

### <a name="unquoted-column-names"></a>Nomes de colunas sem aspas

Se a seleção de **nomes de coluna sem aspas** for selecionada, o mapeamento de fluxos de dados lerá as colunas JSON que não estão entre aspas. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>Tem comentários

Select **tem comentários** se os dados JSON tiverem comentário C C++ ou estilo.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>Entre aspas simples

Selecione **aspas simples** se os campos e valores JSON usarem aspas simples em vez de aspas duplas.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>Barra invertida

Selecione **aspas simples** se as barras invertidas forem usadas para escapar caracteres nos dados JSON.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="higher-order-functions"></a>Funções de ordem superior

Uma função de ordem superior é uma função que usa uma ou mais funções como um argumento. Veja abaixo uma lista de funções de ordem superior com suporte no mapeamento de fluxos de dados que habilitam operações de matriz.

### <a name="filter"></a>filter
Filtra elementos da matriz que não atendem ao predicado fornecido. O filtro espera uma referência a um elemento na função de predicado como #item.

#### <a name="examples"></a>Exemplos
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

### <a name="map"></a>map
Mapeia cada elemento da matriz para um novo elemento usando a expressão fornecida. O mapa espera uma referência a um elemento na função de expressão como #item.

#### <a name="examples"></a>Exemplos
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

### <a name="reduce"></a>diminu
Acumula elementos em uma matriz. A redução espera uma referência a um acumulador e um elemento na primeira função de expressão como #acc e #item e espera que o valor resultante como #result seja usado na segunda função de expressão.

#### <a name="examples"></a>Exemplos
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

### <a name="sort"></a>ordenar
Classifica a matriz usando a função de predicado fornecida. Sort espera uma referência a dois elementos consecutivos na função Expression como #item1 e #item2.

#### <a name="examples"></a>Exemplos
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

### <a name="contains"></a>Contém
Retornará true se qualquer elemento na matriz fornecida for avaliado como true no predicado fornecido. Contains espera uma referência a um elemento na função de predicado como #item.

#### <a name="examples"></a>Exemplos
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>Passos seguintes

* [Use a transformação coluna derivada para criar suas estruturas hierárquicas](data-flow-derived-column.md)
