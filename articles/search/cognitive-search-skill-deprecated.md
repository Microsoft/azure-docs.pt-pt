---
title: Competências cognitivas preprecadas
titleSuffix: Azure Cognitive Search
description: Esta página contém uma lista de habilidades cognitivas que são consideradas depreciadas e não serão suportadas num futuro próximo em habilidades de Pesquisa Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 85f3b9862bd8155c1a4b11860dc82d92a2f9e810
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88936100"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Competências cognitivas preprecadas na Pesquisa Cognitiva Azure

Este documento descreve habilidades cognitivas que são consideradas depreciadas. Utilize o seguinte guia para o conteúdo:

* Nome da habilidade: O nome da habilidade que será depreciada, mapeia para o @odata.type atributo.
* Última versão disponível da API: A última versão da API pública de Pesquisa Cognitiva Azure através da qual os skillsets que contêm a habilidade preiférida correspondente podem ser criados/atualizados.
* Fim do apoio: O último dia após o qual a competência correspondente é considerada não suportada. As competências criadas anteriormente ainda devem continuar a funcionar, mas os utilizadores são recomendados a migrar para longe de uma habilidade precída.
* Recomendações: Caminho de migração para a frente para usar uma habilidade apoiada. Os utilizadores são aconselhados a seguir as recomendações para continuarem a receber suporte.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.text.namedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Última versão api disponível

2017-11-11-Preview

### <a name="end-of-support"></a>Fim do apoio

15 de fevereiro de 2019

### <a name="recommendations"></a>Recomendações 

Utilize [microsoft.skills.text.EntityRecognitionSkill.](cognitive-search-skill-entity-recognition.md) Fornece a maior parte da funcionalidade do Denominado EntidadeRecognitionSkill com uma maior qualidade. Também tem informação mais rica nos seus complexos campos de produção.

Para migrar para a Habilidade de Reconhecimento de [Entidade,](cognitive-search-skill-entity-recognition.md)terá de efetuar uma ou mais das seguintes alterações à sua definição de habilidade. Pode atualizar a definição de habilidades utilizando a [API de Atualização Skillset](/rest/api/searchservice/update-skillset).

> [!NOTE]
> Atualmente, a pontuação de confiança como conceito não é apoiada. O `minimumPrecision` parâmetro existe no para uso futuro e para `EntityRecognitionSkill` retrocompatibilidade.

1. *(Obrigatório)* Mude o `@odata.type` `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` de. para `"#Microsoft.Skills.Text.EntityRecognitionSkill"` .

2. *(Opcional)* Se estiver a utilizar a `entities` saída, utilize a `namedEntities` saída complexa de recolha a partir do em vez `EntityRecognitionSkill` disso. Pode utilizar a `targetName` definição de habilidade para mapeá-la para uma anotação chamada `entities` .

3. *(Opcional)* Se não especificar explicitamente o `categories` , `EntityRecognitionSkill` pode devolver diferentes tipos de categorias para além das que foram suportadas pelo `NamedEntityRecognitionSkill` . Se este comportamento for indesejável, certifique-se de definir explicitamente o `categories` parâmetro para `["Person", "Location", "Organization"]` .

    _Definições de migração de amostras_

    * Migração simples

        _(Antes) Definição de habilidades de reconhecimento de entidades nomeadas_
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
        _(Depois) Definição de habilidades de reconhecimento de entidades_
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

        _(Antes) Definição de habilidades de reconhecimento de entidades nomeadas_
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
        _(Depois) Definição de habilidades de reconhecimento de entidades_
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

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um skillset](cognitive-search-defining-skillset.md)
+ [Competência de Reconhecimento de Entidades](cognitive-search-skill-entity-recognition.md)