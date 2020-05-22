---
title: Chave frase Habilidade cognitiva de extração
titleSuffix: Azure Cognitive Search
description: Avalia texto não estruturado e, para cada registo, devolve uma lista de frases-chave num oleoduto de enriquecimento de IA em Pesquisa Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ddcd95356f9b70fec5a74f36f5b80e55ea56b477
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744004"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Chave frase Habilidade cognitiva de extração

A habilidade de extração de **frases-chave** avalia texto não estruturado e, para cada registo, devolve uma lista de frases-chave. Esta habilidade utiliza os modelos de machine learning fornecidos pela [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) em Serviços Cognitivos.

Esta capacidade é útil se precisar identificar rapidamente os principais pontos de conversa do registo. Por exemplo, dado o texto de entrada "A comida era deliciosa e havia uma equipe maravilhosa", o serviço devolve "comida" e "equipe maravilhosa".

> [!NOTE]
> À medida que expande o âmbito aumentando a frequência do processamento, adicionando mais documentos, ou adicionando mais algoritmos de IA, terá de [anexar um recurso de Serviços Cognitivos faturados.](cognitive-search-attach-cognitive-services.md) As acusações acumulam-se quando se ligam para apis em Serviços Cognitivos, e para extração de imagem como parte da fase de quebra de documentos na Pesquisa Cognitiva Azure. Não há encargos para a extração de texto de documentos.
>
> A execução de competências incorporadas é cobrada pelo preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. Os preços de extração de imagem são descritos na página de preços da [Pesquisa Cognitiva Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um disco deve ser de 50.000 caracteres medido por [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length) . Se precisar de separar os seus dados antes de enviá-los para o extrator de frases-chave, considere utilizar a [habilidade Text Split](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis às maiúsculas e minúsculas.

| Entradas                | Descrição |
|---------------------|-------------|
| código de idioma padrão | (Opcional) O código linguístico a aplicar a documentos que não especificam explicitamente a linguagem.  Se o código de idioma padrão não for especificado, o inglês (en) será utilizado como código de idioma predefinido. <br/> Consulte [a lista completa de línguas suportadas](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). |
| maxKeyPhraseCount   | (Opcional) O número máximo de frases-chave para produzir. |

## <a name="skill-inputs"></a>Inputs de habilidade

| Input  | Descrição |
|--------------------|-------------|
| texto | O texto a ser analisado.|
| languageCode  |  Uma corda indicando a linguagem dos registos. Se este parâmetro não for especificado, o código de idioma padrão será utilizado para analisar os registos. <br/>Ver [lista completa de línguas suportadas](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

## <a name="skill-outputs"></a>Saídas de habilidades

| Saída  | Descrição |
|--------------------|-------------|
| keyPhrases | Uma lista de frases-chave extraídas do texto de entrada. As frases-chave são devolvidas por ordem de importância. |


##  <a name="sample-definition"></a>Definição de amostra

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      },
      {
        "name": "languageCode",
        "source": "/document/languagecode" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
           }
      }
    ]
```


##  <a name="sample-output"></a>Resultado da amostra

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>Erros e advertências
Se fornecer um código de linguagem não suportado, gera-se um erro e não se extraem frases-chave.
Se o seu texto estiver vazio, será emitido um aviso.
Se o seu texto for superior a 50.000 caracteres, apenas serão analisados os primeiros 50.000 caracteres e emitido um aviso.

## <a name="see-also"></a>Veja também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
