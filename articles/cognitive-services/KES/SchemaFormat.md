---
title: Formato de esquema - API de serviço de exploração de conhecimento
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre o formato de esquema no conhecimento exploração de serviço (KES) API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 51a812762659bcc67762b82e9c120772065aab53
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814408"
---
# <a name="schema-format"></a>Formato de esquema

O esquema é especificado num ficheiro JSON que descreve a estrutura de atributo dos objetos no ficheiro de dados utilizado para criar o índice.  Para cada atributo, o esquema Especifica o nome, tipo de dados, operações opcionais e lista de sinónimos opcional.  Um objeto pode ter 0 ou mais valores de cada atributo.  Segue-se um exemplo simplificado de um domínio de publicação para instituições académicas:

``` json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

Nomes de atributo são identificadores de maiúsculas e minúsculas que começam com uma letra e consistem apenas de letras (A-Z), números (0-9) e o caráter de sublinhado (\_).  O atributo de reservado "logprob" é usado para especificar as probabilidades de log natural relativa entre objetos.

## <a name="attribute-type"></a>Tipo de atributo

Segue-se uma lista dos tipos de dados de atributo suportados:

| Tipo | Descrição | Operações | Exemplo |
|------|-------------|------------|---------|
| `String` | A cadeia de caracteres (1 a 1024 carateres) | é igual a, starts_with | "hello world" |
| `Int32` | O número inteiro de 32 bits com sinal | é igual a, starts_with, is_between | 2016 |
| `Int64` | O número inteiro de 64 bits com sinal | é igual a, starts_with, is_between | 9876543210 |
| `Double` | Valor de vírgula flutuante de dupla precisão | é igual a, starts_with, is_between | 1.602E-19 |
| `Date` | Data (1400-01-01 e 9999-12-31) | é igual a, is_between | '2016-03-14' |
| `Guid` | Identificador exclusivo global | é igual a | "602DD052-CC47-4B23-A16A-26B52D30C05B" |
| `Blob` | Dados não-indexada internamente comprimidos | *Nenhum* | "Capacitar todas as pessoas e todas as organizações do mundo a irem mais" |
| `Composite` | Composição de vários atributos secundárias| *N/D* | { "Name":"harry shum", "Affiliation":"microsoft" } |

Atributos de cadeia são utilizados para representar os valores de cadeia de caracteres que podem aparecer como parte da consulta de utilizador.  Eles oferecem suporte a com correspondência exacta *é igual a* operação, bem como os *starts_with* operação para cenários de conclusão da consulta, como correspondência "micros" com "microsoft".  Correspondência de maiúsculas e minúsculas e difusa para lidar com erros ortográficos será suportada numa versão futura.

Atributos de Int32/Int64/Double são usados para representar valores numéricos.  O *is_between* operação ativa o suporte de desigualdade (lt, le, gt, ge) em tempo de execução.  O *starts_with* operação suporta cenários de conclusão da consulta, como correspondência com "2016", "20" ou "3". com "3.14".

Atributos de data são usados para codificar com eficiência os valores de data.  O *is_between* operação ativa o suporte de desigualdade (lt, le, gt, ge) em tempo de execução.
  
Os atributos de GUID são usados para representar eficazmente os valores GUID suporte predefinido para o *é igual a* operação.

Atributos de BLOBs são usados para codificar com eficiência os blobs de dados potencialmente grandes para a pesquisa de tempo de execução do objeto correspondente, sem suporte para qualquer operação de indexação com base no conteúdo dos valores de Blobs.

### <a name="composite-attributes"></a>Atributos compostos

Atributos compostos são usados para representar um agrupamento de valores de atributos.  O nome de cada atributo secundárias inicia com o nome do atributo composto seguido de ".".  Valores para atributos compostos são especificados como um objeto JSON que contém os valores de atributo aninhado.  Atributos compostos podem ter vários valores de objeto.  No entanto, compostos atributos não podem ter atributos secundárias que também são compostos atributos.

O exemplo de publicação para instituições académicas acima, isso permite que o serviço consultar documentos por "harry shum" enquanto ele é "Microsoft".  Sem atributos compostos, o serviço apenas pode consultar para documentos em que é um dos autores "harry shum" e um dos autores é "Microsoft".  Para obter mais informações, consulte [consultas compostas](SemanticInterpretation.md#composite-function).

## <a name="attribute-operations"></a>Operações de atributo

Por predefinição, cada atributo é indexado para oferecer suporte a todas as operações disponíveis para o tipo de dados de atributo.  Se uma determinada operação não é necessária, o conjunto de operações indexadas pode ser especificado explicitamente para reduzir o tamanho do índice.  No seguinte fragmento do esquema de exemplo acima, o atributo Author.Id é indexado para dar suporte apenas a *é igual a* operação, mas não o adicionais *starts_with* e *is_between*  operações para atributos de Int32.
```json
{"name":"Author.Id", "type":"Int32", "operations":["equals"]}
```

Quando um atributo é referenciado no interior de uma gramática, o *starts_with* operação tem de ser especificado no esquema para que o serviço gerar conclusões de consultas parciais.  

## <a name="attribute-synonyms"></a>Sinónimos de atributo

Muitas vezes, é desejável para fazer referência a um valor de atributo de determinada cadeia de caracteres por um sinónimo.  Por exemplo, os utilizadores podem referenciar "Microsoft" como "MSFT" ou "MS".  Nestes casos, a definição do atributo pode especificar o nome de um ficheiro de esquema localizado no mesmo diretório que o ficheiro de esquema.  Cada linha do arquivo de sinónimos representa uma entrada de sinónimos no seguinte formato JSON: `["<canonical>", "<synonym>"]`.  O esquema de exemplo, "AuthorName.syn" é um ficheiro JSON que contém valores de sinónimos para o atributo Author.Name.

`{"name":"Author.Name", "type":"String", "synonyms":"AuthorName.syn"}`


Um valor canônico pode ter vários sinónimos.  Vários valores canónicos podem partilhar um sinónimo comuns.  Nesses casos, o serviço irá resolver a ambiguidade por meio de várias interpretações.  Abaixo é um exemplo de ficheiro de sinónimos AuthorName.syn correspondente no esquema acima:
```json
["harry shum","heung-yeung shum"]
["harry shum","h shum"]
["henry shum","h shum"]
...
```
