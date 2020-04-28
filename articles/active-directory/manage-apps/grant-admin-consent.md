---
title: Conceda consentimento de administrador a nível de inquilino para um pedido - Azure AD
description: Saiba como conceder o consentimento de um inquilino a uma aplicação para que os utilizadores finais não sejam solicitados para o consentimento ao iniciar a sua inscrição numa aplicação.
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: c515fef4997720435c64bd5f3ae7b18f8921fc5d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75480921"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Conceda o consentimento do administrador de todo o inquilino a um pedido

Aprenda a simplificar a experiência do utilizador concedendo consentimento de administração a nível de inquilino sanções a uma aplicação. Este artigo dá as diferentes formas de o conseguir. Os métodos aplicam-se a todos os utilizadores finais do seu inquilino Azure Ative Directory (Azure AD).

Para obter mais informações sobre o consentimento das candidaturas, consulte o quadro de [consentimento do Diretório Ativo do Azure](../develop/consent-framework.md).

## <a name="prerequisites"></a>Pré-requisitos

A concessão do consentimento do administrador em todo o inquilino requer que você assine como [Administrador Global,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)um Administrador de [Aplicação](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)ou um Administrador de [Aplicação cloud](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).

> [!IMPORTANT]
> Quando uma aplicação tiver recebido o consentimento da administração em todo o inquilino, todos os utilizadores poderão iniciar sessão na app, a menos que tenha sido configurado para exigir a atribuição do utilizador. Para restringir quais os utilizadores que podem iniciar sessão numa aplicação, exigir a atribuição do utilizador e, em seguida, atribuir utilizadores ou grupos à aplicação. Para mais informações, consulte [Métodos de atribuição de utilizadores e grupos.](methods-for-assigning-users-and-groups.md)

> [!WARNING]
> A concessão do consentimento da administração de um inquilino a uma aplicação irá conceder à app e ao editor da app acesso aos dados da sua organização. Reveja cuidadosamente as permissões que o pedido solicita antes de conceder o consentimento.

## <a name="grant-admin-consent-from-the-azure-portal"></a>Grant administrador consentimento do portal Azure

### <a name="grant-admin-consent-in-enterprise-apps"></a>Grant admin consent em aplicativos da Enterprise

Você pode conceder o consentimento de administração em todo o inquilino através de *pedidos da Enterprise* se o pedido já tiver sido provisionado no seu inquilino. Por exemplo, uma aplicação poderia ser aprovisionada no seu inquilino se pelo menos um utilizador já tivesse consentido com a aplicação. Para mais informações, consulte [Como e por que as aplicações são adicionadas ao Diretório Ativo Azure](../develop/active-directory-how-applications-are-added.md).

Para conceder o consentimento do administrador de todo o inquilino a uma aplicação listada nas **aplicações da Enterprise:**

1. Inscreva-se no [portal Azure](https://portal.azure.com) como [Administrador Global,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)Administrador de [Aplicações](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)ou Administrador de [Aplicações na Nuvem.](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)
2. Selecione **Diretório Ativo Azure** e depois **aplicações da Enterprise.**
3. Selecione o pedido ao qual pretende conceder o consentimento do administrador para o arrendatário.
4. Selecione **Permissões** e, em seguida, clique em **Grant administrador consentimento**.
5. Reveja cuidadosamente as permissões que o pedido requer.
6. Se concordar com as permissões que o pedido requer, conceda o consentimento. Caso contrário, clique **em Cancelar** ou fechar a janela.

### <a name="grant-admin-consent-in-app-registrations"></a>Concessão de consentimento administrativo nos registos da App

Para aplicações que a sua organização desenvolveu, ou que estão registadas diretamente no seu inquilino Azure AD, também pode conceder o consentimento do administrador de nível inquilino a partir de registos de **Aplicações** no portal Azure.

Para conceder o consentimento do administrador de todo o inquilino dos registos da **App:**

1. Inscreva-se no [portal Azure](https://portal.azure.com) como [Administrador Global,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)Administrador de [Aplicações](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)ou Administrador de [Aplicações na Nuvem.](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)
2. Selecione **Diretório Ativo Azure** e depois registos de **aplicações.**
3. Selecione o pedido ao qual pretende conceder o consentimento do administrador para o arrendatário.
4. Selecione **permissões API** e, em seguida, clique em **Grant administrador consentimento**.
5. Reveja cuidadosamente as permissões que o pedido requer.
6. Se concordar com as permissões que o pedido requer, conceda o consentimento. Caso contrário, clique **em Cancelar** ou fechar a janela.

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Construa o URL para conceder o consentimento do administrador em todo o inquilino

Ao conceder o consentimento administrativo em todo o inquilino utilizando qualquer método acima descrito, uma janela abre-se do portal Azure para solicitar o consentimento da administração em todo o inquilino. Se você conhece o ID do cliente (também conhecido como id de aplicação) do pedido, você pode construir o mesmo URL para conceder o consentimento de administração em todo o inquilino.

O URL de consentimento de administração em todo o inquilino segue o seguinte formato:

    https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}

em que:

* `{client-id}`é o ID do cliente da aplicação (também conhecido como ID da aplicação).
* `{tenant-id}`é o ID do inquilino da sua organização ou qualquer nome de domínio verificado.

Como sempre, reveja cuidadosamente as permissões que um pedido solicita antes de conceder o consentimento.

## <a name="next-steps"></a>Passos seguintes

[Configure como os utilizadores finais consentem com as aplicações](configure-user-consent.md)

[Configure o fluxo de trabalho de consentimento da administração](configure-admin-consent-workflow.md)

[Permissões e consentimento na plataforma de identidade da Microsoft](../develop/active-directory-v2-scopes.md)

[Anúncio Azure em StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
