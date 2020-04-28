---
title: Criar um conjunto de competências
titleSuffix: Azure Cognitive Search
description: Defina passos de extração de dados, processamento de linguagem natural ou análise de imagem para enriquecer e extrair informações estruturadas dos seus dados para utilização na Pesquisa Cognitiva azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 43251783cbcd6501562913b7b9cafb4f9f7cb3f1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75754560"
---
# <a name="how-to-create-a-skillset-in-an-ai-enrichment-pipeline-in-azure-cognitive-search"></a>Como criar uma habilidade num oleoduto de enriquecimento de IA em Pesquisa Cognitiva Azure 

O enriquecimento de IA extrai e enriquece dados para torná-lo pesquisável na Pesquisa Cognitiva Azure. Chamamos de habilidades *cognitivas*de extração e enriquecimento, combinadas num conjunto de *habilidades* referenciados durante a indexação. Um skillset pode usar [habilidades incorporadas](cognitive-search-predefined-skills.md) ou habilidades personalizadas (ver [Exemplo: Criar uma habilidade personalizada num pipeline](cognitive-search-create-custom-skill-example.md) de enriquecimento de IA para mais informações).

Neste artigo, aprende-se a criar um pipeline de enriquecimento para as competências que pretende utilizar. Um skillset é ligado a um [indexador](search-indexer-overview.md)de pesquisa cognitiva Azure . Uma parte do design do oleoduto, abrangida por este artigo, é a construção da própria habilidade. 

