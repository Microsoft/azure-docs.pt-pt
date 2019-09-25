---
title: Habilidades cognitivas preteridas-Azure Search
description: Esta página contém uma lista de habilidades de pesquisa cognitiva que são consideradas preteridas e não terão suporte em um futuro próximo.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: 1e78852ec8b92f1a9e37a4dbcbbcb371c0ac0f97
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265444"
---
# <a name="deprecated-cognitive-search-skills"></a>Habilidades de pesquisa cognitiva preteridas

Este documento descreve as habilidades cognitivas que são consideradas preteridas. Use o seguinte guia para o conteúdo:

* Nome da habilidade: O nome da habilidade que será preterida, que é mapeado para o @odata.type atributo.
* Última versão da API disponível: A última versão da API pública do Azure Search por meio da qual habilidades que contém as habilidades preteridas correspondentes pode ser criada/atualizada.
* Fim do suporte: O último dia após o qual a habilidade correspondente é considerada sem suporte. O habilidades criado anteriormente ainda deve continuar a funcionar, mas é recomendável que os usuários migrem para longe de uma habilidade preterida.
* Recommendations Caminho de migração para encaminhar para usar uma habilidade com suporte. Os usuários são aconselhados a seguir as recomendações para continuar a receber suporte.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Última versão da API disponível

2017-11-11-Preview

### <a name="end-of-support"></a>Fim do suporte

15 de fevereiro de 2019

### <a name="recommendations"></a>Recomendações 

Use [Microsoft. Skills. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) em vez disso. Ele fornece a maior parte da funcionalidade do NamedEntityRecognitionSkill em uma qualidade mais alta. Ele também tem informações mais ricas em seus campos de saída complexos.

Para migrar para a [habilidade de reconhecimento de entidade](cognitive-search-skill-entity-recognition.md), você precisará executar uma ou mais das seguintes alterações em sua definição de habilidade. Você pode atualizar a definição de habilidade usando a [API de atualização do Skill](https://docs.microsoft.com/rest/api/searchservice/update-skillset).

> [!NOTE]
> Atualmente, não há suporte para a pontuação de confiança como um conceito. O `minimumPrecision` parâmetro existe `EntityRecognitionSkill` no para uso futuro e para compatibilidade com versões anteriores.

1. *(Obrigatório)* Altere o `@odata.type` de `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` para `"#Microsoft.Skills.Text.EntityRecognitionSkill"`.

2. *(Opcional)* Se você estiver usando a `entities` saída, use a `namedEntities` saída de coleção `EntityRecognitionSkill` complexa do em vez disso. Você pode usar o `targetName` na definição de habilidade para mapeá-lo para uma anotação `entities`chamada.

3. *(Opcional)* Se você não especificar explicitamente o `categories`, o `EntityRecognitionSkill` poderá retornar um tipo diferente de categorias além daquelas com suporte no `NamedEntityRecognitionSkill`. Se esse comportamento for indesejável, certifique-se de definir `categories` explicitamente o `["Person", "Location", "Organization"]`parâmetro como.

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

## <a name="see-also"></a>Consulte também

+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
+ [Habilidade de reconhecimento de entidade](cognitive-search-skill-entity-recognition.md)
