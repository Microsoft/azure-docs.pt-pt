---
title: Configurar notificações e modelos de e-mail na API Management do Azure | Documentos da Microsoft
description: Saiba como configurar notificações e modelos de e-mail na API Management do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: apimpm
ms.openlocfilehash: 2a959c9d131c6aa0bdc99450cf2b6f09a5d8bfa7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102354"
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Como configurar notificações e modelos de e-mail na API Management do Azure
Gestão de API fornece a capacidade para configurar notificações para eventos específicos e para configurar os modelos de e-mail que são utilizados para comunicar com os administradores e desenvolvedores de uma instância de gestão de API. Este artigo mostra como configurar notificações para os eventos disponíveis e fornece uma descrição geral da configuração os modelos de e-mail utilizados para esses eventos.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma instância de serviço de gestão de API, conclua o início rápido seguinte: [Criar uma instância de gestão de API do Azure](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="publisher-notifications"> </a>Configurar notificações

1. Selecione seu **gestão de API** instância.
2. Clique em **notificações** para ver as notificações disponíveis.

    ![Notificações de publicador][api-management-publisher-notifications]

    A lista seguinte de eventos pode ser configurada para notificações.

   * **Pedidos de subscrição (o que requer aprovação)** -os destinatários de e-mail especificado e os utilizadores irão receber notificações por e-mail sobre pedidos de subscrição para os produtos de API que exigem a aprovação.
   * **Novas subscrições** -os destinatários de e-mail especificado e os utilizadores irão receber notificações por e-mail sobre novas subscrições de produto de API.
   * **Pedidos de Galeria de aplicações** -os destinatários de e-mail especificado e os utilizadores irão receber notificações por e-mail quando novos aplicativos são submetidos para a Galeria de aplicações.
   * **BCC** -os destinatários de e-mail especificado e os utilizadores irão receber o e-mail cego emissões de carbono cópias de todos os e-mails enviados para os desenvolvedores.
   * **Novo problema ou comentário** - os destinatários de e-mail especificado e os utilizadores irão receber notificações por e-mail quando um novo problema ou comentário é submetido no portal do programador.
   * **Mensagem de fechar conta** -os destinatários de e-mail especificado e os utilizadores irão receber notificações por e-mail quando uma conta está fechada.
   * **Limite de quota de subscrição aproximado** -os seguintes destinatários de e-mail e os utilizadores irão receber notificações por e-mail quando a utilização da subscrição chega perto de quota de utilização.

     Para cada evento, pode especificar usando a caixa de texto de endereço de e-mail de destinatários de e-mail ou pode selecionar os utilizadores a partir de uma lista.

3. Para especificar os endereços de e-mail para ser notificado, insira-os na caixa de texto de endereço de e-mail. Se tiver vários endereços de e-mail, separá-los com vírgulas.

    ![Destinatários de notificação][api-management-email-addresses]
4. Prima **Adicionar**.

## <a name="email-templates"> </a>Configurar modelos de notificação
Gestão de API fornece modelos de notificação para as mensagens de e-mail que são enviadas no decorrer de administrar e utilizar o serviço. São fornecidos os seguintes modelos de e-mail.

* Submissão de Galeria de aplicações aprovada
* Letra de farewell de programador
* Limite de quota de desenvolvedor aproximar-se de notificação
* Convidar utilizador
* Novo comentário adicionado a um problema
* Novo problema recebido
* Nova subscrição ativada
* Confirmação de subscrição renovada
* Recusa o pedido de subscrição
* Recebido o pedido de subscrição

Estes modelos podem ser modificados conforme pretendido.

Para ver e configurar os modelos de e-mail para a sua instância de gestão de API, clique em **modelos de notificações**.

![Modelos de E-mail][api-management-email-templates]

Cada modelo de e-mail tem um assunto em texto simples e uma definição de corpo no formato HTML. Cada item pode ser personalizado conforme pretendido.

![Editor de modelos de e-mail][api-management-email-template]

O **parâmetros** lista contém uma lista de parâmetros, que, quando inserido no assunto ou no corpo, será substituído o valor designado quando o e-mail é enviado. Para inserir um parâmetro, coloque o cursor em que desejar que o parâmetro para ir e clique na seta à esquerda do nome do parâmetro.

> [!NOTE] 
> Os parâmetros não são substituídos pelos valores reais quando pré-visualizo ou o envio de um teste.

Para guardar as alterações ao modelo de e-mail, clique em **salvar**, ou para cancelar o clique de alterações **descartar**.
 

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
