---
title: Sinónimos para expansão de consulta sobre um índice de pesquisa
titleSuffix: Azure Cognitive Search
description: Crie um mapa de sinónimo para expandir o âmbito de uma consulta de pesquisa num índice de Pesquisa Cognitiva Azure. O âmbito é alargado para incluir termos equivalentes que fornece numa lista.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: aad953483749d676844221f7e519f50c50b63ad4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88948645"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Sinónimos em Pesquisa Cognitiva Azure

Os sinónimos nos motores de busca associam termos equivalentes que expandem implicitamente o âmbito de uma consulta, sem que o utilizador tenha de fornecer o termo. Por exemplo, dado o termo "cão" e associações de sinónimo de "canino" e "cachorrinho", quaisquer documentos que contenham "cão", "canino" ou "cachorrinho" serão abrangidos pela consulta.

Em Azure Cognitive Search, a expansão do sinónimo é feita no momento da consulta. Pode adicionar mapas de sinónimo a um serviço sem interrupções nas operações existentes. Pode adicionar uma propriedade  **synonymMaps** a uma definição de campo sem ter de reconstruir o índice.

## <a name="create-synonyms"></a>Criar sinónimos

Não existe suporte ao portal para criar sinónimos, mas pode utilizar o REST API ou .NET SDK. Para começar com o REST, recomendamos [a utilização do Carteiro](search-get-started-postman.md) e a formulação de pedidos utilizando esta API: [Create Synonym Maps](/rest/api/searchservice/create-synonym-map). Para desenvolvedores C#, você pode começar com [Add Synonyms in Azure Cognitive Search usando C#](search-synonyms-tutorial-sdk.md).

Opcionalmente, se estiver a utilizar [as teclas geridas pelo cliente](search-security-manage-encryption-keys.md) para encriptação do lado do serviço em repouso, pode aplicar essa proteção ao conteúdo do seu mapa de sinónimos.

## <a name="use-synonyms"></a>Use sinónimos

Na Pesquisa Cognitiva Azure, o suporte ao sinónimo baseia-se em mapas de sinónimo que define e envia para o seu serviço. Estes mapas constituem um recurso independente (como índices ou fontes de dados), e podem ser utilizados por qualquer campo pes pes pes pes pes pesalizável em qualquer índice do seu serviço de pesquisa.

Os mapas e índices de sinonismo são mantidos de forma independente. Uma vez definido um mapa de sinónimo e enviando-o para o seu serviço, pode ativar a funcionalidade de sinónimo num campo adicionando uma nova propriedade chamada **synonymMaps** na definição de campo. Criar, atualizar e eliminar um mapa de sinónimo é sempre uma operação de documento completo, o que significa que não é possível criar, atualizar ou apagar gradualmente partes do mapa de sinónimos. Atualizar mesmo uma única entrada requer uma recarga.

Incorporar sinónimos no seu pedido de pesquisa é um processo em duas etapas:

1.  Adicione um mapa de sinónimo ao seu serviço de pesquisa através das APIs abaixo.  

2.  Configurar um campo pesmável para utilizar o mapa de sinónimo na definição de índice.

Pode criar vários mapas de sinónimo para a sua aplicação de pesquisa (por exemplo, por idioma se a sua aplicação suportar uma base de clientes multilingue). Atualmente, um campo só pode usar um deles. Pode atualizar a propriedade de um campo de synonymMaps a qualquer momento.

### <a name="synonymmaps-resource-apis"></a>APIs de Recursos SynonymMaps

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Adicione ou atualize um mapa de sinónimos sob o seu serviço, utilizando POST ou PUT.

Os mapas synonym são enviados para o serviço via POST ou PUT. Cada regra deve ser delimitada pelo carácter da nova linha ('\n'). Você pode definir até 5.000 regras por mapa de sinónimo em um serviço gratuito e 20.000 regras por mapa em todos os outros SKUs. Cada regra pode ter até 20 expansões.

