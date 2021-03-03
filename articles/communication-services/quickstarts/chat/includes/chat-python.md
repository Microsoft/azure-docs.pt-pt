---
title: incluir ficheiro
description: incluir ficheiro
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: deec1dabe405d13d6009311c8b2d68a930e7aa29
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661685"
---
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
# Add required client library components from quickstart here

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-client-library"></a>Instalar biblioteca de clientes

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Modelo de objeto

As seguintes aulas e interfaces lidam com algumas das principais características da biblioteca de clientes Azure Communication Services Chat para Python.

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
from azure.communication.chat import ChatClient, CommunicationTokenCredential, CommunicationTokenRefreshOptions

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
refresh_options = CommunicationTokenRefreshOptions(<Access Token>)
chat_client = ChatClient(endpoint, CommunicationTokenCredential(refresh_options))
```

## <a name="start-a-chat-thread"></a>Inicie um fio de chat

Utilize o `create_chat_thread` método para criar um fio de chat.

- Utilizar `topic` para dar um tópico de linha; O tópico pode ser atualizado após a criação do fio de chat utilizando a `update_thread` função.
- Utilize `thread_participants` para listar o `ChatThreadParticipant` a ser adicionado ao fio de chat, o `ChatThreadParticipant` tipo de `CommunicationUserIdentifier` tomadas como , que é o `user` que obteve depois de criar por Criar um [utilizador](../../access-tokens.md#create-an-identity)
- Utilize `repeatability_request_id` para direcionar que o pedido é repetível. O cliente pode fazer o pedido várias vezes com o mesmo ID de Pedido de Repetibilidade e obter uma resposta adequada sem que o servidor execute o pedido várias vezes.

A resposta `chat_thread_client` é usada para executar operações no fio de chat recém-criado, como adicionar participantes ao fio de chat, enviar mensagem, eliminar mensagem, etc. Contém uma `thread_id` propriedade que é o ID único do fio de chat.

#### <a name="without-repeatability-request-id"></a>Sem repetibilidade-Pedido-ID
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

chat_thread_client = chat_client.create_chat_thread(topic, participants)
```

#### <a name="with-repeatability-request-id"></a>Com repetibilidade-pedido-iD
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

repeatability_request_id = 'b66d6031-fdcc-41df-8306-e524c9f226b8' # some unique identifier
chat_thread_client = chat_client.create_chat_thread(topic, participants, repeatability_request_id)
```

## <a name="get-a-chat-thread-client"></a>Obtenha um cliente de linha de chat
O `get_chat_thread` método devolve um cliente de linha para um fio que já existe. Pode ser usado para realizar operações no fio criado: adicionar participantes, enviar mensagem, etc. thread_id é o ID único do fio de chat existente.

```python
thread_id = 'id'
chat_thread = chat_client.get_chat_thread(thread_id)
```

## <a name="list-all-chat-threads"></a>Listar todos os fios de chat
O `list_chat_threads` método devolve um iterador do tipo `ChatThreadInfo` . Pode ser usado para listar todos os fios de chat.

- Utilize `start_time` para especificar o ponto mais antigo no tempo para obter linhas de conversação até.
- Utilize `results_per_page` para especificar o número máximo de fios de chat devolvidos por página.

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)
start_time = start_time.replace(tzinfo=pytz.utc)
chat_thread_infos = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)

for info in chat_thread_infos:
    # Iterate over all chat threads
    print("thread id:", info.id)
```

## <a name="delete-a-chat-thread"></a>Excluir um fio de chat
O `delete_chat_thread` é utilizado para eliminar um fio de chat.

- Utilize `thread_id` para especificar a thread_id de um fio de chat existente que precisa de ser eliminado

