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
ms.openlocfilehash: 2b7d00335253772683b867acf0765b77fc493e79
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523934"
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

```console
pip install azure-communication-administration
```

```python
from azure.communication.chat import ChatClient, CommunicationUserCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationUserCredential(<Access Token>))
```

## <a name="start-a-chat-thread"></a>Inicie um fio de chat

Utilize o `create_chat_thread` método para criar um fio de chat.

- Utilizar `topic` para dar um tópico de linha; O tópico pode ser atualizado após a criação do fio de chat utilizando a `update_thread` função.
- Utilize `members` para listar o `ChatThreadMember` a ser adicionado ao fio de chat, o `ChatThreadMember` tipo de `CommunicationUser` tomadas como , que é o `user` que obteve depois de criar por Criar um [utilizador](../../access-tokens.md#create-an-identity)

A resposta `chat_thread_client` é usada para executar operações no fio de chat recém-criado, como adicionar membros ao fio de chat, enviar mensagem, eliminar mensagem, etc. Contém uma `thread_id` propriedade que é o ID único do fio de chat.

```python
from datetime import datetime
from azure.communication.chat import ChatThreadMember

topic="test topic"
thread_members=[ChatThreadMember(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]
chat_thread_client = chat_client.create_chat_thread(topic, thread_members)
```

## <a name="get-a-chat-thread-client"></a>Obtenha um cliente de linha de chat
O método get_chat_thread_client devolve um cliente de linha por um fio que já existe. Pode ser utilizado para a realização de operações no fio criado: adicionar membros, enviar mensagem, etc. thread_id é o ID único do fio de chat existente.

```python
thread_id = 'id'
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>Envie uma mensagem para um fio de chat

Utilize `send_message` o método para enviar uma mensagem para um fio de chat que acabou de criar, identificado pelo threadId.

- Utilizar `content` para fornecer o conteúdo da mensagem de chat;
- Utilize `priority` para especificar o nível de prioridade da mensagem, como 'Normal' ou 'High' ; esta propriedade pode ser usada para ter indicador de UI para o utilizador recetor na sua app para chamar a atenção para a mensagem ou executar lógica de negócio personalizada.
- Utilizar `senderDisplayName` para especificar o nome de visualização do remetente;

A resposta `SendChatMessageResult` contém um "id", que é o ID único dessa mensagem.

```python
from azure.communication.chat import ChatMessagePriority

content='hello world'
priority=ChatMessagePriority.NORMAL
sender_display_name='sender name'

send_message_result = chat_thread_client.send_message(content, priority=priority, sender_display_name=sender_display_name)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Receba mensagens de chat de um fio de chat

Pode recuperar mensagens de chat sondando o `list_messages` método em intervalos especificados.

```python
chat_messages = chat_thread_client.list_messages()
```
`list_messages` retorna a versão mais recente da mensagem, incluindo quaisquer edições ou eliminações que aconteceram com a mensagem usando `update_message` e `delete_message` . Para mensagens `ChatMessage.deleted_on` eliminadas, retorna um valor de hora de data indicando quando essa mensagem foi eliminada. Para mensagens editadas, `ChatMessage.edited_on` retorna uma data indicando quando a mensagem foi editada. O tempo original da criação de mensagens pode ser acedido usando `ChatMessage.created_on` o que pode ser usado para encomendar as mensagens.

`list_messages` retorna diferentes tipos de mensagens que podem ser identificadas por `ChatMessage.type` . Estes tipos são:

- `Text`: Mensagem de chat regular enviada por um membro do thread.

- `ThreadActivity/TopicUpdate`: Mensagem do sistema que indica que o tópico foi atualizado.

- `ThreadActivity/AddMember`: Mensagem do sistema que indica que um ou mais membros foram adicionados ao fio de conversação.

- `ThreadActivity/DeleteMember`: Mensagem do sistema que indica que um membro foi removido do fio de conversação.

Para mais detalhes, consulte [os Tipos de Mensagens](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Adicione um utilizador como membro ao fio de chat

Uma vez criado um fio de chat, pode adicionar e remover os utilizadores do mesmo. Ao adicionar os utilizadores, dá-lhes acesso para poderem enviar mensagens para o fio de chat e adicionar/remover outros membros. Antes de ligar `add_members` para o método, certifique-se de que adquiriu um novo token de acesso e identidade para esse utilizador. O utilizador necessitará desse token de acesso para inicializar o seu cliente de chat.

Utilize `add_members` o método para adicionar os membros do fio ao fio identificado pelo threadId.

- Utilizar `members` para listar os membros a adicionar ao fio de conversação;
- `user`, necessário, é o `CommunicationUser` que criou `CommunicationIdentityClient` na [criação de um utilizador](../../access-tokens.md#create-an-identity)
- `display_name`, opcional, é o nome de visualização do membro thread.
- `share_history_time`, opcional, é o momento a partir do qual a história do chat é partilhada com o membro. Para partilhar a história desde o início do fio de chat, coloque esta propriedade em qualquer data igual ou inferior ao tempo de criação de fios. Para não partilhar nenhuma história anterior à data em que o membro foi adicionado, desemafete-a para a data atual. Para partilhar a história parcial, estabeleça-a como uma data intermediária.

```python
new_user = identity_client.create_user()

from azure.communication.chat import ChatThreadMember
from datetime import datetime
member = ChatThreadMember(
    user=new_user,
    display_name='name',
    share_history_time=datetime.utcnow())
thread_members = [member]
chat_thread_client.add_members(thread_members)
```

## <a name="remove-user-from-a-chat-thread"></a>Remova o utilizador de um fio de chat

Semelhante à adição de um membro, também pode remover membros de um fio. Para remover, terá de rastrear as identificações dos membros que adicionou.

Utilize `remove_member` o método para remover o membro do fio do fio identificado pelo threadId.
- `user` é o Comunicador de Comunicação a ser removido do fio.

```python
chat_thread_client.remove_member(user)
```

## <a name="run-the-code"></a>Executar o código

Executar o pedido do seu diretório de candidaturas com o `python` comando.

```console
python start-chat.py
```
