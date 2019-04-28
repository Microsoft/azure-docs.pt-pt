---
title: Como modelar tipos de dados complexos - Azure Search
description: Aninhadas ou estruturas de dados hierárquica podem ser modeladas num índice da Azure Search utilizando o conjunto de linhas bidimensional e tipo de dados de coleções.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2017
ms.custom: seodec2018
ms.openlocfilehash: 973623d6c4cb57518af2012bccf67c969146d23c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61076213"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Como tipos de dados complexos de modelo no Azure Search
Conjuntos de dados externos utilizados para preencher um índice da Azure Search, às vezes, incluem substructures hierárquicas ou aninhados que não dividir harmoniosamente num conjunto de linhas de tabela. Exemplos de tais estruturas podem incluir várias localizações e números de telefone para um único cliente, várias cores e tamanhos para uma SKU único, vários autores de um livro único e assim por diante. Em termos de modelagem, poderá ver essas estruturas denominadas *tipos de dados complexos*, *composta tipos de dados*, *tipos de dados compostos*, ou *agregado tipos de dados*, para citar alguns.

Tipos de dados complexos não são suportados nativamente no Azure Search, mas uma solução comprovada inclui um processo de dois passos de mesclar a estrutura e, em seguida, utilizar um **coleção** tipo de dados para reconstituir a estrutura interior. A técnica descrita neste artigo a seguir permite que o conteúdo deve ser pesquisada, por facetas, filtrada e ordenados.

## <a name="example-of-a-complex-data-structure"></a>Exemplo de uma estrutura de dados complexos
Normalmente, os dados em questão residem como um conjunto de documentos JSON ou XML ou como itens num arquivo de NoSQL, como o Azure Cosmos DB. Estruturalmente, o desafio é resultante de ter vários itens subordinados que devem ser pesquisadas e filtradas.  Como ponto de partida para ilustrar a solução alternativa, execute o seguinte documento JSON que indica um conjunto de contactos, por exemplo:

~~~~~
[
  {
    "id": "1",
    "name": "John Smith",
    "company": "Adventureworks",
    "locations": [
      {
        "id": "1",
        "description": "Adventureworks Headquarters"
      },
      {
        "id": "2",
        "description": "Home Office"
      }
    ]
  }, 
  {
    "id": "2",
    "name": "Jen Campbell",
    "company": "Northwind",
    "locations": [
      {
        "id": "3",
        "description": "Northwind Headquarter"
      },
      {
        "id": "4",
        "description": "Home Office"
      }
    ]
}]
~~~~~

Embora os campos com o nome "id", "name" e "empresa" podem ser facilmente mapeadas um para um como campos dentro de um índice da Azure Search, o campo 'locais' contém uma matriz de localizações, ter ambos um conjunto de IDs de localização, bem como as descrições de localização. Uma vez que o Azure Search não tem um tipo de dados que oferece suporte a isso, precisamos de outra forma de modelar isso no Azure Search. 

> [!NOTE]
> Essa técnica também é descrita por Kirk Evans numa postagem de blog [indexação do Azure Cosmos DB com o Azure Search](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/), que mostra uma técnica chamada "mesclar os dados", por meio das quais teria um campo chamado `locationsID` e `locationsDescription` que são ambos [coleções](https://msdn.microsoft.com/library/azure/dn798938.aspx) (ou uma matriz de cadeias de caracteres).   
> 
> 

## <a name="part-1-flatten-the-array-into-individual-fields"></a>Parte 1: Nivelamento a matriz em campos individuais
Para criar um índice da Azure Search que permite a este conjunto de dados, criar campos individuais para o sub-estrutura aninhado: `locationsID` e `locationsDescription` com um tipo de dados [coleções](https://msdn.microsoft.com/library/azure/dn798938.aspx) (ou uma matriz de cadeias de caracteres). Nestes campos seria indexar os valores '1' e '2' para o `locationsID` pole Pro John Smith e os valores de "3" & "4" no `locationsID` campo para Jen Campbell.  

Os dados no Azure Search teria o seguinte aspeto: 

![dados de exemplo, 2 linhas](./media/search-howto-complex-data-types/sample-data.png)

## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>Parte 2: Adicionar um campo de coleção na definição do índice
No esquema do índice, as definições de campo podem ter um aspeto semelhantes a este exemplo.

~~~~
var index = new Index()
{
    Name = indexName,
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("name", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("company", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("locationsId", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("locationsDescription", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true }
    }
};
~~~~

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>Validar comportamentos de pesquisa e, opcionalmente, estender o índice
Supondo que criou o índice e carregar os dados, pode agora testar a solução para verificar a execução da consulta de pesquisa contra o conjunto de dados. Cada **recolha** campo deve estar **pesquisável**, **filtrável** e **facetável**. Deve ser capaz de executar consultas como:

* Encontre todas as pessoas que trabalham na sede especiais da Adventureworks.
* Obtenha uma contagem do número de pessoas que trabalham num escritório' Home'.  
* Das pessoas que trabalham num escritório' Home', mostram que outros escritórios funcionam, juntamente com uma contagem das pessoas em cada local.  

Em que essa técnica-recai é quando precisa fazer uma pesquisa que combina tanto o id de localização, bem como a descrição de localização. Por exemplo:

* Encontre todas as pessoas nos quais tenham um escritório de casa e tem um ID de localização de 4.  

Se se lembra o conteúdo original tinha essa aparência:

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

No entanto, agora que separámos os dados em campos separados, não temos nenhuma forma de saber se o Office Home para Jen Campbell está relacionado à `locationsID 3` ou `locationsID 4`.  

Para lidar com esse caso, defina outro campo no índice que combina todos os dados numa única coleção.  No nosso exemplo, vamos chamar esse campo `locationsCombined` e será separamos o conteúdo com um `||` embora pode escolher qualquer separador que acredita que seria um conjunto exclusivo de carateres para o seu conteúdo. Por exemplo: 

![dados de exemplo, 2 linhas com separador](./media/search-howto-complex-data-types/sample-data-2.png)

Usando essa `locationsCombined` campo, podemos pode agora acomodar consultas ainda mais, como:

* Mostre uma contagem de pessoas que trabalham num 'Home Office' com o Id de localização de "4".  
* Procurar pessoas que trabalham num escritório' Home' com o Id "4" de localização. 

## <a name="limitations"></a>Limitações
Essa técnica é útil para inúmeros cenários, mas não é aplicável em todos os casos.  Por exemplo:

1. Se não tiver um conjunto estático de campos no seu tipo de dados complexos e não havia como mapear todos os tipos possíveis para um único campo. 
2. A atualizar os objetos aninhados requer algum trabalho extra para determinar exatamente o que precisa ser atualizado no índice da Azure Search

## <a name="sample-code"></a>Código de exemplo
Pode ver um exemplo sobre como um conjunto de dados JSON complexo de índice para o Azure Search e executar um número de consultas sobre este conjunto de dados isso [repositório do GitHub](https://github.com/liamca/AzureSearchComplexTypes).

## <a name="next-step"></a>Passo seguinte
[Um voto para o suporte nativo para tipos de dados complexos](https://feedback.azure.com/forums/263029-azure-search) no UserVoice de pesquisa do Azure, página e fornecer qualquer entrada adicional que gostaria de ver a considerar relativamente à implementação de recurso. Também pode consultar o-me diretamente no Twitter em @liamca.

