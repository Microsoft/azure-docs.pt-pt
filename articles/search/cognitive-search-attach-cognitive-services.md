---
title: Anexar um recurso de serviços cognitivos com um conjunto de capacidades - Azure Search
description: Instruções para anexar uma subscrição de tudo-em-um dos serviços cognitivos para um pipeline de enriquecimento cognitivos no Azure Search.
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: d5fdae09055f922fe9783f6eb074457af12c60df
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57880420"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Anexar um recurso de serviços cognitivos com um conjunto de capacidades no Azure Search 

Unidade de algoritmos de IA da [pipelines de pesquisa cognitiva](cognitive-search-concept-intro.md) utilizada para processar dados não estruturados numa operação de indexação do Azure Search. Esses algoritmos são baseiam [recursos dos serviços cognitivos](https://azure.microsoft.com/services/cognitive-services/), incluindo [de imagem digitalizada](https://azure.microsoft.com/services/cognitive-services/computer-vision/) para análise de imagem e reconhecimento ótico de carateres (OCR), e [de análise de texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/)para o reconhecimento de entidades, extração de expressões-chave e outros possível.

Pode enriquecer um número limitado de documentos gratuitamente ou anexar um recurso dos serviços cognitivos cobrar para cargas de trabalho maiores e mais freqüentes. Neste artigo, saiba como associar um recurso dos serviços cognitivos com o conjunto de capacidades cognitivo para enriquecer os dados durante [indexação de pesquisa do Azure](search-what-is-an-index.md).

Se o seu pipeline consiste em habilidades não relacionado com APIs serviços cognitivos, ainda deve anexar um recurso dos serviços cognitivos. Fazer substituições por isso, o **gratuito** recursos que limita a uma pequena quantidade de possível por dia. Não existe nenhum custo associado de habilidades que não estão vinculados às APIs serviços cognitivos. Estas capacidades incluem: [competências personalizadas](cognitive-search-create-custom-skill-example.md), [fusão de texto](cognitive-search-skill-textmerger.md), [divisor de texto](cognitive-search-skill-textsplit.md), e [Modelador](cognitive-search-skill-shaper.md).

> [!NOTE]
> A partir de 21 de Dezembro de 2018, pode associar um recurso dos serviços cognitivos com um conjunto de capacidades do Azure Search. Isto permite-nos ser cobrada a execução do conjunto de capacidades. Esta data, também Começámos a cobrar para extração de imagem como parte da fase de aberturas de documentos. Extração de texto de documentos continua a ser disponibilizado sem custos adicionais.
>
> [Competências cognitivas incorporada](cognitive-search-predefined-skills.md) execução é cobrada a [dos serviços cognitivos pay as you go preço](https://azure.microsoft.com/pricing/details/cognitive-services), em para classificar o mesmo como se realizados diretamente a tarefa. Extração de imagem é um valor de Azure Search, atualmente oferecido a preços de pré-visualização. Para obter detalhes, consulte a [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400) ou [como funciona a faturação](search-sku-tier.md#how-billing-works).


## <a name="use-free-resources"></a>Utilizar recursos gratuitos

Pode utilizar uma opção de processamento limitado e gratuito para concluir os exercícios de tutorial e guia de introdução de pesquisa cognitiva. 

> [!Important]
> A partir de 1 de Fevereiro de 2019, o **gratuito (limitado possível)** irá ser restritos a 20 documentos por dia. 

1. Abra o **importar dados** assistente.

   ![Dados comando import](media/search-get-started-portal/import-data-cmd2.png "importar o comando de dados")

1. Escolha uma origem de dados e continuar a **adicionar pesquisa cognitiva (opcional)**. Para obter instruções passo a passo deste assistente, consulte [importação, índice e a consulta com ferramentas do portal](search-get-started-portal.md).

1. Expanda **anexar serviços cognitivos** e selecione **gratuito (limitado possível)**.

   ![Expandir a secção de anexar serviços cognitivos](./media/cognitive-search-attach-cognitive-services/attach1.png "expandido anexar serviços cognitivos")

Continuar para a próxima etapa **adicionar possível**. Para obter uma descrição das habilidades disponíveis no portal, consulte ["passo 2: Adicionar a habilidade cognitiva"](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) no guia de introdução de pesquisa cognitiva.

## <a name="use-billable-resources"></a>Utilizar recursos faturáveis

Para cargas de trabalho mais de 20 possível de numeração diariamente, terá de anexar um recurso dos serviços cognitivos faturável. 

É cobrado apenas para as competências que chamam as APIs serviços cognitivos. Gostam de habilidades não API baseada [competências personalizadas](cognitive-search-create-custom-skill-example.md), [fusão de texto](cognitive-search-skill-textmerger.md), [divisor de texto](cognitive-search-skill-textsplit.md), e [Modelador](cognitive-search-skill-shaper.md) habilidades não são faturadas.

1. Na **importar dados** assistente no **anexar serviços cognitivos**, selecione um recurso existente ou clique em **criar novo recurso de serviços cognitivos**.

1. Para **criar novo recurso de serviços cognitivos**, é aberto um separador novo para que possa criar o recurso. Dê um nome exclusivo ao recurso.

1. Se criar um novo recurso de serviços cognitivos, **selecione a região da mesma** como seu recurso do Azure Search.

1. Escolha o escalão de preço de tudo-em-um, **S0**. Este escalão fornece as funcionalidades de visão e a linguagem que apoiam as competências predefinidas na pesquisa cognitiva.

    ![Criar um novo recurso de serviços cognitivos](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "criar um novo recurso de serviços cognitivos")

1. Clique em **criar** para aprovisionar o novo recurso de serviços cognitivos. 

1. Regresse ao separador o anterior, contendo o **importar dados** assistente. Clique em **atualizar** para mostrar o recurso dos serviços cognitivos e, em seguida, selecione o recurso.

   ![Selecionar recurso dos serviços cognitivos](./media/cognitive-search-attach-cognitive-services/attach2.png "selecionado recurso de serviço dos serviços cognitivos")

1. Expanda a **possível adicionar** secção para selecionar as capacidades cognitivas específicas que pretende executar com os seus dados e prosseguir com o resto do fluxo. Para obter uma descrição das habilidades disponíveis no portal, consulte ["passo 2: Adicionar a habilidade cognitiva"](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) no guia de introdução de pesquisa cognitiva.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Anexar um conjunto de competências existente a um recurso dos serviços cognitivos

Se tiver um conjunto de capacidades existente, pode anexá-lo a um recurso de serviços cognitivos novo ou diferente.

1. Sobre o **descrição geral do serviço** página, clique em **conjuntos de competências**.

   ![Separador conjuntos de competências](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "separador conjuntos de competências")

1. Clique no nome do conjunto de capacidades e, em seguida, selecione um recurso existente ou crie um novo. Clique em **OK** para confirmar as alterações. 

   ![Lista de recursos do conjunto de capacidades](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "lista de recursos do conjunto de capacidades")

Lembre-se de que **gratuito (possível limitado)** é limitado a 20 documentos diariamente e que **criar novo recurso de serviços cognitivos** é utilizado para aprovisionar um novo recurso faturável. Se criar um novo recurso, selecione **atualizar** para atualizar a lista de recursos dos serviços cognitivos e, em seguida, selecione o recurso.

## <a name="attach-cognitive-services-programmatically"></a>Anexar serviços cognitivos através de programação

Quando está a definir o conjunto de capacidades programaticamente, adicione um `cognitiveServices` secção para o conjunto de capacidades. Na secção, inclua a chave do recurso dos serviços cognitivos que pretende associar o conjunto de capacidades. Lembre-se de que o recurso tem de ser na mesma região que o Azure Search. Também incluem `@odata.type`e defina-o como `#Microsoft.Azure.Search.CognitiveServicesByKey`. 

O exemplo seguinte mostra este padrão. Observe a secção de cognitiveServices na parte inferior da definição

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>Exemplo: Estimar custos

Para estimar os custos associados a indexação de pesquisa cognitiva, comece com uma ideia de um documento média semelhante ao seguinte para que possam executar alguns números. Por exemplo, para fins de estimativa, pode fazer uma aproximação:

+ 1000 PDFs
+ Seis páginas
+ Uma imagem por página (6000 imagens)
+ 3000 carateres por página

Suponha que um pipeline que consiste num documento de abertura de cada PDF com a extração de texto e imagem, reconhecimento ótico de carateres (OCR) de imagens e com o nome reconhecimento de entidades de organizações. 

Neste exercício, estamos a utilizar o preço mais caro por transação. Os custos reais podem ser inferiores devido a preços escalonados. Ver [preços dos serviços cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Para com conteúdo de texto e imagem de abertura do documento, extração de texto é atualmente gratuita. Para imagens de 6000 partem do princípio de US $1 para cada 1000 imagens extraídas, custos de US $6.00 para este passo.

2. Para OCR de 6.000 imagens em inglês, a habilidade de cognitiva OCR utiliza o melhor algoritmo (DescribeText). Supondo que um custo de US $2.50 por 1000 imagens a ser analisados, pagaria us $15.00 para este passo.

3. Para extração de entidades, teria um total de 3 registos de texto por página. Cada registo é de 1000 carateres. Três registos de texto por página * 6.000 páginas = 18,000 registos de texto. Partindo do princípio de US $2,00 por 1000 registos de texto, este passo iria custar us $36.00.

Juntando as peças, pagaria aproximadamente us $57.00 ingerir 1000 documentos PDF dessa natureza com o conjunto de capacidades descrito. 

## <a name="next-steps"></a>Passos Seguintes
+ [O Azure Search a página de preços](https://azure.microsoft.com/pricing/details/search/)
+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
+ [Criar conjunto de capacidades (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Como mapear campos plena](cognitive-search-output-field-mapping.md)
