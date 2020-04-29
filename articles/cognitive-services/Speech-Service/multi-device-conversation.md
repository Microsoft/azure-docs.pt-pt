---
title: Conversação multi-dispositivo (Pré-visualização) - Serviço de Discurso
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 7c30ee2ef4a6ab0cd4241cac921a59eeadf5ce17
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81401050"
---
# <a name="what-is-multi-device-conversation-preview"></a>O que é conversação multi-dispositivo (Pré-visualização)?

**A conversa ção multi-dispositivo** facilita a criação de uma conversa de fala ou texto entre vários clientes e coordena as mensagens enviadas entre eles.

Com **conversas multi-dispositivos,** pode:

- Ligue vários clientes à mesma conversa e gerencie o envio e receção de mensagens entre eles.
- Transcretem facilmente o áudio de cada cliente e enviem a transcrição para os outros, com tradução opcional.
- Envie facilmente mensagens de texto entre clientes, com tradução opcional.

Pode construir uma funcionalidade ou solução que funcione através de uma série de dispositivos. Cada dispositivo pode enviar mensagens de forma independente (transcrições de mensagens áudio ou instantâneas) para todos os outros dispositivos.

Enquanto a [**Transcrição de Conversações**](conversation-transcription.md) funciona num único dispositivo com uma matriz de microfone multicanal, a **Conversa ção multi-dispositivo** é adequada para cenários com vários dispositivos, cada um com um único microfone.

>[!IMPORTANT]
> A conversação multi-dispositivo **não** suporta o envio de ficheiros áudio entre clientes: apenas a transcrição e/ou tradução.

## <a name="key-features"></a>Principais funcionalidades

