---
title: Criar um conconhecimento em um pipeline de pesquisa cognitiva-Azure Search
description: Defina as etapas de extração de dados, processamento de idioma natural ou análise de imagem para enriquecer e extrair informações estruturadas de seus dados para uso em Azure Search.
manager: nitinme
author: luiscabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: f78b8c3b9619b7eea92b6a4f04ed4f6543916efe
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "71265520"
---
# <a name="how-to-create-a-skillset-in-an-enrichment-pipeline"></a>Como criar um congrau de habilidade em um pipeline de enriquecimento

A pesquisa cognitiva extrai e enriquece os dados para torná-los pesquisáveis em Azure Search. Chamamos as *habilidades*de extração e enriquecimento, combinadas em um conjunto de *qualificações* referenciadas durante a indexação. Um técnico pode usar [habilidades internas](cognitive-search-predefined-skills.md) ou habilidades personalizadas (consulte [o exemplo: criando uma habilidade personalizada para pesquisa cognitiva](cognitive-search-create-custom-skill-example.md) para obter mais informações).

Neste artigo, você aprenderá a criar um pipeline de enriquecimento para as habilidades que deseja usar. Um contratador de qualificações é anexado a um [indexador](search-indexer-overview.md)de Azure Search. Uma parte do design de pipeline, abordada neste artigo, está construindo o próprio próprio contratador. 

