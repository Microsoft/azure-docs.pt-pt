---
title: Habilidade cognitiva de deteção pii (pré-visualização)
titleSuffix: Azure Cognitive Search
description: Extrair e mascarar informações pessoais de texto num oleoduto de enriquecimento em Azure Cognitive Search. Esta habilidade está atualmente em visualização pública.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: acacf617d3f1d9ab891d08b32fc2dfb14deb64a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540528"
---
# <a name="pii-detection-cognitive-skill"></a>Competência cognitiva de deteção de PII

> [!IMPORTANT] 
> Esta habilidade está atualmente em visualização pública. A funcionalidade de pré-visualização é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Atualmente não existe porta ou suporte .NET SDK.

A habilidade **de Deteção PII** extrai informações pessoais de um texto de entrada e dá-lhe a opção de mascar. Esta habilidade utiliza os modelos de aprendizagem automática fornecidos pela [Text Analytics](../cognitive-services/text-analytics/overview.md) em Serviços Cognitivos.

> [!NOTE]
> À medida que expande o âmbito, aumentando a frequência do processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, terá de [anexar um recurso de Serviços Cognitivos faturados.](cognitive-search-attach-cognitive-services.md) As taxas acumulam-se ao chamar APIs em Serviços Cognitivos, e para a extração de imagem como parte da fase de cracking de documentos em Azure Cognitive Search. Não há encargos para a extração de texto a partir de documentos.
>
> A execução de competências incorporadas é cobrada pelo preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. Os preços de extração de imagem são descritos na [página de preços de Pesquisa Cognitiva Azure](https://azure.microsoft.com/pricing/details/search/).

## <a name="odatatype"></a>@odata.type

Microsoft.Skills.Text.PIIDetectionskill

## <a name="data-limits"></a>Limites de dados

O tamanho máximo de um disco deve ser de 50.000 caracteres medido por [`String.Length`](/dotnet/api/system.string.length) . Se precisar de estudá-los antes de os enviar para a habilidade, considere utilizar a [habilidade Text Split](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis a casos e todos são opcionais.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| `defaultLanguageCode` |    Código linguístico do texto de entrada. Por enquanto, só `en` é apoiado. |
| `minimumPrecision` | Um valor entre 0,0 e 1.0. Se a pontuação de confiança (na `piiEntities` saída) for inferior ao `minimumPrecision` valor definido, a entidade não é devolvida ou mascarada. O padrão é 0.0. |
| `maskingMode` | Um parâmetro que fornece várias formas de mascarar as informações pessoais detetadas no texto de entrada. As seguintes opções são suportadas: <ul><li>`none` (predefinição): Não ocorre máscara e a `maskedText` saída não será devolvida. </li><li> `redact`: Remove as entidades detetadas do texto de entrada e não substitui os valores eliminados. Neste caso, a compensação na `piiEntities` saída será em relação ao texto original, e não ao texto mascarado. </li><li> `replace`: Substitui as entidades detetadas pelo carácter indicado no `maskingCharacter` parâmetro. O carácter será repetido ao comprimento da entidade detetada de modo a que as compensações correspondam corretamente tanto ao texto de entrada como à saída `maskedText` .</li></ul> |
| `maskingCharacter` | O carácter utilizado para mascarar o texto se o `maskingMode` parâmetro estiver definido para `replace` . As seguintes opções são suportadas: `*` (padrão), `#` `X` . Este parâmetro só pode ser `null` se não estiver definido para `maskingMode` `replace` . |

## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada      | Descrição                   |
|---------------|-------------------------------|
| `languageCode`    | Opcional. A predefinição é `en`.  |
| `text`          | O texto para analisar.          |

## <a name="skill-outputs"></a>Saídas de competências

| Nome de saída      | Descrição                   |
|---------------|-------------------------------|
| `piiEntities` | Uma variedade de tipos complexos que contém os seguintes campos: <ul><li>texto (O PII real extraído)</li> <li>tipo</li><li>subtiType</li><li>pontuação (Maior valor significa que é mais provável ser uma entidade real)</li><li>compensação (no texto de entrada)</li><li>length</li></ul> </br> [Possíveis tipos e subtiTypes podem ser encontrados aqui.](../cognitive-services/text-analytics/named-entity-types.md?tabs=personal) |
| `maskedText` | Se `maskingMode` for definida para um valor `none` diferente, esta saída será o resultado da máscara realizada no texto de entrada, tal como descrito pelo selecionado `maskingMode` .  Se `maskingMode` estiver programado para , esta saída não estará `none` presente. |

## <a name="sample-definition"></a>Definição de amostra

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.PIIDetectionSkill",
    "defaultLanguageCode": "en",
    "minimumPrecision": 0.5,
    "maskingMode": "replace",
    "maskingCharacter": "*",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "piiEntities"
      },
      {
        "name": "maskedText"
      }
    ]
  }
```

## <a name="sample-input"></a>Entrada de exemplo

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Microsoft employee with ssn 859-98-0987 is using our awesome API's."
           }
      }
    ]
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "piiEntities":[ 
           { 
              "text":"859-98-0987",
              "type":"U.S. Social Security Number (SSN)",
              "subtype":"",
              "offset":28,
              "length":11,
              "score":0.65
           }
        ],
        "maskedText": "Microsoft employee with ssn *********** is using our awesome API's."
      }
    }
  ]
}
```

As compensações devolvidas às entidades na saída desta habilidade são diretamente devolvidas da [API text Analytics](../cognitive-services/text-analytics/overview.md), o que significa que se estiver a usá-las para indexar na cadeia original, deverá utilizar a classe [StringInfo](/dotnet/api/system.globalization.stringinfo) em .NET para extrair o conteúdo correto.  [Mais detalhes podem ser encontrados aqui.](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="errors-and-warnings"></a>Erros e avisos

Se o código linguístico do documento não for suportado, um aviso é devolvido e nenhuma entidade é extraída.
Se o seu texto estiver vazio, um aviso é devolvido.
Se o seu texto for superior a 50.000 caracteres, apenas os primeiros 50.000 caracteres serão analisados e será emitido um aviso.

Se a habilidade devolver um aviso, a saída pode estar vazia, o `maskedText` que pode afetar quaisquer habilidades a jusante que esperam a saída. Por esta razão, certifique-se de investigar todos os avisos relacionados com a falta de saída ao escrever a sua definição de skillset.

## <a name="see-also"></a>Consulte também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um skillset](cognitive-search-defining-skillset.md)