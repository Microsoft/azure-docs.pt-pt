---
title: Habilidade cognitiva de extração de frase-chave
titleSuffix: Azure Cognitive Search
description: Avalia texto não estruturado e, para cada registo, devolve uma lista de frases-chave num oleoduto de enriquecimento de IA em Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8aafb08ff0ccc9391071f796450e69f87de279ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102547837"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Habilidade cognitiva de extração de frase-chave

A habilidade **de extração de frases-chave** avalia texto não estruturado e, para cada registo, devolve uma lista de frases-chave. Esta habilidade utiliza os modelos de aprendizagem automática fornecidos pela [Text Analytics](../cognitive-services/text-analytics/overview.md) em Serviços Cognitivos.

Esta capacidade é útil se precisar de identificar rapidamente os principais pontos de conversação do registo. Por exemplo, dado o texto de entrada "A comida era deliciosa e havia um pessoal maravilhoso", o serviço devolve "comida" e "funcionários maravilhosos".

> [!NOTE]
> À medida que expande o âmbito, aumentando a frequência do processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, terá de [anexar um recurso de Serviços Cognitivos faturados.](cognitive-search-attach-cognitive-services.md) As taxas acumulam-se ao chamar APIs em Serviços Cognitivos, e para a extração de imagem como parte da fase de cracking de documentos em Azure Cognitive Search. Não há encargos para a extração de texto a partir de documentos.
>
> A execução de competências incorporadas é cobrada pelo preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. Os preços de extração de imagem são descritos na [página de preços de Pesquisa Cognitiva Azure](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um disco deve ser de 50.000 caracteres medido por [`String.Length`](/dotnet/api/system.string.length) . Se precisar de separar os seus dados antes de os enviar para o extrator de frases-chave, considere utilizar a [habilidade Text Split](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis às maiúsculas e minúsculas.

| Entradas | Description |
|---------------------|-------------|
| `defaultLanguageCode` | (Opcional) O código linguístico a aplicar a documentos que não especificam explicitamente a língua.  Se o código linguístico predefinido não for especificado, o inglês (en) será utilizado como código idioma predefinido. <br/> Consulte [a lista completa de línguas suportadas.](../cognitive-services/text-analytics/language-support.md) |
| `maxKeyPhraseCount`   | (Opcional) O número máximo de frases-chave a produzir. |
| `modelVersion`   | (Opcional) A versão do modelo a utilizar ao ligar para o serviço Text Analytics. Será o mais recente disponível quando não especificado. Recomendamos que não especifique este valor a menos que seja absolutamente necessário. Consulte [a versão modelo na API de Análise de Texto](../cognitive-services/text-analytics/concepts/model-versioning.md) para obter mais detalhes. |

## <a name="skill-inputs"></a>Entradas de habilidades

| Entrada  | Description |
|--------------------|-------------|
| `text` | O texto a ser analisado.|
| `languageCode`    |  Uma corda que indica a linguagem dos registos. Se este parâmetro não for especificado, o código linguístico predefinido será utilizado para analisar os registos. <br/>Ver [lista completa de línguas apoiadas](../cognitive-services/text-analytics/language-support.md)|

## <a name="skill-outputs"></a>Saídas de competências

| Saída     | Description |
|--------------------|-------------|
| `keyPhrases` | Uma lista de frases-chave extraídas do texto de entrada. As frases-chave são devolvidas por ordem de importância. |


##  <a name="sample-definition"></a>Definição de amostra

Considere um registo SQL que tenha os seguintes campos:

```json
{
    "content": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
    "language": "en"
}
```

Então a sua definição de habilidade pode ser assim:

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
        "source": "/document/language" 
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

##  <a name="sample-output"></a>Saída de exemplo

Para o exemplo acima, a saída da sua habilidade será escrita para um novo nó na árvore enriquecida chamada "document/myKeyPhrases" uma vez que é o `targetName` que especificamos. Se não especificar `targetName` um, então seria "document/keyPhrases".

#### <a name="documentmykeyphrases"></a>documento/myKeyPhrases 
```json
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
```

Pode utilizar o "document/myKeyPhrases" como entrada noutras habilidades, ou como fonte de um [mapeamento](cognitive-search-output-field-mapping.md)de campo de saída .

## <a name="warnings"></a>Avisos
Se fornecer um código linguístico não suportado, é gerado um aviso e as frases-chave não são extraídas.
Se o seu texto estiver vazio, será produzido um aviso.
Se o seu texto for superior a 50.000 caracteres, apenas os primeiros 50.000 caracteres serão analisados e será emitido um aviso.

## <a name="see-also"></a>Ver também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um skillset](cognitive-search-defining-skillset.md)
+ [Como definir mapeamentos de campos de saída](cognitive-search-output-field-mapping.md)