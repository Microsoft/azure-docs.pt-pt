---
title: Visão geral da biblioteca do cliente de chat para serviços de comunicação Azure
titleSuffix: An Azure Communication Services concept document
description: Conheça a biblioteca de clientes de chat da Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 705bd926c2ac6f414464254969b5c511c88891f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656112"
---
# <a name="chat-client-library-overview"></a>Descrição geral da biblioteca de cliente de chat  

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]    

As bibliotecas de clientes Azure Communication Services Chat podem ser usadas para adicionar conversas ricas e em tempo real às suas aplicações.
    
## <a name="chat-client-library-capabilities"></a>Capacidades da biblioteca de clientes de chat 

A lista a seguir apresenta o conjunto de funcionalidades que estão atualmente disponíveis nas bibliotecas de clientes de chat dos Serviços de Comunicação.  

| Grupo de funcionalidades | Funcionalidade | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Capacidades do Núcleo | Crie um fio de chat entre 2 ou mais utilizadores (até 250 utilizadores)                                                       | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Atualize o tópico de um fio de chat                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Adicione ou remova os participantes de um fio de chat                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Escolha se partilha o histórico de mensagens de chat com a adição do participante                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Obtenha uma lista de participantes em um fio de chat                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Excluir um fio de chat                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Dado um utilizador de comunicação, obtenha a lista de linhas de chat que o utilizador faz parte                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Obtenha informações para um fio de chat particular                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Enviar e receber mensagens num fio de chat                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Editar o conteúdo de uma mensagem enviada                                                                                | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Eliminar uma mensagem                                                                                                       | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Leia os recibos de mensagens que foram lidas por outros participantes num chat <br/> *Não disponível quando há mais de 20 participantes num fio de chat*    | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Seja notificado quando os participantes estão a escrever ativamente uma mensagem num fio de chat <br/> *Não disponível quando há mais de 20 membros numa linha de chat*      | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | Receba todas as mensagens num fio de chat <br/>                                                                         | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Enviar emojis Unicode como parte do conteúdo da mensagem                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|Sinalização em tempo real (ativada por pacote de sinalização proprietário**)|  Subscreva para obter atualizações em tempo real para mensagens recebidas e outras operações na sua aplicação de chat. Para ver uma lista de atualizações suportadas para sinalização em tempo real, consulte [os conceitos de Chat](concepts.md#real-time-signaling)                                     | ✔️   | ❌    | ❌  | ❌  | ❌  | ❌  |    
| Suporte à grelha de eventos             | Use a integração com a Azure Event Grid e configuure o seu serviço de comunicação para executar lógica de negócio com base na atividade de chat ou para ligar um serviço de notificação push personalizado   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
| Monitorização        | Utilize as métricas de pedido da API emitidas no portal Azure para construir dashboards, monitorizar a saúde da sua app de chat e definir alertas para detetar anomalias      | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Configure o seu recurso de Serviços de Comunicação para receber registos operacionais de chat para fins de monitorização e diagnóstico          | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  


**O pacote de sinalização proprietário é implementado com tomadas web. Vai voltar a ser votado se as tomadas web não forem apoiadas.  

## <a name="javascript-chat-client-library-support-by-os-and-browser"></a>JavaScript chat cliente suporte à biblioteca por OS e browser 

A tabela a seguir representa o conjunto de navegadores e versões suportadas que estão atualmente disponíveis.
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPad OS|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **Biblioteca de clientes de chat** | *Firefox, Chrome,* novo Edge | *Firefox, Chrome,* Safari* | Cromado*  | Cromado* | Cromado* | Safari* | Safari* |

*Note que a versão mais recente é suportada para além das duas versões anteriores.<br/>   

## <a name="next-steps"></a>Passos seguintes   

> [!div class="nextstepaction"] 
> [Começa com o chat](../../quickstarts/chat/get-started.md)    

Os seguintes documentos podem ser interessantes para si:  
- Familiarize-se com [conceitos de chat](../chat/concepts.md)