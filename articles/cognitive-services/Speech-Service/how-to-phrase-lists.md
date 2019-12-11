---
title: Listas de frases-serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como fornecer o serviço de fala com uma lista de frases usando o objeto `PhraseListGrammar` para melhorar os resultados de reconhecimento de fala em texto.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rhurey
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 2ceb53b50810aef501278710ae990c57fc45030c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74971677"
---
# <a name="phrase-lists-for-speech-to-text"></a>Listas de frases para conversão de fala em texto

Ao fornecer o serviço de fala com uma lista de frases, você pode melhorar a precisão do reconhecimento de fala. As listas de frases são usadas para identificar frases conhecidas em dados de áudio, como o nome de uma pessoa ou um local específico.

Por exemplo, se você tiver um comando "mover para" e um possível destino de "para trás" que pode ser falado, você poderá adicionar uma entrada de "mover para o mais à frente". A adição de uma frase aumentará a probabilidade de quando o áudio for reconhecido de "mover para o mais para cima" será reconhecido em vez de "ir para".

Palavras únicas ou frases completas podem ser adicionadas a uma lista de frases. Durante o reconhecimento, uma entrada em uma lista de frases será usada se uma correspondência exata para a frase inteira for incluída no áudio como uma frase separada. Se uma correspondência exata à frase não for encontrada, o reconhecimento não será assistido.

>[!Note]
> Atualmente, as listas de frases dão suporte apenas ao inglês para conversão de fala em texto.

## <a name="how-to-use-phrase-lists"></a>Como usar listas de frases

Os exemplos a seguir ilustram como criar uma lista de frases usando o objeto `PhraseListGrammar`.

::: zone pivot="programming-language-csharp"

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

::: zone-end

::: zone pivot="programming-language-more"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> O número máximo de listas de frases que o serviço de fala usará para corresponder à fala é de 1024 frases.

Você também pode limpar as frases associadas à `PhraseListGrammar` chamando Clear ().

::: zone pivot="programming-language-csharp"

```cs
phraseList.Clear();
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
phraselist->Clear();
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
phraseListGrammar.clear();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar.clear()
```

::: zone-end

::: zone pivot="programming-language-more"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> As alterações em um objeto `PhraseListGrammar` entram em vigor no próximo reconhecimento ou após uma reconexão com o serviço de fala.

## <a name="next-steps"></a>Passos seguintes

* [Documentação de referência do SDK de fala](speech-sdk.md)
