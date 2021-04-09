---
title: Anexar serviços cognitivos a um skillset
titleSuffix: Azure Cognitive Search
description: Saiba como anexar uma subscrição de Serviços Cognitivos tudo-em-um a um oleoduto de enriquecimento de IA em Azure Cognitive Search.
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 77735166fafe9d39dff483baa89a4b31db31275d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100577925"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Anexar um recurso de Serviços Cognitivos a um skillset em Azure Cognitive Search

Ao configurar um oleoduto de [enriquecimento de IA](cognitive-search-concept-intro.md) na Pesquisa Cognitiva Azure, pode enriquecer um número limitado de documentos gratuitamente. Para cargas de trabalho maiores e mais frequentes, deve anexar um recurso de Serviços Cognitivos "tudo-em-um". Uma subscrição "tudo-em-um" refere "Serviços Cognitivos" como a oferta, em vez de serviços individuais, com acesso concedido através de uma única chave API.

Um recurso "tudo-em-um" dos Serviços Cognitivos impulsiona as [habilidades predefinidas](cognitive-search-predefined-skills.md) que pode incluir num skillset:

+ [Visão computacional](https://azure.microsoft.com/services/cognitive-services/computer-vision/) para análise de imagem e reconhecimento de caracteres óticos (OCR)
+ [Análise de texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) para deteção de linguagens, reconhecimento de entidades, análise de sentimentos e extração de frases-chave
+ [Tradução de Texto](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)

Uma chave de Serviços Cognitivos "tudo-em-um" é opcional numa definição de skillset. Quando as transações diárias são inferiores a 20 por dia, o custo é absorvido. No entanto, quando as transações excedem esse número, é necessária uma chave de recursos válida para que o processamento continue.

Qualquer chave de recursos "tudo-em-um" é válida. Internamente, um serviço de pesquisa usará o recurso que está co-localizado na mesma região física, mesmo que a chave "tudo-em-um" seja para um recurso numa região diferente. A página [de disponibilidade do produto](https://azure.microsoft.com/global-infrastructure/services/?products=search) mostra disponibilidade regional lado a lado.

> [!NOTE]
> Se omitir habilidades predefinidas num skillset, então os Serviços Cognitivos não são acedidos, e você não será cobrado, mesmo que o skillset especifique uma chave.

## <a name="how-billing-works"></a>Como funciona a faturação

+ A Azure Cognitive Search utiliza a chave de recursos dos Serviços Cognitivos que fornece numa habilidade para faturar para o enriquecimento de imagem e texto. A execução de competências de faturação está no preço de pagamento dos [Serviços Cognitivos.](https://azure.microsoft.com/pricing/details/cognitive-services/)

+ A extração de imagem é uma operação de Pesquisa Cognitiva Azure que ocorre quando os documentos são rachados antes do enriquecimento. A extração de imagem é faturada. Para obter preços de extração de imagens, consulte a [página de preços de Pesquisa Cognitiva Azure](https://azure.microsoft.com/pricing/details/search/).

+ A extração de texto também ocorre durante a frase de rachadura do documento. Não é exequível.

+ As competências que não chamam serviços cognitivos, incluindo habilidades condicionais, shaper, fundindo de texto e de divisão de texto, não são faturantes.

## <a name="same-region-requirement"></a>Requisitos da mesma região

Tanto a Pesquisa Cognitiva como os Serviços Cognitivos devem existir na mesma região física, conforme indicado na página de disponibilidade do [produto.](https://azure.microsoft.com/global-infrastructure/services/?products=search) A maioria das regiões que oferecem Pesquisa Cognitiva também oferecem Serviços Cognitivos.

Se tentar o enriquecimento de IA na região que não tem ambos os serviços, verá esta mensagem: "Desde que a chave não seja uma chave de tipo CognitiveServices válida para a região do seu serviço de pesquisa."

> [!NOTE]
> Algumas competências incorporadas baseiam-se em Serviços Cognitivos não regionais (por exemplo, a Habilidade de [Tradução de Texto).](cognitive-search-skill-text-translation.md) A utilização de uma habilidade não regional significa que o seu pedido pode ser servido numa região que não seja a região de Pesquisa Cognitiva de Azure. Para obter mais informações sobre serviços não regionais, consulte o [produto Serviços Cognitivos por página.](https://aka.ms/allinoneregioninfo)

## <a name="use-free-resources"></a>Utilizar recursos gratuitos

Você pode usar uma opção de processamento limitada e gratuita para completar os exercícios de enriquecimento de IA e quickstart.

Os recursos gratuitos (enriquecimentos limitados) são limitados a 20 documentos por dia, por indexante. Pode [redefinir o indexante](search-howto-run-reset-indexers.md) para redefinir o contador.

Se estiver a utilizar o assistente **de dados de importação** para enriquecimento de IA, encontrará as opções "Anexar serviços cognitivos" na página de **enriquecimento de AI (Opcional).**

![Secção de Serviços Cognitivos Anexados Expandido](./media/cognitive-search-attach-cognitive-services/attach1.png "Secção de Serviços Cognitivos Anexados Expandido")

## <a name="use-billable-resources"></a>Utilizar recursos faturais

Para cargas de trabalho que criam mais de 20 enriquecimentos por dia, certifique-se de anexar um recurso de Serviços Cognitivos faturados. Recomendamos que anexe sempre um recurso de Serviços Cognitivos faturados, mesmo que nunca pretenda ligar para as APIs dos Serviços Cognitivos. A fixação de um recurso substitui o limite diário.

É cobrado apenas por habilidades que chamam de APIs de Serviços Cognitivos. Você não é cobrado para [habilidades personalizadas](cognitive-search-create-custom-skill-example.md), ou habilidades como [fusão de texto,](cognitive-search-skill-textmerger.md) [splitter de texto](cognitive-search-skill-textsplit.md), e [shaper](cognitive-search-skill-shaper.md), que não são baseados em API.

Se estiver a utilizar o assistente **de dados De importação,** pode configurar um recurso faturado a partir da página de **enriquecimento de AI (Opcional).**

1. Expandir **Os Serviços Cognitivos e,** em seguida, selecionar **Criar novos recursos de Serviços Cognitivos**. Abre-se um novo separador para que possa criar o recurso:

   ![Criar um recurso dos Serviços Cognitivos](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Criar um recurso dos Serviços Cognitivos")

1. Na lista **Localização,** selecione a mesma região que tem o seu serviço de pesquisa.

1. Na lista **de preços,** selecione **S0** para obter a coleção all-in-one de funcionalidades de Serviços Cognitivos, incluindo as funcionalidades de Visão e Linguagem que apoiam as habilidades incorporadas fornecidas pela Azure Cognitive Search.

   Para o nível S0, você pode encontrar tarifas para cargas de trabalho específicas na [página de preços dos Serviços Cognitivos.](https://azure.microsoft.com/pricing/details/cognitive-services/)
  
   + Na lista **'Oferecer' Selecione,** certifique-se de que **os Serviços Cognitivos** estão selecionados.
   + Nas funcionalidades **linguísticas,** as taxas para **Text Analytics Standard** aplicam-se à indexação de IA.
   + No âmbito das funcionalidades **Visão** Visão, aplicam-se as taxas para **o Computer Vision S1.**

1. Selecione **Criar** para a provisionar o novo recurso serviços cognitivos.

1. Volte ao separador anterior. Selecione **Refresh** para mostrar o recurso Serviços Cognitivos e, em seguida, selecione o recurso:

   ![Selecione o recurso Serviços Cognitivos](./media/cognitive-search-attach-cognitive-services/attach2.png "Selecione o recurso Serviços Cognitivos")

1. Expanda a secção **de competências cognitivas Adicionar** para selecionar as habilidades cognitivas específicas que pretende executar nos seus dados. Complete o resto do feiticeiro.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Anexar um skillset existente a um recurso de Serviços Cognitivos

Se tiver uma habilidade existente, pode anexá-la a um recurso novo ou diferente dos Serviços Cognitivos.

1. Na página geral do serviço de pesquisa, selecione **Skillsets**:

   ![Separador skillsets](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Separador skillsets")

1. Selecione o nome do skillset e, em seguida, selecione um recurso existente ou crie um novo. Selecione **OK** para confirmar as suas alterações.

   ![Lista de recursos skillset](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Lista de recursos skillset")

   Lembre-se que a opção **Free (Enriquecimentos Limitados)** limita-o a 20 documentos por dia e que pode utilizar **Criar novos recursos de Serviços Cognitivos** para forrê um novo recurso faturado. Se criar um novo recurso, selecione **Refresh** para refrescar a lista de recursos dos Serviços Cognitivos e, em seguida, selecione o recurso.

## <a name="attach-cognitive-services-programmatically"></a>Anexar Serviços Cognitivos programáticamente

Quando estiveres a definir o skillset programáticamente, adicione uma `cognitiveServices` secção ao skillset. Nessa secção, inclua a chave do recurso Serviços Cognitivos que pretende associar ao skillset. Lembre-se que o recurso deve estar na mesma região que o seu recurso de Pesquisa Cognitiva Azure. Inclua `@odata.type` também, e defini-lo para `#Microsoft.Azure.Search.CognitiveServicesByKey` .

O exemplo a seguir mostra este padrão. Note a `cognitiveServices` secção no final da definição.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
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

## <a name="example-estimate-costs"></a>Exemplo: Estimativa de custos

Para estimar os custos associados à indexação da pesquisa cognitiva, comece com uma ideia de como é um documento médio para que possa executar alguns números. Por exemplo, pode aproximar-se:

+ 1.000 PDFs.
+ Seis páginas cada.
+ Uma imagem por página (6.000 imagens).
+ 3.000 caracteres por página.

Assuma um pipeline que consiste na quebra de documentos de cada PDF, extração de imagem e texto, reconhecimento de caracteres óticos (OCR) de imagens e reconhecimento de entidades.

Os preços apresentados neste artigo são hipotéticos. São usados para ilustrar o processo de estimativa. Os seus custos podem ser mais baixos. Para os preços reais das transações, consulte os [preços dos Serviços Cognitivos.](https://azure.microsoft.com/pricing/details/cognitive-services)

1. Para a quebra de documentos com conteúdo de texto e imagem, a extração de texto é atualmente gratuita. Para 6.000 imagens, assuma $1 por cada 1.000 imagens extraídas. É um custo de $6,00 por este passo.

2. Para OCR de 6.000 imagens em inglês, a habilidade cognitiva OCR usa o melhor algoritmo (DescreverText). Assumindo um custo de $2.50 por 1.000 imagens a serem analisadas, pagaria $15,00 por este passo.

3. Para a extração de entidades, teria um total de três registos de texto por página. Cada disco tem 1.000 caracteres. Três registos de texto por página multiplicados por 6.000 páginas equivalem a 18.000 registos de texto. Assumindo $2,00 por 1.000 registos de texto, este passo custaria $36,00.

Juntando tudo, pagaria cerca de $57,00 para ingerir 1.000 documentos PDF deste tipo com o skillset descrito.

## <a name="next-steps"></a>Passos seguintes

+ [Página de preços de pesquisa cognitiva Azure](https://azure.microsoft.com/pricing/details/search/)
+ [Como definir um skillset](cognitive-search-defining-skillset.md)
+ [Criar Skillset (REST)](/rest/api/searchservice/create-skillset)
+ [Como mapear campos enriquecidos](cognitive-search-output-field-mapping.md)