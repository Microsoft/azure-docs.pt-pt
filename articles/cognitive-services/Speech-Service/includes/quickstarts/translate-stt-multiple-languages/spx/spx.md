---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: c73ee93d89a350763e8ced490187e1c47f8918de
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806174"
---
## <a name="run-the-speech-cli"></a>Executar o ClI do Discurso

Agora estápronto para executar o Discurso CLI para traduzir o discurso em texto em duas línguas diferentes.

Da linha de comando, mude para o diretório que contém o ficheiro binário CLI da fala, e escreva:

```bash
spx translate --microphone --target de-DE --target es-MX
```

O Discurso CLI traduzirá a língua natural falada inglês em texto impresso em espanhol alemão e (mexicano).
Pressione ENTER para parar a ferramenta.

> [!NOTE]
> O Discurso CLI falha em inglês. Pode escolher uma língua diferente [da tabela Discurso-a-texto](../../../../language-support.md).
> Por exemplo, adicione `--source ja-JP` a reconhecer o discurso japonês.
