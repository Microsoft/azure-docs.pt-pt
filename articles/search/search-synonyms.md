---
title: Sinónimos para expansão de consulta sobre um índice de pesquisa
titleSuffix: Azure Cognitive Search
description: Crie um mapa de sinónimo para expandir o âmbito de uma consulta de pesquisa num índice de pesquisa cognitiva Azure. O âmbito é alargado para incluir termos equivalentes que fornece numa lista.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: aa573e84fa9fff83bd6a894f516ce5f67b3afa79
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194347"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Sinónimos em Pesquisa Cognitiva Azure

Os sinónimos nos motores de busca associam termos equivalentes que implicitamente expandem o âmbito de uma consulta, sem que o utilizador tenha de fornecer o termo. Por exemplo, dado o termo "dog" e associações sinónimo de "canino" e "cachorrinho", quaisquer documentos que contenham "cão", "canino" ou "cachorrinho" serão abrangidos pela consulta.

Na Pesquisa Cognitiva Azure, a expansão do sinónimo é feita no momento da consulta. Pode adicionar mapas de sinónimo a um serviço sem interrupções nas operações existentes. Você pode adicionar uma propriedade **sinnoniamMaps** a uma definição de campo sem ter que reconstruir o índice.

## <a name="create-synonyms"></a>Criar sinónimos

Não existe suporte para o portal para criar sinónimos, mas pode utilizar o REST API ou .NET SDK. Para começar com o REST, recomendamos [a utilização](search-get-started-postman.md) de Carteiro e formulação de pedidos usando este API: [Create Synonym Maps](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map). Para C# os desenvolvedores, você pode começar com [Add Synonyms em Azure Cognitive Search usando C# ](search-synonyms-tutorial-sdk.md).

Opcionalmente, se estiver a usar [chaves geridas pelo cliente](search-security-manage-encryption-keys.md) para encriptação do lado do serviço em repouso, pode aplicar essa proteção ao conteúdo do seu mapa de sinónimo.

## <a name="use-synonyms"></a>Utilizar sinónimos

Na Pesquisa Cognitiva Azure, o suporte ao sinónimo baseia-se em mapas de sinónimo que define e envia para o seu serviço. Estes mapas constituem um recurso independente (como índices ou fontes de dados), e podem ser utilizados por qualquer campo pesquisável em qualquer índice do seu serviço de pesquisa.

Os mapas e índices de sinónimo são mantidos de forma independente. Uma vez definido um mapa de sinónimo e carregá-lo para o seu serviço, pode ativar a funcionalidade de sinónimo num campo adicionando uma nova propriedade chamada **sinnonymMaps** na definição de campo. Criar, atualizar e apagar um mapa de sinónimo sinonia é sempre uma operação de documento completo, o que significa que não pode criar, atualizar ou eliminar partes do mapa do sinónimo de forma incremental. Atualizar mesmo uma única entrada requer uma recarga.

Incorporar sinónimos na sua aplicação de pesquisa é um processo em duas etapas:

1.  Adicione um mapa de sinónimo ao seu serviço de pesquisa através das APIs abaixo.  

2.  Configure um campo pesquisável para utilizar o mapa de sinónimo na definição de índice.

Pode criar vários mapas de sinónimo para a sua aplicação de pesquisa (por exemplo, por idioma se a sua aplicação suportar uma base de cliente multilingual). Atualmente, um campo só pode usar um deles. Pode atualizar a propriedade de sinnonymMaps de um campo a qualquer momento.

### <a name="synonymmaps-resource-apis"></a>APIs de recurso SynonymMaps

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Adicione ou atualize um mapa sinónimo sob o seu serviço, utilizando POST ou PUT.

Os mapas de sinónimo são enviados para o serviço via POST ou PUT. Cada regra deve ser delimitada pelo novo carácter de linha ('\n'). Você pode definir até 5.000 regras por mapa sinónimo em um serviço gratuito e 20.000 regras por mapa em todas as outras SKUs. Cada regra pode ter até 20 expansões.

Os mapas de sinónimo devem estar no formato Apache Solr, que é explicado abaixo. Se tiver um dicionário de sinónimo existente num formato diferente e quiser utilizá-lo diretamente, informe-nos no [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Pode criar um novo mapa de sinónimo utilizando http POST, como no seguinte exemplo:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

Em alternativa, pode utilizar PUT e especificar o nome do mapa do sinónimo no URI. Se o mapa do sinónimo não existir, será criado.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Formato sinónimo Apache Solr

O formato Solr suporta mapeamentos de sinónimo equivalentes e explícitos. As regras de mapeamento aderem à especificação de filtro sinónimo de Apache Solr, descrita neste documento: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Abaixo está uma regra de amostra para sinónimos equivalentes.
```
USA, United States, United States of America
```

Com a regra acima, uma consulta de pesquisa "EUA" expandir-se-á para "EUA" ou "Estados Unidos" ou "Estados Unidos da América".

O mapeamento explícito é denotado por uma seta "=>". Quando especificado, uma sequência de termo de uma consulta de pesquisa que corresponda ao lado esquerdo de "=>" será substituída pelas alternativas do lado direito. Dada a regra abaixo, perguntas de pesquisa "Washington", "Wash". ou "WA" serão todos reescritos para "WA". O mapeamento explícito aplica-se apenas na direção especificada e não reescreve a consulta "WA" a "Washington" neste caso.
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>Liste mapas sinónimo sob o seu serviço.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>Obtenha um mapa sinónimo sob o seu serviço.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>Elimine um mapa de sinónimos sob o seu serviço.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Configure um campo pesquisável para utilizar o mapa de sinónimo na definição de índice.

Um novo **sinónimo** de propriedade de campo Maps pode ser usado para especificar um mapa de sinónimo para usar para um campo pesquisável. Os mapas de sinónimo são recursos de nível de serviço e podem ser referenciados por qualquer campo de um índice sob o serviço.

    POST https://[servicename].search.windows.net/indexes?api-version=2019-05-06
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

**sinnonymMaps** podem ser especificados para campos pesquisáveis do tipo 'Edm.String' ou 'Collection(Edm.String)'.

> [!NOTE]
> Só se pode ter um mapa sinónimo por campo. Se quiser utilizar vários mapas de sinónimo, informe-nos no [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Impacto dos sinónimos em outras funcionalidades de pesquisa

Os sinónimos reescrevem a consulta original com sinónimos com o operador de OR. Por esta razão, os perfis de destaque e pontuação de sucesso tratam o termo original e os sinónimos como equivalentes.

A função sinónimo aplica-se a consultas de pesquisa e não se aplica a filtros ou facetas. Do mesmo modo, as sugestões baseiam-se apenas no termo original; os jogos de sinónimo não aparecem na resposta.

As expansões do sinónimo não se aplicam aos termos de pesquisa wildcard; prefixo, fuzzy e termos regex não são expandidos.

Se precisar de fazer uma única consulta que aplique expansão de sinónimo e pesquisas de wildcard, regex ou difusas, pode combinar as consultas usando a sintaxe DE. Por exemplo, para combinar sinónimos com wildcards para simples sintaxe de consulta, o termo seria `<query> | <query>*`.

Se tiver um índice existente num ambiente de desenvolvimento (não produtivo), experimente um pequeno dicionário para ver como a adição de sinónimos muda a experiência de pesquisa, incluindo impacto na pontuação de perfis, destaque de sucesso e sugestões.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um mapa sinónimo](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)