---
title: incluir ficheiro
ms.topic: include
ms.custom: include file
ms.date: 5/17/2020
ms.openlocfilehash: 79a45825be66b44d869ef741917436de87a1f5d0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653182"
---
Aprenda [conceitos](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) de normalização e como utilizar a [versão](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) APIs para atualizar estas definições ou utilizar a secção **Gerir** do portal LUIS, **página Definições.**


|Definição de UI|Definição de API|Informações|
|--|--|--|
|Utilizar formação não determinística|`UseAllTrainingData`|O treino utiliza uma pequena percentagem de amostragem negativa. Se quiser utilizar todos os dados em vez da pequena amostragem negativa, definida para `true` . |
|Normalizar os diacríticos|`NormalizeDiacritics`|Normalizar os diacríticos substitui os personagens por diacritics em expressões por caracteres regulares. Esta definição só está disponível em [idiomas](../luis-reference-application-settings.md#diacritics-normalization) que suportem diacríticos.|
|Normalizar a pontuação|`NormalizePunctuation`|Normalizar a pontuação significa que antes que os seus modelos sejam treinados e antes que as suas consultas de ponto final sejam previstas, a pontuação será removida das expressões.|
|Normalizar os formulários de palavras|`NormalizeWordForm`|Ignore formas de palavra sem raiz.|
