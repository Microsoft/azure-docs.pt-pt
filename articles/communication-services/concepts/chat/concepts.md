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
ms.openlocfilehash: cf500d529eb22cdd333d796f156eedcd284ea20d
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642317"
---
# <a name="chat-concepts"></a>Conceitos de chat 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]    

Os SDKs de chat dos Serviços de Comunicação Azure podem ser utilizados para adicionar chat de texto em tempo real às suas aplicações. Esta página resume os principais conceitos e capacidades do Chat.    

Consulte a visão geral do [Chat SDK dos Serviços de Comunicação](./sdk-features.md) para saber mais sobre línguas e capacidades específicas da SDK.  

## <a name="chat-overview"></a>Visão geral do chat    

Conversas de chat acontecem dentro de **linhas de chat**. Os fios de chat têm as seguintes propriedades:

- Um fio de chat é identificado exclusivamente pela sua `ChatThreadId` . 
- Os fios de chat podem ter um ou muitos utilizadores como participantes que podem enviar mensagens para ele. 
- Um utilizador pode fazer parte de um ou muitos fios de chat. 
- Apenas os participantes do fio têm acesso a um determinado fio de chat, e só eles podem realizar operações de chat thread. Estas operações incluem o envio e receção de mensagens, a adição de participantes e a remoção dos participantes. 
- Os utilizadores são automaticamente adicionados como participantes a quaisquer fios de chat que criem.

### <a name="user-access"></a>Acesso do utilizador
Tipicamente, o criador de fios e os participantes têm o mesmo nível de acesso ao fio e podem executar todas as operações relacionadas disponíveis no SDK, incluindo a sua eliminação. Os participantes não têm acesso a mensagens enviadas por outros participantes, o que significa que apenas o remetente de mensagens pode atualizar ou apagar as suas mensagens enviadas. Se outro participante tentar fazer isso, terá um erro. 

Se pretende renunciar ao acesso às funcionalidades de chat para um conjunto de utilizadores, pode configurar o acesso como parte do seu serviço de confiança. O seu serviço de confiança é o serviço que orquestra a autenticação e autorização dos participantes do chat. Vamos explorar isto mais detalhadamente abaixo.  

### <a name="chat-data"></a>Dados de Chat 
Os Serviços de Comunicação armazenam o histórico de conversas até explicitamente eliminados. Os participantes do chat thread podem usar `ListMessages` para ver o histórico da mensagem para um determinado fio. Os utilizadores removidos de um fio de chat poderão ver o histórico de mensagens anteriores, mas não poderão enviar ou receber novas mensagens como parte desse fio de chat. Um fio totalmente ocioso sem participantes será automaticamente eliminado após 30 dias. Para saber mais sobre os dados armazenados pelos Serviços de Comunicação, consulte a documentação sobre [privacidade.](../privacy.md)  

### <a name="service-limits"></a>Limites do serviço  
- O número máximo de participantes permitidos num fio de chat é de 250.   
- O tamanho máximo da mensagem permitida é de aproximadamente 28 KB.  
- Para linhas de chat com mais de 20 participantes, os recibos de leitura e as funcionalidades do indicador de dactilografia não são suportados.    

## <a name="chat-architecture"></a>Arquitetura de chat    

Existem duas partes fundamentais para a arquitetura de chat: 1) Serviço Fidedigno e 2) Aplicação ao Cliente.    

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagrama mostrando a arquitetura de chat dos Serviços de Comunicação."::: 

 - **Serviço de confiança:** Para gerir corretamente uma sessão de chat, precisa de um serviço que o ajude a ligar-se aos Serviços de Comunicação utilizando a sua cadeia de ligação de recursos. Este serviço é responsável pela criação de linhas de chat, adicionando e removendo os participantes, e emitindo fichas de acesso aos utilizadores. Mais informações sobre os tokens de acesso podem ser encontradas nos [nossos tokens](../../quickstarts/access-tokens.md) de acesso rápido.  
 - **Aplicativo de cliente:**  A aplicação do cliente conecta-se ao seu serviço de confiança e recebe os tokens de acesso que são utilizados pelos utilizadores para se conectarem diretamente aos Serviços de Comunicação. Uma vez que o seu serviço de confiança criou o fio de chat e acrescentou utilizadores como participantes, eles podem usar a aplicação do cliente para ligar ao fio de chat e enviar mensagens. Utilize a funcionalidade de notificações em tempo real, que iremos discutir abaixo, na aplicação do seu cliente para subscrever a mensagem & atualizações de thread de outros participantes.
    
        
## <a name="message-types"></a>Tipos de mensagens    

Como parte do histórico de mensagens, o Chat partilha mensagens geradas pelo utilizador, bem como mensagens geradas pelo sistema. As mensagens do sistema são geradas quando um fio de chat é atualizado e podem ajudar a identificar quando um participante foi adicionado ou removido ou quando o tópico do fio de chat foi atualizado. Quando ligar `List Messages` ou entrar numa linha de `Get Messages` chat, o resultado conterá ambos os tipos de mensagens por ordem cronológica.

