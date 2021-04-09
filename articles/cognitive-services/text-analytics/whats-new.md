---
title: Novidades na API de Análise de Texto
titleSuffix: Text Analytics - Azure Cognitive Services
description: Este artigo fornece-lhe informações sobre novos lançamentos e funcionalidades para o Azure Cognitive Services Text Analytics.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/18/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: a2b001d34d265c8e7246b03875c32168f2c5c962
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104598903"
---
# <a name="whats-new-in-the-text-analytics-api"></a>O que há de novo na API de Análise de Texto?

A API text Analytics é atualizada numa base contínua. Para se manter atualizado com os recentes desenvolvimentos, este artigo fornece-lhe informações sobre novos lançamentos e funcionalidades.

## <a name="march-2021"></a>março de 2021

### <a name="general-api-updates"></a>Atualizações gerais da API
* Lançamento do novo API v3.1-preview.4 que inclui 
   * Alterações no órgão de resposta da Opinion Mining JSON: 
      * `aspects` é agora `targets` e `opinions` é agora `assessments` . 
   * Alterações no corpo de resposta JSON da API web acolhida de Text Analytics para a saúde: 
      * O `isNegated` nome booleano de um objeto de entidade detetado para Negação é depreciado e substituído por Deteção de Afirmação.
      * Um novo imóvel chamado `role` faz agora parte da relação extraída entre um atributo e uma entidade, bem como a relação entre entidades.  Isto adiciona especificidade ao tipo de relação detetado.
   * A ligação de entidades está agora disponível como uma tarefa assíncronea no `/analyze` ponto final.
   * Um novo `pii-categories` parâmetro está agora disponível no ponto `/pii` final.
      * Este parâmetro permite especificar entidades PII selecionadas, bem como as que não são suportadas por padrão para o idioma de entrada.
* Bibliotecas de clientes atualizadas, que incluem análise assíncronea, e Análise de Texto para operações de saúde. Pode encontrar exemplos no GitHub:

    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
    * [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)
    * [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/javascript)
    
> [!div class="nextstepaction"]
> [Saiba mais sobre text Analytics API v3.1-Preview.4](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-4/operations/Languages)

### <a name="text-analytics-for-health-updates"></a>Análise de texto para atualizações de saúde

* Uma nova versão modelo `2021-03-01` para o ponto final e recipiente no local que `/health` fornece
    * Um rebatizador do `Gene` tipo de entidade para `GeneOrProtein` .
    * Um novo `Date` tipo de entidade.
    * Deteção de afirmação que substitui a deteção de negação (apenas disponível em API v3.1-preview.4).
    * Um novo imóvel preferencial `name` para entidades ligadas que é normalizado a partir de várias sistemas de onlineologia e codificação (disponível apenas em API v3.1-preview.4). 
* Uma nova imagem de recipiente com etiqueta `3.0.015370001-onprem-amd64` e a nova versão modelo foi lançada para o `2021-03-01` repositório de pré-visualização do recipiente. 
* O Text Analytics para a imagem do recipiente de saúde vai mudar-se para um novo repositório no próximo mês.  Por favor, consulte uma comunicação por e-mail sobre a localização da sua nova casa.
> [!div class="nextstepaction"]
> [Saiba mais sobre Text Analytics para saúde](how-tos/text-analytics-for-health.md)
>

### <a name="text-analytics-resource-portal-update"></a>Atualização do portal de recursos de Text Analytics
* **Os Registos de Texto processados** estão agora disponíveis como métrica na secção **de Monitorização** do seu recurso Text Analytics no portal Azure.  

## <a name="february-2021"></a>Fevereiro de 2021

* A `2021-01-15` versão modelo para o ponto final pii em Reconhecimento de Entidade [Nomeada](how-tos/text-analytics-how-to-entity-linking.md) v3.1-preview.x, que fornece 
  * Apoio alargado a 9 novas línguas
  * Melhoria da qualidade da IA das categorias de entidades nomeadas para línguas apoiadas.