Os mapas de synonym devem estar no formato Apache Solr, que é explicado abaixo. Se tiver um dicionário de sinónimo existente num formato diferente e quiser utilizá-lo diretamente, informe-nos no [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Pode criar um novo mapa de sinónimos utilizando HTTP POST, como no seguinte exemplo:

```synonym-map
    POST https://[servicename].search.windows.net/synonymmaps?api-version=2020-06-30
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }
```

Em alternativa, pode utilizar o PUT e especificar o nome do mapa de sinónimo no URI. Se o mapa do sinónimo não existir, será criado.

```synonym-map
    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2020-06-30
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }
```

##### <a name="apache-solr-synonym-format"></a>Formato de sinónimo Apache Solr

O formato Solr suporta mapeamentos de sinónimos equivalentes e explícitos. As regras de mapeamento aderem à especificação de filtro de sinónimo de código aberto da Apache Solr, descrita neste documento: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Abaixo está uma regra de amostra para sinónimos equivalentes.

```
USA, United States, United States of America
```

Com a regra acima, uma consulta de pesquisa "EUA" expandir-se-á para "EUA" ou "Estados Unidos" ou "Estados Unidos da América".

O mapeamento explícito é denotado por uma seta "=>". Quando especificado, uma sequência de termo de uma consulta de pesquisa que corresponda ao lado esquerdo de "=>" será substituída pelas alternativas do lado direito. Dada a regra abaixo, consultas de pesquisa "Washington", "Wash". ou "WA" será reescrito para "WA". O mapeamento explícito só se aplica na direção especificada e não reescreve a consulta "WA" a "Washington" neste caso.

```
Washington, Wash., WA => WA
```

Se precisar de definir sinónimos que contenham vírgulas, pode escapar-lhes com uma pestana, como neste exemplo:

```
WA\, USA, WA, Washington
```

Uma vez que o backslash é em si um personagem especial em outras línguas como JSON e C#, você provavelmente precisará de escapar duas vezes. Por exemplo, o JSON enviado à API REST para o mapa de sinónimo acima seria assim:

```json
    {
       "format":"solr",
       "synonyms": "WA\\, USA, WA, Washington"
    }
```

#### <a name="list-synonym-maps-under-your-service"></a>Liste mapas de sinónimos sob o seu serviço.

```synonym-map
    GET https://[servicename].search.windows.net/synonymmaps?api-version=2020-06-30
    api-key: [admin key]
```

#### <a name="get-a-synonym-map-under-your-service"></a>Obtenha um mapa de sinónimo sob o seu serviço.

```synonym-map
    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2020-06-30
    api-key: [admin key]
```

#### <a name="delete-a-synonyms-map-under-your-service"></a>Elimine um mapa de sinónimos sob o seu serviço.

```synonym-map
    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2020-06-30
    api-key: [admin key]
```

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Configurar um campo pesmável para utilizar o mapa de sinónimo na definição de índice.

Um novo **sinónimo de** propriedade de campo pode ser usado para especificar um mapa de sinónimo para usar para um campo pesmável. Os mapas de synonym são recursos de nível de serviço e podem ser referenciados por qualquer campo de um índice sob o serviço.

```synonym-map
    POST https://[servicename].search.windows.net/indexes?api-version=2020-06-30
    api-key: [admin key]

    {
       "name":"myindex",
       "fields":[
          {
             "name":"id",
             "type":"Edm.String",
             "key":true
          },
          {
             "name":"name",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"en.lucene",
             "synonymMaps":[
                "mysynonymmap"
             ]
          },
          {
             "name":"name_jp",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"ja.microsoft",
             "synonymMaps":[
                "japanesesynonymmap"
             ]
          }
       ]
    }
```

**os synonymMaps** podem ser especificados para campos pes pesjáveis do tipo 'Edm.String' ou 'Collection(Edm.String)'.

> [!NOTE]
> Só se pode ter um mapa de sinónimo por campo. Se quiser utilizar vários mapas de sinónimo, informe-nos no [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Impacto dos sinónimos em outras funcionalidades de pesquisa

A função de sinónimos reescreve a consulta original com sinónimos com o operador OR. Por esta razão, os perfis de destaque e pontuação de impacto tratam o termo original e os sinónimos como equivalentes.

A funcionalidade Synonym aplica-se a consultas de pesquisa e não se aplica a filtros ou facetas. Da mesma forma, as sugestões baseiam-se apenas no termo original; os jogos de sinónimo não aparecem na resposta.

As expansões de synonym não se aplicam aos termos de pesquisa wildcard; prefixo, fuzzy e termos regex não são expandidos.

Se precisar de fazer uma única consulta que aplique expansão de sinónimo e pesquisas de wildcard, regex ou fuzzy, pode combinar as consultas usando a sintaxe OR. Por exemplo, para combinar sinónimos com wildcards para sintaxe de consulta simples, o termo seria `<query> | <query>*` .

Se tiver um índice existente num ambiente de desenvolvimento (não-produção), experimente com um pequeno dicionário para ver como a adição de sinónimos altera a experiência de pesquisa, incluindo o impacto nos perfis de pontuação, destaque de sucesso e sugestões.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um mapa de sinónimos](/rest/api/searchservice/create-synonym-map)