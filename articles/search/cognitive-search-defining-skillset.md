---
title: Criar um conjunto de competências
titleSuffix: Azure Cognitive Search
description: Defina a extração de dados, o processamento de linguagem natural ou os passos de análise de imagem para enriquecer e extrair informação estruturada dos seus dados para utilização na Pesquisa Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 39a7c92ca6c83684658cf767722698806ed994ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935454"
---
# <a name="how-to-create-a-skillset-in-an-ai-enrichment-pipeline-in-azure-cognitive-search"></a>Como criar um skillset num oleoduto de enriquecimento de IA em Azure Cognitive Search 

![estágios indexantes](media/cognitive-search-defining-skillset/indexer-stages-skillset.png "estágios indexantes")

Um skillset define as operações que extraem e enriquecem dados para torná-lo pesmável. Um skillset executa após o texto e o conteúdo de imagem serem extraídos de documentos de origem, e depois de quaisquer campos do documento de origem serem (opcionalmente) mapeados para campos de destino em um índice ou loja de conhecimento.

Um skillset contém uma ou mais *habilidades cognitivas* que representam uma operação específica de enriquecimento, como traduzir texto, extrair frases-chave ou realizar reconhecimento de caracteres óticos a partir de um ficheiro de imagem. Para criar um skillset, pode utilizar [habilidades incorporadas](cognitive-search-predefined-skills.md) da Microsoft, ou habilidades personalizadas que contenham modelos ou lógica de processamento que fornece (ver [Exemplo: Criar uma habilidade personalizada num pipeline de enriquecimento de IA](cognitive-search-create-custom-skill-example.md) para obter mais informações).

Neste artigo, aprende-se a criar um oleoduto de enriquecimento para as competências que pretende utilizar. Um skillset é anexado a um [indexante](search-indexer-overview.md)de pesquisa cognitiva Azure . Uma parte do design do gasoduto, abrangida por este artigo, é a construção do próprio skillset. 

