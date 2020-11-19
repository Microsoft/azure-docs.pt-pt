---
title: Conceitos de chat nos Serviços de Comunicação Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre os conceitos de Chat de Serviços de Comunicação.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: f0e69e3f62d3b9e4debb5761d877dcdfdd246f60
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886027"
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
    
## <a name="message-types"></a>Tipos de mensagens

Serviços de Comunicação O Chat partilha mensagens geradas pelo utilizador, bem como mensagens geradas pelo sistema chamadas **thread activities**. As atividades de thread são geradas quando um fio de chat é atualizado. Quando ligar `List Messages` ou num fio de `Get Messages` chat, o resultado conterá as mensagens de texto geradas pelo utilizador, bem como as mensagens do sistema por ordem cronológica. Isto ajuda-o a identificar quando um membro foi adicionado ou removido ou quando o tópico do fio de chat foi atualizado. Os tipos de mensagens suportadas são:  

 - `Text`: Mensagem real composta e enviada pelo utilizador como parte da conversação de chat. 
 - `ThreadActivity/AddMember`: Mensagem do sistema que indica que um ou mais membros foram adicionados ao fio de conversação. Por exemplo:

```xml

<addmember>
    <eventtime>1598478187549</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <detailedinitiatorinfo>
        <friendlyName>User 1</friendlyName>
    </detailedinitiatorinfo>
    <rosterVersion>1598478184564</rosterVersion>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</id>
        <friendlyName>User 1</friendlyName>
    </detailedtargetinfo>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</id>
        <friendlyName>User 2</friendlyName>
    </detailedtargetinfo>
</addmember>

```  

- `ThreadActivity/DeleteMember`: Mensagem do sistema que indica que um membro foi removido do fio de conversação. Por exemplo:

```xml

<deletemember>
    <eventtime>1598478187642</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <detailedinitiatorinfo>
        <friendlyName>User 1</friendlyName>
    </detailedinitiatorinfo>
    <rosterVersion>1598478184564</rosterVersion>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</id>
        <friendlyName>User 2</friendlyName>
    </detailedtargetinfo>
</deletemember>

```

- `ThreadActivity/TopicUpdate`: Mensagem do sistema que indica que o tópico foi atualizado. Por exemplo:

```xml

<topicupdate>
    <eventtime>1598477591811</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <value>New topic</value>
</topicupdate>

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