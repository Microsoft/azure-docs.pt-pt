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
ms.openlocfilehash: 7011d971ddb1888b312173a42ecd4a50f0954395
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849048"
---
#   <a name="pii-detection-cognitive-skill"></a>Habilidade cognitiva de deteção de PII

> [!IMPORTANT] 
> Esta habilidade está atualmente em pré-visualização pública. A funcionalidade de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Atualmente não existe nenhum suporte de Portal ou .NET SDK.

A habilidade **pii deteção** extrai informações pessoalmente identificáveis de um texto de entrada e dá-lhe a opção de a mascarar desse texto de várias maneiras. Essa habilidade usa os modelos de aprendizado de máquina fornecidos pelo [análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) em serviços cognitivas.

> [!NOTE]
> Ao expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de ia, você precisará [anexar um recurso de serviços cognitivas cobráveis](cognitive-search-attach-cognitive-services.md). As cobranças são acumuladas ao chamar APIs em serviços cognitivas e para extração de imagem como parte do estágio de quebra de documento no Azure Pesquisa Cognitiva. Não há encargos para a extração de texto de documentos.
>
> A execução de habilidades internas é cobrada pelo [preço pago pelo uso dos serviços cognitivas](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. O preço de extração de imagem é descrito na [página de preços do Azure pesquisa cognitiva](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um registro deve ser de 50.000 caracteres conforme medido por [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Se precisar de separar os seus dados antes de enviá-los para a habilidade, considere utilizar a [habilidade Text Split](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis aos casos e todos são opcionais.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| defaultLanguageCode | Código linguístico do texto de entrada. Por enquanto, só `en` é apoiado. |
| mínimoPrecisão | Um valor entre 0,0 e 1,0. Se a pontuação de confiança (na `piiEntities` saída) for inferior ao valor definido `minimumPrecision`, a entidade não é devolvida ou mascarada. O padrão é 0.0. |
| modo de mascaramento | Um parâmetro que fornece várias formas de mascarar o PII detetado no texto de entrada. As seguintes opções são suportadas: <ul><li>`none` (predefinido): Isto significa que não será feita máscara e a saída `maskedText` não será devolvida. </li><li> `redact`: Esta opção removerá as entidades detetadas do texto de entrada e não as substituirá por nada. Note que, neste caso, a compensação na saída `piiEntities` será em relação ao texto original, e não ao texto mascarado. </li><li> `replace`: Esta opção substituirá as entidades detetadas pelo carácter dado no parâmetro `maskingCharacter`.  O carácter será repetido ao comprimento da entidade detetada de modo a que as compensações correspondam corretamente tanto ao texto de entrada como à saída `maskedText`.</li></ul> |
| personagem de máscara | O personagem que será usado para mascarar o texto se o parâmetro `maskingMode` estiver definido para `replace`. São suportadas as seguintes opções: `*` (padrão), `#`, `X`. Este parâmetro só pode ser `null` se `maskingMode` não estiver definido para `replace`. |


## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada      | Descrição                   |
|---------------|-------------------------------|
| languageCode  | Opcional. A predefinição é `en`.  |
| texto          | O texto para analisar.          |

## <a name="skill-outputs"></a>Saídas de habilidades

| Nome da saída     | Descrição                   |
|---------------|-------------------------------|
| piiEntidades | Uma variedade de tipos complexos que contém os seguintes campos: <ul><li>texto (o PII real como extraído)</li> <li>tipo</li><li>Subtipo</li><li>pontuação (valor mais elevado significa que é mais provável ser uma entidade real)</li><li>compensação (no texto de entrada)</li><li>length</li></ul> </br> [Podem ser encontrados tipos e subtipos possíveis aqui.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/named-entity-types?tabs=personal) |
| texto mascarado | Se `maskingMode` for definido para um valor diferente do `none`, esta saída será o resultado da máscara realizada no texto de entrada descrito pelo `maskingMode`selecionado .  Se `maskingMode` estiver programado para `none`, esta saída não estará presente. |

##  <a name="sample-definition"></a>Definição de exemplo

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
##  <a name="sample-input"></a>Entrada da amostra

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

##  <a name="sample-output"></a>Resultado da amostra

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


## <a name="error-and-warning-cases"></a>Casos de erro e de alerta
Se o código linguístico do documento não for suportado, um aviso é devolvido e nenhuma entidade é extraída.
Se o texto estiver vazio, um aviso será produzido.
Se o seu texto for superior a 50.000 caracteres, apenas serão analisados os primeiros 50.000 caracteres e emitido um aviso.

Se a habilidade devolver um aviso, a saída `maskedText` pode estar vazia.  Isto significa que, se esperar que essa produção exista para a entrada em competências posteriores, não funcionará como pretendido. Tenha isto em mente ao escrever a definição de habilidade.

## <a name="see-also"></a>Ver também

+ [Habilidades internas](cognitive-search-predefined-skills.md)
+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
