---
title: incluir ficheiro
description: incluir ficheiro
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 31704e705b828cc0070e3b79f5d527cfa9deb0c3
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386907"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de:

- Crie uma conta Azure com uma subscrição ativa. Para mais detalhes, consulte [Criar uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Instalar [Python](https://www.python.org/downloads/)
- Criar um recurso Azure Communication Services. Para mais detalhes, consulte [Criar um Recurso de Comunicação Azure](../../create-communication-resource.md). Terá de gravar o seu **ponto final** de recurso para este arranque rápido
- Um [token de acesso ao utilizador](../../access-tokens.md). Certifique-se de definir o âmbito para "chat", e note a cadeia simbólica, bem como a cadeia userId.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

Abra o seu terminal ou janela de comando crie um novo diretório para a sua aplicação e navegue até ele.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Use um editor de texto para criar um ficheiro chamado **start-chat.py** no diretório de raiz do projeto e adicione a estrutura para o programa, incluindo o manuseamento básico de exceções. Irá adicionar todo o código fonte para este arranque rápido a este ficheiro nas seguintes secções.

```python
import os
# Add required SDK components from quickstart here

try:
    print('Azure Communication Services - Chat Quickstart')
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-sdk"></a>Instalar O SDK

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características do Azure Communication Services Chat SDK para Python.

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Esta classe é necessária para a funcionalidade Chat. Você instantaneamente com as suas informações de subscrição, e usá-lo para criar, obter e apagar fios. |
| ChatThreadClient | Esta classe é necessária para a funcionalidade Chat Thread. Obtém um caso através do ChatClient e utiliza-o para enviar/receber/atualizar/apagar mensagens, adicionar/remover/obter utilizadores, enviar notificações de dactilografia e ler recibos. |

## <a name="create-a-chat-client"></a>Criar um cliente de chat

Para criar um cliente de chat, utilizará o ponto final do Serviço de Comunicações e o `Access Token` que foi gerado como parte das etapas pré-necessárias. Saiba mais sobre [tokens de acesso ao utilizador.](../../access-tokens.md)

Este quickstart não cobre a criação de um nível de serviço para gerir fichas para a sua aplicação de chat, embora seja recomendado. Consulte a seguinte documentação para obter mais detalhes [Sobre a Arquitetura chat](../../../concepts/chat/concepts.md)

```console
pip install azure-communication-identity
```

```python
from azure.communication.chat import ChatClient, CommunicationTokenCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationTokenCredential("<Access Token>"))
```

## <a name="start-a-chat-thread"></a>Inicie um fio de chat

Utilize o `create_chat_thread` método para criar um fio de chat.

- Utilizar `topic` para dar um tópico de linha; O tópico pode ser atualizado após a criação do fio de chat utilizando a `update_thread` função.
- Utilize `thread_participants` para listar o `ChatParticipant` a ser adicionado ao fio de chat, o `ChatParticipant` tipo de `CommunicationUserIdentifier` tomadas como , que é o `user` que obteve depois de criar por Criar um [utilizador](../../access-tokens.md#create-an-identity)

`CreateChatThreadResult` é o resultado devolvido da criação de um fio, pode usá-lo para ir buscar `id` o fio de chat que foi criado. Isto `id` pode então ser usado para buscar um `ChatThreadClient` objeto usando o `get_chat_thread_client` método. `ChatThreadClient` pode ser usado para realizar outras operações de chat para este fio de chat.

```python
topic="test topic"

create_chat_thread_result = chat_client.create_chat_thread(topic)
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)
```

## <a name="get-a-chat-thread-client"></a>Obtenha um cliente de linha de chat
O `get_chat_thread_client` método devolve um cliente de linha para um fio que já existe. Pode ser usado para realizar operações no fio criado: adicionar participantes, enviar mensagem, etc. thread_id é o ID único do fio de chat existente.

`ChatThreadClient` pode ser usado para realizar outras operações de chat para este fio de chat.

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```


## <a name="list-all-chat-threads"></a>Listar todos os fios de chat
O `list_chat_threads` método devolve um iterador do tipo `ChatThreadItem` . Pode ser usado para listar todos os fios de chat.

- Utilize `start_time` para especificar o ponto mais antigo no tempo para obter linhas de conversação até.
- Utilize `results_per_page` para especificar o número máximo de fios de chat devolvidos por página.

Um iterador de `[ChatThreadItem]` é a resposta devolvida de linhas de listagem

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)

chat_threads = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)
for chat_thread_item_page in chat_threads.by_page():
    for chat_thread_item in chat_thread_item_page:
        print(chat_thread_item)
        print('Chat Thread Id: ', chat_thread_item.id)
