---
title: Conceitos de chat nos Serviços de Comunicação Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre os conceitos de Chat de Serviços de Comunicação.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 22ffa59339d85af80a7398e581862d19eef17222
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103490658"
---
# <a name="chat-concepts"></a>Conceitos de chat

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


As bibliotecas de clientes Azure Communication Services Chat podem ser usadas para adicionar chat de texto em tempo real às suas aplicações. Esta página resume os principais conceitos e capacidades do Chat.

Consulte a biblioteca de [clientes de Comunicação Chat](./sdk-features.md) para saber mais sobre as línguas e capacidades específicas da biblioteca do cliente.

## <a name="chat-overview"></a>Visão geral do chat

Conversas de chat acontecem dentro de linhas de chat. Um fio de chat pode conter muitas mensagens e muitos utilizadores. Cada mensagem pertence a um único fio, e um utilizador pode fazer parte de um ou muitos fios.

Cada utilizador no fio de chat é chamado de membro. Você pode ter até 250 membros em um fio de chat. Apenas os membros do thread podem enviar e receber mensagens ou adicionar/remover membros num fio de chat. O tamanho máximo de mensagem permitido é de aproximadamente 28KB. Pode recuperar todas as mensagens num fio de chat utilizando a `List/Get Messages` operação. Os Serviços de Comunicação armazenam o histórico de conversas até executar uma operação de eliminação no fio de chat ou mensagem, ou até que nenhum membro permaneça no fio do chat, altura em que é órfão e processado para eliminação.

Para linhas de chat com mais de 20 membros, os recibos de leitura e as características do indicador de dactilografia estão desativadas.

## <a name="chat-architecture"></a>Arquitetura de chat

Existem duas partes fundamentais para a arquitetura de chat: 1) Serviço Fidedigno e 2) Aplicação ao Cliente.

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagrama mostrando a arquitetura de chat dos Serviços de Comunicação.":::

 - **Serviço de confiança:** Para gerir corretamente uma sessão de chat, precisa de um serviço que o ajude a ligar-se aos Serviços de Comunicação utilizando a sua cadeia de ligação de recursos. Este serviço é responsável pela criação de linhas de chat, gestão de membros de threads e fornecimento de fichas de acesso aos utilizadores. Mais informações sobre os tokens de acesso podem ser encontradas nos [nossos tokens](../../quickstarts/access-tokens.md) de acesso rápido.

 - **Aplicativo de cliente:**  A aplicação do cliente conecta-se ao seu serviço de confiança e recebe os tokens de acesso que são usados para se ligar diretamente aos Serviços de Comunicação. Depois desta ligação ser feita, a aplicação do seu cliente pode enviar e receber mensagens.

Recomendamos gerar fichas de acesso utilizando o nível de serviço fidedigno. Neste cenário, o lado do servidor seria responsável pela criação e gestão dos utilizadores e pela emissão dos seus tokens.

## <a name="message-types"></a>Tipos de mensagens

Serviços de Comunicação O Chat partilha mensagens geradas pelo utilizador, bem como mensagens geradas pelo sistema chamadas **thread activities**. As atividades de thread são geradas quando um fio de chat é atualizado. Quando ligar `List Messages` ou num fio de `Get Messages` chat, o resultado conterá as mensagens de texto geradas pelo utilizador, bem como as mensagens do sistema por ordem cronológica. Isto ajuda-o a identificar quando um membro foi adicionado ou removido ou quando o tópico do fio de chat foi atualizado. Os tipos de mensagens suportadas são:

 - `Text`: Uma simples mensagem de texto composta e enviada por um utilizador como parte de uma conversa de chat.
 - `RichText/HTML`: Uma mensagem de texto formatada. Note que os utilizadores dos Serviços de Comunicação não podem enviar mensagens RichText. Este tipo de mensagem é suportado por mensagens enviadas de utilizadores de Equipas para utilizadores de Serviços de Comunicação em cenários de Teams Interop.

 - `ThreadActivity/ParticipantAdded`: Uma mensagem do sistema que indica que um ou mais participantes foram adicionados ao fio de conversação. Por exemplo:

```
{
            "id": "1613589626560",
            "type": "participantAdded",
            "sequenceId": "7",
            "version": "1613589626560",
            "content":
            {
                "participants":
                [
                    {
                        "id": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4df6-f40f-343a0d003226",
                        "displayName": "Jane",
                        "shareHistoryTime": "1970-01-01T00:00:00Z"
                    }
                ],
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"
            },
            "createdOn": "2021-02-17T19:20:26Z"
        }
```

