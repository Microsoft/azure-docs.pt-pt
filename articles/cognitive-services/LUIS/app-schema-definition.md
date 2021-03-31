---
title: Definição de esquema de aplicativo
description: A aplicação LUIS está representada na `.json` ou e inclui todas as `.lu` intenções, entidades, exemplo de expressões, funcionalidades e configurações.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 08/22/2020
ms.openlocfilehash: db4fd52dad82542f20e58ebb3b8526c5be7f2f88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "91327328"
---
# <a name="app-schema-definition"></a>Definição de esquema de aplicativo

A aplicação LUIS está representada na `.json` ou e inclui todas as `.lu` intenções, entidades, exemplo de expressões, funcionalidades e configurações.

## <a name="format"></a>Formato

Quando importar e exportar a aplicação, escolha `.json` `.lu` ou.

|Formato|Informações|
|--|--|
|`.json`| Formato de programação padrão|
|`.lu`|Apoiado pelas [ferramentas bot builder](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md)da Bot Framework.|

## <a name="version-7x"></a>Versão 7.x

* Passando para a versão 7.x, as entidades são representadas como entidades aninhadas de aprendizagem automática.
* Apoio à autoria de entidades aninhadas de aprendizagem automática com `enableNestedChildren` propriedade nas seguintes APIs de autoria:
    * [Adicionar etiqueta](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)
    * [Adicionar rótulo de lote](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09)
    * [Etiquetas de revisão](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c0a)
    * [Sugerir consultas de ponto final para entidades](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2e)
    * [Sugerir consultas de ponto final para intenções](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2d)

```json
{
  "luis_schema_version": "7.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [
  ],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

| elemento                  | Comentário                              |
|--------------------------|--------------------------------------|
| "hierárquicos": [],     | Depreciadas, [utilizem entidades de aprendizagem automática.](luis-concept-entity-types.md)   |
| "Compósitos": [],        | Depreciadas, [utilizem entidades de aprendizagem automática.](luis-concept-entity-types.md) [Referência de entidade composta.](reference-entity-composite.md) |
| "listas fechadas": [],       | [Referência de entidades](reference-entity-list.md) de lista, usada principalmente como recursos para entidades.    |
| "versãoId": "0.1",      | Versão de uma aplicação LUIS.|
| "Nome": "exemplo-app",   | Nome da app LUIS. |
| "desc": "",              | Descrição opcional da aplicação LUIS.  |
| "cultura": "en-us",      | [Linguagem](luis-language-support.md) da app, impacta características subjacentes, tais como entidades pré-construídas, machine-learning e tokenizer.  |
| "TokenizerVersion": "1.0.0", | [Tokenizer](luis-language-support.md#tokenization)  |
| "padrõesInstações": [],   | [Entidade Pattern.any](reference-entity-pattern-any.md)    |
| "regex_entities": [],    |  [Entidade de expressão regular](reference-entity-regular-expression.md)   |
| "Phraselists": [],       |  [Listas de frases (recurso)](luis-concept-feature.md#create-a-phrase-list-for-a-concept)   |
| "regex_features": [],    |  Depreciadas, [utilizem entidades de aprendizagem automática.](luis-concept-entity-types.md) |
| "padrões": [],          |  [Os padrões melhoram a precisão da previsão](luis-concept-patterns.md) com [a sintaxe do padrão](reference-pattern-syntax.md)   |
| "Definições": []           | [Definições da aplicação](luis-reference-application-settings.md)|

## <a name="version-6x"></a>Versão 6.x

* Passando para a versão 6.x, use a nova [entidade de aprendizagem automática](reference-entity-machine-learned-entity.md) para representar as suas entidades.

```json
{
  "luis_schema_version": "6.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-4x"></a>Versão 4.x

```json
{
  "luis_schema_version": "4.0.0",
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "intents": [
    {
      "name": "None"
    }
  ],
  "entities": [],
  "composites": [],
  "closedLists": [],
  "patternAnyEntities": [],
  "regex_entities": [],
  "prebuiltEntities": [],
  "model_features": [],
  "regex_features": [],
  "patterns": [],
  "utterances": [],
  "settings": []
}
```

## <a name="next-steps"></a>Passos seguintes

* Migrar para as [APIs de autoria V3](luis-migration-authoring-entities.md)