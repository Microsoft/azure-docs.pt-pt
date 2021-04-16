---
title: Visão geral do Chat SDK para serviços de comunicação Azure
titleSuffix: An Azure Communication Services concept document
description: Conheça o Azure Communication Services Chat SDK.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 561855704d157f9ad826b5db83600a79d9437fc6
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500690"
---
# <a name="chat-sdk-overview"></a>Visão geral do Chat SDK 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-chat.md)]

Os SDKs de Chat de Serviços de Comunicação Azure podem ser usados para adicionar conversas ricas e em tempo real às suas aplicações.
    
## <a name="chat-sdk-capabilities"></a>Capacidades chat SDK    

A lista a seguir apresenta o conjunto de funcionalidades que estão atualmente disponíveis nos Serviços de Comunicação chat SDKs.  

| Grupo de funcionalidades | Funcionalidade | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Capacidades do Núcleo | Criar um fio de chat entre 2 ou mais utilizadores                                                     | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Atualize o tópico de um fio de chat                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Adicione ou remova os participantes de um fio de chat                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Escolha se partilha o histórico de mensagens de chat com a adição do participante                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Obtenha uma lista de participantes em um fio de chat                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Excluir um fio de chat                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Dado um utilizador de comunicação, obtenha a lista de linhas de chat que o utilizador faz parte                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Obtenha informações para um fio de chat particular                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Enviar e receber mensagens num fio de chat                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Atualize o conteúdo da sua mensagem enviada                                                                               | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Apague uma mensagem que enviou anteriormente                                                                                                      | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Leia os recibos de mensagens que foram lidas por outros participantes num chat                                        | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Seja notificado quando os participantes estão a escrever ativamente uma mensagem num fio de chat                                         | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | Receba todas as mensagens num fio de chat                                                                        | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Enviar emojis Unicode como parte do conteúdo da mensagem                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|Notificações em tempo real (habilitados por pacote de sinalização proprietário**)|  Os clientes de chat podem subscrever para obter atualizações em tempo real para mensagens recebidas e outras operações que ocorram num fio de chat. Para ver uma lista de atualizações suportadas para notificações em tempo real, consulte [os conceitos de Chat](concepts.md#real-time-notifications)                                     | ✔️   | ❌    | ❌  | ❌  | ✔️  | ✔️  |   
| Integração com a grelha de eventos Azure             | Utilize os eventos de chat disponíveis na Azure Event Grid para tapar serviços de notificação personalizados ou publicar esse evento num webhook para executar lógica de negócios como atualizar registos de CRM após o fim de um chat   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
| Relatórios </br>(Esta informação está disponível no separador de monitorização do seu recurso de Serviços de Comunicação no portal Azure)      | Compreenda o tráfego da API a partir da sua app de chat monitorizando as métricas publicadas no Azure Metrics Explorer e deteta alertas para detetar anomalias     | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Monitorize e depure a sua solução de Serviços de Comunicação, permitindo a registo de diagnóstico para o seu recurso    | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   


**O pacote de sinalização proprietário é implementado com tomadas web. Vai voltar a ser votado se as tomadas web não forem apoiadas.  

## <a name="javascript-chat-sdk-support-by-os-and-browser"></a>Suporte SDK de Chat JavaScript por OS e browser    

A tabela a seguir representa o conjunto de navegadores e versões suportadas que estão atualmente disponíveis.
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPad OS|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **Chat SDK** | *Firefox, Chrome,* novo Edge | *Firefox, Chrome,* Safari* | Cromado*  | Cromado* | Cromado* | Safari* | Safari* |

*Note que a versão mais recente é suportada para além das duas versões anteriores.<br/>   

## <a name="next-steps"></a>Passos seguintes   

> [!div class="nextstepaction"] 
> [Começa com o chat](../../quickstarts/chat/get-started.md)    

Os seguintes documentos podem ser interessantes para si:  
- Familiarize-se com [conceitos de chat](../chat/concepts.md)
- Entenda como [os preços](../pricing.md#chat) funcionam para o chat
