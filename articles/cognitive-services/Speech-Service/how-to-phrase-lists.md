---
title: Listas de frases - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como fornecer o serviço de Voz com uma Lista de Frases utilizando o `PhraseListGrammar` objeto para melhorar os resultados do reconhecimento de voz a texto.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: trbye
zone_pivot_groups: programming-languages-speech-services-one-nomore-no-go
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1e0b345eb4f1d6b3ab8ba917794b6878180ac558
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320953"
---
# <a name="phrase-lists-for-speech-to-text"></a>Listas de frases para discurso-a-texto

Ao fornecer ao serviço de Discurso uma lista de frases, pode melhorar a precisão do reconhecimento da fala. As listas de frases são usadas para identificar frases conhecidas em dados áudio, como o nome de uma pessoa ou uma localização específica.

Como exemplo, se tiver um comando "Move to" e um possível destino de "Ward" que possa ser falado, pode adicionar uma entrada de "Move to Ward". A adição de uma frase aumentará a probabilidade de, quando o áudio for reconhecido, "Mover-se para Ward" ser reconhecido em vez de "Mover-se para".

Palavras simples ou frases completas podem ser adicionadas a uma Lista de Frases. Durante o reconhecimento, uma entrada numa lista de frases é usada se uma correspondência exata para toda a frase for incluída no áudio como uma frase separada. Se não for encontrado um jogo exato com a frase, o reconhecimento não é assistido.

>[!Note]
> Atualmente, as Listas de Frases suportam apenas o inglês para falar-a-texto.

## <a name="how-to-use-phrase-lists"></a>Como usar listas de frases

As amostras abaixo ilustram como construir uma Lista de Frases utilizando o `PhraseListGrammar` objeto.

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

::: zone pivot="programming-language-javascript"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> O número máximo de Listas de Frases que o serviço de Discurso utilizará para combinar discursos é de 1024 frases.

Também pode limpar as frases associadas ao `PhraseListGrammar` chamado claro().

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

::: zone pivot="programming-language-javascript"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> As alterações a um `PhraseListGrammar` objeto têm efeito no próximo reconhecimento ou na sequência de uma reconexão ao serviço Discurso.

## <a name="next-steps"></a>Passos seguintes

* [Documentação de referência do SDK de fala](speech-sdk.md)
