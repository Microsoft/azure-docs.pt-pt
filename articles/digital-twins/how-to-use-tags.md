---
title: Adicionar tags a gémeos digitais
titleSuffix: Azure Digital Twins
description: Veja como implementar tags em gémeos digitais
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 59f68909e2f3704fea5c38e3f1535f5996b284ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87099171"
---
# <a name="add-tags-to-digital-twins"></a>Adicionar tags a gémeos digitais 

Pode utilizar o conceito de tags para identificar e categorizar ainda mais os seus gémeos digitais. Em particular, os utilizadores podem querer replicar tags de sistemas existentes, como [as Tags do Palheiro,](https://project-haystack.org/doc/TagModel)dentro das suas instâncias Azure Digital Twins. 

Este documento descreve padrões que podem ser usados para implementar tags em gémeos digitais.

As tags são adicionadas primeiro como propriedades dentro do [modelo](concepts-models.md) que descreve um gémeo digital. Essa propriedade é então definida no gémeo quando é criada com base no modelo. Depois disso, as etiquetas podem ser usadas em [consultas](concepts-query-language.md) para identificar e filtrar os seus gémeos.

## <a name="marker-tags"></a>Etiquetas de marcador 

Uma **etiqueta de marcador** é uma corda simples que é usada para marcar ou categorizar um gémeo digital, como "azul" ou "vermelho". Esta corda é o nome da etiqueta, e as etiquetas marcadoras não têm valor significativo - a etiqueta é significativa apenas pela sua presença (ou ausência). 

### <a name="add-marker-tags-to-model"></a>Adicione etiquetas de marcador ao modelo 

As etiquetas de marcação são modeladas como um mapa [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) de `string` `boolean` . O `mapValue` booleano é ignorado, já que a presença da etiqueta é tudo o que é importante. 

Aqui está um excerto de um modelo gémeo implementando uma etiqueta de marcador como uma propriedade:

```json
{
  "@type": "Property",
  "name": "tags",
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "tagName",
      "schema": "string"
    },
    "mapValue": {
      "name": "tagValue",
      "schema": "boolean"
    }
  }
}
```

### <a name="add-marker-tags-to-digital-twins"></a>Adicione etiquetas de marcador a gémeos digitais

Uma vez que a `tags` propriedade faz parte do modelo de um twin digital, você pode definir a etiqueta de marcador no twin digital definindo o valor deste imóvel. 

Aqui está um exemplo que povoa o marcador `tags` para três gémeos:

```csharp
entity-01: "tags": { "red": true, "round": true } 
entity-02: "tags": { "blue": true, "round": true } 
entity-03: "tags": { "red": true, "large": true } 
```

### <a name="query-with-marker-tags"></a>Consulta com etiquetas de marcador

Uma vez adicionadas etiquetas aos gémeos digitais, as etiquetas podem ser usadas para filtrar os gémeos em consultas. 

Aqui está uma consulta para obter todos os gémeos que foram marcados como "vermelho": 

```sql
select * from digitaltwins where is_defined(tags.red) 
```

Também pode combinar tags para consultas mais complexas. Aqui está uma consulta para obter todos os gémeos que são redondos, e não vermelho: 

```sql
select * from digitaltwins where not is_defined(tags.red) and is_defined(tags.round) 
```

## <a name="value-tags"></a>Etiquetas de valor 

Uma etiqueta de **valor** é um par de valores-chave que é usado para dar a cada marca um valor, como `"color": "blue"` ou `"color": "red"` . Uma vez criada uma etiqueta de valor, também pode ser usada como marcação, ignorando o valor da etiqueta. 

### <a name="add-value-tags-to-model"></a>Adicione etiquetas de valor ao modelo 

As etiquetas de valor são modeladas como um Mapa [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) de `string` `string` . Tanto o `mapKey` e o `mapValue` são significativos. 

Aqui está um excerto de um modelo gémeo implementando uma etiqueta de valor como propriedade:

```json
{
  "@type": "Property",
  "name": "tags",
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "tagName",
      "schema": "string"
    },
    "mapValue": {
      "name": "tagValue",
      "schema": "string"
    }
  }
} 
```

### <a name="add-value-tags-to-digital-twins"></a>Adicione etiquetas de valor a gémeos digitais

Tal como acontece com as etiquetas de marcação, pode definir a etiqueta de valor num twin digital definindo o valor desta `tags` propriedade a partir do modelo. Para utilizar uma etiqueta de valor como marca de marcador, pode definir o `tagValue` campo para o valor de cadeia vazio `""` (). 

Aqui está um exemplo que povoa o valor `tags` de três gémeos:

```csharp
entity-01: "tags": { "red": "", "size": "large" } 
entity-02: "tags": { "purple": "", "size": "small" } 
entity-03: "tags": { "red": "", "size": "small" } 
```

Note que `red` e `purple` são usados como etiquetas de marcação neste exemplo.

### <a name="query-with-value-tags"></a>Consulta com etiquetas de valor

Tal como acontece com as etiquetas de marcação, pode utilizar etiquetas de valor para filtrar os gémeos em consultas. Também pode utilizar etiquetas de valor e etiquetas de marcador juntas.

Pelo exemplo acima, `red` está a ser usado como uma etiqueta de marcação. Aqui está uma consulta para obter todos os gémeos que foram marcados como "vermelho": 

```sql
select * from digitaltwins where is_defined(tags.red) 
```

Aqui está uma consulta para obter todas as entidades que são pequenas (etiqueta de valor), e não vermelhas: 

```sql
select * from digitaltwins where not is_defined(tags.red) and tags.size = 'small' 
```

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre a conceção e gestão de modelos digitais twin:
* [*Como fazer: Gerir modelos personalizados*](how-to-manage-model.md)

Leia mais sobre a consulta do gráfico gémeo:
* [*Como fazer: Consulta do gráfico gémeo*](how-to-query-graph.md)