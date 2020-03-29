---
title: Listas de Frases - Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Aprenda a fornecer o serviço de `PhraseListGrammar` Fala com uma Lista de Frases usando o objeto para melhorar os resultados de reconhecimento de discurso-texto.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: f84ea6b2b0f1e8246e1d765e54f663cd01f29b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77560852"
---
# <a name="phrase-lists-for-speech-to-text"></a>Listas de frases para discurso-texto

Ao fornecer ao serviço da Fala uma lista de frases, pode melhorar a precisão do reconhecimento da fala. As listas de frases são usadas para identificar frases conhecidas em dados áudio, como o nome de uma pessoa ou um local específico.

Como exemplo, se tiver um comando "Move to" e um possível destino de "Ward" que possa ser falado, pode adicionar uma entrada de "Move to Ward". Adicionar uma frase aumentará a probabilidade de que quando o áudio for reconhecido, "Move to Ward" será reconhecido em vez de "Mover-se para".

Palavras simples ou frases completas podem ser adicionadas a uma Lista de Frases. Durante o reconhecimento, uma entrada numa lista de frases é usada se uma correspondência exata para toda a frase for incluída no áudio como uma frase separada. Se não for encontrado um fósforo exato com a frase, o reconhecimento não é assistido.

>[!Note]
> Atualmente, as Listas de Frases suportam apenas o inglês para o discurso-a-texto.

## <a name="how-to-use-phrase-lists"></a>Como usar listas de frases

As amostras abaixo ilustram como `PhraseListGrammar` construir uma Lista de Frases usando o objeto.

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
> O número máximo de Listas de Frases que o serviço de Fala usará para combinar a fala é de 1024 frases.

Também pode limpar as frases `PhraseListGrammar` associadas ao chamar claro().

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
> As alterações a um `PhraseListGrammar` objeto têm efeito no próximo reconhecimento ou na sequência de uma reconexão ao serviço da Fala.

## <a name="next-steps"></a>Passos seguintes

* [Documentação de referência do SDK do discurso](speech-sdk.md)
