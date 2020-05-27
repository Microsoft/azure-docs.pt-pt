---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: a9beaea70dd3c8ce852344b11a50c5f20e5f8ca2
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806394"
---
## <a name="enable-microphone"></a>Ativar o microfone

Ligue e ligue o microfone do PC e desligue todas as aplicações que também possam utilizar o microfone. Alguns computadores têm um microfone incorporado, enquanto outros requerem configuração de um dispositivo Bluetooth.

## <a name="run-the-speech-cli"></a>Executar o ClI do Discurso

Agora estás pronto para executar o DISCURSO CLI para reconhecer o discurso do teu microfone.

1. **Inicie a sua aplicação** - Da linha de comando, mude para o diretório que contém o ficheiro binário DO Discurso CLI e escreva:
    ```bash
    spx recognize --microphone
    ```

    > [!NOTE]
    > O Discurso CLI falha em inglês. Pode escolher uma língua diferente [da tabela Discurso-a-texto](../../../../language-support.md).
    > Por exemplo, adicione `--source de-DE` a reconhecer o discurso alemão.

2. **Iniciar o reconhecimento** - Fale ao microfone. Verá a transcrição das suas palavras em texto em tempo real. O Discurso CLI parará após um período de silêncio, ou quando premir ctrl-C.
