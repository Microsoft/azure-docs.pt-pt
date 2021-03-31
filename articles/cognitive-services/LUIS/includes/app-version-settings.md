---
title: incluir ficheiro
ms.topic: include
ms.custom: include file
ms.date: 5/17/2020
ms.openlocfilehash: 79a45825be66b44d869ef741917436de87a1f5d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "83653182"
---
Aprenda [conceitos](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) de normalização e como utilizar APIs [de versão](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) para atualizar estas definições ou utilizar a secção De **Gestão,** **Definições** do portal LUIS.


|Definição de UI|Definição de API|Informações|
|--|--|--|
|Utilizar formação não determinística|`UseAllTrainingData`|O treino usa uma pequena percentagem de amostragem negativa. Se quiser utilizar todos os dados em vez da pequena amostragem negativa, de definido para `true` . |
|Normalizar diacríticos|`NormalizeDiacritics`|A normalização dos diacríticos substitui os caracteres por diacríticos em expressões por caracteres regulares. Esta definição só está disponível em [idiomas](../luis-reference-application-settings.md#diacritics-normalization) que suportam diacríticos.|
|Normalizar pontuação|`NormalizePunctuation`|Normalizar a pontuação significa que antes que os seus modelos sejam treinados e antes que as suas consultas de ponto final sejam previstas, a pontuação será removida das expressões.|
|Normalizar formas de palavras|`NormalizeWordForm`|Ignore formas de palavras além da raiz.|
