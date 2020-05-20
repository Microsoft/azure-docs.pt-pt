---
title: Como enviar eventos de Auth0 para Azure usando a Grelha de Eventos Azure
description: Como terminar os eventos de Auth0 para serviços Azure com azure Event Grid.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 950b17611ba4932cc986d1b6488b904f29fdf04b
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83691321"
---
# <a name="integrate-azure-event-grid-with-auth0"></a>Integrar a Grelha de Eventos Azure com a Auth0

Este artigo descreve como ligar as suas contas Auth0 e Azure criando um tópico de parceiro event grid.

Consulte os [códigos do tipo de evento Auth0](https://auth0.com/docs/logs/references/log-event-type-codes) para obter uma lista completa dos eventos que a Auth0 suporta

## <a name="send-events-from-auth0-to-azure-event-grid"></a>Envie eventos de Auth0 para Azure Event Grid
Para enviar eventos Auth0 para Azure:

1. Ativar o fornecedor de recursos do Event Grid
1. Criar um tópico de parceiro Auth0 no Painel Auth0
1. Ativar o tópico do parceiro em Azure
1. Subscreva eventos a partir de Auth0

Para obter mais informações sobre estes conceitos, consulte os conceitos da Grelha de [Eventos.](concepts.md)

### <a name="enable-event-grid-resource-provider"></a>Ativar o fornecedor de recursos do Event Grid
A menos que tenha usado a Grelha de Eventos antes, terá de registar o fornecedor de recursos da Rede de Eventos. Se já usou a Grelha de Eventos antes, salte para a secção seguinte.

No portal do Azure:
1. Selecione Subscrições no menu esquerdo
1. Selecione a subscrição que está a usar para a Grelha de Eventos
1. No menu esquerdo, em Definições, selecione Fornecedores de Recursos
1. Encontre Microsoft.EventGrid
1. Selecione Registar
1. Atualizar para garantir que o estado se altera ao Registado

### <a name="set-up-an-auth0-partner-topic"></a>Configurar um tópico de parceiro Auth0
Parte do processo de integração é configurar o Auth0 para ser usado como fonte de evento (este passo acontece no seu [Painel Auth0).](https://manage.auth0.com/)

1. Faça login no [Painel Auth0](https://manage.auth0.com/).
1. Navegue para Logs > Streams.
1. Clique + Criar o Stream.
1. Selecione Azure Event Grid e introduza um nome único para o seu novo stream.
1. Crie a fonte do evento fornecendo o seu ID de Subscrição Azure, região azure e um nome de Grupo de Recursos. 
1. Clique em Guardar.

### <a name="activate-your-auth0-partner-topic-in-azure"></a>Ative o seu Tópico parceiro Auth0 em Azure
Ativar o tema Auth0 em Azure permite que os eventos fluam de Auth0 para Azure.

1. Inicie sessão no portal do Azure.
1. Procure `Partner Topics` no topo e clique em `Event Grid Partner Topics` serviços.
1. Clique no tópico que corresponde ao fluxo que criou no seu Painel Auth0.
1. Confirme que o `Source` campo corresponde à sua conta Auth0.
1. Clique em Ativar.

### <a name="subscribe-to-auth0-events"></a>Subscreva eventos da Auth0

#### <a name="create-an-event-handler"></a>Criar um manipulador de eventos
Para testar o seu Tópico de Parceiro, vai precisar de um manipulador de eventos. Vá à sua subscrição Azure e gire um serviço que é suportado como um manipulador de [eventos](event-handlers.md) como uma [Função Azure](custom-event-to-function.md).

#### <a name="subscribe-to-your-auth0-partner-topic"></a>Subscreva o seu Tópico de Parceiro Auth0
Subscrever o seu Tópico de Parceiro Auth0 permite-lhe dizer à Rede de Eventos para onde quer que os seus eventos Auth0 vão.

1. Na lâmina tópico do parceiro para a sua integração Auth0, selecione + Subscrição de Eventos no topo.
1. Na página de Subscrição de Eventos Criar:
    1. Insira um nome para a subscrição do evento.
    1. Selecione o serviço Azure ou WebHook que criou para o tipo Endpoint.
    1. Siga as instruções para o serviço em particular.
    1. Clique em Criar.

## <a name="testing"></a>Testar
A integração do Tópico do Parceiro Auth0 com o Azure deve estar pronta a ser utilizada.

### <a name="verify-the-integration"></a>Verificar a integração
Para verificar se a integração está a funcionar como esperado:

1. Faça login no Painel Auth0.
1. Navegue para Logs > Streams.
1. Clique no seu fluxo de Grelha de Eventos.
1. Uma vez no fluxo, clique no separador Saúde. O fluxo deve estar ativo e desde que não veja erros, o fluxo está funcionando.

Tente [invocar qualquer uma das ações do Auth0 que desencadeiem um evento a ser publicado](https://auth0.com/docs/logs/references/log-event-type-codes) para ver os eventos fluirem.

## <a name="delivery-attempts-and-retries"></a>Tentativas e tentativas de entrega
Os eventos auth0 são entregues ao Azure através de um mecanismo de streaming. Cada evento é enviado como é desencadeado em Auth0. Se a Rede de Eventos não conseguir receber o evento, a Auth0 voltará a tentar até três vezes para entregar o evento. Caso contrário, a Auth0 registará a falha na entrega no seu sistema.

## <a name="next-steps"></a>Passos seguintes

- [Tópico parceiro auth0](auth0-overview.md)
- [Visão geral dos tópicos dos parceiros](partner-topics-overview.md)
- [Torne-se um parceiro da Grelha de Eventos](partner-onboarding-overview.md)