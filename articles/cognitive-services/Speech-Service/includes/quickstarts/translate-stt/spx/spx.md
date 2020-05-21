---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: 57383668ef025b46d0bae1f98c5ed5cd75417c63
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715032"
---
## <a name="run-the-spx-tool"></a>Executar a ferramenta SPX

Agora está pronto para executar a ferramenta SPX para traduzir o discurso em texto numa língua diferente.

Da linha de comando, mude para o diretório que contém o ficheiro binário da ferramenta SPX e escreva:

```bash
spx translate --microphone --target de-DE
```

A ferramenta SPX traduzirá a língua natural falada inglês em texto impresso em alemão.
Pressione ENTER para parar a ferramenta.

> [!NOTE]
> A ferramenta SPX falha em inglês. Pode escolher uma língua diferente [da tabela Discurso-a-texto](../../../../language-support.md).
> Por exemplo, adicione `--source ja-JP` a reconhecer o discurso japonês.
