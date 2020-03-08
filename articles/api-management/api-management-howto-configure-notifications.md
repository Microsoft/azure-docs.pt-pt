---
title: Configure notificações e modelos de e-mail
titleSuffix: Azure API Management
description: Saiba como configurar notificações e modelos de e-mail na Gestão aPI azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 786a9e26003a7afb98307e0bd7fae94c42a2f00d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375099"
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Como configurar notificações e modelos de e-mail na API Management do Azure

A API Management fornece a capacidade de configurar notificações para eventos específicos, e configurar os modelos de e-mail que são usados para comunicar com os administradores e desenvolvedores de uma instância de Gestão API. Este artigo mostra como configurar notificações para os eventos disponíveis, e fornece uma visão geral da configuração dos modelos de e-mail utilizados para estes eventos.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma instância de serviço de Gestão API, complete o seguinte quickstart: Crie uma instância de [Gestão API Azure](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="publisher-notifications"> </a>Configurar notificações

1.  Selecione a sua instância **de GESTÃO API.**
2.  Clique em **Notificações** para ver as notificações disponíveis.

    ![Notificações da editora][api-management-publisher-notifications]

    A seguinte lista de eventos pode ser configurada para notificações.

    -   **Pedidos de subscrição (que requerem aprovação)** - Os destinatários e utilizadores especificados receberão notificações por e-mail sobre pedidos de subscrição de produtos API que necessitem de aprovação.
    -   **Novas subscrições** - Os destinatários e utilizadores de e-mail especificados receberão notificações por e-mail sobre novas subscrições de produtos API.
    -   Pedidos de galeria de **aplicações** - Os destinatários e utilizadores especificados receberão notificações de e-mail quando novas aplicações forem submetidas à galeria de aplicações.
    -   **BCC** - Os destinatários e utilizadores especificados receberão cópias de carbono cegas de todos os e-mails enviados aos desenvolvedores.
    -   **Novo problema ou comentário** - Os destinatários e utilizadores de e-mail especificados receberão notificações de e-mail quando um novo problema ou comentário for submetido no portal do desenvolvedor.
    -   **Mensagem de conta próxima** - Os destinatários e utilizadores especificados receberão notificações de e-mail quando uma conta estiver fechada.
    -   **Aproximando-se** do limite de quotas de subscrição - Os seguintes destinatários e utilizadores receberão notificações de e-mail quando o uso da subscrição se aproximar da quota de utilização.

        > [!NOTE]
        > As notificações são desencadeadas apenas pela quota por política de [subscrição.](api-management-access-restriction-policies.md#SetUsageQuota) [A política de quota por chave](api-management-access-restriction-policies.md#SetUsageQuotaByKey) não gera notificações.

    Para cada evento, pode especificar destinatários de e-mail utilizando a caixa de texto de endereço de e-mail ou pode selecionar utilizadores a partir de uma lista.

3.  Para especificar os endereços de e-mail a notificar, insira-os na caixa de texto de endereço de e-mail. Se tiver vários endereços de e-mail, separe-os usando vírgulas.

    ![Destinatários da notificação][api-management-email-addresses]

4.  Prima **Adicionar**.

## <a name="email-templates"> </a>Configurar modelos de notificação

A API Management fornece modelos de notificação para as mensagens de correio eletrónico que são enviadas no decurso da administração e utilização do serviço. Os seguintes modelos de e-mail são fornecidos.

-   Submissão de galeria de candidatura aprovada
-   Carta de despedida do desenvolvedor
-   Limite de quota do programador que se aproxima da notificação
-   Convidar o utilizador
-   Novo comentário adicionado a um problema
-   Nova emissão recebida
-   Nova subscrição ativada
-   Confirmação renovada por subscrição
-   Pedido de subscrição diminui
-   Pedido de subscrição recebido

Estes modelos podem ser modificados conforme desejado.

Para visualizar e configurar os modelos de e-mail para a sua instância de Gestão API, clique em **modelos de Notificações**.

![Modelos de E-mail][api-management-email-templates]

Cada modelo de e-mail tem um assunto em texto simples, e uma definição corporal em formato HTML. Cada item pode ser personalizado conforme desejado.

![Editor de modelo de e-mail][api-management-email-template]

A lista de **parâmetros** contém uma lista de parâmetros, que quando inseridos no sujeito ou corpo, serão substituídos o valor designado quando o e-mail for enviado. Para inserir um parâmetro, coloque o cursor onde desejar que o parâmetro se desloque e clique na seta à esquerda do nome do parâmetro.

> [!NOTE]
> Os parâmetros não são substituídos por valores reais ao visualizar ou enviar um teste.

Para guardar as alterações no modelo de e-mail, clique em **Guardar**, ou para cancelar as alterações clique em **Descartar**.

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png
[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png
[configure publisher notifications]: #publisher-notifications
[configure email templates]: #email-templates
[how to create and use groups]: api-management-howto-create-groups.md
[how to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[get started with azure api management]: get-started-create-service-instance.md
[create an api management service instance]: get-started-create-service-instance.md