```python
thread_id='id'
chat_client.delete_chat_thread(thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>Envie uma mensagem para um fio de chat

Utilize `send_message` o método para enviar uma mensagem para um fio de chat que acabou de criar, identificado por thread_id.

- Utilizar `content` para fornecer o conteúdo da mensagem de chat;
- Utilize `chat_message_type` para especificar o tipo de conteúdo da mensagem. Os valores possíveis são 'texto' e 'html'; se não for atribuído o valor predefinido especificado de 'texto'.
- Utilizar `sender_display_name` para especificar o nome de visualização do remetente;

A resposta é um tipo de "id", `str` que é o ID único dessa mensagem.

#### <a name="message-type-not-specified"></a>Tipo de mensagem não especificado
```python

content='hello world'
sender_display_name='sender name'

send_message_result_id = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name)
```

#### <a name="message-type-specified"></a>Tipo de mensagem especificado
```python
from azure.communication.chat import ChatMessageType

content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_id_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)

# specify chat message type as string
send_message_result_id_w_str = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type='text')
```

## <a name="get-a-specific-chat-message-from-a-chat-thread"></a>Obtenha uma mensagem de chat específica a partir de um fio de chat
A `get_message` função pode ser usada para recuperar uma mensagem específica, identificada por um message_id

- Utilize `message_id` para especificar o id de mensagem

A resposta do tipo `ChatMessage` contém todas as informações relacionadas com a única mensagem.

```python
message_id = 'message_id'
chat_message = chat_thread_client.get_message(message_id)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Receba mensagens de chat de um fio de chat

Pode recuperar mensagens de chat sondando o `list_messages` método em intervalos especificados.

- Utilize `results_per_page` para especificar o número máximo de mensagens a devolver por página.
- Utilize `start_time` para especificar o ponto mais antigo no tempo para obter mensagens.

```python
chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
```

`list_messages` retorna a versão mais recente da mensagem, incluindo quaisquer edições ou eliminações que aconteceram com a mensagem usando `update_message` e `delete_message` . Para mensagens `ChatMessage.deleted_on` eliminadas, retorna um valor de hora de data indicando quando essa mensagem foi eliminada. Para mensagens editadas, `ChatMessage.edited_on` retorna uma data indicando quando a mensagem foi editada. O tempo original da criação de mensagens pode ser acedido usando `ChatMessage.created_on` o que pode ser usado para encomendar as mensagens.

`list_messages` retorna diferentes tipos de mensagens que podem ser identificadas por `ChatMessage.type` . Estes tipos são:

- `ChatMessageType.TEXT`: Mensagem de chat regular enviada por um participante de thread.

- `ChatMessageType.HTML`: Mensagem de chat HTML enviada por um participante de thread.

- `ChatMessageType.TOPIC_UPDATED`: Mensagem do sistema que indica que o tópico foi atualizado.

- `ChatMessageType.PARTICIPANT_ADDED`: Mensagem do sistema que indica que um ou mais participantes foram adicionados ao fio de conversação.

- `ChatMessageType.PARTICIPANT_REMOVED`: Mensagem do sistema que indica que um participante foi removido do fio de conversação.

