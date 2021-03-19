---
title: Como gerir as contas dos utilizadores na Azure API Management | Microsoft Docs
description: Saiba como criar ou convidar utilizadores na Azure API Management. Ver recursos adicionais para usar após a criação de uma conta de programador.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 92e032eb104835788f515cc7800fe5dacfa8adaa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88566136"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Como gerir contas de utilizador na Gestão de API do Azure

Na Gestão da API, os desenvolvedores são os utilizadores das APIs que expõe usando a API Management. Este guia mostra como criar e convidar os desenvolvedores a utilizarem as APIs e os produtos que lhes disponibiliza com a sua instância de Gestão da API. Para obter informações sobre a gestão das contas dos utilizadores programáticamente, consulte a documentação da [entidade utilizadora](/rest/api/apimanagement/2019-12-01/user) na referência [API Management REST.](/rest/api/apimanagement/)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Pré-requisitos

Tarefas completas neste artigo: [Criar uma instância de Gestão API Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-new-developer"></a><a name="create-developer"> </a>Criar um novo desenvolvedor

Para adicionar um novo utilizador, siga os passos desta secção:

1. Selecione o **separador Utilizadores** à esquerda do ecrã.
2. Prima **+Adicionar**.
3. Introduza informações apropriadas para o utilizador.
4. Prima **Adicionar**.

    ![Adicionar um novo utilizador](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Por padrão, as contas de programadores recém-criadas são **Ativas** e associadas ao grupo **Developers.** As contas de programadores que se encontrem num estado **ativo** podem ser usadas para aceder a todas as APIs para as quais têm subscrições. Para associar o programador recém-criado a grupos adicionais, consulte [Como associar grupos com desenvolvedores.][How to associate groups with developers]

## <a name="invite-a-developer"></a><a name="invite-developer"> </a>Convidar um desenvolvedor
Para convidar um desenvolvedor, siga os passos desta secção:

1. Selecione o **separador Utilizadores** à esquerda do ecrã.
2. Prima **+Convidar**.

Uma mensagem de confirmação é exibida, mas o programador recém-convidado só aparece na lista depois de aceitar o convite. 

Quando um desenvolvedor é convidado, um e-mail é enviado para o desenvolvedor. Este e-mail é gerado usando um modelo e é personalizável. Para obter mais informações, consulte [os modelos de e-mail configure][Configure email templates].

Uma vez que o convite é aceite, a conta torna-se ativa.

## <a name="deactivate-or-reactivate-a-developer-account"></a><a name="block-developer"></a> Desativar ou reativar uma conta de programador

Por padrão, as contas de programadores recém-criadas ou convidadas são **Ativas.** Para desativar uma conta de programador, clique em **Bloquear**. Para reativar uma conta de programador bloqueada, clique em **Ativar**. Uma conta de programador bloqueada não pode aceder ao portal do desenvolvedor ou chamar APIs. Para eliminar uma conta de utilizador, clique em **Eliminar**.

Para bloquear um utilizador, siga os seguintes passos.

1. Selecione o **separador Utilizadores** à esquerda do ecrã.
2. Clique no utilizador que pretende bloquear.
3. **Bloco de** Imprensa .

## <a name="reset-a-user-password"></a>Repor a palavra-passe de um utilizador

Para trabalhar programáticamente com contas de utilizador, consulte a documentação da entidade utilizadora na referência [API Management REST API.](/rest/api/apimanagement/) Para redefinir uma palavra-passe da conta de utilizador para um valor específico, pode utilizar a [atualização de uma](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity#UpdateUser) operação de utilizador e especificar a palavra-passe desejada.

## <a name="next-steps"></a><a name="next-steps"> </a>Passos seguintes
Uma vez criada uma conta de programador, pode associá-la a funções e subscrevê-la em produtos e APIs. Para obter mais informações, consulte [Como criar e utilizar grupos.][How to create and use groups]

[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png


[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates
