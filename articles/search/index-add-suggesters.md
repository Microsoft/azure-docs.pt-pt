---
title: Adicionar sugestores para um índice da Azure Search
description: Permite que os campos para ações de consulta antecipada, onde as consultas sugeridas são compostas de texto dos campos um índice da Azure Search.
ms.date: 02/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: fd4b29134fd45ed2888fbc81ded413ecf7286959
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57308657"
---
# <a name="add-suggesters-to-an-azure-search-index"></a>Adicionar sugestores para um índice da Azure Search

R **Sugestor** é uma construção de Azure Search o "pesquisa-como--type" de suporte [sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions) funcionalidade e o [preenchimento automático (pré-visualização)](search-autocomplete-tutorial.md) funcionalidade. Antes de poder chamar a API de sugestões, tem de definir uma **sugestor** num índice para ativar as sugestões em campos específicos.

Embora uma **sugestor** tem várias propriedades, é principalmente uma coleção de campos para o qual pretende ativar a [sugestões API](https://docs.microsoft.com/rest/api/searchservice/suggestions). Por exemplo, uma aplicação de viagens poderá querer ativar pesquisa typeahead sobre destinos, cidades e attractions. Como tal, todos os três campos iria da coleção de campo.

Pode ter apenas um **sugestor** recurso para cada índice (especificamente, uma **sugestor** no **sugestores** coleção).

## <a name="creating-a-suggester"></a>Criar um sugestor

Pode criar uma **sugestor** em qualquer altura, mas o impacto no seu índice varia com base nos campos.

+ Novos campos adicionados a um sugestor como parte da mesma atualização são o menor impacto em que nenhuma recriação de índice é necessária.
+ Existente no entanto, campos adicionados a um sugestor, altera a definição de campo, evitar a necessidade de uma reconstrução completa do índice.

**Sugestores** funcionam melhor quando usadas para sugerir documentos específicos em vez de perder todas termos ou frases. Os campos de Release candidate melhor são títulos, nomes e outras expressões relativamente curtos que podem identificar um item. Menos eficazes são campos repetitivos, como categorias e etiquetas, ou campos muito longos, como campos de descrições ou comentários.

Depois de um sugestor é criada, adicione a [sugestões API](https://docs.microsoft.com/rest/api/searchservice/suggestions) na sua lógica de consulta para invocar a funcionalidade.

Propriedades que definem uma **sugestor** incluem o seguinte:

|Propriedade|Descrição|
|--------------|-----------------|
|`name`|O nome da **sugestor**. Utilize o nome da **sugestor** ao chamar os [sugestões &#40;API de REST do serviço de pesquisa do Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions).|
|`searchMode`|A estratégia utilizada para pesquisar expressões candidatas. O único modo suportado atualmente é `analyzingInfixMatching`, que executa a correspondência de expressões no início ou no meio de frases flexível.|
|`sourceFields`|Uma lista de um ou mais campos que são a origem do conteúdo para sugestões. Apenas os campos do tipo `Edm.String` e `Collection(Edm.String)` pode dar origem a sugestões. Podem ser usados apenas os campos que não têm um analisador de idioma personalizado definido. |

## <a name="suggester-example"></a>Exemplo do sugestor
R **sugestor** é parte da definição do índice. Apenas um **sugestor** pode existir na **sugestores** coleção na versão atual, juntamente com o **campos** coleção e **scoringProfiles**.

```
{
  "name": "hotels",
  "fields": [
    . . .
  ],
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["hotelName", "category"]
    }
  ],
  "scoringProfiles": [
    . . .
  ]
}

```

## <a name="see-also"></a>Consulte também
[Criar índice &#40;API de REST do serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
[Atualizar o índice &#40;API de REST do serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/update-index)  
[Sugestões de &#40;API de REST do serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions)  
[Operações de índice &#40;API de REST do serviço de pesquisa do Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/index-operations)  
[REST do serviço de pesquisa do Azure](https://docs.microsoft.com/rest/api/searchservice/)  
[O Azure Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)
