---
title: Capacidades cognitivas preteridas - Azure Search
description: Esta página contém uma lista de pesquisa cognitiva as competências que são consideradas preterida e não será suportada em breve.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: f773cd298c8faaac90b30d88a74e8ddcb51c3afa
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021960"
---
# <a name="deprecated-cognitive-search-skills"></a>Habilidades de pesquisa cognitiva preterido

Este documento descreve as capacidades cognitivas que são consideradas preteridas. Utilize o guia seguinte para o conteúdo:

* Nome de habilidades: O nome da habilidade que vai ser preterido, mapeia para o @odata.type atributo.
* Última versão de api disponíveis: A última versão do Azure pesquise API pública através da qual conjuntos de habilidades que contém a habilidade de preterido correspondente pode ser criado/atualizado.
* Fim do suporte: O último dia após o qual a habilidade de correspondente é considerada não suportado. Criado anteriormente conjuntos de habilidades ainda deverão continuar a funcionar, mas os utilizadores são recomendados para evitar uma habilidade preterida.
* Recomendações: Caminho de migração para a frente para utilizar uma habilidade suportada. Os usuários são aconselhados a seguir as recomendações para continuarão a receber suporte.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Última versão de api disponíveis

2019-05-06-pré-visualização

### <a name="end-of-support"></a>Fim do suporte

15 de Fevereiro de 2019

### <a name="recommendations"></a>Recomendações 

Uso [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) em vez disso. Ele fornece a maior parte da funcionalidade de NamedEntityRecognitionSkill numa maior qualidade. Também tem informações mais detalhadas em seus campos de saída complexa.

Para migrar para o [habilidade de reconhecimento de entidades](cognitive-search-skill-entity-recognition.md), terá de realizar uma ou mais das seguintes alterações à sua definição de habilidade. Pode atualizar a definição de habilidades utilizando o [atualizar o conjunto de capacidades API](https://docs.microsoft.com/rest/api/searchservice/update-skillset).

> [!NOTE]
> Atualmente, a pontuação de confiança, como um conceito não é suportada. O `minimumPrecision` parâmetro existe no `EntityRecognitionSkill` para utilização futura e para efeitos compatibilidade.

1. *(Obrigatório)*  Alteração a `@odata.type` partir `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` para `"#Microsoft.Skills.Text.EntityRecognitionSkill"`.

2. *(Opcional)*  Se estiver fazendo utilizar do `entities` de saída, utilize o `namedEntities` saída de coleção complexa do `EntityRecognitionSkill` em vez disso. Pode utilizar o `targetName` a habilidade definição para mapeá-lo a uma anotação chamado `entities`.

3. *(Opcional)*  Se não especificar explicitamente o `categories`, o `EntityRecognitionSkill` pode retornar um tipo diferente de categorias, além de os que eram suportados pelo `NamedEntityRecognitionSkill`. Se este comportamento é indesejável, certifique-se definir explicitamente o `categories` parâmetro `["Person", "Location", "Organization"]`.

    _Definições de migração de exemplo_

    * Migração Simple

        _(Antes) Definição de habilidades NamedEntityRecognition_
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
        _(Depois) Definição de habilidades EntityRecognition_
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
    
    * Um pouco complicada de migração

        _(Antes) Definição de habilidades NamedEntityRecognition_
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
        _(Depois) Definição de habilidades EntityRecognition_
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

+ [Competências predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
+ [Habilidade de reconhecimento de entidades](cognitive-search-skill-entity-recognition.md)
