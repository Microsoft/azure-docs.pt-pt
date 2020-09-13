---
title: Entenda como os utilizadores são atribuídos a apps no Azure Ative Directory
description: Compreenda como os utilizadores são designados para uma aplicação que está a usar o Azure Ative Directory para gestão de identidade.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: 243e5ece0d0a14cb7e3ade409ee68510cef64a9c
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89397105"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>Entenda como os utilizadores são atribuídos a apps no Azure Ative Directory
Este artigo ajuda-o a entender como os utilizadores são designados para uma aplicação no seu inquilino.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Como é que os utilizadores são designados para uma aplicação no Azure AD?
Para que um utilizador aceda a uma aplicação, deve primeiro ser-lhe atribuído de alguma forma. A atribuição pode ser realizada por um administrador, um delegado de negócios, ou, por vezes, o próprio utilizador. Abaixo descreve a forma como os utilizadores podem ser designados para aplicações:

*  Um administrador [atribui um utilizador](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) à aplicação diretamente
*  Um administrador [atribui um grupo](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) ao qual o utilizador é membro da aplicação, incluindo:
    * Um grupo que foi sincronizado a partir do local
    * Um grupo de segurança estática criado na nuvem
    * Um [grupo de segurança dinâmico](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) criado na nuvem
    * Um grupo do Office 365 criado na nuvem
    * O grupo [de todos os utilizadores](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups)
*  Um administrador permite o Acesso a [Aplicações self-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) para permitir que um utilizador adicione uma aplicação usando a funcionalidade [My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Add App** sem **aprovação do negócio**
*  Um administrador permite o Acesso a [Aplicações self-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) para permitir que um utilizador adicione uma aplicação utilizando a funcionalidade [My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Add App,** mas apenas w**aprovação prévia de um conjunto selecionado de aprovadores de negócios**
*  Um administrador permite que a [Self-service Group Management](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) permita que um utilizador se junte a um grupo a que uma aplicação é atribuída **sem aprovação do negócio**
*  Um administrador permite que a [Self-service Group Management](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) permita que um utilizador se junte a um grupo ao qual uma aplicação é atribuída, mas **apenas com aprovação prévia de um conjunto selecionado de aprovadores de negócios**
*  Um administrador atribui uma licença a um utilizador diretamente para uma primeira aplicação de partido, como [o Microsoft Office 365](https://products.office.com/)
*  Um administrador atribui uma licença a um grupo do qual o utilizador é membro de uma aplicação de primeira parte, como [o Microsoft Office 365](https://products.office.com/)
*  Um [administrador consente que uma aplicação](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) seja usada por todos os utilizadores e, em seguida, um utilizador assina a aplicação
* Um utilizador [consente com uma aplicação](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) por si mesmo, inscrevendo-se na aplicação

## <a name="next-steps"></a>Próximos passos
* [Quickstart Series em Gestão de Aplicações](view-applications-portal.md)
* [O que é a gestão de aplicações?](what-is-application-management.md)
* [O que é o início de sessão único?](what-is-single-sign-on.md)
