---
title: Conceder consentimento administrativo a um pedido - Azure AD
description: Saiba como conceder o consentimento do inquilino a uma aplicação para que os utilizadores finais não sejam solicitados para o consentimento ao iniciarem a sessão de um pedido.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/04/2019
ms.author: kenwith
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c24023bdd3540785dec58ebeaa14d99ab659ab1
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85848376"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Conceder consentimento de administrador ao nível do inquilino a uma aplicação

Saiba como simplificar a experiência do utilizador concedendo consentimento administrativo a um pedido. Este artigo dá as diferentes formas de o conseguir. Os métodos aplicam-se a todos os utilizadores finais do seu inquilino Azure Ative(Azure AD).

Para obter mais informações sobre o consentimento dos pedidos, consulte [o quadro de consentimento do Diretório Ativo Azure](../develop/consent-framework.md).

## <a name="prerequisites"></a>Pré-requisitos

A concessão do consentimento administrativo ao inquilinos requer que se inscreva como [Administrador Global,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) [Administrador de Aplicação](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)ou [Administrador de Aplicação em Nuvem.](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)

> [!IMPORTANT]
> Quando uma aplicação tiver recebido o consentimento administrativo do arrendatário, todos os utilizadores poderão inscrever-se na app a menos que tenha sido configurada para exigir a atribuição do utilizador. Para restringir quais os utilizadores que podem iniciar sessão numa aplicação, exija a atribuição do utilizador e, em seguida, atribua utilizadores ou grupos à aplicação. Para obter mais informações, consulte [Métodos para atribuir utilizadores e grupos.](methods-for-assigning-users-and-groups.md)

> [!WARNING]
> A concessão do consentimento administrativo a uma aplicação concederá à app e ao editor da app acesso aos dados da sua organização. Reveja cuidadosamente as permissões que o pedido solicita antes de conceder o consentimento.

## <a name="grant-admin-consent-from-the-azure-portal"></a>Conceder consentimento administrativo do portal Azure

### <a name="grant-admin-consent-in-enterprise-apps"></a>Conceder consentimento administrativo em apps da Enterprise

Você pode conceder o consentimento administrativo do inquilino através de *pedidos da Enterprise* se o pedido já tiver sido apresentado no seu inquilino. Por exemplo, uma aplicação poderia ser disponibilizada no seu inquilino se pelo menos um utilizador já tiver consentido com a aplicação. Para mais informações, consulte [como e por que as aplicações são adicionadas ao Diretório Ativo Azure](../develop/active-directory-how-applications-are-added.md).

Para conceder o consentimento administrativo do arrendatário a uma aplicação listada em **aplicações da Enterprise:**

1. Inscreva-se no [portal Azure](https://portal.azure.com) como [Administrador Global,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) [Administrador de Aplicação](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)ou [Administrador de Aplicação cloud](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).
2. Selecione **Azure Ative Directory** e depois **aplicações enterprise**.
3. Selecione o pedido ao qual pretende conceder o consentimento administrativo ao arrendatário.
4. Selecione **Permissões** e, em seguida, clique em **Conceder o consentimento de administração**.
5. Reveja cuidadosamente as permissões que a aplicação requer.
6. Se concordar com as permissões que o pedido requer, conceda o consentimento. Caso contrário, clique em **Cancelar** ou feche a janela.

### <a name="grant-admin-consent-in-app-registrations"></a>Conceder consentimento administrativo nos registos da App

Para aplicações que a sua organização desenvolveu, ou que estão registadas diretamente no seu inquilino Azure AD, também pode conceder o consentimento administrativo a partir de **registos** da App no portal Azure.

Para conceder o consentimento administrativo do arrendatário a partir dos registos da **App:**

1. Inscreva-se no [portal Azure](https://portal.azure.com) como [Administrador Global,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) [Administrador de Aplicação](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)ou [Administrador de Aplicação cloud](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).
2. Selecione **Azure Ative Directory** e depois **registos de aplicações**.
3. Selecione o pedido ao qual pretende conceder o consentimento administrativo ao arrendatário.
4. Selecione **permissões API** e, em seguida, clique em **Grant consentimento administrativo**.
5. Reveja cuidadosamente as permissões que a aplicação requer.
6. Se concordar com as permissões que o pedido requer, conceda o consentimento. Caso contrário, clique em **Cancelar** ou feche a janela.

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Construa o URL para conceder consentimento administrativo a todo o arrendatário

Ao conceder o consentimento administrativo do arrendatário utilizando qualquer método descrito acima, uma janela abre-se a partir do portal Azure para solicitar o consentimento administrativo em todo o inquilino. Se você conhece o ID do cliente (também conhecido como iD de aplicação) do pedido, você pode construir o mesmo URL para conceder consentimento administrativo ao inquilino.

O URL de consentimento administrativo em todo o inquilino segue o seguinte formato:

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

em que:

* `{client-id}`é o ID do cliente da aplicação (também conhecido como ID da aplicação).
* `{tenant-id}`é o ID do inquilino da sua organização ou qualquer nome de domínio verificado.

Como sempre, reveja cuidadosamente as permissões que um pedido de pedido antes de conceder o consentimento.

## <a name="next-steps"></a>Próximos passos

[Configurar a forma como os utilizadores finais concedem consentimento às aplicações](configure-user-consent.md)

[Configure o fluxo de trabalho de consentimento administrativo](configure-admin-consent-workflow.md)

[Permissões e consentimento na plataforma de identidade da Microsoft](../develop/active-directory-v2-scopes.md)

[Azure AD no StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
