---
title: Anexar um recurso de serviços cognitivos com um conjunto de capacidades - Azure Search
description: Instruções para anexar uma subscrição de tudo-em-um dos serviços cognitivos para um pipeline de enriquecimento cognitivos no Azure Search.
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 44f16b3334b991e071fa85ca4cffbc0837f0a6ec
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244437"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Anexar um recurso de serviços cognitivos com um conjunto de capacidades no Azure Search 

Unidade de algoritmos de IA da [pipelines de indexação cognitivos](cognitive-search-concept-intro.md) utilizado para a melhoria de documento no Azure Search. Esses algoritmos baseiam-se nos recursos de serviços cognitivos do Azure, incluindo [de imagem digitalizada](https://azure.microsoft.com/services/cognitive-services/computer-vision/) para análise de imagem e reconhecimento ótico de carateres (OCR) e [análise de texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) para reconhecimento de entidades, Extração de expressões-chave e outros possível. Como utilizado pelo Azure Search para fins de melhoria de documento, os algoritmos são encapsulados dentro de um *habilidades*, colocá-la num *conjunto de capacidades*e referenciada por um *indexador* durante indexação.

Pode enriquecer a um número limitado de documentos gratuitamente. Em alternativa, pode anexar um recurso dos serviços cognitivos cobrar para um *conjunto de capacidades* para cargas de trabalho maiores e mais freqüentes. Neste artigo, aprenderá como anexar um recurso dos serviços cognitivos cobrar para enriquecer os documentos durante a Azure Search [indexação](search-what-is-an-index.md).

> [!NOTE]
> Eventos a cobrar incluem as chamadas para extração de APIs serviços cognitivos e a imagem como parte da fase de aberturas de documentos no Azure Search. Não há nenhum custo para extração de texto de documentos ou de habilidades não chamar os serviços cognitivos.
>
> Execução de habilidades faturáveis é no [dos serviços cognitivos pay as you go preço](https://azure.microsoft.com/pricing/details/cognitive-services/). Para preços de extração de imagem, veja a [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="same-region-requirement"></a>Requisito da mesma região

É necessário que a Azure Search e os serviços cognitivos do Azure existem na mesma região. Caso contrário, obterá esta mensagem no tempo de execução: `"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

Não é possível mover um serviço em várias regiões. Se obtiver este erro, deve criar um novo recurso de serviços cognitivos na mesma região que o Azure Search.

## <a name="use-free-resources"></a>Utilizar recursos gratuitos

Pode utilizar uma opção de processamento limitado e gratuito para concluir os exercícios de tutorial e guia de introdução de pesquisa cognitiva.

Recursos gratuitos (limitado possível) estão limitados a 20 documentos por dia, por subscrição.

1. Abra o Assistente de importação de dados:

   ![Abra o Assistente de importação de dados](media/search-get-started-portal/import-data-cmd2.png "abrir o Assistente de importação de dados")

1. Escolha uma origem de dados e continuar a **adicionar pesquisa cognitiva (opcional)** . Para obter instruções passo a passo deste assistente, consulte [importação, índice e a consulta utilizando ferramentas do portal](search-get-started-portal.md).

1. Expanda **anexar serviços cognitivos** e, em seguida, selecione **gratuito (limitado possível)** :

   ![Expandir a secção de anexar serviços cognitivos](./media/cognitive-search-attach-cognitive-services/attach1.png "secção expandido anexar serviços cognitivos")

1. Continuar para a próxima etapa **possível adicionar**. Para obter uma descrição das habilidades disponíveis no portal, consulte [passo 2: Adicionar capacidades cognitivas](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) no guia de introdução de pesquisa cognitiva.

## <a name="use-billable-resources"></a>Utilizar recursos faturáveis

Para cargas de trabalho que criar mais de 20 possível por dia, certifique-se anexar um recurso dos serviços cognitivos faturável. Recomendamos que anexa sempre um recurso dos serviços cognitivos faturável, mesmo que não pretenda chamar APIs serviços cognitivos. Anexar um recurso substitui o limite diário.

É-lhe cobrado apenas para as competências que chamam as APIs serviços cognitivos. Não está a ser cobrado para [competências personalizadas](cognitive-search-create-custom-skill-example.md), ou habilidades, como [fusão de texto](cognitive-search-skill-textmerger.md), [divisor de texto](cognitive-search-skill-textsplit.md), e [Modelador](cognitive-search-skill-shaper.md), que não são baseados em API.

1. Abra o Assistente de importação de dados, escolha uma origem de dados e continuar a **adicionar pesquisa cognitiva (opcional)** .

1. Expanda **anexar serviços cognitivos** e, em seguida, selecione **criar novo recurso de serviços cognitivos**. É aberto um separador novo para que possa criar o recurso:

   ![Criar um recurso de serviços cognitivos](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "criar um recurso de serviços cognitivos")

1. Na **localização** , selecione a região onde está localizado o serviço Azure Search. Certifique-se utilizar esta região por motivos de desempenho. Também nesta região a utilizar voids custos de largura de banda de saída em várias regiões.

1. Na **escalão de preço** lista, selecione **S0** para obter a coleção de tudo-em-um dos recursos de serviços cognitivos, incluindo os recursos de visão e a linguagem que apoiam as competências predefinidas utilizadas pelo Azure Search.

   Para escalão S0, pode encontrar as taxas de para cargas de trabalho específicas sobre o [página de preços dos serviços cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + Na **selecione oferecem** lista, certifique-se **dos serviços cognitivos** está selecionada.
   + Sob **linguagem** apresenta as taxas de **análise de texto padrão** aplicam-se a indexação de IA.
   + Sob **visão** apresenta as taxas de **S1 de visão do computador** aplicam-se.

1. Selecione **criar** para aprovisionar o novo recurso de serviços cognitivos.

1. Regresse ao separador anterior, que contém o Assistente de importação de dados. Selecione **atualizar** para mostrar o recurso dos serviços cognitivos e, em seguida, selecione o recurso:

   ![Selecione o recurso de serviços cognitivos](./media/cognitive-search-attach-cognitive-services/attach2.png "selecione o recurso de serviços cognitivos")

1. Expanda a **possível adicionar** secção para selecionar as capacidades cognitivas específicas que pretende executar nos seus dados. Conclua o resto do assistente. Para obter uma descrição das habilidades disponíveis no portal, consulte [passo 2: Adicionar capacidades cognitivas](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) no guia de introdução de pesquisa cognitiva.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Anexar um conjunto de competências existente a um recurso dos serviços cognitivos

Se tiver um conjunto de capacidades existente, pode anexá-lo a um recurso de serviços cognitivos novo ou diferente.

1. Sobre o **descrição geral do serviço** página, selecione **conjuntos de competências**:

   ![Separador conjuntos de competências](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "separador conjuntos de competências")

1. Selecione o nome do conjunto de capacidades e, em seguida, selecione um recurso existente ou crie um novo. Selecione **OK** para confirmar as alterações.

   ![Lista de recursos do conjunto de capacidades](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "lista de recursos do conjunto de capacidades")

   Lembre-se de que o **gratuito (possível limitado)** opção limita a 20 documentos diariamente e que pode utilizar **criar novo recurso de serviços cognitivos** para aprovisionar um novo recurso faturável. Se criar um novo recurso, selecione **atualizar** para atualizar a lista de recursos dos serviços cognitivos e, em seguida, selecione o recurso.

## <a name="attach-cognitive-services-programmatically"></a>Anexar serviços cognitivos através de programação

Quando está a definir o conjunto de capacidades programaticamente, adicione um `cognitiveServices` secção para o conjunto de capacidades. Nesta secção, inclua a chave do recurso dos serviços cognitivos que pretende associar o conjunto de capacidades. Lembre-se de que o recurso tem de ser na mesma região que o recurso do Azure Search. Também incluem `@odata.type`e defina-o como `#Microsoft.Azure.Search.CognitiveServicesByKey`.

O exemplo seguinte mostra este padrão. Tenha em atenção o `cognitiveServices` secção no final da definição.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
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

Para estimar os custos associados a indexação de pesquisa cognitiva, comece com uma ideia de um documento média semelhante ao seguinte para que possa executar alguns números. Por exemplo, pode fazer uma aproximação:

+ 1000 PDFs.
+ Seis páginas cada.
+ Uma imagem por página (6.000 imagens).
+ 3.000 carateres por página.

Suponha que um pipeline que consiste de aberturas de documentos de cada extração PDF, imagens e texto, reconhecimento ótico de carateres (OCR) de imagens e reconhecimento de entidades de organizações.

Os preços apresentados neste artigo são hipotéticos. Eles são usados para ilustrar o processo de estimativa. Os seus custos poderiam ser mais baixos. Para obter os preços reais de transações, consulte [preços dos serviços cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Para com conteúdo de texto e imagem de abertura do documento, extração de texto é atualmente gratuita. Para 6.000 imagens, partem do princípio de US $1 para cada 1000 imagens extraídas. Que é um custo de US $6.00 para este passo.

2. Para OCR de 6.000 imagens em inglês, a habilidade de cognitiva OCR utiliza o melhor algoritmo (DescribeText). Supondo que um custo de US $2.50 por 1000 imagens a ser analisados, pagaria us $15.00 para este passo.

3. Para extração de entidades, teria um total de registos de texto três por página. Cada registo é de 1000 carateres. Três registos de texto por página multiplicado por 6.000 páginas é igual a 18,000 registos de texto. Partindo do princípio de US $2,00 por 1000 registos de texto, este passo iria custar us $36.00.

Juntando as peças, pagaria aproximadamente us $57.00 ingerir 1000 documentos PDF deste tipo com o conjunto de capacidades descrito.

## <a name="next-steps"></a>Passos Seguintes
+ [O Azure Search a página de preços](https://azure.microsoft.com/pricing/details/search/)
+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
+ [Criar conjunto de capacidades (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Como mapear campos plena](cognitive-search-output-field-mapping.md)