* Os níveis de preços S0-S4 estão a ser retirados a 8 de março de 2021. Se tiver um recurso de Análise de Texto existente utilizando o nível de preços S0-S4, deverá atualizá-lo para utilizar o [nível de preços](how-tos/text-analytics-how-to-call-api.md#change-your-pricing-tier)Standard (S).
* O [recipiente de deteção de idiomas](how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment) está agora geralmente disponível.
* v2.1 da API está a ser reformado. 

## <a name="january-2021"></a>Janeiro de 2021

* A `2021-01-15` versão modelo para Reconhecimento de Entidade [Nomeada](how-tos/text-analytics-how-to-entity-linking.md) v3.x, que fornece 
  * Apoio linguístico alargado para [várias categorias de entidades gerais.](named-entity-types.md) 
  * Melhor qualidade de IA das categorias de entidades gerais para todas as línguas v3 suportadas. 

* A `2021-01-05` versão modelo para [deteção de idiomas,](how-tos/text-analytics-how-to-language-detection.md)que fornece [suporte linguístico](language-support.md?tabs=language-detection)adicional.

Estas versões de modelos estão atualmente indisponíveis na região leste dos EUA. 

> [!div class="nextstepaction"]
> [Saiba mais sobre o novo modelo NER](https://azure.microsoft.com/updates/text-analytics-ner-improved-ai-quality)

## <a name="december-2020"></a>Dezembro de 2020

* [Detalhes de preços atualizados](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) para a API text Analytics.

## <a name="november-2020"></a>Novembro de 2020

* Um [novo ponto final](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Analyze) com API API de Análise de Texto v3.1-preview.3 para a nova [API de análise](how-tos/text-analytics-how-to-call-api.md?tabs=analyze)assíncrono, que suporta o processamento de lotes para NER, PII e operações de extração de frases-chave.
* Um [novo ponto final](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) com API API API de Análise de Texto v3.1-preview.3 para o novo Text Analytics assíncrono para a [saúde](how-tos/text-analytics-for-health.md) hospedado API com suporte para processamento de lotes.
* Ambas as novas funcionalidades acima enumeradas só estão disponíveis nas seguintes regiões: `West US 2` `East US 2` , , e `Central US` `North Europe` `West Europe` regiões.
* O português (Brasil) `pt-BR` é agora apoiado na Análise de [Sentimento](how-tos/text-analytics-how-to-sentiment-analysis.md) v3.x, começando pela versão `2020-04-01` modelo. Acrescenta ao apoio existente `pt-PT` ao português.
* Bibliotecas de clientes atualizadas, que incluem análise assíncronea, e Análise de Texto para operações de saúde. Pode encontrar exemplos no GitHub:

    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
    * [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)
    * 
> [!div class="nextstepaction"]
> [Saiba mais sobre text Analytics API v3.1-Preview.3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Languages)

## <a name="october-2020"></a>Outubro de 2020

* Suporte hindi para Análise de Sentimento v3.x, começando com a versão modelo `2020-04-01` . 
* Versão modelo `2020-09-01` para o ponto final v3/idiomas, que adiciona melhorias aumentadas na deteção da linguagem e precisão.
* disponibilidade v3 na Índia Central e UAE Norte.

## <a name="september-2020"></a>Setembro de 2020

### <a name="general-api-updates"></a>Atualizações gerais da API

* Lançamento de um novo URL para a pré-visualização pública de Text Analytics v3.1 para suportar atualizações aos seguintes pontos finais de Reconhecimento de Entidades Nomeadas v3: 
    * `/pii` o ponto final inclui agora o novo `redactedText` imóvel na resposta JSON onde as entidades PII detetadas no texto de entrada são substituídas por um `*` para cada personagem dessas entidades.
    * `/linking` o ponto final inclui agora o `bingID` imóvel na resposta JSON para entidades ligadas.
* Os seguintes pontos finais de pré-visualização da API text analytics foram retirados em 4 de setembro de 2020:
    * v2.1 pré-visualização
    * v3.0 - pré-visualização
    * v3.0-pré-visualização.1
    
> [!div class="nextstepaction"]
> [Saiba mais sobre text Analytics API v3.1-Preview.2](quickstarts/client-libraries-rest-api.md)

### <a name="text-analytics-for-health-container-updates"></a>Análise de texto para atualizações de recipientes de saúde

As seguintes atualizações são específicas para a versão de setembro do Text Analytics apenas para recipientes de saúde.
* Uma nova imagem de recipiente com etiqueta `1.1.013530001-amd64-preview` com a nova versão modelo foi lançada para o `2020-09-03` repositório de pré-visualização do recipiente. 
* Esta versão modelo fornece melhorias no reconhecimento de entidades, deteção de abreviaturas e melhorias de latência.

> [!div class="nextstepaction"]
> [Saiba mais sobre Text Analytics para saúde](how-tos/text-analytics-for-health.md)

## <a name="august-2020"></a>Agosto de 2020

### <a name="general-api-updates"></a>Atualizações gerais da API

* Versão modelo `2020-07-01` para o v3 `/keyphrases` , e `/pii` `/languages` pontos finais, que adiciona:
    * Categorias adicionais [de entidades específicas](named-entity-types.md?tabs=personal) do governo e do país para reconhecimento de entidades nomeadas.
    * Apoio norueguês e turco na Análise de Sentimento v3.
* Um erro HTTP 400 será agora devolvido para pedidos de V3 API que excedam os limites de [dados publicados](concepts/data-limits.md). 
* Os pontos finais que devolvem uma compensação suportam agora o `stringIndexType` parâmetro opcional, que ajusta os `offset` valores devolvidos e `length` os valores para corresponder a um sistema de [índice de cadeia](concepts/text-offsets.md)suportado .

### <a name="text-analytics-for-health-container-updates"></a>Análise de texto para atualizações de recipientes de saúde

As seguintes atualizações são específicas para a libertação de agosto do Text Analytics apenas para recipientes de saúde.

* Nova versão modelo para Text Analytics para a saúde: `2020-07-24`
* Novo URL para envio de Texto Analytics para pedidos de saúde: `http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health` (Por favor, note que será necessária uma limpeza de cache do navegador para utilizar a app web de demonstração incluída nesta nova imagem de recipiente)

As seguintes propriedades na resposta JSON mudaram:

* `type` mudou de nome para `category` 
* `score` mudou de nome para `confidenceScore`
* As entidades no `category` domínio da saída JSON estão agora no caso Pascal. As seguintes entidades foram renomeadas:
    * `EXAMINATION_RELATION` foi renomeado para `RelationalOperator` .
    * `EXAMINATION_UNIT` foi renomeado para `MeasurementUnit` .
    * `EXAMINATION_VALUE` foi renomeado para `MeasurementValue` .
    * `ROUTE_OR_MODE` foi `MedicationRoute` renomeado.
    * A entidade relacional `ROUTE_OR_MODE_OF_MEDICATION` foi renomeada para `RouteOfMedication` .

Foram acrescentadas as seguintes entidades:

* NER
    * `AdministrativeEvent`
    * `CareEnvironment`
    * `HealthcareProfession`
    * `MedicationForm` 

* Extração de relação
    * `DirectionOfCondition`
    * `DirectionOfExamination`
    * `DirectionOfTreatment`

> [!div class="nextstepaction"]
> [Saiba mais sobre Text Analytics para recipiente de saúde](how-tos/text-analytics-for-health.md)

## <a name="july-2020"></a>Julho de 2020 

### <a name="text-analytics-for-health-container---public-gated-preview"></a>Texto Analytics para recipiente de saúde - Visualização fechada do público

O Texto Analytics para o recipiente de saúde está agora em pré-visualização fechada pública, que permite extrair informações de textos não estruturados em língua inglesa em documentos clínicos como: formulários de admissão de pacientes, notas do médico, trabalhos de investigação e resumos de descarga. Atualmente, não será cobrado para Text Analytics para uso de recipientes de saúde.

O recipiente oferece as seguintes características:

* Reconhecimento de Entidades Nomeadas
* Extração de relação
* Ligação de entidades
* Negação

## <a name="may-2020"></a>Maio de 2020

### <a name="text-analytics-api-v3-general-availability"></a>Disponibilidade Geral de API v3 API de Texto

A API v3 de Análise de Texto está disponível geralmente com as seguintes atualizações:

* Versão modelo `2020-04-01`
* Novos [limites de dados](concepts/data-limits.md) para cada recurso
* Suporte [linguístico](language-support.md) atualizado para [análise de sentimento (SA) v3](how-tos/text-analytics-how-to-sentiment-analysis.md)
* Ponto final separado para ligação de entidades 
* Nova categoria de entidade "Address" em [Reconhecimento de Entidades Nomeadas (NER) v3](how-tos/text-analytics-how-to-entity-linking.md).
* Novas subcategorias em NER v3:
   * Localização - Geográfica
   * Localização - Estrutural
   * Organização - Bolsa de Valores
   * Organização - Médico
   * Organização - Esportes
   * Evento - Cultural
   * Evento - Natural
   * Evento - Esportes

Foram adicionadas as seguintes propriedades na resposta JSON:
   * `SentenceText` em Análise de Sentimento
   * `Warnings` para cada documento 

Os nomes das seguintes propriedades na resposta JSON foram alterados, se aplicável:

* `score` mudou de nome para `confidenceScore`
    * `confidenceScore` tem dois pontos decimais de precisão. 
* `type` mudou de nome para `category`
* `subtype` mudou de nome para `subcategory`

> [!div class="nextstepaction"]
> [Saiba mais sobre text Analytics API v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>API API de Análise de Texto v3.1 Visualização pública
   * Novo recurso de Análise de Sentimento - [Mineração de Opinião](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * Novo filtro de domínio pessoal `PII` para informações de saúde protegidas `PHI` ().

> [!div class="nextstepaction"]
> [Saiba mais sobre a API API v3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>Fevereiro de 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Suporte SDK para API API v3 Visualização pública de texto

Como parte da versão unificada do [Azure SDK,](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)a API v3 SDK text Analytics está agora disponível como pré-visualização pública para as seguintes linguagens de programação:
   * [C#](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-csharp&tabs=version-3)
   * [Python](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-python&tabs=version-3)
   * [JavaScript (Node.js)](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-javascript&tabs=version-3)
   * [Java](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-java&tabs=version-3)
   
> [!div class="nextstepaction"]
> [Saiba mais sobre Text Analytics API v3 SDK](./quickstarts/client-libraries-rest-api.md?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Nome de Reconhecimento de Entidade v3 visualização pública

Os tipos de entidades adicionais estão agora disponíveis no serviço de pré-visualização pública denominado NER v3 à medida que expandimos a deteção de entidades de informação geral e pessoal encontradas em texto. Esta atualização introduz [a versão modelo,](concepts/model-versioning.md) `2020-02-01` que inclui:

* Reconhecimento dos seguintes tipos gerais de entidades (apenas em inglês):
    * PersonType
    * Produto
    * Evento
    * Entidade Geopolítica (GPE) como subtipo na Localização
    * Habilidade

* Reconhecimento dos seguintes tipos de entidades de informação pessoal (apenas em inglês):
    * Pessoa
    * Organização
    * Idade como subtipo em Quantidade
    * Data como subtipo em DateTime
    * E-mail 
    * Número de telefone (apenas NOS)
    * URL
    * Endereço IP

### <a name="october-2019"></a>Outubro de 2019

#### <a name="named-entity-recognition-ner"></a>Reconhecimento de Entidades Nomeadas (NER)

* Um [novo ponto final](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionPii) para o reconhecimento de tipos de entidades de informação pessoal (apenas em inglês)

* Pontos finais separados para [reconhecimento de entidades](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionGeneral) e [ligação de entidades.](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesLinking)

* [Versão do modelo,](concepts/model-versioning.md) `2019-10-01` que inclui:
    * Deteção e categorização alargadas de entidades encontradas em texto. 
    * Reconhecimento dos seguintes novos tipos de entidades:
        * Número de telefone
        * Endereço IP

A ligação de entidade suporta inglês e espanhol. O suporte linguístico NER varia de acordo com o tipo de entidade.

#### <a name="sentiment-analysis-v3-public-preview"></a>Análise de Sentimento v3 visualização pública

* Um [novo ponto final](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/Sentiment) para analisar o sentimento.
* [Versão do modelo,](concepts/model-versioning.md) `2019-10-01` que inclui:

    * Melhorias significativas na precisão e detalhe da categorização e pontuação de texto da API.
    * Rotulagem automática para diferentes sentimentos em texto.
    * Análise de sentimento e saída no nível de documento e frase. 

Apoia o inglês ( `en` japonês `ja` ), chinês simplificado ( `zh-Hans` , chinês tradicional ( ( ( ( ) e alemão , `zh-Hant` e `fr` está `it` `es` `nl` `pt` `de` disponível nas seguintes regiões: `Australia East` , , , , , , , , `Central Canada` , , , , , `Central US` e `East Asia` `East US` `East US 2` `North Europe` `Southeast Asia` `South Central US` `UK South` `West Europe` `West US 2` . 

> [!div class="nextstepaction"]
> [Saiba mais sobre a Análise de Sentimento v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Passos seguintes

* [O que é a API de Análise de Texto?](overview.md)  
* [Cenários de utilizador acionáveis](text-analytics-user-scenarios.md)
* [Análise de sentimentos](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Deteção linguística](how-tos/text-analytics-how-to-language-detection.md)
* [Reconhecimento de entidades](how-tos/text-analytics-how-to-entity-linking.md)
* [Extração de frase-chave](how-tos/text-analytics-how-to-keyword-extraction.md)