> [!NOTE]
> Outra parte do desenho do gasoduto é especificar um indexante, coberto no [passo seguinte](#next-step). Uma definição de indexante inclui uma referência ao skillset, mais mapeamentos de campo usados para ligar entradas a saídas no índice alvo.

Pontos-chave a lembrar:

+ Só se pode ter uma habilidade por indexante.
+ Uma habilidade deve ter pelo menos uma habilidade.
+ Pode criar múltiplas habilidades do mesmo tipo (por exemplo, variantes de uma habilidade de análise de imagem).

## <a name="begin-with-the-end-in-mind"></a>Comece com o fim em mente

Um passo inicial recomendado é decidir quais os dados a extrair dos seus dados brutos e como pretende utilizar esses dados numa solução de pesquisa. Criar uma ilustração de todo o oleoduto de enriquecimento pode ajudá-lo a identificar os passos necessários.

Suponha que esteja interessado em processar um conjunto de comentários de analistas financeiros. Para cada ficheiro, pretende extrair nomes da empresa e o sentimento geral dos comentários. Pode também querer escrever um enriquecedor personalizado que utilize o serviço bing entity search para encontrar informações adicionais sobre a empresa, como em que tipo de negócio a empresa está envolvida. Essencialmente, pretende extrair informações como as seguintes, indexadas para cada documento:

| texto de gravação | empresas | sentimento | descrições da empresa |
|--------|-----|-----|-----|
|sample-record| ["Microsoft", "LinkedIn"] | 0.99 | ["A Microsoft Corporation é uma empresa americana de tecnologia multinacional..." "O LinkedIn é uma rede social orientada para o negócio e para o emprego..."]

O diagrama que se segue ilustra um hipotético gasoduto de enriquecimento:

![Um hipotético oleoduto de enriquecimento](media/cognitive-search-defining-skillset/sample-skillset.png "Um hipotético oleoduto de enriquecimento")


Uma vez que tenha uma ideia justa do que quer no oleoduto, pode expressar a habilidade que fornece estes passos. Funcionalmente, a habilidade é expressa quando envia a definição do indexante para a Pesquisa Cognitiva Azure. Para saber mais sobre como carregar o seu indexador, consulte a [documentação do indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).


No diagrama, o passo *de rachadura* do documento acontece automaticamente. Essencialmente, a Azure Cognitive Search sabe abrir ficheiros bem conhecidos e cria um campo de *conteúdo* contendo o texto extraído de cada documento. As caixas brancas são enriquecedoras incorporadas, e a caixa pontilhada "Bing Entity Search" representa um enriquecedor personalizado que está a criar. Como ilustrado, a habilidade contém três habilidades.

## <a name="skillset-definition-in-rest"></a>Definição de skillset em REST

Uma habilidade é definida como uma variedade de habilidades. Cada habilidade define a fonte das suas inputs e o nome das saídas produzidas. Utilizando a [Create Skillset REST API,](https://docs.microsoft.com/rest/api/searchservice/create-skillset)pode definir um conjunto de habilidades que corresponde ao diagrama anterior: 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
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

Ao criar uma habilidade, pode fornecer uma descrição para fazer a habilidade auto-documentar. Uma descrição é opcional, mas útil para acompanhar o que um skillset faz. Como o skillset é um documento JSON, que não `description` permite comentários, deve utilizar um elemento para isso.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

A próxima peça na habilidade é uma variedade de habilidades. Pode pensar em cada habilidade como uma primitiva de enriquecimento. Cada habilidade executa uma pequena tarefa neste oleoduto de enriquecimento. Cada um recebe uma entrada (ou um conjunto de entradas) e devolve algumas saídas. As próximas secções focam-se em como especificar habilidades incorporadas e personalizadas, acorrentar competências através de referências de entrada e saída. As inputs podem vir de dados de origem ou de outra habilidade. As saídas podem ser mapeadas para um campo num índice de pesquisa ou usadas como uma entrada para uma habilidade a jusante.

## <a name="add-built-in-skills"></a>Adicionar habilidades incorporadas

Vejamos a primeira habilidade, que é a habilidade de reconhecimento de [entidades](cognitive-search-skill-entity-recognition.md)incorporadas:

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

* Todas as habilidades `odata.type` `input`incorporadas `output` têm, e propriedades. Propriedades específicas de habilidades fornecem informações adicionais aplicáveis a essa habilidade. Para o `categories` reconhecimento de entidades, é uma entidade entre um conjunto fixo de tipos de entidades que o modelo pré-treinado pode reconhecer.

* Cada habilidade deve ```"context"```ter um. O contexto representa o nível a que as operações ocorrem. Na habilidade acima, o contexto é todo o documento, o que significa que a habilidade de reconhecimento da entidade é chamada uma vez por documento. As saídas também são produzidas a este nível. Mais especificamente, ```"organizations"``` são gerados ```"/document"```como um membro de . Nas habilidades a jusante, pode ```"/document/organizations"```consultar esta informação recém-criada como .  Se ```"context"``` o campo não for explicitamente definido, o contexto predefinido é o documento.

* A habilidade tem uma entrada chamada "texto", com ```"/document/content"```uma entrada de origem definida para . A habilidade (reconhecimento de entidades) opera no campo de *conteúdo* de cada documento, que é um campo padrão criado pelo indexante de blob Azure. 

* A habilidade tem ```"organizations"```uma saída chamada . As saídas só existem durante o processamento. Para acorrentar esta saída à entrada de uma ```"/document/organizations"```habilidade a jusante, referência a saída como .

* Para um documento específico, ```"/document/organizations"``` o valor é um conjunto de organizações extraídas do texto. Por exemplo:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Algumas situações exigem a referenciação de cada elemento de uma matriz separadamente. Por exemplo, suponha que ```"/document/organizations"``` você quer passar cada elemento separadamente para outra habilidade (como o enriquecedor de pesquisa personalizado da entidade Bing). Pode consultar cada elemento da matriz adicionando um asterisco ao caminho:```"/document/organizations/*"``` 

A segunda habilidade para a extração de sentimentos segue o mesmo padrão que o primeiro enriquecedor. É ```"/document/content"``` preciso como entrada e devolve uma pontuação de sentimento para cada instância de conteúdo. Como não definiu ```"context"``` o campo explicitamente, a saída (mySentiment) é agora uma criança de ```"/document"```.

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

Lembre-se da estrutura do enriquecedor de pesquisa personalizado da entidade Bing:

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

Esta definição é uma [habilidade personalizada](cognitive-search-custom-skill-web-api.md) que chama uma API web como parte do processo de enriquecimento. Para cada organização identificada pelo reconhecimento de entidades, esta habilidade chama uma API web para encontrar a descrição dessa organização. A orquestração de quando chamar a Web API e como fluir a informação recebida é tratada internamente pelo motor de enriquecimento. No entanto, a inicialização necessária para chamar esta API personalizada deve ser fornecida no JSON (como uri, httpHeaders e as inputs esperadas). Para obter orientação na criação de uma API web personalizada para o pipeline de enriquecimento, consulte [Como definir uma interface personalizada](cognitive-search-custom-skill-interface.md).

Note que o campo "contexto" está definido ```"/document/organizations/*"``` com um asterisco, o que significa que o passo de enriquecimento é chamado para *cada* organização em ```"/document/organizations"```. 

A produção, neste caso, uma descrição da empresa, é gerada para cada organização identificada. Ao referir-se à descrição num degrau a jusante (por ```"/document/organizations/*/description"``` exemplo, na extração de frases-chave), utilizaria o caminho para o fazer. 

## <a name="add-structure"></a>Adicionar estrutura

O skillset gera informação estruturada a partir de dados não estruturados. Considere o exemplo seguinte:

*"No quarto trimestre, a Microsoft registou 1,1 mil milhões de dólares em receitas do LinkedIn, a empresa de redes sociais que comprou no ano passado. A aquisição permite à Microsoft combinar capacidades do LinkedIn com as suas capacidades crm e Office. Os acionistas estão entusiasmados com o progresso até agora."*

Um resultado provável seria uma estrutura gerada semelhante à seguinte ilustração:

![Estrutura de saída da amostra](media/cognitive-search-defining-skillset/enriched-doc.png "Estrutura de saída da amostra")

Até agora, esta estrutura tem sido apenas interna, apenas de memória, e usada apenas em índices de Pesquisa Cognitiva Azure. A adição de uma loja de conhecimentos dá-lhe uma forma de guardar enriquecimentos em forma para uso fora da pesquisa.

## <a name="add-a-knowledge-store"></a>Adicione uma loja de conhecimento

[A Knowledge Store](knowledge-store-concept-intro.md) é uma funcionalidade de pré-visualização na Azure Cognitive Search para salvar o seu documento enriquecido. Uma loja de conhecimento que cria, apoiada por uma conta de armazenamento Azure, é o repositório onde os seus dados enriquecidos aterram. 

Uma definição de loja de conhecimento é adicionada a um skillset. Para uma passagem de todo o processo, consulte Criar uma loja de [conhecimentos em REST](knowledge-store-create-rest.md).

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

Pode optar por guardar os documentos enriquecidos como tabelas com relações hierárquicas preservadas ou como documentos JSON no armazenamento de blob. A saída de qualquer uma das habilidades no skillset pode ser obtida como a entrada para a projeção. Se procura projetar os dados numa forma específica, a habilidade de [shaper](cognitive-search-skill-shaper.md) atualizada pode agora modelar tipos complexos para que possa utilizar. 

<a name="next-step"></a>

## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com o oleoduto de enriquecimento e as habilidades, continue com [como referenciar anotações num skillset](cognitive-search-concept-annotations-syntax.md) ou [como mapear saídas para campos num índice](cognitive-search-output-field-mapping.md). 