> [!NOTE]
> Outra parte do design do pipeline é especificar um indexador, abordado na [próxima etapa](#next-step). Uma definição de indexador inclui uma referência para o skillset, além de mapeamentos de campo usados para conectar entradas a saídas no índice de destino.

Pontos principais a serem lembrados:

+ Você só pode ter um conceda por indexador.
+ Um qualificable deve ter pelo menos uma habilidade.
+ Você pode criar várias habilidades do mesmo tipo (por exemplo, variantes de uma habilidade de análise de imagem).

## <a name="begin-with-the-end-in-mind"></a>Comece com o final em mente

Uma etapa inicial recomendada é decidir quais dados serão extraídos dos dados brutos e como você deseja usar esses dados em uma solução de pesquisa. A criação de uma ilustração de todo o pipeline de enriquecimento pode ajudá-lo a identificar as etapas necessárias.

Suponha que você esteja interessado em processar um conjunto de comentários de analistas financeiros. Para cada arquivo, você deseja extrair os nomes da empresa e o inconveniente geral dos comentários. Você também pode querer escrever um aprimoramento personalizado que usa o serviço de Pesquisa de Entidade do Bing para encontrar informações adicionais sobre a empresa, como o tipo de negócios em que a empresa está envolvida. Essencialmente, você deseja extrair informações como as seguintes, indexadas para cada documento:

| registro-texto | das | sentimento | descrições da empresa |
|--------|-----|-----|-----|
|amostra-registro| ["Microsoft", "LinkedIn"] | 0,99 | ["A Microsoft Corporation é uma empresa de tecnologia multinacional americana...", "o LinkedIn é uma rede social, orientada a negócios e empregos..."]

O diagrama a seguir ilustra um pipeline de enriquecimento hipotético:

![Um pipeline de enriquecimento hipotético](media/cognitive-search-defining-skillset/sample-skillset.png "Um pipeline de enriquecimento hipotético")


Depois de ter uma ideia justa do que você deseja no pipeline, você pode expressar o tipo de habilidade que fornece essas etapas. Funcionalmente, o contratador de habilidades é expresso quando você carrega a definição do indexador para Azure Search. Para saber mais sobre como carregar o indexador, consulte a [documentação do indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).


No diagrama, a etapa de *quebra de documento* ocorre automaticamente. Essencialmente, Azure Search sabe como abrir arquivos bem conhecidos e cria um campo de *conteúdo* que contém o texto extraído de cada documento. As caixas brancas são aprimoramentos internos e a caixa "Pesquisa de Entidade do Bing" pontilhada representa um aprimorado personalizado que você está criando. Conforme ilustrado, o skillset contém três habilidades.

## <a name="skillset-definition-in-rest"></a>Definição de conconhecimento em REST

Um conjunto de qualificações é definido como uma matriz de habilidades. Cada habilidade define a origem de suas entradas e o nome das saídas produzidas. Usando a [API REST](https://docs.microsoft.com/rest/api/searchservice/create-skillset)do Configurador de habilidades, você pode definir um qualificable que corresponde ao diagrama anterior: 

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

Ao criar um qualificable, você pode fornecer uma descrição para tornar a autodocumentação do consentido. Uma descrição é opcional, mas útil para manter o controle do que o seu continha. Como o skillset é um documento JSON, que não permite comentários, você deve usar um elemento `description` para isso.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

A próxima parte do conjunto de qualificações é uma matriz de habilidades. Você pode considerar cada habilidade como um primitivo de enriquecimento. Cada habilidade executa uma tarefa pequena nesse pipeline de enriquecimento. Cada uma usa uma entrada (ou um conjunto de entradas) e retorna algumas saídas. As próximas seções concentram-se em como especificar habilidades internas e personalizadas, encadeando habilidades em referências de entrada e saída. As entradas podem vir de dados de origem ou de outra habilidade. As saídas podem ser mapeadas para um campo em um índice de pesquisa ou usadas como uma entrada para uma habilidade de downstream.

## <a name="add-built-in-skills"></a>Adicionar habilidades internas

Vamos examinar a primeira habilidade, que é a habilidade interna de reconhecimento de [entidade](cognitive-search-skill-entity-recognition.md):

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

* Todas as habilidades internas têm `odata.type`, `input` e `output` Propriedades. As propriedades específicas da habilidade fornecem informações adicionais aplicáveis a essa habilidade. Para o reconhecimento de entidade, `categories` é uma entidade entre um conjunto fixo de tipos de entidade que o modelo pretreinado pode reconhecer.

* Cada habilidade deve ter um ```"context"```. O contexto representa o nível no qual as operações ocorrem. Na habilidade acima, o contexto é o documento inteiro, o que significa que a habilidade de reconhecimento de entidade é chamada uma vez por documento. As saídas também são produzidas nesse nível. Mais especificamente, ```"organizations"``` são geradas como um membro de ```"/document"```. Em habilidades de downstream, você pode consultar essas informações recentemente criadas como ```"/document/organizations"```.  Se o campo ```"context"``` não estiver definido explicitamente, o contexto padrão será o documento.

* A habilidade tem uma entrada chamada "texto", com uma entrada de origem definida como ```"/document/content"```. A habilidade (reconhecimento de entidade) opera no campo de *conteúdo* de cada documento, que é um campo padrão criado pelo indexador de blob do Azure. 

* A habilidade tem uma saída chamada ```"organizations"```. As saídas existem somente durante o processamento. Para encadear essa saída à entrada de uma habilidade downstream, faça referência à saída como ```"/document/organizations"```.

* Para um documento específico, o valor de ```"/document/organizations"``` é uma matriz de organizações extraída do texto. Por exemplo:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Algumas situações chamam a referência de cada elemento de uma matriz separadamente. Por exemplo, suponha que você queira passar cada elemento de ```"/document/organizations"``` separadamente para outra habilidade (como a pesquisa de entidade personalizada do Bing mais sofisticada). Você pode fazer referência a cada elemento da matriz adicionando um asterisco ao caminho: ```"/document/organizations/*"``` 

A segunda habilidade para extração de sentimentos segue o mesmo padrão que o primeiro enriquecimento. Ele usa ```"/document/content"``` como entrada e retorna uma pontuação de sentimentos para cada instância de conteúdo. Como você não definiu o campo ```"context"``` explicitamente, a saída (mysentimentos) agora é um filho de ```"/document"```.

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

Lembre-se de que a estrutura da pesquisa de entidade personalizada do Bing aprimora o aprimoramento:

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

Essa definição é uma [habilidade personalizada](cognitive-search-custom-skill-web-api.md) que chama uma API da Web como parte do processo de enriquecimento. Para cada organização identificada pelo reconhecimento de entidade, essa habilidade chama uma API da Web para localizar a descrição dessa organização. A orquestração de quando chamar a API da Web e como fluir as informações recebidas é manipulada internamente pelo mecanismo de enriquecimento. No entanto, a inicialização necessária para chamar essa API personalizada deve ser fornecida no JSON (como URI, httpHeaders e as entradas esperadas). Para obter orientação sobre como criar uma API Web personalizada para o pipeline de enriquecimento, consulte [como definir uma interface personalizada](cognitive-search-custom-skill-interface.md).

Observe que o campo "contexto" é definido como ```"/document/organizations/*"``` com um asterisco, o que significa que a etapa de enriquecimento é chamada *para cada* organização em ```"/document/organizations"```. 

A saída, nesse caso, uma descrição da empresa, é gerada para cada organização identificada. Ao se referir à descrição em uma etapa de downstream (por exemplo, em extração de frase-chave), você usaria o caminho ```"/document/organizations/*/description"``` para fazer isso. 

## <a name="add-structure"></a>Adicionar estrutura

O qualificable gera informações estruturadas de dados não estruturados. Considere o seguinte exemplo:

*"Em seu quarto trimestre, a Microsoft registrou $1100000000 em receita do LinkedIn, a empresa de rede social comprada no ano passado. A aquisição permite que a Microsoft Combine recursos do LinkedIn com seus recursos do CRM e do Office. Os acionistas estão empolgados com o progresso até agora. "*

Um resultado provável seria uma estrutura gerada semelhante à ilustração a seguir:

![Estrutura de saída de exemplo](media/cognitive-search-defining-skillset/enriched-doc.png "Estrutura de saída de exemplo")

Até agora, essa estrutura tem sido somente interno, somente de memória e usada somente em índices Azure Search. A adição de uma loja de conhecimento oferece uma maneira de salvar os aprimoramentos moldados para uso fora da pesquisa.

## <a name="add-a-knowledge-store"></a>Adicionar uma loja de conhecimento

O [repositório de conhecimento](knowledge-store-concept-intro.md) é um recurso de visualização no Azure Search para salvar seu documento aprimorado. Uma loja de conhecimento que você cria, apoiada por uma conta de armazenamento do Azure, é o repositório no qual seus dados aprimorados ficam. 

Uma definição de repositório de conhecimento é adicionada a um conconhecedor. Para obter uma explicação do processo inteiro, consulte [como começar a usar a loja de conhecimento](knowledge-store-howto.md).

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

Você pode optar por salvar os documentos aprimorados como tabelas com relações hierárquicas preservadas ou como documentos JSON no armazenamento de BLOBs. A saída de qualquer uma das habilidades no conferent pode ser originada como a entrada para a projeção. Se você pretende projetar os dados em uma forma específica, a [habilidade](cognitive-search-skill-shaper.md) atualizada do modelador agora pode modelar tipos complexos para uso. 

<a name="next-step"></a>

## <a name="next-steps"></a>Passos seguintes

Agora que você está familiarizado com o pipeline de enriquecimento e habilidades, continue com [como fazer referência a anotações em um](cognitive-search-concept-annotations-syntax.md) configurador de habilidades ou [como mapear saídas para campos em um índice](cognitive-search-output-field-mapping.md). 
