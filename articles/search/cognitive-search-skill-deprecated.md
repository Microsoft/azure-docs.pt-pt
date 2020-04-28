---
title: Habilidades cognitivas depreciadas
titleSuffix: Azure Cognitive Search
description: Esta página contém uma lista de habilidades cognitivas que são consideradas depreciadas e não serão suportadas num futuro próximo em habilidades de Pesquisa Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5f3587e4398be28cbaa2372be720258196bb48ff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72792026"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Habilidades cognitivas depreciadas na Pesquisa Cognitiva Azure

Este documento descreve habilidades cognitivas que são consideradas depreciadas. Utilize o seguinte guia para o conteúdo:

* Nome de habilidade: O nome da habilidade que será depreciada, mapeia para o @odata.type atributo.
* Última versão disponível da API: A última versão da API pública de Pesquisa Cognitiva Azure através da qual podem ser criadas/atualizadas as competências que contêm a habilidade depreciada correspondente.
* Fim do suporte: O último dia após o qual a habilidade correspondente é considerada não suportada. As habilidades previamente criadas devem continuar a funcionar, mas recomenda-se aos utilizadores que migram para longe de uma habilidade depreciada.
* Recomendações: Caminho de migração para a frente para usar uma habilidade apoiada. Os utilizadores são aconselhados a seguir as recomendações para continuar a receber apoio.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Última versão api disponível

2017-11-11-Preview

### <a name="end-of-support"></a>Fim do apoio

15 de fevereiro de 2019

### <a name="recommendations"></a>Recomendações 

Utilize [microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) em vez disso. Fornece a maior parte da funcionalidade do NomeadoEntityRecognitionSkill com uma qualidade mais elevada. Também tem informação mais rica nos seus complexos campos de produção.

Para migrar para a Habilidade de Reconhecimento de [Entidades,](cognitive-search-skill-entity-recognition.md)terá de realizar uma ou mais das seguintes alterações à sua definição de competência. Pode atualizar a definição de habilidade utilizando a [API update skillset](https://docs.microsoft.com/rest/api/searchservice/update-skillset).

> [!NOTE]
> Atualmente, a pontuação da confiança como conceito não é suportada. O `minimumPrecision` parâmetro existe `EntityRecognitionSkill` no futuro uso e para retrocompatibilidade.

1. *(Obrigatório)* Mude `@odata.type` o `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` `"#Microsoft.Skills.Text.EntityRecognitionSkill"`de .

2. *(Opcional)* Se estiver a utilizar `entities` a saída, utilize `namedEntities` a `EntityRecognitionSkill` saída complexa da recolha a partir da. Pode usar `targetName` a definição de habilidade para mapeá-la para uma anotação chamada `entities`.

3. *(Opcional)* Se não especificar explicitamente `categories` `EntityRecognitionSkill` o , pode devolver diferentes tipos de categorias para além das que foram apoiadas pelo `NamedEntityRecognitionSkill`. Se este comportamento for indesejável, certifique-se de definir explicitamente o `categories` parâmetro para `["Person", "Location", "Organization"]`.

    _Definições de migração da amostra_

    * Migração simples

        _(Antes) Definição de competências de Reconhecimento de Entidades Nomeadas_
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
        _(Depois) Definição de competências de Reconhecimento de Entidades_
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

        _(Antes) Definição de competências de Reconhecimento de Entidades Nomeadas_
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
        _(Depois) Definição de competências de Reconhecimento de Entidades_
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
+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
+ [Habilidade de Reconhecimento de Entidades](cognitive-search-skill-entity-recognition.md)
