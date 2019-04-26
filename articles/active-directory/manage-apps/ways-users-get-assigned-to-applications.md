---
title: Como atribuir utilizadores a aplicações | Documentos da Microsoft
description: Compreender como os utilizadores recebem a uma aplicação no seu inquilino
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: be36bfeb0991dc4a4b7fce723cadb6b508c0d6a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60291029"
---
# <a name="how-to-assign-users-to-applications"></a>Como atribuir utilizadores a aplicações

Este artigo ajuda-o a compreender como os utilizadores recebem a uma aplicação no seu inquilino.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Como os utilizadores ser atribuídos a uma aplicação no Azure AD?

Para um utilizador aceder a uma aplicação, eles tem primeiro de atribuir a ele de alguma forma. Atribuição pode ser executada por um administrador, um delegado de negócios ou, às vezes, o utilizador o propriamente ditas. Abaixo descreve as formas como os utilizadores podem ser atribuídos a aplicativos:

1.  Um administrador [atribui um utilizador](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) diretamente na aplicação

2.  Um administrador [atribui um grupo](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) que o utilizador é membro para o aplicativo, incluindo:

    * Um grupo que foi sincronizado no local

    * Um grupo de segurança estático criado na cloud

    * R [grupo de segurança dinâmica](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) criados na cloud

    * Um grupo do Office 365 criado na cloud

    * O [todos os utilizadores](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups) grupo

3.  Um administrador ativa [acesso a aplicações Self-Service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) para permitir que um utilizador adicionar uma aplicação com o [painel de acesso de aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Adicionar aplicação** funcionalidade **sem a aprovação de negócios**

4.  Um administrador ativa [acesso a aplicações Self-Service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) para permitir que um utilizador adicionar uma aplicação com o [painel de acesso de aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Adicionar aplicação** funcionalidade, mas apenas w**om aprovação anterior de um conjunto selecionado de aprovadores empresariais**

5.  Um administrador ativa [gestão de grupos Self-Service](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) para permitir que um utilizador aderir a um grupo que uma aplicação é atribuída a **sem a aprovação de negócios**

6.  Um administrador ativa [gestão de grupos Self-Service](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) para permitir que um utilizador aderir a um grupo que uma aplicação é atribuída a, mas apenas **com aprovação anterior de um conjunto selecionado de aprovadores empresariais**

7.  Um administrador atribui uma licença a um utilizador diretamente para uma primeira aplicação de terceiros, como [do Microsoft Office 365](https://products.office.com/)

8.  Um administrador atribui uma licença a um grupo que o utilizador é membro para uma primeira aplicação de terceiros, como [do Microsoft Office 365](https://products.office.com/)

9.  Uma [administrador autorizar uma aplicação](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) a serem utilizados por todos os utilizadores e, em seguida, um utilizador inicia sessão na aplicação

10. Um usuário [autorizar uma aplicação](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) próprios ao iniciar sessão na aplicação

## <a name="next-steps"></a>Passos Seguintes
[Managing Applications with Azure Active Directory](what-is-application-management.md) (Gerir Aplicações com o Azure Active Directory)