```


## <a name="send-a-message-to-a-chat-thread"></a>Envie uma mensagem para um fio de chat

Utilize `send_message` o método para enviar uma mensagem para um fio de chat que acabou de criar, identificado por thread_id.

- Utilizar `content` para fornecer o conteúdo da mensagem de chat;
- Utilize `chat_message_type` para especificar o tipo de conteúdo da mensagem. Os valores possíveis são 'texto' e 'html'; se não for atribuído o valor predefinido especificado de 'texto'.
- Utilizar `sender_display_name` para especificar o nome de visualização do remetente;

`SendChatMessageResult` é a resposta devolvida do envio de uma mensagem, contém um ID, que é o ID único da mensagem.

```python
from azure.communication.chat import ChatMessageType

topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)


content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)
print("Message sent: id: ", send_message_result_w_enum.id)
```


## <a name="receive-chat-messages-from-a-chat-thread"></a>Receba mensagens de chat de um fio de chat

Pode recuperar mensagens de chat sondando o `list_messages` método em intervalos especificados.

- Utilize `results_per_page` para especificar o número máximo de mensagens a devolver por página.
- Utilize `start_time` para especificar o ponto mais antigo no tempo para obter mensagens.

Um iterador de `[ChatMessage]` é a resposta devolvida a partir de mensagens de listagem

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=1)

chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print("ChatMessage: Id=", chat_message.id, "; Content=", chat_message.content.message)
```

`list_messages` retorna a versão mais recente da mensagem, incluindo quaisquer edições ou eliminações que aconteceram com a mensagem usando `update_message` e `delete_message` . Para mensagens `ChatMessage.deleted_on` eliminadas, retorna um valor de hora de data indicando quando essa mensagem foi eliminada. Para mensagens editadas, `ChatMessage.edited_on` retorna uma data indicando quando a mensagem foi editada. O tempo original da criação de mensagens pode ser acedido usando `ChatMessage.created_on` o que pode ser usado para encomendar as mensagens.

`list_messages` retorna diferentes tipos de mensagens que podem ser identificadas por `ChatMessage.type` . 

Leia mais sobre Tipos de Mensagens aqui: [Tipos de Mensagens](../../../concepts/chat/concepts.md#message-types).

## <a name="send-read-receipt"></a>Enviar recibo de leitura
O `send_read_receipt` método pode ser usado para colocar um evento de recibo de leitura num fio, em nome de um utilizador.

- Utilize `message_id` para especificar o ID da última mensagem lida pelo utilizador atual.

```python
content='hello world'

send_message_result = chat_thread_client.send_message(content)
chat_thread_client.send_read_receipt(message_id=send_message_result.id)
```


## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Adicione um utilizador como participante ao fio de chat

Uma vez criado um fio de chat, pode adicionar e remover os utilizadores do mesmo. Ao adicionar os utilizadores, dá-lhes acesso para poderem enviar mensagens para o fio de chat e adicionar/remover outros participantes. Antes de ligar `add_participants` para o método, certifique-se de que adquiriu um novo token de acesso e identidade para esse utilizador. O utilizador necessitará desse token de acesso para inicializar o seu cliente de chat.

Um ou mais utilizadores podem ser adicionados ao fio de chat usando o `add_participants` método, desde que seja disponibilizado um novo token de acesso e identificação para todos os utilizadores.

A `list(tuple(ChatParticipant, CommunicationError))` é devolvido. Quando o participante é adicionado com sucesso, espera-se uma lista vazia. Em caso de erro encontrado ao adicionar participante, a lista é preenchida com os participantes falhados juntamente com o erro que foi encontrado.

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatParticipant
from datetime import datetime

# create 2 users
identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
new_users = [identity_client.create_user() for i in range(2)]

# # conversely, you can also add an existing user to a chat thread; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
#
# user_id = 'some user id'
# user_display_name = "Wilma Flinstone"
# new_user = CommunicationUserIdentifier(user_id)
# participant = ChatParticipant(
#     user=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

participants = []
for _user in new_users:
  chat_thread_participant = ChatParticipant(
    user=_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow()
  ) 
  participants.append(chat_thread_participant) 

response = chat_thread_client.add_participants(participants)

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

# verify if all users has been successfully added or not
# in case of partial failures, you can retry to add all the failed participants 
retry = [p for p, e in response if decide_to_retry(e)]
if retry:
    chat_thread_client.add_participants(retry)
```


## <a name="list-thread-participants-in-a-chat-thread"></a>List thread participantes em um fio de chat

Semelhante à adição de um participante, também pode listar os participantes de um fio.

Utilize `list_participants` para recuperar os participantes do fio.
- `results_per_page`Utilização, opcional, O número máximo de participantes a devolver por página.
- Utilizar `skip` , opcional, para saltar os participantes até uma posição especificada em resposta.

Um iterador de `[ChatParticipant]` é a resposta devolvida dos participantes da listagem

```python
chat_thread_participants = chat_thread_client.list_participants()
for chat_thread_participant_page in chat_thread_participants.by_page():
    for chat_thread_participant in chat_thread_participant_page:
        print("ChatParticipant: ", chat_thread_participant)
```

## <a name="run-the-code"></a>Executar o código

Executar o pedido do seu diretório de candidaturas com o `python` comando.

```console
python start-chat.py
```
