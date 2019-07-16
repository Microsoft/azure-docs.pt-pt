---
title: Listas de frase - serviços de voz
titlesuffix: Azure Cognitive Services
description: Aprenda a fornecer os serviços de voz com uma lista de frase, utilizando o `PhraseListGrammar` objeto para melhorar os resultados de reconhecimento de voz em texto.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rhurey
ms.openlocfilehash: d73a63a8f58e14149121d0860268fc23930001bf
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226354"
---
# <a name="phrase-lists-for-speech-to-text"></a>Listas de frase de voz em texto

Ao fornecer os serviços de voz com uma lista de frase, pode melhorar a precisão de reconhecimento de fala. Listas de frase são usadas para identificar as frases conhecidas em dados de áudio, como o nome de uma pessoa ou uma localização específica.

Por exemplo, se tiver um comando "Mover para" e um destino possível de "Ward", que pode ser falado, pode adicionar uma entrada de "Mover para Ward". Adicionar uma frase irá aumentar a probabilidade de que quando o áudio é reconhecido que "Mover para Ward" será reconhecido em vez de "Mudança para os".

Únicas palavras ou frases completas podem ser adicionados a uma lista de frase. Durante o reconhecimento, uma entrada numa lista de frase é utilizada se uma correspondência exata está incluída no áudio. Criar no exemplo anterior, se a lista de frase inclui "Mover para Ward" e a frase capturada é a "Progride lentamente", em seguida, o resultado de reconhecimento será "Mover para Ward lentamente".

>[!Note]
> Atualmente, a lista de frase suporta apenas em inglês para conversão de voz em texto.

## <a name="how-to-use-phrase-lists"></a>Como utilizar a lista de frase

Os exemplos abaixo mostram como criar uma lista de frase, utilizando o `PhraseListGrammar` objeto.

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

>[!Note]
> O número máximo de frase apresenta uma lista de que o serviço de voz utilizará para corresponder à voz é 1024 frases.

Pode também limpar as frases associadas a `PhraseListGrammar` por limpar (chamada).

```C++
phraselist->Clear();
```

```cs
phraseList.Clear();
```

```Python
phrase_list_grammar.clear()
```

```JavaScript
phraseListGrammar.clear();
```

```Java
phraseListGrammar.clear();
```

> [!NOTE]
> Alterado para um `PhraseListGrammar` take efeito sobre o reconhecimento seguinte ou seguir uma nova ligação para os serviços de voz de objeto.

## <a name="next-steps"></a>Passos Seguintes

* [Documentação de referência do SDK de voz](speech-sdk.md)