- **Transcrição em tempo real** – Todos receberão uma transcrição da conversa, para que possam seguir o texto em tempo real ou guardá-lo para mais tarde.
- **Tradução em tempo real** – Com mais de 60 [línguas suportadas](language-support.md#text-languages) para tradução de texto, os utilizadores podem traduzir a conversa para a sua língua preferida.
- **Transcrições legíveis** – A transcrição e tradução são fáceis de seguir, com pontuação e quebras de frases.
- **Entrada** de voz ou texto – Cada utilizador pode falar ou escrever no seu próprio dispositivo, dependendo das capacidades de suporte da linguagem ativadas para o idioma escolhido do participante. Por favor, consulte o suporte à [linguagem](language-support.md#speech-to-text).
- **Retransmissão de mensagens** - O serviço de conversação multi-dispositivo sairá mensagens enviadas por um cliente a todos os outros, no idioma(s) da sua escolha.
- **Identificação da mensagem** – Cada mensagem que os utilizadores recebem na conversação será marcada com o apelido do utilizador que a enviou.

## <a name="use-cases"></a>Casos de utilização

### <a name="lightweight-conversations"></a>Conversas leves

Criar e juntar uma conversa é fácil. Um utilizador atuará como o 'anfitrião' e criará uma conversação, que gera um código de conversação de cinco letras aleatório e um código QR. Todos os outros utilizadores podem juntar-se à conversa digitando o código de conversação ou digitalizando o código QR. 

Uma vez que os utilizadores se juntam através do código de conversação e não são obrigados a partilhar informações de contacto, é fácil criar conversas rápidas no local.

### <a name="inclusive-meetings"></a>Reuniões inclusivas

A transcrição e tradução em tempo real podem ajudar a tornar as conversas acessíveis para pessoas que falam línguas diferentes e/ou são surdas ou difíceis de ouvir. Cada pessoa também pode participar ativamente na conversa, falando a sua língua preferida ou enviando mensagens instantâneas.

### <a name="presentations"></a>Apresentações

Também pode fornecer legendas para apresentações e palestras tanto no ecrã como nos próprios dispositivos dos membros do público. Depois de o público se juntar ao código de conversação, podem ver a transcrição na sua língua preferida, no seu próprio dispositivo.

> [!NOTE]
> Para ver um exemplo, consulte o Tradutor de [Apresentação,](https://www.microsoft.com/translator/apps/presentation-translator/)um add-in PowerPoint que utiliza o serviço de conversação multi-dispositivos. Pode transferi-lo [aqui](https://www.microsoft.com/download/details.aspx?id=55024).

## <a name="how-it-works"></a>Como funciona

Todos os clientes usarão o SDK de Discurso para criar ou juntar-se a uma conversa. O Speech SDK interage com o serviço de conversação multi-dispositivos, que gere a vida útil de uma conversa, incluindo a lista de participantes, o idioma escolhido de cada cliente e mensagens enviadas.  

Cada cliente pode enviar mensagens áudio ou instantâneas. O serviço utilizará o reconhecimento da fala para converter áudio em texto e enviar mensagens instantâneas como está. Se os clientes escolherem idiomas diferentes, o serviço traduzirá todas as mensagens para o ou os idiomas especificados de cada cliente.

![Diagrama de visão geral de conversação multi-dispositivo](media/scenarios/multi-device-conversation.png)

## <a name="overview-of-conversation-host-and-participant"></a>Visão geral da Conversa, Anfitrião e Participante

Uma **conversa** é uma sessão que um utilizador inicia para que os outros utilizadores participantes se juntem. Todos os clientes se ligam à conversa utilizando o código de **conversação**de cinco letras .

Cada conversa cria metadados que incluem:
-    Selos temporais de quando a conversa começou e terminou
-    Lista de todos os participantes na conversa, que inclui o apelido escolhido de cada utilizador e a língua primária para a entrada de discurso ou texto.


Existem dois tipos de utilizadores numa conversa: **anfitrião** e **participante.**

O **anfitrião** é o utilizador que inicia uma conversa, e que age como administrador dessa conversa.
- Cada conversa só pode ter um anfitrião
- O hospedeiro deve estar ligado à conversa durante a conversação. Se o anfitrião deixar a conversa, a conversa terminará para todos os outros participantes.
- O hospedeiro tem alguns controlos extra para gerir a conversa: 
    - Bloqueie a conversa - evite que participantes adicionais se juntem
    - Silenciar todos os participantes - evite que outros participantes enviem mensagens para a conversação, seja transcrita de discurso ou mensagens instantâneas
    - Silenciar participantes individuais
    - Desamuta todos os participantes
    - Unmute participantes individuais

Um **participante** é um utilizador que se junta a uma conversa.
- Um participante pode sair e voltar a juntar-se à mesma conversa a qualquer momento, sem terminar a conversa para outros participantes.
- Os participantes não podem bloquear a conversação ou silenciar/desmutar outros

> [!NOTE]
> Cada conversa pode ter até 100 participantes, dos quais 10 podem estar a falar simultaneamente a qualquer momento.

## <a name="language-support"></a>Suporte de idiomas

Ao criar ou aderir a uma conversação, cada utilizador deve escolher um **idioma primário**: o idioma em que falará e enviará mensagens instantâneas, bem como o idioma em que verá as mensagens de outros utilizadores.

Existem dois tipos de línguas: **discurso a texto** e **apenas texto:**
- Se o utilizador escolher uma linguagem **fala-a-texto** como a sua língua primária, então poderá utilizar a entrada de fala e texto na conversação.

- Se o utilizador escolher um idioma **apenas por texto,** só poderá utilizar a entrada de texto e enviar mensagens instantâneas na conversação. As línguas apenas com texto são as línguas que são suportadas para a tradução de texto, mas não para o texto. Pode ver idiomas disponíveis na página de [suporte linguístico.](supported-languages.md)

Além da sua língua primária, cada participante também pode especificar idiomas adicionais para traduzir a conversa.

Abaixo está um resumo do que o utilizador será capaz de fazer numa conversa multi-dispositivo, com base na sua língua primária escolhida.


| O que o utilizador pode fazer na conversa | Conversão de voz em texto | Só de texto |
|-----------------------------------|----------------|------|
| Use a entrada da fala | ✔️ | ❌ |
| Enviar mensagens instantâneas | ✔️ | ✔️ |
| Traduzir a conversa | ✔️ | ✔️ |

> [!NOTE]
> Para listas de línguas disponíveis de tradução de texto e texto, consulte [as línguas apoiadas.](supported-languages.md)



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Traduzir conversas em tempo real](quickstarts/multi-device-conversation.md)