Para as mensagens geradas pelo utilizador, o tipo de mensagem pode ser definido `SendMessageOptions` ao enviar uma mensagem para o fio de chat. Se não for fornecido qualquer valor, os Serviços de Comunicação falharão em `text` escrever. Definir este valor é importante no envio de HTML. Quando `html` especificado, os Serviços de Comunicação irão higienizar o conteúdo para garantir que seja prestado com segurança em dispositivos clientes.
 - `text`: Uma simples mensagem de texto composta e enviada por um utilizador como parte de um fio de chat. 
 - `html`: Uma mensagem formatada utilizando html, composta e enviada por um utilizador como parte do fio de chat. 

Tipos de mensagens do sistema: 
 - `participantAdded`: Mensagem do sistema que indica que um ou mais participantes foram adicionados ao fio de conversação.
 - `participantRemoved`: Mensagem do sistema que indica que um participante foi removido do fio de conversação.
 - `topicUpdated`: Mensagem do sistema que indica que o tópico da linha foi atualizado.

## <a name="real-time-notifications"></a>Notificações em tempo real  

Alguns SDKs (como o JavaScript Chat SDK) suportam notificações em tempo real. Esta funcionalidade permite que os clientes ouçam os Serviços de Comunicação para atualizações em tempo real e mensagens recebidas para um fio de chat sem terem de fazer sondagens às APIs. A aplicação do cliente pode subscrever os seguintes eventos:
 - `chatMessageReceived` - quando uma nova mensagem é enviada para um fio de chat por um participante.
 - `chatMessageEdited` - quando uma mensagem é editada num fio de chat. 
 - `chatMessageDeleted` - quando uma mensagem é apagada num fio de chat.   
 - `typingIndicatorReceived` - quando outro participante envia um indicador de dactilografia para o fio de chat.    
 - `readReceiptReceived` - quando outro participante envia um recibo de leitura para uma mensagem que leu.  
 - `chatThreadCreated` - quando um fio de chat é criado por um utilizador dos Serviços de Comunicação.    
 - `chatThreadDeleted` - quando um fio de chat é eliminado por um utilizador dos Serviços de Comunicação.    
 - `chatThreadPropertiesUpdated` - quando as propriedades do fio de chat forem atualizadas; atualmente, apenas a atualização do tópico para o fio é suportado. 
 - `participantsAdded` - quando um utilizador é adicionado como participante de um fio de chat.     
 - `participantsRemoved` - quando um participante existente é removido do fio de conversação.

As notificações em tempo real podem ser usadas para proporcionar uma experiência de chat em tempo real para os seus utilizadores. Para enviar notificações push para mensagens perdidas pelos seus utilizadores enquanto estiveram fora, os Serviços de Comunicação integram-se com a Azure Event Grid para publicar eventos relacionados com o chat (pós-operação) que podem ser ligados ao seu serviço de notificação de aplicações personalizadas. Para mais detalhes, consulte [Eventos do Servidor](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fcommunication-services%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json).


## <a name="build-intelligent-ai-powered-chat-experiences"></a>Construa experiências inteligentes e alimentadas por IA de chat   

Você pode usar [Azure Cognitive APIs](../../../cognitive-services/index.yml) com o Chat SDK para construir casos de uso como:

- Permitir que os utilizadores conversem uns com os outros em diferentes idiomas.  
- Ajude um agente de apoio a priorizar os bilhetes detetando um sentimento negativo de uma mensagem recebida de um cliente. 
- Analise as mensagens recebidas para deteção de chaves e reconhecimento de entidades e apromia informações relevantes para o utilizador na sua aplicação com base no conteúdo da mensagem.

Uma maneira de conseguir isso é fazendo com que o seu serviço de confiança atue como participante de um fio de chat. Digamos que quer ativar a tradução linguística. Este serviço será responsável por ouvir as mensagens que estão a ser trocadas por outros participantes [1], chamar apis cognitivos para traduzir o conteúdo para a língua desejada[2,3] e enviar o resultado traduzido como mensagem no fio de chat[4].

Desta forma, o histórico da mensagem conterá mensagens originais e traduzidas. Na aplicação do cliente, pode adicionar lógica para mostrar a mensagem original ou traduzida. Veja [este quickstart](../../../cognitive-services/translator/quickstart-translator.md) para entender como usar APIs cognitivos para traduzir texto para diferentes línguas. 
    
:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagrama mostrando Serviços Cognitivos interagindo com serviços de comunicação."::: 

## <a name="next-steps"></a>Passos seguintes   

> [!div class="nextstepaction"] 
> [Começa com o chat](../../quickstarts/chat/get-started.md)    

Os seguintes documentos podem ser interessantes para si:  
- Familiarize-se com o [Chat SDK](sdk-features.md)