- `ThreadActivity/ParticipantRemoved`: Mensagem do sistema que indica que um participante foi removido do fio de conversação. Por exemplo:

```
{
            "id": "1613589627603",
            "type": "participantRemoved",
            "sequenceId": "8",
            "version": "1613589627603",
            "content":
            {
                "participants":
                [
                    {
                        "id": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4df6-f40f-343a0d003226",
                        "displayName": "Jane",
                        "shareHistoryTime": "1970-01-01T00:00:00Z"
                    }
                ],
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"
            },
            "createdOn": "2021-02-17T19:20:27Z"
        }
```

- `ThreadActivity/TopicUpdate`: Mensagem do sistema que indica que o tópico da linha foi atualizado. Por exemplo:

```
{
            "id": "1613589623037",
            "type": "topicUpdated",
            "sequenceId": "2",
            "version": "1613589623037",
            "content":
            {
                "topic": "New topic",
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"
            },
            "createdOn": "2021-02-17T19:20:23Z"
        }
```

## <a name="real-time-signaling"></a>Sinalização em tempo real

A biblioteca de clientes Chat JavaScript inclui sinalização em tempo real. Isto permite que os clientes ouçam atualizações em tempo real e mensagens recebidas para um fio de chat sem terem de fazer sondagens às APIs. Os eventos disponíveis incluem:

 - `ChatMessageReceived` - quando uma nova mensagem é enviada para um fio de chat do qual o utilizador é membro. Este evento não é enviado para mensagens de sistema geradas automaticamente que discutimos no tópico anterior.
 - `ChatMessageEdited` - quando uma mensagem é editada num fio de chat do qual o utilizador é membro.
 - `ChatMessageDeleted` - quando uma mensagem é apagada num fio de chat do qual o utilizador é membro.
 - `TypingIndicatorReceived` - quando outro membro está a escrever uma mensagem num fio de chat do qual o utilizador é membro.
 - `ReadReceiptReceived` - quando outro membro tiver lido a mensagem que o utilizador enviou num fio de chat.

## <a name="chat-events"></a>Eventos de chat

A sinalização em tempo real permite que os seus utilizadores conversem em tempo real. Os seus serviços podem utilizar a Azure Event Grid para subscrever eventos relacionados com chat. Para mais detalhes, consulte [Event Handling conceptual.](../event-handling.md)

## <a name="using-cognitive-services-with-chat-client-library-to-enable-intelligent-features"></a>Utilização de Serviços Cognitivos com biblioteca de clientes Chat para permitir funcionalidades inteligentes

Pode utilizar [APIs cognitivos do Azure](../../../cognitive-services/index.yml) com a biblioteca do cliente Chat para adicionar funcionalidades inteligentes às suas aplicações. Pode, por exemplo:

- Permitir que os utilizadores conversem uns com os outros em diferentes idiomas.
- Ajude um agente de apoio a priorizar os bilhetes, detetando um sentimento negativo de um problema de entrada de um cliente.
- Analise as mensagens recebidas para deteção de chaves e reconhecimento de entidades e apromia informações relevantes para o utilizador na sua aplicação com base no conteúdo da mensagem.

Uma maneira de conseguir isso é fazendo com que o seu serviço de confiança atue como membro de um fio de chat. Digamos que quer ativar a tradução linguística. Este serviço será responsável por ouvir as mensagens que estão a ser trocadas por outros membros [1], chamar APIs cognitivos para traduzir o conteúdo para a língua desejada[2,3] e enviar o resultado traduzido como mensagem no fio do chat[4].

Desta forma, o histórico da mensagem conterá mensagens originais e traduzidas. Na aplicação do cliente, pode adicionar lógica para mostrar a mensagem original ou traduzida. Veja [este quickstart](../../../cognitive-services/translator/quickstart-translator.md) para entender como usar APIs cognitivos para traduzir texto para diferentes línguas.

:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagrama mostrando Serviços Cognitivos interagindo com serviços de comunicação.":::

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começa com o chat](../../quickstarts/chat/get-started.md)

Os seguintes documentos podem ser interessantes para si:

- Familiarize-se com a biblioteca de [clientes Chat](sdk-features.md)
