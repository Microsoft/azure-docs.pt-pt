---
title: Habilidade cognitiva de deteção pii (pré-visualização)
titleSuffix: Azure Cognitive Search
description: Extrair e mascarar informação pessoalmente identificável a partir de texto num oleoduto de enriquecimento em Azure Cognitive Search. Esta habilidade está atualmente em visualização pública.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: b2e35ba083e376f519ccbc32c71c1ac9b1e03a41
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935301"
---
#    <a name="pii-detection-cognitive-skill"></a>Competência cognitiva de deteção de PII

> [!IMPORTANT] 
> Esta habilidade está atualmente em visualização pública. A funcionalidade de pré-visualização é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Atualmente não existe porta ou suporte .NET SDK.

A habilidade **de deteção pii** extrai informações pessoalmente identificáveis a partir de um texto de entrada e dá-lhe a opção de mascar a partir desse texto de várias maneiras. Esta habilidade utiliza os modelos de aprendizagem automática fornecidos pela [Text Analytics](../cognitive-services/text-analytics/overview.md) em Serviços Cognitivos.

> [!NOTE]
> À medida que expande o âmbito, aumentando a frequência do processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, terá de [anexar um recurso de Serviços Cognitivos faturados.](cognitive-search-attach-cognitive-services.md) As taxas acumulam-se ao chamar APIs em Serviços Cognitivos, e para a extração de imagem como parte da fase de cracking de documentos em Azure Cognitive Search. Não há encargos para a extração de texto a partir de documentos.
>
> A execução de competências incorporadas é cobrada pelo preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. Os preços de extração de imagem são descritos na [página de preços de Pesquisa Cognitiva Azure](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.PIIDetectionskill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um disco deve ser de 50.000 caracteres medido por [`String.Length`](/dotnet/api/system.string.length) . Se precisar de separar os seus dados antes de os enviar para a habilidade, considere utilizar a [habilidade Text Split](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis a casos e todos são opcionais.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| `defaultLanguageCode` |    Código linguístico do texto de entrada. Por enquanto, só `en` é apoiado. |
| `minimumPrecision` | Um valor entre 0,0 e 1.0. Se a pontuação de confiança (na `piiEntities` saída) for inferior ao `minimumPrecision` valor definido, a entidade não é devolvida ou mascarada. O padrão é 0.0. |
| `maskingMode` | Um parâmetro que fornece várias formas de mascarar o PII detetado no texto de entrada. As seguintes opções são suportadas: <ul><li>`none` Isto significa que não será efetuada nenhuma máscara e que a `maskedText` saída não será devolvida. </li><li> `redact`: Esta opção removerá as entidades detetadas do texto de entrada e não as substituirá por nada. Note que, neste caso, a compensação na `piiEntities` saída será em relação ao texto original, e não ao texto mascarado. </li><li> `replace`: Esta opção substituirá as entidades detetadas pelo carácter indicado no `maskingCharacter` parâmetro.  O carácter será repetido ao comprimento da entidade detetada de modo a que as compensações correspondam corretamente tanto ao texto de entrada como à saída `maskedText` .</li></ul> |
| `maskingCharacter` | O carácter que será utilizado para mascarar o texto se o `maskingMode` parâmetro estiver definido para `replace` . As seguintes opções são suportadas: `*` (padrão), `#` `X` . Este parâmetro só pode ser `null` se não estiver definido para `maskingMode` `replace` . |


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

##    <a name="sample-definition"></a>Definição de amostra

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
##    <a name="sample-input"></a>Entrada de exemplo

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

##    <a name="sample-output"></a>Saída de exemplo

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

Note que as compensações devolvidas às entidades na saída desta habilidade são diretamente devolvidas da [Text Analytics API](../cognitive-services/text-analytics/overview.md), o que significa que se estiver a usá-las para indexar na cadeia original, deverá utilizar a classe [StringInfo](/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) em .NET para extrair o conteúdo correto.  [Mais detalhes podem ser encontrados aqui.](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="error-and-warning-cases"></a>Casos de erro e de alerta
Se o código linguístico do documento não for suportado, um aviso é devolvido e nenhuma entidade é extraída.
Se o seu texto estiver vazio, será produzido um aviso.
Se o seu texto for superior a 50.000 caracteres, apenas os primeiros 50.000 caracteres serão analisados e será emitido um aviso.

Se a habilidade devolver um aviso, a saída `maskedText` pode estar vazia.  Isto significa que, se espera que essa saída exista para a entrada em competências posteriores, não funcionará como pretendido. Tenha isto em mente ao escrever a sua definição de skillset.

## <a name="see-also"></a>Ver também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um skillset](cognitive-search-defining-skillset.md)