> [!NOTE]
> Outra parte do projeto do pipeline é especificar um indexante, coberto no [passo seguinte](#next-step). Uma definição de indexante inclui uma referência ao skillset, além de mapeamentos de campo usados para ligar entradas a saídas no índice-alvo.

Pontos-chave para lembrar:

+ Só se pode ter um skillset por indexante.
+ Um skillset deve ter pelo menos uma habilidade.
+ Pode criar múltiplas habilidades do mesmo tipo (por exemplo, variantes de uma habilidade de análise de imagem).

## <a name="begin-with-the-end-in-mind"></a>Comece com o fim em mente

Um passo inicial recomendado é decidir quais os dados a extrair dos seus dados brutos e como pretende utilizar esses dados numa solução de pesquisa. Criar uma ilustração de todo o oleoduto de enriquecimento pode ajudá-lo a identificar os passos necessários.

Suponha que esteja interessado em processar um conjunto de comentários de analistas financeiros. Para cada ficheiro, pretende extrair nomes da empresa e o sentimento geral dos comentários. Também pode querer escrever um enriquecedor personalizado que utilize o serviço de Pesquisa de Entidade Bing para encontrar informações adicionais sobre a empresa, como o tipo de negócio em que a empresa está envolvida. Essencialmente, pretende extrair informações como as seguintes, indexadas a cada documento:

| texto-registo | empresas | sentimento | descrições da empresa |
|--------|-----|-----|-----|
|registo de amostras| ["Microsoft", "LinkedIn"] | 0.99 | ["A Microsoft Corporation é uma multinacional americana de tecnologia..." " LinkedIn é uma rede social orientada para o negócio e para o emprego..."]

O seguinte diagrama ilustra um hipotético gasoduto de enriquecimento:

![Um oleoduto hipotético de enriquecimento](media/cognitive-search-defining-skillset/sample-skillset.png "Um oleoduto hipotético de enriquecimento")


Uma vez que tenha uma ideia justa do que quer no oleoduto, poderá expressar a habilidade que fornece estes passos. Funcionalmente, o skillset é expresso quando envia a definição do indexante para Azure Cognitive Search. Para saber mais sobre como carregar o seu indexante, consulte a [documentação indexante](/rest/api/searchservice/create-indexer).


No diagrama, o passo *de rachadura* do documento acontece automaticamente. Essencialmente, a Azure Cognitive Search sabe abrir ficheiros bem conhecidos e cria um campo de *conteúdo* contendo o texto extraído de cada documento. As caixas brancas são enriquecedores embutidos, e a caixa pontilhada "Bing Entity Search" representa um enriquecimento personalizado que está a criar. Como ilustrado, o skillset contém três habilidades.

## <a name="skillset-definition-in-rest"></a>Definição de skillset em REST

Um skillset é definido como uma variedade de habilidades. Cada habilidade define a origem das suas entradas e o nome das saídas produzidas. Utilizando a [API Create Skillset REST,](/rest/api/searchservice/create-skillset)pode definir um skillset que corresponda ao diagrama anterior: 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
```

```json
{
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "Calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
  ]
}
```

## <a name="create-a-skillset"></a>Criar um conjunto de competências

Ao criar um skillset, você pode fornecer uma descrição para fazer o skillset auto-documentar. Uma descrição é opcional, mas útil para acompanhar o que um skillset faz. Como o skillset é um documento JSON, que não permite comentários, deve utilizar um `description` elemento para isso.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

A próxima peça no skillset é uma variedade de habilidades. Pode pensar em cada habilidade como uma primitiva de enriquecimento. Cada habilidade executa uma pequena tarefa neste oleoduto de enriquecimento. Cada um pega numa entrada (ou um conjunto de entradas) e devolve algumas saídas. As próximas secções focam-se em como especificar competências incorporadas e personalizadas, acorrentando habilidades em conjunto através de referências de entrada e saída. As entradas podem vir de dados de origem ou de outra habilidade. As saídas podem ser mapeadas para um campo num índice de pesquisa ou usadas como entrada para uma habilidade a jusante.

## <a name="add-built-in-skills"></a>Adicionar habilidades incorporadas

Vamos olhar para a primeira habilidade, que é a habilidade de reconhecimento de [entidade](cognitive-search-skill-entity-recognition.md)incorporada:

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    }
```

* Todas as habilidades incorporadas têm `odata.type` `input` propriedades e `output` propriedades. Propriedades específicas de habilidades fornecem informações adicionais aplicáveis a essa habilidade. Para o reconhecimento de entidades, `categories` é uma entidade entre um conjunto fixo de tipos de entidades que o modelo pré-treinado pode reconhecer.

* Cada habilidade deve ter um ```"context"``` . O contexto representa o nível a que as operações ocorrem. Na competência acima, o contexto é todo o documento, o que significa que a competência de reconhecimento da entidade é chamada uma vez por documento. As saídas também são produzidas a este nível. Mais especificamente, ```"organizations"``` são gerados como membros da ```"/document"``` . Nas competências a jusante, pode consultar esta informação recém-criada como ```"/document/organizations"``` .  Se o ```"context"``` campo não estiver explicitamente definido, o contexto padrão é o documento.

* A habilidade tem uma entrada chamada "texto", com uma entrada de origem definida para ```"/document/content"``` . A habilidade (reconhecimento de entidade) opera no campo de *conteúdo* de cada documento, que é um campo padrão criado pelo indexante blob Azure. 

* A habilidade tem uma saída chamada ```"organizations"``` . As saídas só existem durante o processamento. Para acorrentar esta saída à entrada de uma habilidade a jusante, refira a saída como ```"/document/organizations"``` .

* Para um documento em particular, o valor de ```"/document/organizations"``` é um conjunto de organizações extraídas do texto. Por exemplo:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Algumas situações exigem a referência de cada elemento de uma matriz separadamente. Por exemplo, suponha que pretende passar cada elemento ```"/document/organizations"``` de separadamente para outra habilidade (como o enriquecimento de pesquisa da entidade Bing personalizada). Pode consultar cada elemento da matriz adicionando um asterisco ao caminho: ```"/document/organizations/*"``` 

