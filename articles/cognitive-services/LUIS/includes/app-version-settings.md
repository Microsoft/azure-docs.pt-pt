---
title: incluir ficheiro
ms.topic: include
ms.custom: include file
ms.date: 5/04/2020
ms.openlocfilehash: 819dfa3127eb91a2f08687a76ea0586439b650a4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590996"
---
|Nível|Definição de UI|Definição de API|Informações|
|--|--|--|--|
|Aplicação|Tornar os pontos finais públicos|`Public`|Qualquer pessoa pode aceder à sua aplicação pública se tiver uma chave de previsão e conhecer o ID da sua aplicação. |
|Versão|Utilizar formação não determinística|`UseAllTrainingData`|O treino utiliza uma pequena percentagem de amostragem negativa. Se quiser utilizar todos os dados em vez da pequena amostragem negativa, definida para `true` . |
|Versão|Normalizar os diacríticos|`NormalizeDiacritics`|Normalizar os diacríticos substitui os personagens por diacritics em expressões por caracteres regulares.|
|Versão|Normalizar a pontuação|`NormalizePunctuation`|Normalizar a pontuação significa que antes que os seus modelos sejam treinados e antes que as suas consultas de ponto final sejam previstas, a pontuação será removida das expressões.|
|Versão|Normalizar os formulários de palavras|`NormalizeWordForm`|Ignore formas de palavra sem raiz.|

Aprenda [conceitos](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) de normalização e como usar as APIs de [aplicação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58aeface39e2bb03dcd5909e) e [versão](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) para atualizar estas definições ou utilizar a secção **Gerir** do portal LUIS, página de Definições de **Aplicações.**