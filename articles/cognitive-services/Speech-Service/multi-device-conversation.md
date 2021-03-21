---
title: Conversação multi-dispositivo (Pré-visualização) - Serviço de Fala
titleSuffix: Azure Cognitive Services
description: A conversação multi-dispositivo torna fácil criar uma conversa de fala ou texto entre vários clientes e coordenar as mensagens que são enviadas entre eles.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 7b38bc390b28788003c2de757f45e8ecdfd5c89a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98932413"
---
# <a name="what-is-multi-device-conversation-preview"></a>O que é Conversa multi-dispositivo (Pré-visualização)?

**A conversação multi-dispositivo** torna fácil criar uma conversa de fala ou texto entre vários clientes e coordenar as mensagens enviadas entre eles.

Com **conversação multi-dispositivos,** pode:

- Ligue vários clientes à mesma conversa e gere o envio e receção de mensagens entre eles.
- Transcreva facilmente áudio de cada cliente e envie a transcrição para os outros, com tradução opcional.
- Enviar facilmente mensagens de texto entre clientes, com tradução opcional.

Pode construir uma funcionalidade ou solução que funcione através de uma série de dispositivos. Cada dispositivo pode enviar mensagens (transcrições de áudio ou mensagens instantâneas) para todos os outros dispositivos.

Enquanto [**a Transcrição de Conversa funciona**](conversation-transcription.md) num único dispositivo com uma matriz de microfone multicanal, o **Multi-device Conversation** é adequado para cenários com vários dispositivos, cada um com um único microfone.

>[!IMPORTANT]
> A conversação multi-dispositivo **não** suporta o envio de ficheiros áudio entre clientes: apenas a transcrição e/ou tradução.

## <a name="key-features"></a>Principais funcionalidades

