---
title: Habilidade de pesquisa cognitiva de extração de expressões-chave - Azure Search
description: Avalia a texto não estruturado e para cada registo, devolve uma lista de expressões-chave num pipeline de enriquecimento de Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: f3c8fd3043f20ab34dda1f4e3762e3ebc4bb6ae1
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749890"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Competências cognitivas de extração de expressões chave

O **extração de expressões chave** habilidade avalia o texto não estruturado e para cada registo, devolve uma lista de expressões-chave. Essa habilidade usa os modelos de machine learning fornecidos pela [análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) nos serviços cognitivos.

Esta funcionalidade é útil se precisar de identificar rapidamente os principais pontos de conversa no registo. Por exemplo, texto de entrada especificado "alimentar foi delicious e havia maravilhoso equipe", o serviço devolve "food" e "funcionário maravilhoso".

> [!NOTE]
> A partir de 21 de Dezembro de 2018, pode [anexar um recurso dos serviços cognitivos](cognitive-search-attach-cognitive-services.md) com um conjunto de capacidades do Azure Search. Isso permite que iniciemos a cobrança de consumo para a execução do conjunto de capacidades. Esta data, também Começámos a cobrar para extração de imagem como parte da fase de aberturas de documentos. Extração de texto de documentos continua a ser disponibilizado sem custos adicionais.
>
> [Competências cognitivas incorporada](cognitive-search-predefined-skills.md) execução é cobrada a [dos serviços cognitivos pay as you go preço](https://azure.microsoft.com/pricing/details/cognitive-services), em para classificar o mesmo como se realizados diretamente a tarefa. Extração de imagem é um valor de Azure Search, atualmente oferecido a preços de pré-visualização. Para obter detalhes, consulte a [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400) ou [como funciona a faturação](search-sku-tier.md#how-billing-works).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um registo deve ser 50.000 carateres conforme medido pela `String.Length`. Se tiver de dividir os dados antes de os enviar para o extrator de expressões-chave, considere utilizar o [habilidade de divisão de texto](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidades

Parâmetros diferenciam maiúsculas de minúsculas.
| Entradas                | Descrição |
|---------------------|-------------|
| defaultLanguageCode | (Opcional) O código de idioma para aplicar a documentos que não especificar explicitamente o idioma.  Se o código de idioma padrão não for especificado, em inglês (en) será utilizado como o código de idioma padrão. <br/> Ver [uma lista completa das linguagens suportadas](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). |
| maxKeyPhraseCount   | (Opcional) O número máximo de expressões-chave para produzir. |

## <a name="skill-inputs"></a>Entradas de habilidades
| Entradas     | Descrição |
|--------------------|-------------|
| texto | O texto a ser analisados.|
| languageCode  |  Uma cadeia que indica o idioma dos registos. Se este parâmetro não for especificado, o código de idioma padrão será utilizado para analisar os registos. <br/>Consulte [lista completa das linguagens suportadas](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

##  <a name="sample-definition"></a>Definição de exemplo

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
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

##  <a name="sample-input"></a>Entrada de exemplo

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


## <a name="errors-and-warnings"></a>Erros e avisos
Se fornecer um código de idioma não suportado, é gerado um erro e expressões-chave não é extraídos.
Se o texto estiver vazio, um aviso será produzido.
Se o texto é maior do que 50.000 carateres, apenas os primeiras 50.000 carateres irão ser analisados e será emitido um aviso.

## <a name="see-also"></a>Consulte também

+ [Competências predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
