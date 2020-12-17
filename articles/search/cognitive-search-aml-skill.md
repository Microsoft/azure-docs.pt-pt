---
title: Habilidade AML personalizada em skillsets
titleSuffix: Azure Cognitive Search
description: Alargar as capacidades de azure Cognitive Search skillsets com modelos Azure Machine Learning.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 6cefe543ea8ba992b028448070bf041a77bfec64
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630280"
---
# <a name="aml-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Habilidade AML num oleoduto de enriquecimento de pesquisa cognitiva Azure

> [!IMPORTANT] 
> Esta habilidade está atualmente em visualização pública. A funcionalidade de pré-visualização é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Atualmente não existe suporte .NET SDK.

A habilidade **AML** permite-lhe estender o enriquecimento de IA com um modelo personalizado [de Aprendizagem automática Azure](../machine-learning/overview-what-is-azure-ml.md) (AML). Uma vez treinado [e implantado](../machine-learning/concept-azure-machine-learning-architecture.md#workspace)um modelo AML, uma habilidade **AML** integra-o no enriquecimento de IA.

Tal como as competências incorporadas, uma habilidade **AML** tem entradas e saídas. As entradas são enviadas para o seu serviço AML implantado como um objeto JSON, que produz uma carga útil JSON como resposta juntamente com um código de estado de sucesso. Espera-se que a resposta tenha as saídas especificadas pela sua habilidade **AML.** Qualquer outra resposta é considerada um erro e não são realizados enriquecimentos.

> [!NOTE]
> O indexante tentará duas vezes para determinados códigos de estado HTTP padrão devolvidos do serviço AML. Estes códigos de estado HTTP são:
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="prerequisites"></a>Pré-requisitos

* Um [espaço de trabalho AML](../machine-learning/concept-workspace.md)
* Um [alvo de computação AML de serviço AZure Kubernetes](../machine-learning/concept-compute-target.md) neste espaço de trabalho com um [modelo implantado](../machine-learning/how-to-deploy-azure-kubernetes-service.md)
  * O [objetivo do cálculo deve ter SSL ativado](../machine-learning/how-to-secure-web-service.md#deploy-on-azure-kubernetes-service). A Azure Cognitive Search só permite o acesso a **pontos finais https**
  * Os certificados auto-assinados não podem ser utilizados.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.custom.AmlSkill

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis às maiúsculas e minúsculas. Quais os parâmetros que escolheu utilizar depende da autenticação que [o seu serviço AML requer, se houver](#WhatSkillParametersToUse)

| Nome do parâmetro | Descrição |
|--------------------|-------------|
| `uri` | (Requerido [para não autenticação ou autenticação de chaves)](#WhatSkillParametersToUse) O [URI de pontuação do serviço AML](../machine-learning/how-to-consume-web-service.md) para o qual será enviada a carga útil _JSON._ Apenas é permitido o sistema **https** URI. |
| `key` | (Requerido para [a autenticação das chaves)](#WhatSkillParametersToUse) A [chave para o serviço AML.](../machine-learning/how-to-consume-web-service.md#authentication-with-keys) |
| `resourceId` | (Requerido [para autenticação simbólica).](#WhatSkillParametersToUse) O ID de recursos do Azure Resource Manager do serviço AML. Deve estar nas subscrições de formato/{guid}/resourceGroups/{resource-group-name}/Microsoft.MachineLearningServices/workspaces/{workspace-name}/services/{service_name}. |
| `region` | (Opcional para [autenticação simbólica).](#WhatSkillParametersToUse) A [região](https://azure.microsoft.com/global-infrastructure/regions/) em que o serviço AML está implantado. |
| `timeout` | (Opcional) Quando especificado, indica o tempo limite para o cliente http que e faz a chamada da API. Deve ser formatado como um valor XSD "dayTimeDuration" (um subconjunto restrito de um valor de [duração ISO 8601).](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) Por exemplo, `PT60S` durante 60 segundos. Se não estiver definido, é escolhido um valor predefinido de 30 segundos. O tempo limite pode ser definido para um máximo de 230 segundos e um mínimo de 1 segundo. |
| `degreeOfParallelism` | (Opcional) Quando especificado, indica o número de chamadas que o indexante fará paralelamente ao ponto final que forneceu. Pode diminuir este valor se o seu ponto final estiver a falhar abaixo de uma carga de pedido demasiado elevada, ou aumentá-lo se o seu ponto final for capaz de aceitar mais pedidos e pretender um aumento no desempenho do indexante.  Se não for definido, é utilizado um valor predefinido de 5. O grau de Espargllelismo pode ser definido para um máximo de 10 e um mínimo de 1.

<a name="WhatSkillParametersToUse"></a>

## <a name="what-skill-parameters-to-use"></a>Que parâmetros de habilidade usar

Quais parâmetros de habilidade AML são necessários depende da autenticação que o seu serviço AML utiliza, se houver. Os serviços AML fornecem três opções de autenticação:

* [Autenticação baseada em chaves.](../machine-learning/how-to-authenticate-web-service.md#key-based-authentication) Uma chave estática é fornecida para autenticar pedidos de pontuação a partir de habilidades AML
  * Use os _parâmetros uri_ e _chave_
* [Autenticação baseada em fichas.](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication) O serviço AML é [implantado utilizando a autenticação baseada em símbolos.](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication) A [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) do serviço de Pesquisa Cognitiva Azure é concedida ao Papel do [Leitor](../machine-learning/how-to-assign-roles.md) no espaço de trabalho do serviço AML. A habilidade AML utiliza então a identidade gerida do serviço Azure Cognitive Search para autenticar contra o serviço AML, sem necessidade de chaves estáticas.
  * Utilize o parâmetro _resourceId._
  * Se o serviço de Pesquisa Cognitiva Azure se encontra numa região diferente do espaço de trabalho AML, utilize o parâmetro da _região_ para definir a região em que o serviço AML foi implantado em
* Sem autenticação. Não é necessária autenticação para utilizar o serviço AML
  * Use o parâmetro _uri_

## <a name="skill-inputs"></a>Entradas de habilidades

Não há entradas "predefinidas" para esta habilidade. Pode escolher um ou mais campos que já estariam disponíveis no momento da execução desta habilidade como entradas e a carga útil _JSON_ enviada para o serviço AML terá diferentes campos.

## <a name="skill-outputs"></a>Saídas de competências

Não há saídas "predefinidas" para esta habilidade. Dependendo da resposta, o seu serviço AML irá devolver, adicione campos de saída para que possam ser recolhidos a partir da resposta _JSON._

## <a name="sample-definition"></a>Definição de amostra

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="sample-input-json-structure"></a>Estrutura JSON de entrada de amostra

Esta estrutura _JSON_ representa a carga útil que será enviada para o seu serviço AML. Os campos de nível superior da estrutura corresponderão aos "nomes" especificados na `inputs` secção da definição de habilidade. O valor desses campos será a partir `source` desses campos (que podem ser de um campo no documento, ou potencialmente de outra habilidade)

```json
{
  "text": "Este es un contrato en Inglés"
}
```

## <a name="sample-output-json-structure"></a>Estrutura JSON de saída de amostra

A saída corresponde à resposta devolvida do seu serviço AML. O serviço AML só deve devolver uma carga útil _JSON_ (verificada olhando para o `Content-Type` cabeçalho de resposta) e deve ser um objeto em que os campos são enriquecimentos correspondentes aos "nomes" do `output` e cujo valor é considerado o enriquecimento.

```json
{
    "detected_language_code": "es"
}
```

## <a name="inline-shaping-sample-definition"></a>Definição de amostra de modelação inline

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "shapedText",
        "sourceContext": "/document",
        "inputs": [
            {
              "name": "content",
              "source": "/document/content"
            }
        ]
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="inline-shaping-input-json-structure"></a>Estrutura JSON de modelação de formação inline

```json
{
  "shapedText": { "content": "Este es un contrato en Inglés" }
}
```

## <a name="inline-shaping-sample-output-json-structure"></a>Estrutura JSON de produção de amostra de modelação inline

```json
{
    "detected_language_code": "es"
}
```

## <a name="error-cases"></a>Casos de erro
Além de o seu AML estar indisponível ou enviar códigos de estado não bem sucedidos, são considerados casos erróneos:

* Se o serviço AML devolver um código de estado de sucesso mas a resposta indicar que não é `application/json` , então a resposta é considerada inválida e não serão realizados enriquecimentos.
* Se o serviço AML devolver json inválido

Para casos em que o serviço AML não esteja disponível ou devolva um erro HTTP, será adicionado um erro amigável com quaisquer detalhes disponíveis sobre o erro HTTP ao histórico de execução do indexante.

## <a name="see-also"></a>Ver também

+ [Como definir um skillset](cognitive-search-defining-skillset.md)
+ [Resolução de problemas do serviço AML](../machine-learning/how-to-troubleshoot-deployment.md)