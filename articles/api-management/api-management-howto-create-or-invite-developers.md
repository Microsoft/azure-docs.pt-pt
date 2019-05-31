---
title: Como gerir contas de utilizador na gestão de API do Azure | Documentos da Microsoft
description: Saiba como criar ou convidar utilizadores na gestão de API do Azure
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
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: d9cc5da0cd2936c1ac79a308366b8164548cb202
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241730"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Como gerir contas de utilizador na gestão de API do Azure

Na gestão de API, os desenvolvedores são os utilizadores das APIs que expõem a utilizar a API Management. Este guia mostra como criar e convidar desenvolvedores para utilizar as APIs e os produtos que disponibilizar-lhes com a instância de gestão de API. Para obter informações sobre como gerir contas de usuário por meio de programação, consulte a [entidade User](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user) documentação na [REST de gestão de API](/rest/api/apimanagement/) referência.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Pré-requisitos

Concluir as tarefas neste artigo: [Criar uma instância de gestão de API do Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-developer"> </a>Criar um novo Programador

Para adicionar um novo utilizador, siga os passos nesta secção:

1. Selecione o **utilizadores** separador à esquerda do ecrã.
2. Prima **+ adicionar**.
3. Introduza as informações apropriadas para o utilizador.
4. Prima **Adicionar**.

    ![Adicionar um novo utilizador](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Por predefinição, são contas de programador recém-criado **Active Directory**e associados a **os desenvolvedores** grupo. As contas de desenvolvedores que estão numa **Active Directory** Estado pode ser utilizado para aceder a todas as APIs para as quais têm subscrições. Para associar o desenvolvedor acabada de criar grupos adicionais, consulte [como associar grupos a programadores][How to associate groups with developers].

## <a name="invite-developer"> </a>Convidar um desenvolvedor
Para convidar um desenvolvedor, siga os passos nesta secção:

1. Selecione o **utilizadores** separador à esquerda do ecrã.
2. Prima **+ convidar**.

É apresentada uma mensagem de confirmação, mas o desenvolvedor recentemente convidado não aparece na lista até assim que aceitarem o convite. 

Quando um desenvolvedor é convidado, é enviado um e-mail para o desenvolvedor. Este e-mail é gerado com um modelo e é personalizável. Para obter mais informações, consulte [configurar modelos de e-mail][Configure email templates].

Depois do convite for aceita, a conta fica ativa.

## <a name="block-developer"> </a> Desativar ou reativar uma conta de programador

Por predefinição, são contas de programador recentemente criados ou convidados **Active Directory**. Para desativar uma conta de programador, clique em **bloco**. Para reativar uma conta de programador bloqueado, clique em **Activate**. Uma conta de programador bloqueado não é possível aceder ao portal de programador ou chamam nenhuma API. Para eliminar uma conta de utilizador, clique em **eliminar**.

Para impedir um utilizador, siga os passos seguintes.

1. Selecione o **utilizadores** separador à esquerda do ecrã.
2. Clique no utilizador que pretende bloquear.
3. Prima **bloco**.

## <a name="reset-a-user-password"></a>Repor uma palavra-passe do utilizador

Para trabalhar programaticamente com contas de utilizador, consulte a documentação de entidade de usuário na [API do REST de gestão de API](/rest/api/apimanagement/) referência. Para repor uma palavra-passe da conta de utilizador para um valor específico, pode utilizar o [atualizar um utilizador](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity#UpdateUser) operação e especifique a palavra-passe pretendida.

## <a name="next-steps"> </a>Passos seguintes
Depois de criar uma conta de programador, pode associá-la com funções e subscrever produtos e APIs. Para obter mais informações, consulte [como criar e utilizar grupos][How to create and use groups].

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
