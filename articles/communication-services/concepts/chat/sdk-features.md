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
ms.openlocfilehash: dcd8222b46262f6ec70459ec670789ae4a433c1d
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625269"
---
# <a name="chat-client-library-overview"></a>Descrição geral da biblioteca de cliente de chat

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

As bibliotecas de clientes Azure Communication Services Chat podem ser usadas para adicionar conversas ricas e em tempo real às suas aplicações.

## <a name="chat-client-library-capabilities"></a>Capacidades da biblioteca de clientes de chat

A lista a seguir apresenta o conjunto de funcionalidades que estão atualmente disponíveis nas bibliotecas de clientes de chat dos Serviços de Comunicação.

| Grupo de funcionalidades | Funcionalidade                                                                                                          | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | --- | ----- | ---- | -----  |
| Capacidades do Núcleo | Crie um fio de chat entre 2 ou mais utilizadores (até 250 utilizadores)                                                       | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Atualize o tópico de um fio de chat                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |
|                   | Adicione ou remova os membros de um fio de chat                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Escolha se partilhar o histórico de mensagens de chat com membros recém-adicionados - *tudo/nenhum/até determinado tempo* | ✔️   | ✔️   | ✔️    | ✔️  |
|                   | Obtenha uma lista de todos os membros do chat thread                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Excluir um fio de chat                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Obtenha uma lista das associações de chat thread de um utilizador                                                                  | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Obtenha informações para um fio de chat particular                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Enviar e receber mensagens num fio de chat                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |
|                   | Edite o conteúdo de uma mensagem depois de ter sido enviada                                                                   | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Eliminar uma mensagem                                                                                                       | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Marque uma mensagem com prioridade como normal ou alta no momento do envio                                               | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Enviar e receber recibos de leitura para mensagens que foram lidas por membros <br/> *Não disponível quando há mais de 20 membros numa linha de chat*    | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Enviar e receber notificações de dactilografia quando um membro está a escrever ativamente uma mensagem num fio de chat <br/> *Não disponível quando há mais de 20 membros numa linha de chat*      | ✔️   | ✔️   | ✔️    | ✔️    |
|                   | Receba todas as mensagens num fio de chat <br/> *Emojis unicode suportados*                                                  | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Enviar emojis como parte do conteúdo da mensagem                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |
|Sinalização em tempo real (ativada por pacote de sinalização proprietário)| Seja notificado quando um utilizador recebe uma nova mensagem num fio de chat que é membro da                                     | ✔️   | ❌    | ❌  | ❌  |
|                    | Seja notificado quando uma mensagem é editada por outro membro em um fio de chat eles são um membro de                | ✔️   | ❌    | ❌    | ❌  |
|                    | Seja notificado quando uma mensagem é apagada por outro membro num fio de chat que são membros da                | ✔️   | ❌    | ❌    | ❌  |
|                    | Seja notificado quando outro membro do fio de chat estiver digitando                                                             | ✔️   | ❌    | ❌    | ❌  |
|                    | Seja notificado quando outro membro tiver lido uma mensagem (ler recibo) no fio do chat                               | ✔️   | ❌    | ❌    | ❌  |
| Eventos             | Utilize a Grade de Eventos para subscrever a atividade do utilizador que acontece em linhas de chat e integre serviços de notificação personalizados ou lógica de negócio     | ✔️   | ✔️  | ✔️    | ✔️  |
| Monitorização        | Monitorize o uso em termos de mensagens enviadas                                                                               | ✔️   | ✔️  | ✔️    | ✔️  |
|                    | Monitorize a qualidade e o estado dos pedidos de API feitos pela sua app e configuure alertas através do portal                                                          | ✔️   | ✔️  | ✔️    | ✔️  |
|Características adicionais | Use [APIs de Serviços Cognitivos](../../../cognitive-services/index.yml) juntamente com a biblioteca de clientes de chat para permitir funcionalidades inteligentes - *tradução linguística & análise de sentimento da mensagem recebida num cliente, conversão de texto para compor uma mensagem enquanto o membro fala, etc.*                                                                                         | ✔️   | ✔️  | ✔️    | ✔️  |

## <a name="javascript-chat-client-library-support-by-os-and-browser"></a>JavaScript chat cliente suporte à biblioteca por OS e browser

A tabela a seguir representa o conjunto de navegadores e versões suportadas que estão atualmente disponíveis.

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPad OS|
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ | -------|
| **Biblioteca de clientes de chat** | *Firefox, Chrome,* novo Edge | *Firefox, Chrome,* Safari* | Cromado*  | Cromado* | Cromado* | Safari* | Safari* |


*Note que a versão mais recente é suportada para além das duas versões anteriores.<br/>

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começa com o chat](../../quickstarts/chat/get-started.md)

Os seguintes documentos podem ser interessantes para si:

- Familiarize-se com [conceitos de chat](../chat/concepts.md)
