---
title: Criar um conjunto de capacidades num pipeline de pesquisa cognitiva - Azure Search
description: Definir a extração de dados, processamento, de linguagem natural ou passos de análise de imagem para enriquecer e extrair informações estruturadas dos seus dados para utilizam no Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 010880304c5011bd5a767650aa0ae6b6103bdde2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540861"
---
# <a name="how-to-create-a-skillset-in-an-enrichment-pipeline"></a>Como criar um conjunto de capacidades num pipeline de melhoria

Pesquisa cognitiva extrai e otimiza os dados para torná-lo pesquisáveis no Azure Search. Chamamos passos de extração e enriquecimento *capacidades cognitivas*, combinados num *conjunto de capacidades* referenciado durante a indexação. Pode utilizar um conjunto de capacidades [habilidades internas](cognitive-search-predefined-skills.md) ou competências personalizadas (consulte [exemplo: criar uma habilidade personalizada](cognitive-search-create-custom-skill-example.md) para obter mais informações).

Neste artigo, saiba como criar um pipeline de melhoria para as competências que pretende utilizar. Um conjunto de capacidades está ligado a um Azure Search [indexador](search-indexer-overview.md). Uma parte do design de pipeline, abordada neste artigo, é construir o conjunto de capacidades em si. 

> [!NOTE]
> Outra parte do design de pipeline está especificando um indexador, abordado os [próxima etapa](#next-step). Uma definição de indexador inclui uma referência para o conjunto de capacidades, além de mapeamentos de campo utilizados para ligar entradas para saídas no índice de destino.

Pontos principais a serem lembrados:

+ Só pode ter um conjunto de capacidades por indexador.
+ Um conjunto de capacidades tem de ter, pelo menos, uma habilidade.
+ Pode criar várias habilidades do mesmo tipo (por exemplo, variantes de uma habilidade de análise de imagem).

## <a name="begin-with-the-end-in-mind"></a>Começar com o fim em mente

Um passo inicial recomendado é decidir quais os dados para extrair dos seus dados não processados e como deseja utilizar esses dados numa solução de pesquisa. Criar uma ilustração do pipeline enriquecimento inteira pode ajudar a identificar as etapas necessárias.

Suponha que esteja interessado no processamento de um conjunto de comentários do analista financeiro. Para cada ficheiro que pretende extrair nomes de empresa e o sentimento geral dos comentários. Também poderá escrever um enricher personalizado que utiliza o serviço de pesquisa de entidades do Bing para encontrar informações adicionais sobre a empresa, como o tipo de negócios, a empresa está participando de. Essencialmente, que pretende extrair informações semelhantes ao seguinte indexados para cada documento:

| record-text | Empresas | sentimento | descrições de empresa |
|--------|-----|-----|-----|
|sample-record| ["Microsoft", "LinkedIn"] | 0,99 | ["Microsoft Corporation é uma empresa de tecnologia multinacional American...", "LinkedIn é um e emprego-orientado a negócios Comunidades..."]

O diagrama seguinte ilustra um pipeline de enriquecimento hipotético:

![Um pipeline de enriquecimento hipotético](media/cognitive-search-defining-skillset/sample-skillset.png "um pipeline de enriquecimento hipotético")


Assim que tiver boa idéia de que pretende no pipeline, pode expressar o conjunto de capacidades que fornece estes passos. Funcionalmente, o conjunto de capacidades é expressa ao carregar a definição de indexador para o Azure Search. Para saber mais sobre como carregar seu indexador, consulte a [documentação de indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).


No diagrama, o *aberturas de documentos* passo ocorre automaticamente. Essencialmente, o Azure Search sabe como abrir ficheiros bem conhecidos e cria um *conteúdo* campo que contém os textos extraídos dos cada documento. As caixas brancas são enrichers internos e caixa pontilhada da "Pesquisa de entidades do Bing" representa um enricher personalizado que está a criar. Conforme ilustrado, o conjunto de capacidades contém três habilidades.

## <a name="skillset-definition-in-rest"></a>Definição de conjunto de capacidades no REST

Um conjunto de capacidades é definido como uma matriz de habilidades. Cada habilidade define a origem das respetivas entradas e o nome de saídas produzido. Utilizar o [criar API REST do conjunto de capacidades](https://docs.microsoft.com/rest/api/searchservice/create-skillset), pode definir um conjunto de capacidades que corresponde ao diagrama anterior: 

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

Ao criar um conjunto de capacidades, pode fornecer uma descrição para tornar o conjunto de capacidades Self-documentar. Uma descrição é opcional, mas o mais útil para controlar o que faz um conjunto de capacidades. Como o conjunto de capacidades é documentos JSON, que não permitem comentários, tem de utilizar um `description` elemento para que isso.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

A próxima peça o conjunto de capacidades é uma matriz de habilidades. Pode pensar cada habilidades como um primitivo de melhoria. Cada habilidade executa uma tarefa de small neste pipeline de melhoria. Cada um deles usa uma entrada (ou um conjunto de entradas) e retorna algumas saídas. As próximas seções se concentrar em como especificar as capacidades incorporadas e personalizadas, encadeamento habilidades em conjunto através de referências de entrada e saídas. Entradas podem vir de dados de origem ou de outra habilidade. Saídas podem ser mapeadas para um campo num índice de pesquisa ou usadas como entrada para uma habilidade de downstream.

## <a name="add-built-in-skills"></a>Adicionar habilidades internas

Vamos examinar a habilidade de primeira, que é incorporada [habilidade de reconhecimento de entidades](cognitive-search-skill-entity-recognition.md):

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

* Tem de todas as habilidades internas `odata.type`, `input`, e `output` propriedades. Propriedades de habilidades específicas fornecem informações adicionais aplicáveis a essa habilidade. Para reconhecimento de entidades, `categories` é uma entidade entre um conjunto fixo de tipos de entidade que o modelo pretrained pode reconhecer.

* Cada habilidade deve ter um ```"context"```. O contexto representa o nível em que operações ocorrem. Na habilidade acima, o contexto é todo o documento, que significa que a habilidade de reconhecimento de entidades é chamada de uma vez por documento. Saídas também são produzidas nesse nível. Mais especificamente, ```"organizations"``` são gerados como membro do ```"/document"```. No downstream habilidades, pode consultar recentemente criada informações como ```"/document/organizations"```.  Se o ```"context"``` campo não for explicitamente definido, o contexto predefinido é o documento.

* A habilidade tem uma entrada chamada "text", com um conjunto de entrada de origem para ```"/document/content"```. A habilidade (reconhecimento de entidades) funciona com o *conteúdo* campo de cada documento, o que é um campo padrão criado pelo indexador blob do Azure. 

* A habilidade tem uma saída chamada ```"organizations"```. Saídas de existir apenas durante o processamento. Para encadear esta saída à entrada de uma habilidade downstream, referenciar a saída como ```"/document/organizations"```.

* Para um documento em particular, o valor de ```"/document/organizations"``` é uma matriz de organizações extraídos a partir do texto. Por exemplo:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Algumas situações chamam para referenciar a cada elemento de uma matriz em separado. Por exemplo, suponha que quiser passar de cada elemento da ```"/document/organizations"``` separadamente para outra habilidade (por exemplo, enricher o personalizada do Bing entidade pesquisa). Pode consultar a cada elemento da matriz, adicionando um asterisco para o caminho: ```"/document/organizations/*"``` 

O segundo de habilidades para extração de sentimento segue o mesmo padrão como o primeiro enricher. Demora ```"/document/content"``` como entrada e devolve um sentimento pontuação para cada instância de conteúdo. Uma vez que não definiu os ```"context"``` campo explicitamente, a saída (mySentiment) agora é um filho de ```"/document"```.

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

## <a name="add-a-custom-skill"></a>Adicionar uma habilidade personalizada

Lembre-se a estrutura do enricher de pesquisa de entidade personalizada do Bing:

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      }
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

Esta definição é uma [competências personalizadas](cognitive-search-custom-skill-web-api.md) que chama uma API web como parte do processo de melhoria. Para cada organização identificada pelo reconhecimento de entidades, essa habilidade chama uma API web para encontrar a descrição de nessa organização. A orquestração de quando a chamada da API web e como as informações recebidas de fluxo é tratada internamente pelo mecanismo de melhoria. No entanto, a inicialização necessária para chamar esta API personalizada deve ser fornecida no JSON (por exemplo, uri, httpHeaders e as entradas esperadas). Para obter orientações sobre como criar uma API de web personalizado para o pipeline de melhoria, consulte [como definir uma interface personalizada](cognitive-search-custom-skill-interface.md).

Tenha em atenção que o campo de "contexto" é definido como ```"/document/organizations/*"``` com um asterisco, o que significa que o passo de melhoria é chamado *para cada* organização sob ```"/document/organizations"```. 

A saída, neste caso uma descrição da empresa, é gerado para cada organização identificada. Quando nos Referimos a descrição num passo downstream (por exemplo, na extração de expressões-chave), usaria o caminho ```"/document/organizations/*/description"``` para fazer isso. 

## <a name="add-structure"></a>Adicionar estrutura

O conjunto de capacidades gera informações estruturadas dos dados não estruturados. Considere o exemplo a seguir:

*"No respetivo quarto trimestre, Microsoft registado 1.1 mil milhões de dólares em receitas do LinkedIn, a empresa de redes sociais que comprou o ano passado. A aquisição permite que a Microsoft combinar as capacidades do LinkedIn com seu CRM e as capacidades do Office. Stockholders estão entusiasmados com o progresso até agora."*

Um resultado provável seria uma estrutura gerada semelhante à ilustração seguinte:

![Estrutura de saída de exemplo](media/cognitive-search-defining-skillset/enriched-doc.png "estrutura de saída de exemplo")

Até agora, essa estrutura tem sido apenas internos, como só de memória e é utilizado apenas em índices de pesquisa do Azure. A adição de um arquivo de dados de conhecimento fornece uma maneira de economizar moldada possível para utilização fora da pesquisa.

## <a name="add-a-knowledge-store"></a>Adicionar um arquivo de dados de conhecimento

[Arquivo de dados de conhecimento](knowledge-store-concept-intro.md) é uma funcionalidade de pré-visualização do Azure Search para salvar seu documento plena. Um arquivo de dados de conhecimento que criar, de uma conta de armazenamento do Azure, é o repositório onde os dados plena que chegam. 

Uma definição de arquivo de dados de conhecimento é adicionada a um conjunto de capacidades. Para obter instruções de todo o processo, consulte [como começar com o arquivo de dados de conhecimento](knowledge-store-howto.md).

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

Pode optar por guardar os documentos plena como tabelas com relações hierárquicas preservadas ou como documentos JSON no armazenamento de Blobs. Saída de qualquer uma das habilidades das competências pode ser disponibilizada como entrada para a projeção. Se estiver à procura para os dados do projeto num específico formatar, atualizada [habilidade Modelador](cognitive-search-skill-shaper.md) agora pode modelar tipos complexos que pode utilizar. 

<a name="next-step"></a>

## <a name="next-steps"></a>Passos Seguintes

Agora que já está familiarizado com o pipeline de enriquecimento e conjuntos de habilidades, prosseguir [como fazer referência a anotações num conjunto de capacidades](cognitive-search-concept-annotations-syntax.md) ou [como mapear saídas para campos num índice](cognitive-search-output-field-mapping.md). 
