---
title: Habilidade cognitiva de deteção pii (pré-visualização)
titleSuffix: Azure Cognitive Search
description: Extrair e mascarar informações pessoalmente identificáveis a partir de texto num oleoduto de enriquecimento em Pesquisa Cognitiva Azure. Esta habilidade está atualmente em pré-visualização pública.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: f21200bc6f5b25f3330f5bb87c0843caa5a84e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298886"
---
#    <a name="pii-detection-cognitive-skill"></a>Habilidade cognitiva de deteção de PII

> [!IMPORTANT] 
> Esta habilidade está atualmente em pré-visualização pública. A funcionalidade de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure . Atualmente não existe nenhum suporte de Portal ou .NET SDK.

A habilidade **pii deteção** extrai informações pessoalmente identificáveis de um texto de entrada e dá-lhe a opção de a mascarar desse texto de várias maneiras. Esta habilidade utiliza os modelos de machine learning fornecidos pela [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) em Serviços Cognitivos.

> [!NOTE]
> À medida que expande o âmbito aumentando a frequência do processamento, adicionando mais documentos, ou adicionando mais algoritmos de IA, terá de [anexar um recurso de Serviços Cognitivos faturados.](cognitive-search-attach-cognitive-services.md) As acusações acumulam-se quando se ligam para apis em Serviços Cognitivos, e para extração de imagem como parte da fase de quebra de documentos na Pesquisa Cognitiva Azure. Não há encargos para a extração de texto de documentos.
>
> A execução de competências incorporadas é cobrada pelo preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. Os preços de extração de imagem são descritos na página de preços da [Pesquisa Cognitiva Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um disco deve ser de [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)50.000 caracteres medido por . Se precisar de separar os seus dados antes de enviá-los para a habilidade, considere utilizar a [habilidade Text Split](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis aos casos e todos são opcionais.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| código de idioma padrão |    Código linguístico do texto de entrada. Por enquanto, só `en` é apoiado. |
| mínimoPrecisão | Um valor entre 0,0 e 1,0. Se a pontuação `piiEntities` de confiança (na `minimumPrecision` saída) for inferior ao valor definido, a entidade não é devolvida ou mascarada. O padrão é 0.0. |
| modo de mascaramento | Um parâmetro que fornece várias formas de mascarar o PII detetado no texto de entrada. As seguintes opções são suportadas: <ul><li>`none`Isto significa que não será efetuada máscara `maskedText` e a saída não será devolvida. </li><li> `redact`: Esta opção removerá as entidades detetadas do texto de entrada e não as substituirá por nada. Note que, neste caso, `piiEntities` a compensação na saída será em relação ao texto original, e não ao texto mascarado. </li><li> `replace`: Esta opção substituirá as entidades detetadas pelo carácter dado no `maskingCharacter` parâmetro.  O carácter será repetido ao comprimento da entidade detetada de modo a que as compensações correspondam `maskedText`corretamente tanto ao texto de entrada como à saída .</li></ul> |
| personagem de máscara | O personagem que será usado para `maskingMode` mascarar o `replace`texto se o parâmetro estiver definido para . As seguintes opções `*` são `#`suportadas: (padrão), `X`. Este parâmetro só `null` pode `maskingMode` ser se `replace`não estiver definido para . |


## <a name="skill-inputs"></a>Inputs de habilidade

| Nome de entrada      | Descrição                   |
|---------------|-------------------------------|
| languageCode    | Opcional. A predefinição é `en`.  |
| texto          | O texto para analisar.          |

## <a name="skill-outputs"></a>Saídas de habilidades

| Nome de saída      | Descrição                   |
|---------------|-------------------------------|
| piiEntidades | Uma variedade de tipos complexos que contém os seguintes campos: <ul><li>texto (o PII real como extraído)</li> <li>tipo</li><li>subTipo</li><li>pontuação (valor mais elevado significa que é mais provável ser uma entidade real)</li><li>compensação (no texto de entrada)</li><li>length</li></ul> </br> [Podem ser encontrados tipos e subtipos possíveis aqui.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/named-entity-types?tabs=personal) |
| texto mascarado | Se `maskingMode` for definido para `none`um valor diferente, esta saída será o resultado da sequência da `maskingMode`máscara realizada no texto de entrada descrito pelo selecionado .  Se `maskingMode` estiver `none`programado para, esta saída não estará presente. |

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
##    <a name="sample-input"></a>Entrada da amostra

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

##    <a name="sample-output"></a>Resultado da amostra

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

Note que as compensações devolvidas para entidades na saída desta habilidade são devolvidas diretamente da API text [analytics,](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)o que significa que se estiver a usá-las para indexar na cadeia original, deve utilizar a classe [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) em .NET para extrair o conteúdo correto.  [Mais detalhes podem ser encontrados aqui.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-and-warning-cases"></a>Casos de erro e de alerta
Se o código linguístico do documento não for suportado, um aviso é devolvido e nenhuma entidade é extraída.
Se o seu texto estiver vazio, será emitido um aviso.
Se o seu texto for superior a 50.000 caracteres, apenas serão analisados os primeiros 50.000 caracteres e emitido um aviso.

Se a habilidade devolver um `maskedText` aviso, a saída pode estar vazia.  Isto significa que, se esperar que essa produção exista para a entrada em competências posteriores, não funcionará como pretendido. Tenha isto em mente ao escrever a definição de habilidade.

## <a name="see-also"></a>Consulte também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
