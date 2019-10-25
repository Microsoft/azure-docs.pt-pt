---
title: Habilidades cognitivas preteridas
titleSuffix: Azure Cognitive Search
description: Esta página contém uma lista de habilidades cognitivas que são consideradas preteridas e que não terão suporte em um futuro próximo no Azure Pesquisa Cognitiva habilidades.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5f3587e4398be28cbaa2372be720258196bb48ff
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792026"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Habilidades cognitivas preteridas no Azure Pesquisa Cognitiva

Este documento descreve as habilidades cognitivas que são consideradas preteridas. Use o seguinte guia para o conteúdo:

* Nome da habilidade: o nome da habilidade que será preterida, que é mapeado para o atributo @odata.type.
* Última versão da API disponível: a última versão da API pública do Azure Pesquisa Cognitiva por meio da qual habilidades que contém as habilidades preteridas correspondentes pode ser criada/atualizada.
* Fim do suporte: o último dia após o qual a habilidade correspondente é considerada sem suporte. O habilidades criado anteriormente ainda deve continuar a funcionar, mas é recomendável que os usuários migrem para longe de uma habilidade preterida.
* Recomendações: caminho de migração para frente para usar uma habilidade com suporte. Os usuários são aconselhados a seguir as recomendações para continuar a receber suporte.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft. Skills. Text. NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Última versão da API disponível

2017-11-11-Preview

### <a name="end-of-support"></a>Fim do suporte

15 de fevereiro de 2019

### <a name="recommendations"></a>Recomendações 

Use [Microsoft. Skills. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) em vez disso. Ele fornece a maior parte da funcionalidade do NamedEntityRecognitionSkill em uma qualidade mais alta. Ele também tem informações mais ricas em seus campos de saída complexos.

Para migrar para a [habilidade de reconhecimento de entidade](cognitive-search-skill-entity-recognition.md), você precisará executar uma ou mais das seguintes alterações em sua definição de habilidade. Você pode atualizar a definição de habilidade usando a [API de atualização do Skill](https://docs.microsoft.com/rest/api/searchservice/update-skillset).

> [!NOTE]
> Atualmente, não há suporte para a pontuação de confiança como um conceito. O parâmetro `minimumPrecision` existe no `EntityRecognitionSkill` para uso futuro e para compatibilidade com versões anteriores.

1. *(Obrigatório)* Altere o `@odata.type` de `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` para `"#Microsoft.Skills.Text.EntityRecognitionSkill"`.

2. *(Opcional)* Se você estiver usando a saída de `entities`, use a saída de coleção `namedEntities` complexa da `EntityRecognitionSkill` em vez disso. Você pode usar o `targetName` na definição de habilidade para mapeá-lo para uma anotação chamada `entities`.

3. *(Opcional)* Se você não especificar explicitamente o `categories`, o `EntityRecognitionSkill` poderá retornar um tipo diferente de categorias além daquelas com suporte no `NamedEntityRecognitionSkill`. Se esse comportamento for indesejável, certifique-se de definir explicitamente o parâmetro `categories` como `["Person", "Location", "Organization"]`.

    _Exemplos de definições de migração_

    * Migração simples

        _Anterior Definição de habilidade do NamedEntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
        _Após Definição de habilidade do EntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
    
    * Migração ligeiramente complicada

        _Anterior Definição de habilidade do NamedEntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "entities"
            }
            ]
        }
        ```
        _Após Definição de habilidade do EntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "namedEntities",
                "targetName": "entities"
            }
            ]
        }
        ```

## <a name="see-also"></a>Ver também

+ [Habilidades internas](cognitive-search-predefined-skills.md)
+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
+ [Habilidade de reconhecimento de entidade](cognitive-search-skill-entity-recognition.md)
