---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 9f1da13efc9f75d14fdf2d158e8b7547d4a9fa94
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715320"
---
## <a name="enable-microphone"></a>Ativar o microfone

Ligue e ligue o microfone do PC e desligue todas as aplicações que também possam utilizar o microfone. Alguns computadores têm um microfone incorporado, enquanto outros requerem configuração de um dispositivo Bluetooth.

## <a name="run-the-spx-tool"></a>Executar a ferramenta SPX

Agora está pronto para executar a ferramenta SPX para reconhecer o discurso do microfone.

1. **Inicie a sua aplicação** - Da linha de comando, mude para o diretório que contém o ficheiro binário da ferramenta SPX e escreva:
    ```bash
    spx recognize --microphone
    ```

    > [!NOTE]
    > A ferramenta SPX falha em inglês. Pode escolher uma língua diferente [da tabela Discurso-a-texto](../../../../language-support.md).
    > Por exemplo, adicione `--source de-DE` a reconhecer o discurso alemão.

2. **Iniciar o reconhecimento** - Fale ao microfone. Verá a transcrição das suas palavras em texto em tempo real. A ferramenta SPX para após um período de silêncio, ou quando premir ctrl-C.
