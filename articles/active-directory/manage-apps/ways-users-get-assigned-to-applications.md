---
title: Entenda como os utilizadores são atribuídos a apps no Azure Ative Directory
description: Compreenda como os utilizadores são designados para uma aplicação que está a usar o Azure Ative Directory para gestão de identidade.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: kenwith
ms.openlocfilehash: 161df0446c9478ca0f2b135c1e426f3786b164fc
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257445"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>Entenda como os utilizadores são atribuídos a apps no Azure Ative Directory
Este artigo ajuda-o a entender como os utilizadores são designados para uma aplicação no seu inquilino.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Como é que os utilizadores são designados para uma aplicação no Azure AD?
Para que um utilizador aceda a uma aplicação, deve primeiro ser-lhe atribuído de alguma forma. A atribuição pode ser realizada por um administrador, um delegado de negócios, ou, por vezes, o próprio utilizador. Abaixo descreve a forma como os utilizadores podem ser designados para aplicações:

*  Um administrador [atribui um utilizador](./assign-user-or-group-access-portal.md) à aplicação diretamente
*  Um administrador [atribui um grupo](./assign-user-or-group-access-portal.md) ao qual o utilizador é membro da aplicação, incluindo:
    * Um grupo que foi sincronizado a partir do local
    * Um grupo de segurança estática criado na nuvem
    * Um [grupo de segurança dinâmico](../enterprise-users/groups-dynamic-membership.md) criado na nuvem
    * Um grupo Microsoft 365 criado na nuvem
    * O grupo [de todos os utilizadores](../fundamentals/active-directory-groups-create-azure-portal.md)
*  Um administrador permite o Acesso a [Aplicações self-service](./manage-self-service-access.md) para permitir que um utilizador adicione uma aplicação usando a funcionalidade [My Apps](../user-help/my-apps-portal-end-user-access.md) **Add App** sem **aprovação do negócio**
*  Um administrador permite o Acesso a [Aplicações self-service](./manage-self-service-access.md) para permitir que um utilizador adicione uma aplicação utilizando a funcionalidade [My Apps](../user-help/my-apps-portal-end-user-access.md) **Add App,** mas **apenas com aprovação prévia de um conjunto selecionado de aprovadores de negócios**
*  Um administrador permite que a [Self-service Group Management](../enterprise-users/groups-self-service-management.md) permita que um utilizador se junte a um grupo a que uma aplicação é atribuída **sem aprovação do negócio**
*  Um administrador permite que a [Self-service Group Management](../enterprise-users/groups-self-service-management.md) permita que um utilizador se junte a um grupo ao qual uma aplicação é atribuída, mas **apenas com aprovação prévia de um conjunto selecionado de aprovadores de negócios**
*  Um administrador atribui uma licença a um utilizador diretamente para uma primeira aplicação de partido, como [o Microsoft 365](https://products.office.com/)
*  Um administrador atribui uma licença a um grupo do qual o utilizador é membro de uma aplicação de primeira parte, como [a Microsoft 365](https://products.office.com/)
*  Um [administrador consente que uma aplicação](../develop/howto-convert-app-to-be-multi-tenant.md) seja usada por todos os utilizadores e, em seguida, um utilizador assina a aplicação
* Um utilizador [consente com uma aplicação](../develop/howto-convert-app-to-be-multi-tenant.md) por si mesmo, inscrevendo-se na aplicação

## <a name="next-steps"></a>Passos seguintes
* [Quickstart Series em Gestão de Aplicações](view-applications-portal.md)
* [O que é a gestão de aplicações?](what-is-application-management.md)
* [O que é um único sinal?](what-is-single-sign-on.md)