Para mais detalhes, consulte [os Tipos de Mensagens](../../../concepts/chat/concepts.md#message-types).

## <a name="update-topic-of-a-chat-thread"></a>Tópico de atualização de um fio de chat
Pode atualizar o tópico de um fio de chat usando o `update_topic` método

```python
topic = "updated thread topic"
chat_thread_client.update_topic(topic=topic)
```

## <a name="update-a-message"></a>Atualizar uma mensagem
Pode atualizar o conteúdo de uma mensagem existente utilizando o `update_message` método, identificado pelo message_id

- Utilize `message_id` para especificar o message_id
- Use `content` para definir o novo conteúdo da mensagem

```python
message_id='id'
content = 'updated content'
chat_thread_client.update_message(message_id=message_id, content=content)
```

## <a name="send-read-receipt-for-a-message"></a>Enviar recibo de leitura para uma mensagem
O `send_read_receipt` método pode ser usado para colocar um evento de recibo de leitura num fio, em nome de um utilizador.

- Utilize `message_id` para especificar o id da última mensagem lida pelo utilizador atual

```python
message_id='id'
chat_thread_client.send_read_receipt(message_id=message_id)
```

## <a name="list-read-receipts-for-a-chat-thread"></a>Lista ler recibos para um fio de chat
O `list_read_receipts` método pode ser usado para obter recibos de leitura para um fio.

- Utilize `results_per_page` para especificar o número máximo de recibos de leitura de mensagens de chat a serem devolvidos por página.
- Utilize `skip` para especificar os recibos de leitura de mensagens de chat até uma posição especificada em resposta.

```python
read_receipts = chat_thread_client.list_read_receipts(results_per_page=2, skip=0)

for page in read_receipts.by_page():
    for item in page:
        print(item)
```

## <a name="send-typing-notification"></a>Enviar notificação de dactilografia
O `send_typing_notification` método pode ser usado para colocar um evento de dactilografia num fio, em nome de um utilizador.

```python
chat_thread_client.send_typing_notification()
```

## <a name="delete-message"></a>Eliminar mensagem
O `delete_message` método pode ser usado para apagar uma mensagem, identificada por um message_id

- Utilize `message_id` para especificar o message_id

```python
message_id='id'
chat_thread_client.delete_message(message_id=message_id)
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Adicione um utilizador como participante ao fio de chat

Uma vez criado um fio de chat, pode adicionar e remover os utilizadores do mesmo. Ao adicionar os utilizadores, dá-lhes acesso para poderem enviar mensagens para o fio de chat e adicionar/remover outros participantes. Antes de ligar `add_participant` para o método, certifique-se de que adquiriu um novo token de acesso e identidade para esse utilizador. O utilizador necessitará desse token de acesso para inicializar o seu cliente de chat.

Utilize `add_participant` o método para adicionar os participantes do fio ao fio identificado por thread_id.

- Utilizar `thread_participant` para especificar o participante a ser adicionado ao fio de chat;
- `user`, necessário, é o `CommunicationUserIdentifier` que criou `CommunicationIdentityClient` na [criação de um utilizador](../../access-tokens.md#create-an-identity)
- `display_name`, opcional, é o nome de exibição do participante do thread.
- `share_history_time`, opcional, é o momento a partir do qual a história do chat é partilhada com o participante. Para partilhar a história desde o início do fio de chat, coloque esta propriedade em qualquer data igual ou inferior ao tempo de criação de fios. Para não partilhar nenhuma história anterior à data da adição do participante, desafete-a para a data atual. Para partilhar a história parcial, estabeleça-a como uma data intermediária.

```python
new_user = identity_client.create_user()

from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

new_chat_thread_participant = ChatThreadParticipant(
    user=new_user,
    display_name='name',
    share_history_time=datetime.utcnow())

chat_thread_client.add_participant(new_chat_thread_participant)
```

Vários utilizadores também podem ser adicionados ao fio de chat usando o `add_participants` método, desde que seja disponibilizado um novo token de acesso e identificados para todos os utilizadores.

```python
from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

new_chat_thread_participant = ChatThreadParticipant(
        user=self.new_user,
        display_name='name',
        share_history_time=datetime.utcnow())
thread_participants = [new_chat_thread_participant] # instead of passing a single participant, you can pass a list of participants
chat_thread_client.add_participants(thread_participants)
```


## <a name="remove-user-from-a-chat-thread"></a>Remova o utilizador de um fio de chat

Semelhante à adição de um participante, também pode remover os participantes de um fio. Para remover, terá de rastrear as identificações dos participantes que adicionou.

Utilize `remove_participant` o método para remover o participante do fio identificado pelo threadId.
- `user` é o `CommunicationUserIdentifier` a ser removido do fio.

```python
chat_thread_client.remove_participant(user)
```

## <a name="run-the-code"></a>Executar o código

Executar o pedido do seu diretório de candidaturas com o `python` comando.

```console
python start-chat.py
```
