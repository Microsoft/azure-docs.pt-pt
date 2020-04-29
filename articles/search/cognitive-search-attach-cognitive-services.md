---
title: Anexar os Serviços Cognitivos a um skillset
titleSuffix: Azure Cognitive Search
description: Instruções para anexar uma subscrição all-in-one dos Serviços Cognitivos a um oleoduto de enriquecimento de IA em Pesquisa Cognitiva Azure.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 254c912114e3f1c7a495f389bc6a6416cbde7e11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77472456"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Anexar um recurso dos Serviços Cognitivos a um skillset em Pesquisa Cognitiva Azure 

Ao configurar um pipeline de enriquecimento em Azure Cognitive Search, pode enriquecer gratuitamente um número limitado de documentos. Para cargas de trabalho maiores e mais frequentes, deve anexar um recurso de Serviços Cognitivos faturados.

Neste artigo, você aprenderá a anexar um recurso atribuindo uma chave a um skillset que define um pipeline de enriquecimento.

## <a name="resources-used-during-enrichment"></a>Recursos utilizados durante o enriquecimento

A Pesquisa Cognitiva Azure tem uma dependência de Serviços Cognitivos, incluindo [Visão Computacional](https://azure.microsoft.com/services/cognitive-services/computer-vision/) para análise de imagem e reconhecimento ótico de caracteres (OCR), [Análise de Texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) para processamento de linguagem natural, e outros enriquecimentos como tradução de [texto.](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) No contexto do enriquecimento na Pesquisa Cognitiva Azure, estes algoritmos de IA são embrulhados dentro de uma *habilidade*, colocada num *skillset*, e referenciadas por um *indexante* durante a indexação.

## <a name="how-billing-works"></a>Como funciona a faturação

+ A Azure Cognitive Search utiliza a chave de recursos dos Serviços Cognitivos que fornece num conjunto de competências para faturar para enriquecimento de imagem e texto. A execução de competências faturadas está no preço de pagamento dos [Serviços Cognitivos.](https://azure.microsoft.com/pricing/details/cognitive-services/)

+ A extração de imagem é uma operação de Pesquisa Cognitiva Azure que ocorre quando os documentos são rachadoantes antes do enriquecimento. A extração de imagem é faturada. Para obter preços de extração de imagem, consulte a página de preços da [Pesquisa Cognitiva Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

+ A extração de texto também ocorre durante a frase de rachadura do documento. Não é cobrado.

+ As competências que não chamam Serviços Cognitivos, incluindo competências condicionais, shaper, text merge e text split, não são faturadas.

## <a name="same-region-requirement"></a>Requisitoda na mesma região

Exigimos que existam serviços de pesquisa cognitiva azure e serviços cognitivos azure dentro da mesma região. Caso contrário, receberá esta mensagem no momento da execução:`"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

Não há como mover um serviço através das regiões. Se tiver este erro, deve criar um novo recurso dos Serviços Cognitivos na mesma região que a Pesquisa Cognitiva Azure.

> [!NOTE]
> Algumas competências incorporadas baseiam-se em Serviços Cognitivos não regionais (por exemplo, a [Habilidade de Tradução](cognitive-search-skill-text-translation.md)de Texto). Usar uma habilidade não-regional significa que o seu pedido pode ser servido numa região diferente da região de Pesquisa Cognitiva Azure. Para obter mais informações não regionais, consulte o [produto Serviços Cognitivos por página região.](https://aka.ms/allinoneregioninfo)

## <a name="use-free-resources"></a>Utilizar recursos gratuitos

Você pode usar uma opção de processamento limitada e livre para completar o tutorial de enriquecimento de IA e exercícios de arranque rápido.

Os recursos gratuitos (enriquecimentos limitados) são limitados a 20 documentos por dia, por indexante. Pode eliminar e recriar o indexante para repor o contador.

1. Abra o assistente de dados da Importação:

   ![Abra o assistente de dados da Importação](media/search-get-started-portal/import-data-cmd.png "Abra o assistente de dados da Importação")

1. Escolha uma fonte de dados e continue a adicionar enriquecimento de **IA (Opcional)**. Para uma passagem passo a passo deste assistente, consulte [Criar um índice no portal Azure](search-get-started-portal.md).

1. Expandir **anexar serviços cognitivos** e, em seguida, selecionar **Free (enriquecimentos limitados)**:

   ![Secção de Serviços Cognitivos anexados expandidos](./media/cognitive-search-attach-cognitive-services/attach1.png "Secção de Serviços Cognitivos anexados expandidos")

1. Pode agora continuar os próximos passos, incluindo **adicionar habilidades cognitivas.**

## <a name="use-billable-resources"></a>Utilizar recursos faturados

Para cargas de trabalho que criam mais de 20 enriquecimentos por dia, certifique-se de anexar um recurso de Serviços Cognitivos faturados. Recomendamos que anexe sempre um recurso de Serviços Cognitivos faturados, mesmo que nunca pretenda ligar para as APIs dos Serviços Cognitivos. A fixação de um recurso sobrepõe-se ao limite diário.

Só é cobrado por habilidades que chamam de APIs dos Serviços Cognitivos. Não é cobrado por [habilidades personalizadas](cognitive-search-create-custom-skill-example.md), ou habilidades como fusão de [texto,](cognitive-search-skill-textmerger.md) [splitter de texto](cognitive-search-skill-textsplit.md)e [shaper](cognitive-search-skill-shaper.md), que não são baseados em API.

1. Abra o assistente de dados da Importação, escolha uma fonte de dados e continue a adicionar enriquecimento de **IA (Opcional)**.

1. Expandir **anexar serviços cognitivos** e, em seguida, selecionar **Criar um novo recurso de Serviços Cognitivos**. Abre-se um novo separador para que possa criar o recurso:

   ![Criar um recurso dos Serviços Cognitivos](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Criar um recurso dos Serviços Cognitivos")

1. Na lista **de Localização,** selecione a região onde está localizado o seu serviço de Pesquisa Cognitiva Azure. Certifique-se de utilizar esta região por razões de desempenho. A utilização desta região também anula as tarifas de largura de banda de saída em todas as regiões.

1. Na lista de níveis de **preços,** selecione **S0** para obter a coleção tudo-em-um de recursos de Serviços Cognitivos, incluindo as funcionalidades de Visão e Linguagem que apoiam as competências incorporadas fornecidas pela Azure Cognitive Search.

   Para o nível S0, você pode encontrar tarifas para cargas de trabalho específicas na página de preços dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + Na lista **de Oferta Selecionada,** certifique-se de que **os Serviços Cognitivos são selecionados.**
   + De acordo com as características **linguísticas,** as tarifas para **O Padrão de Análise** de Texto aplicam-se à indexação de IA.
   + Sob as funcionalidades **vision,** aplicam-se as tarifas para **Computer Vision S1.**

1. Selecione **Criar** para fornecer o novo recurso dos Serviços Cognitivos.

1. Volte ao separador anterior, que contém o assistente de dados da Importação. Selecione **Refresh** para mostrar o recurso dos Serviços Cognitivos e, em seguida, selecione o recurso:

   ![Selecione o recurso Serviços Cognitivos](./media/cognitive-search-attach-cognitive-services/attach2.png "Selecione o recurso Serviços Cognitivos")

1. Expanda a secção **de habilidades cognitivas Adicionar** para selecionar as habilidades cognitivas específicas que pretende executar nos seus dados. Complete o resto do assistente.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Anexar uma habilidade existente a um recurso dos Serviços Cognitivos

Se tiver uma habilidade existente, pode anexá-la a um novo ou diferente recurso dos Serviços Cognitivos.

1. Na página de visão geral do **Serviço,** selecione **Skillsets:**

   ![Separador skillsets](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Separador skillsets")

1. Selecione o nome do skillset e, em seguida, selecione um recurso existente ou crie um novo. Selecione **OK** para confirmar as suas alterações.

   ![Lista de recursos de skillset](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Lista de recursos de skillset")

   Lembre-se que a opção **Free (enriquecimentos limitados)** limita-o a 20 documentos por dia e que pode utilizar **a Create new Cognitive Services resource** para fornecer um novo recurso faturado. Se criar um novo recurso, selecione **Refresh** para refrescar a lista de recursos dos Serviços Cognitivos e, em seguida, selecione o recurso.

## <a name="attach-cognitive-services-programmatically"></a>Anexar serviços cognitivos programáticamente

Quando estiver a definir a habilidade programáticamente, adicione uma `cognitiveServices` secção à habilidade. Nessa secção, inclua a chave do recurso dos Serviços Cognitivos que pretende associar com o skillset. Lembre-se que o recurso deve estar na mesma região que o seu recurso Azure Cognitive Search. Inclua `@odata.type`também, e `#Microsoft.Azure.Search.CognitiveServicesByKey`coloque-o para .

O exemplo que se segue mostra este padrão. Repare `cognitiveServices` na secção no final da definição.

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

## <a name="example-estimate-costs"></a>Exemplo: Custos de estimativa

Para estimar os custos associados à indexação da pesquisa cognitiva, comece com uma ideia de como é um documento médio para que possa executar alguns números. Por exemplo, pode aproximar-se:

+ 1.000 PDFs.
+ Seis páginas cada.
+ Uma imagem por página (6.000 imagens).
+ 3.000 caracteres por página.

Assuma um pipeline que consiste em quebra de documentos de cada PDF, extração de imagem e texto, reconhecimento ótico de caracteres (OCR) de imagens e reconhecimento de entidades das organizações.

Os preços apresentados neste artigo são hipotéticos. São usados para ilustrar o processo de estimativa. Os seus custos podem ser mais baixos. Para os preços reais das transações, consulte os preços dos [Serviços Cognitivos.](https://azure.microsoft.com/pricing/details/cognitive-services)

1. Para a rutura de documentos com conteúdo de texto e imagem, a extração de texto é atualmente gratuita. Por 6.000 imagens, assuma $1 por cada 1.000 imagens extraídas. É um custo de $6,00 por este passo.

2. Para OCR de 6.000 imagens em inglês, a habilidade cognitiva oCR usa o melhor algoritmo (Descrever Texto). Assumindo um custo de $2,50 por 1.000 imagens a serem analisadas, pagaria $15,00 por este passo.

3. Para extração de entidades, teria um total de três registos de texto por página. Cada disco é de 1.000 caracteres. Três registos de texto por página multiplicados por 6.000 páginas equivalem a 18.000 registos de texto. Assumindo $2,00 por 1.000 registos de texto, este passo custaria $36,00.

Juntando tudo, pagaria cerca de $57,00 para ingerir 1.000 documentos PDF deste tipo com a habilidade descrita.

## <a name="next-steps"></a>Passos seguintes
+ [Página de preços de pesquisa cognitiva azure](https://azure.microsoft.com/pricing/details/search/)
+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
+ [Criar Skillset (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Como mapear campos enriquecidos](cognitive-search-output-field-mapping.md)
