---
title: Habilidade cognitiva de tradução de texto
titleSuffix: Azure Cognitive Search
description: Avalia o texto e, para cada registo, devolve o texto traduzido para a linguagem-alvo especificada num oleoduto de enriquecimento de IA na Azure Cognitive Search.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0953d750ee8b59e9889512bb64cfd276a0bbeb53
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654869"
---
#   <a name="text-translation-cognitive-skill"></a>Habilidade cognitiva de tradução de texto

A habilidade **de tradução** de texto avalia o texto e, para cada registo, devolve o texto traduzido para a linguagem-alvo especificada. Esta habilidade utiliza o [Texto Tradutor API v3.0](../cognitive-services/translator/reference/v3-0-translate.md) disponível nos Serviços Cognitivos.

Esta capacidade é útil se espera que os seus documentos não estejam todos numa língua, caso em que pode normalizar o texto para um único idioma antes de indexar a pesquisa, traduzindo-o.  Também é útil para localização de casos de uso, onde você pode querer ter cópias do mesmo texto disponível em vários idiomas.

O [Tradutor Texto API v3.0](../cognitive-services/translator/reference/v3-0-reference.md) é um Serviço Cognitivo não regional, o que significa que os seus dados não estão garantidos para permanecer na mesma região que o seu recurso Azure Cognitive Search ou serviços cognitivos anexados.

> [!NOTE]
> À medida que expande o âmbito, aumentando a frequência do processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, terá de [anexar um recurso de Serviços Cognitivos faturados.](cognitive-search-attach-cognitive-services.md) As taxas acumulam-se ao chamar APIs em Serviços Cognitivos, e para a extração de imagem como parte da fase de cracking de documentos em Azure Cognitive Search. Não há encargos para a extração de texto a partir de documentos.
>
> A execução de competências incorporadas é cobrada pelo preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. Os preços de extração de imagem são descritos na [página de preços de Pesquisa Cognitiva Azure](https://azure.microsoft.com/pricing/details/search/).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.text.translationskill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um disco deve ser de 50.000 caracteres medido por [`String.Length`](/dotnet/api/system.string.length) . Se precisar de separar os seus dados antes de os enviar para a habilidade de tradução de texto, considere utilizar a [habilidade Text Split](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis às maiúsculas e minúsculas.

| Entradas | Descrição |
|---------------------|-------------|
| códigoToLanguageCode | (Obrigatório) O código linguístico para traduzir documentos em documentos que não especificam explicitamente a linguagem. <br/> Consulte [a lista completa de línguas suportadas.](../cognitive-services/translator/language-support.md) |
| defaultFromLanguageCode | (Opcional) O código linguístico para traduzir documentos de documentos que não especificam explicitamente o da língua.  Se o Código de Identificação Padrão não for especificado, a deteção automática da linguagem fornecida pela API de Texto do Tradutor será utilizada para determinar a partir da idioma. <br/> Consulte [a lista completa de línguas suportadas.](../cognitive-services/translator/language-support.md) |
| sugeriuFrom | (Opcional) O código linguístico para traduzir documentos a partir do momento em que nem a entrada doLanguageCode nem o parâmetro padrão DoLanguageCode são fornecidos, e a deteção automática de idiomas não é bem sucedida.  Se a língua sugeridaFrom não for especificada, o inglês (en) será usado como a língua sugerida. <br/> Consulte [a lista completa de línguas suportadas.](../cognitive-services/translator/language-support.md) |

## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada     | Descrição |
|--------------------|-------------|
| texto | O texto a ser traduzido.|
| toLanguageCode    | Uma cadeia que indique a língua para a que o texto deve ser traduzido. Se esta entrada não for especificada, o Código de Liga Padrão será utilizado para traduzir o texto. <br/>Ver [lista completa de línguas apoiadas](../cognitive-services/translator/language-support.md)|
| deLanguageCode  | Uma corda que indica a linguagem atual do texto. Se este parâmetro não for especificado, o Código de Idioma padrão (ou deteção automática da linguagem se o Código de Língua Padrão não for fornecido) será utilizado para traduzir o texto. <br/>Ver [lista completa de línguas apoiadas](../cognitive-services/translator/language-support.md)|

## <a name="skill-outputs"></a>Saídas de competências

| Nome de saída    | Descrição |
|--------------------|-------------|
| traduzidoText | O resultado da sequência da tradução de texto do Código de Liga traduzido para o Código de Honra traduzido.|
| traduzidoToLanguageCode  | Uma cadeia indicando o código de linguagem para o que o texto foi traduzido. Útil se estiver a traduzir para várias línguas e quiser ser capaz de acompanhar qual o texto que é a língua.|
| traduzidoFromLanguageCode    | Uma cadeia indicando o código de linguagem de que o texto foi traduzido. Útil se optar pela opção automática de deteção de idiomas, uma vez que esta saída lhe dará o resultado dessa deteção.|

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

##  <a name="sample-input"></a>Entrada de exemplo

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


##  <a name="sample-output"></a>Saída de exemplo

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


## <a name="errors-and-warnings"></a>Erros e avisos
Se fornecer um código linguístico não suportado para o idioma ou para a língua, um erro é gerado e o texto não é traduzido.
Se o seu texto estiver vazio, será produzido um aviso.
Se o seu texto for superior a 50.000 caracteres, apenas os primeiros 50.000 caracteres serão traduzidos e um aviso será emitido.

## <a name="see-also"></a>Ver também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um skillset](cognitive-search-defining-skillset.md)