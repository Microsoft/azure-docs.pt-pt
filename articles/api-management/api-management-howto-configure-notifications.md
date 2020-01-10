---
title: Configure notificações e modelos de e-mail
titleSuffix: Azure API Management
description: Saiba como configurar notificações e modelos de email no gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/02/2018
ms.author: apimpm
ms.openlocfilehash: e0477b1ba044004eaf95cc138bc2e19f40379566
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442685"
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Como configurar notificações e modelos de e-mail na API Management do Azure
O gerenciamento de API fornece a capacidade de configurar notificações para eventos específicos e configurar os modelos de email que são usados para se comunicar com os administradores e desenvolvedores de uma instância de gerenciamento de API. Este artigo mostra como configurar notificações para os eventos disponíveis e fornece uma visão geral da configuração dos modelos de email usados para esses eventos.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma instância de serviço de gerenciamento de API, conclua o início rápido a seguir: [criar uma instância de gerenciamento de API do Azure](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="publisher-notifications"> </a>Configurar notificações

1. Selecione sua instância de **Gerenciamento de API** .
2. Clique em **notificações** para exibir as notificações disponíveis.

    ![Notificações do Publicador][api-management-publisher-notifications]

    A lista de eventos a seguir pode ser configurada para notificações.

   * **Solicitações de assinatura (que exigem aprovação)** -os usuários e destinatários de email especificados receberão notificações por email sobre solicitações de assinatura para produtos de API que exigem aprovação.
   * **Novas assinaturas** -os usuários e destinatários de email especificados receberão notificações por email sobre novas assinaturas de produto de API.
   * **Solicitações da Galeria de aplicativos** -os usuários e destinatários de email especificados receberão notificações por email quando novos aplicativos forem enviados para a Galeria de aplicativos.
   * **Cco** -os usuários e destinatários de email especificados receberão cópias carbono de email de todos os emails enviados aos desenvolvedores.
   * **Novo problema ou comentário** -os usuários e destinatários de email especificados receberão notificações por email quando um novo problema ou comentário for enviado no portal do desenvolvedor.
   * **Mensagem fechar conta** -os destinatários de email e os usuários especificados receberão notificações por email quando uma conta for fechada.
   * Melhorando o **limite de cota de assinatura** -os seguintes destinatários de email e usuários receberão notificações por email quando o uso da assinatura ficar perto da cota de uso.

     Para cada evento, você pode especificar destinatários de email usando a caixa de texto endereço de email ou pode selecionar usuários de uma lista.

3. Para especificar os endereços de email a serem notificados, insira-os na caixa de texto endereço de email. Se você tiver vários endereços de email, separe-os usando vírgulas.

    ![Destinatários da notificação][api-management-email-addresses]
4. Prima **Adicionar**.

## <a name="email-templates"> </a>Configurar modelos de notificação
O gerenciamento de API fornece modelos de notificação para as mensagens de email que são enviadas no decorrer da administração e do uso do serviço. Os seguintes modelos de email são fornecidos.

* Envio da Galeria de aplicativos aprovado
* Carta de despedida do desenvolvedor
* Notificação de abordagem de limite de cota do desenvolvedor
* Convidar utilizador
* Novo comentário adicionado a um problema
* Novo problema recebido
* Nova assinatura ativada
* Confirmação de renovação de assinatura
* A solicitação de assinatura é recusada
* Solicitação de assinatura recebida

Esses modelos podem ser modificados conforme desejado.

Para exibir e configurar os modelos de email para sua instância de gerenciamento de API, clique em **modelos de notificações**.

![Modelos de E-mail][api-management-email-templates]

Cada modelo de email tem um assunto em texto sem formatação e uma definição de corpo no formato HTML. Cada item pode ser personalizado conforme desejado.

![Editor de modelos de email][api-management-email-template]

A lista de **parâmetros** contém uma lista de parâmetros, que, quando inseridos no assunto ou no corpo, será substituído o valor designado quando o email for enviado. Para inserir um parâmetro, coloque o cursor onde você deseja que o parâmetro vá e clique na seta à esquerda do nome do parâmetro.

> [!NOTE] 
> Os parâmetros não são substituídos por valores reais ao visualizar ou enviar um teste.

Para salvar as alterações no modelo de email, clique em **salvar**ou para cancelar as alterações, clique em **descartar**.
 

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png


[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png


[Configure publisher notifications]: #publisher-notifications
[Configure email templates]: #email-templates

[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
