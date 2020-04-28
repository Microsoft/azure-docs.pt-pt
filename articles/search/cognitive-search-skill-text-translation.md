---
title: Habilidade cognitiva de tradução de texto
titleSuffix: Azure Cognitive Search
description: Avalia o texto e, para cada registo, devolve texto traduzido para a linguagem-alvo especificada num pipeline de enriquecimento de IA em Pesquisa Cognitiva Azure.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5089174fcfd5a97128c1f789b818243243a5282f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75460768"
---
#   <a name="text-translation-cognitive-skill"></a>Habilidade cognitiva de tradução de texto

A habilidade de **Tradução de Texto** avalia texto e, para cada registo, devolve o texto traduzido para a linguagem-alvo especificada. Esta habilidade utiliza o [Tradutor Text API v3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) disponível em Serviços Cognitivos.

Esta capacidade é útil se esperar que os seus documentos não estejam todos numa só língua, caso em que pode normalizar o texto para uma única língua antes de indexar para pesquisa traduzindo-o.  Também é útil para casos de uso de localização, onde você pode querer ter cópias do mesmo texto disponíveis em várias línguas.

O [Tradutor Text API v3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) é um Serviço Cognitivo não regional, o que significa que os seus dados não estão garantidos para permanecer na mesma região que o seu recurso Azure Cognitive Search ou serviços cognitivos anexados.

> [!NOTE]
> À medida que expande o âmbito aumentando a frequência do processamento, adicionando mais documentos, ou adicionando mais algoritmos de IA, terá de [anexar um recurso de Serviços Cognitivos faturados.](cognitive-search-attach-cognitive-services.md) As acusações acumulam-se quando se ligam para apis em Serviços Cognitivos, e para extração de imagem como parte da fase de quebra de documentos na Pesquisa Cognitiva Azure. Não há encargos para a extração de texto de documentos.
>
> A execução de competências incorporadas é cobrada pelo preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. Os preços de extração de imagem são descritos na página de preços da [Pesquisa Cognitiva Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.TranslationSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um disco deve ser de [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)50.000 caracteres medido por . Se precisar de separar os seus dados antes de enviá-los para a habilidade de tradução de texto, considere utilizar a [habilidade text split](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis às maiúsculas e minúsculas.

| Entradas                | Descrição |
|---------------------|-------------|
| padrãoCódigo De Linguagem | (Obrigatório) O código linguístico para traduzir documentos para documentos que não especificam explicitamente a linguagem. <br/> Consulte [a lista completa de línguas suportadas](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| padrãoFromLanguageCode | (Opcional) O código linguístico para traduzir documentos de documentos que não especificam explicitamente o idioma.  Se o código de idioma não for especificado, a deteção automática de idiomas fornecida pela API de Texto tradutor será utilizada para determinar o idioma a partir do idioma. <br/> Consulte [a lista completa de línguas suportadas](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| sugeridoFrom | (Opcional) O código de idioma para traduzir documentos a partir de quando nem a entrada do LanguageCode nem o parâmetro padrãoFromLanguageCode são fornecidos, e a deteção automática de idiomas não é bem sucedida.  Se a língua sugerida não for especificada, o inglês (en) será utilizado como a língua sugerida. <br/> Consulte [a lista completa de línguas suportadas](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |

## <a name="skill-inputs"></a>Inputs de habilidade

| Nome de entrada     | Descrição |
|--------------------|-------------|
| texto | O texto a ser traduzido.|
| paraLanguageCode    | Uma cadeia indicando a língua para a a que o texto deve ser traduzido. Se esta entrada não for especificada, o código de idioma padrão será utilizado para traduzir o texto. <br/>Ver [lista completa de línguas suportadas](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|
| fromLanguageCode  | Uma corda indicando a linguagem atual do texto. Se este parâmetro não for especificado, o Código de Idiomas (ou deteção automática de idiomas se o Código de Idioma não for fornecido) será utilizado para traduzir o texto. <br/>Ver [lista completa de línguas suportadas](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|

## <a name="skill-outputs"></a>Saídas de habilidades

| Nome de saída    | Descrição |
|--------------------|-------------|
| texto traduzido | O resultado da tradução de texto do código de idioma traduzido para o código de linguagem traduzido para o código de linguagem traduzido.|
| traduzidoPara LanguageCode  | Uma cadeia indicando o código linguístico para o que o texto foi traduzido. Útil se estiver a traduzir para várias línguas e quiser ser capaz de acompanhar qual o texto que é a língua.|
| traduzidoFromLanguageCode    | Uma cadeia indicando o código linguístico do texto foi traduzido. Útil se optou pela opção automática de deteção de idiomas, uma vez que esta saída lhe dará o resultado dessa deteção.|

##  <a name="sample-definition"></a>Definição de amostra

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
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
          "text": "We hold these truths to be self-evident, that all men are created equal."
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "text": "Estamos muy felices de estar con ustedes."
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
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>Erros e advertências
Se fornecer um código de idioma não suportado para o de ou para o idioma, um erro é gerado e o texto não é traduzido.
Se o seu texto estiver vazio, será emitido um aviso.
Se o seu texto for superior a 50.000 caracteres, apenas os primeiros 50.000 caracteres serão traduzidos e um aviso será emitido.

## <a name="see-also"></a>Consulte também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