A segunda habilidade para a extração de sentimentos segue o mesmo padrão que o primeiro enriquente. Toma ```"/document/content"``` como entrada, e devolve uma pontuação de sentimento para cada instância de conteúdo. Uma vez que não definiu o ```"context"``` campo explicitamente, a saída (meuSentiment) é agora uma criança de ```"/document"``` .

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
```

## <a name="add-a-custom-skill"></a>Adicione uma habilidade personalizada

Lembre-se da estrutura do enriquecimento de pesquisa da entidade Bing personalizada:

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
```

Esta definição é uma [habilidade personalizada](cognitive-search-custom-skill-web-api.md) que chama uma API web como parte do processo de enriquecimento. Para cada organização identificada pelo reconhecimento de entidades, esta habilidade chama uma API web para encontrar a descrição dessa organização. A orquestração de quando chamar a API web e como fluir a informação recebida é tratada internamente pelo motor de enriquecimento. No entanto, a inicialização necessária para chamar a esta API personalizada deve ser fornecida no JSON (tais como uri, httpHeaders, e os inputs esperados). Para obter orientações na criação de uma API web personalizada para o pipeline de enriquecimento, consulte [como definir uma interface personalizada.](cognitive-search-custom-skill-interface.md)

Note que o campo "contexto" é definido ```"/document/organizations/*"``` com um asterisco, o que significa que o passo de enriquecimento é solicitado *para cada* organização sob ```"/document/organizations"``` . 

A produção, neste caso, é gerada uma descrição da empresa, para cada organização identificada. Quando se refere à descrição num passo a jusante (por exemplo, na extração de frases-chave), utilizaria o caminho ```"/document/organizations/*/description"``` para o fazer. 

## <a name="add-structure"></a>Adicionar estrutura

O skillset gera informação estruturada a partir de dados não estruturados. Considere o exemplo seguinte:

*"No quarto trimestre, a Microsoft registou 1,1 mil milhões de dólares em receitas do LinkedIn, a empresa de redes sociais que comprou no ano passado. A aquisição permite à Microsoft combinar as capacidades do LinkedIn com as suas capacidades crm e Office. Os acionistas estão entusiasmados com o progresso até agora."*

Um resultado provável seria uma estrutura gerada semelhante à seguinte ilustração:

![Estrutura de saída de amostra](media/cognitive-search-defining-skillset/enriched-doc.png "Estrutura de saída de amostra")

Até agora, esta estrutura tem sido apenas interna, apenas de memória, e usada apenas em índices de Pesquisa Cognitiva Azure. A adição de uma loja de conhecimento dá-lhe uma forma de guardar enriquecimentos em forma para uso fora da pesquisa.

## <a name="add-a-knowledge-store"></a>Adicione uma loja de conhecimento

[A loja de](knowledge-store-concept-intro.md) conhecimento é uma característica da Azure Cognitive Search para guardar o seu documento enriquecido. Uma loja de conhecimento que cria, apoiada por uma conta de armazenamento Azure, é o repositório onde os seus dados enriquecidos aterram. 

Uma definição de loja de conhecimento é adicionada a um skillset. Para uma passagem por todo o processo, consulte Criar uma loja de [conhecimento em REST](knowledge-store-create-rest.md).

```json
"knowledgeStore": {
  "storageConnectionString": "<an Azure storage connection string>",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "containername",
          "source": "/document/EnrichedShape/",
          "key": "/document/Id"
        }
      ]
    }
  ]
}
```

Pode optar por guardar os documentos enriquecidos como tabelas com relações hierárquicas preservadas ou como documentos JSON no armazenamento de bolhas. A produção de qualquer uma das habilidades no skillset pode ser obtido como o input para a projeção. Se procura projetar os dados numa forma específica, a habilidade de modelador atualizado pode agora [modelar](cognitive-search-skill-shaper.md) tipos complexos para que possa utilizar. 

<a name="next-step"></a>

## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com o oleoduto de enriquecimento e habilidades, continue com [como referenciar anotações em um skillset](cognitive-search-concept-annotations-syntax.md) ou [Como mapear saídas para campos em um índice](cognitive-search-output-field-mapping.md).