- **Transcrição** em tempo real – Todos receberão uma transcrição da conversa, para que possam seguir o texto em tempo real ou guardá-lo para mais tarde.
- **Tradução em tempo real** – Com mais de 70 [idiomas suportados](language-support.md#text-languages) para tradução de texto, os utilizadores podem traduzir a conversa para o seu(s) idiomas preferidos).
- **Transcrições legíveis** – A transcrição e a tradução são fáceis de seguir, com pontuação e quebras de frases.
- **Entrada de voz ou texto** – Cada utilizador pode falar ou escrever no seu próprio dispositivo, dependendo das capacidades de suporte linguístico ativadas para o idioma escolhido pelo participante. Consulte o [suporte linguístico.](language-support.md#speech-to-text)
- **Retransmissão de mensagens** - O serviço de conversação multi-dispositivo distribuirá mensagens enviadas por um cliente a todos os outros, no(s) idioma(s) à sua escolha.
- **Identificação de mensagens** – Todas as mensagens que os utilizadores recebem na conversa serão marcadas com o apelido do utilizador que a enviou.

## <a name="use-cases"></a>Casos de utilização

### <a name="lightweight-conversations"></a>Conversas leves

Criar e juntar uma conversa é fácil. Um utilizador atuará como o 'anfitrião' e criará uma conversação, que gera um código de conversação aleatório de cinco letras e um código QR. Todos os outros utilizadores podem juntar-se à conversação digitando o código de conversação ou digitalizando o código QR. 

Uma vez que os utilizadores se juntam através do código de conversação e não são obrigados a partilhar informações de contacto, é fácil criar conversas rápidas e no local.

### <a name="inclusive-meetings"></a>Reuniões inclusivas

A transcrição e tradução em tempo real podem ajudar a tornar as conversas acessíveis a pessoas que falam línguas diferentes e/ou são surdas ou difíceis de ouvir. Cada pessoa também pode participar ativamente na conversa, falando a sua língua preferida ou enviando mensagens instantâneas.

### <a name="presentations"></a>Apresentações

Também pode fornecer legendas para apresentações e palestras tanto no ecrã como nos próprios dispositivos dos membros do público. Depois de o público se juntar ao código de conversação, podem ver a transcrição na sua língua preferida, no seu próprio dispositivo.

## <a name="how-it-works"></a>Como funciona

Todos os clientes usarão o Speech SDK para criar ou participar de uma conversa. O Speech SDK interage com o serviço de conversação multi-dispositivos, que gere a vida de uma conversa, incluindo a lista de participantes, a(s) idiomas escolhidas por cada cliente e mensagens enviadas.  

Cada cliente pode enviar mensagens áudio ou instantâneas. O serviço utilizará o reconhecimento da fala para converter áudio em texto e enviar mensagens instantâneas como está. Se os clientes escolherem diferentes idiomas, o serviço traduzirá todas as mensagens para os idiomas especificados de cada cliente.

![Diagrama de visão geral de conversação multi-dispositivo](media/scenarios/multi-device-conversation.png)

## <a name="overview-of-conversation-host-and-participant"></a>Visão geral da Conversa, Anfitrião e Participante

Uma **conversa** é uma sessão que um utilizador começa para os outros utilizadores participantes se juntarem. Todos os clientes se ligam à conversa usando o código de **conversação** de cinco letras.

Cada conversa cria metadados que incluem:
-    Tempos de quando a conversa começou e terminou
-    Lista de todos os participantes na conversa, que inclui o apelido escolhido de cada utilizador e linguagem primária para entrada de fala ou texto.


Existem dois tipos de utilizadores numa conversa: **anfitrião** e **participante.**

O **anfitrião** é o utilizador que inicia uma conversa, e que age como administrador dessa conversa.
- Cada conversa só pode ter um hospedeiro
- O hospedeiro deve estar ligado à conversa durante a conversação. Se o anfitrião deixar a conversa, a conversa terminará para todos os outros participantes.
- O anfitrião tem alguns controlos extra para gerir a conversa: 
    - Bloqueie a conversa - evite que participantes adicionais se juntem
    - Silenciar todos os participantes - evite que outros participantes enviem mensagens para a conversa, seja transcrita de discurso ou mensagens instantâneas
    - Participantes individuais mudos
    - Desmutam todos os participantes
    - Unmute participantes individuais

Um **participante** é um utilizador que se junta a uma conversa.
- Um participante pode sair e voltar a juntar-se à mesma conversa a qualquer momento, sem terminar a conversa para outros participantes.
- Os participantes não podem bloquear a conversa ou silenciar/descodir outros

> [!NOTE]
> Cada conversa pode ter até 100 participantes, dos quais 10 podem falar simultaneamente a qualquer momento.

## <a name="language-support"></a>Suporte de idiomas

Ao criar ou juntar uma conversa, cada utilizador deve escolher um **idioma primário**: o idioma em que falará e enviará mensagens instantâneas, bem como o idioma em que verá mensagens de outros utilizadores.

Existem dois tipos de línguas: **discurso-a-texto** e **texto- apenas:**
- Se o utilizador escolher uma linguagem **fala-a-texto** como língua principal, então poderá utilizar a entrada de fala e texto na conversa.

- Se o utilizador escolher um **idioma apenas de texto,** só poderá utilizar a entrada de texto e enviar mensagens instantâneas na conversação. As línguas apenas de texto são as línguas que são apoiadas para a tradução de texto, mas não de voz para texto. Pode ver os idiomas disponíveis na página de suporte ao [idioma.](./language-support.md)

Além da sua linguagem primária, cada participante também pode especificar idiomas adicionais para traduzir a conversa.

Abaixo está um resumo do que o utilizador será capaz de fazer numa conversa multi-dispositivo, baseada na sua língua primária escolhida.


| O que o utilizador pode fazer na conversa | Conversão de voz em texto | Apenas por texto |
|-----------------------------------|----------------|------|
| Use a entrada da fala | ✔️ | ❌ |
| Enviar mensagens instantâneas | ✔️ | ✔️ |
| Traduzir a conversa | ✔️ | ✔️ |

> [!NOTE]
> Para listas de línguas de tradução de texto e texto disponíveis, consulte [as línguas apoiadas.](./language-support.md)



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Traduzir conversas em tempo real](quickstarts/multi-device-conversation.md)