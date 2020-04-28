---
title: Como Atribuir utilizadores a aplicações Microsoft Docs
description: Compreenda como os utilizadores são atribuídos a uma aplicação no seu inquilino
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b818fe1d8b6bbc9d2d8c5b460b4d71dccdd39366
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "65825978"
---
# <a name="how-to-assign-users-to-applications"></a>Como atribuir utilizadores a aplicações

Este artigo ajuda-o a entender como os utilizadores são atribuídos a uma aplicação no seu inquilino.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Como é que os utilizadores são atribuídos a uma aplicação em Azure AD?

Para que um utilizador aceda a uma aplicação, deve primeiro ser-lhe atribuído de alguma forma. A atribuição pode ser realizada por um administrador, um delegado de negócios, ou, por vezes, o próprio utilizador. Abaixo descreve as formas como os utilizadores podem ser atribuídos a aplicações:

1.  Um administrador [atribui um utilizador](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) diretamente à aplicação

2.  Um administrador [atribui a um grupo](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) que o utilizador é membro da aplicação, incluindo:

    * Um grupo que foi sincronizado a partir do local

    * Um grupo de segurança estática criado na nuvem

    * Um [grupo de segurança dinâmico](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) criado na nuvem

    * Um grupo do Office 365 criado na nuvem

    * O grupo [All Users](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups)

3.  Um administrador permite o acesso à [aplicação self-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) para permitir que um utilizador adicione uma aplicação utilizando a funcionalidade de adição de **app** do Painel de Acesso à [Aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **sem aprovação de empresas**

4.  Um administrador permite o acesso à [aplicação self-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) para permitir que um utilizador adicione uma aplicação utilizando a funcionalidade de **adição** de app do Painel de Acesso à [Aplicação,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) mas apenas w**ith aprovação prévia de um conjunto selecionado de aprovadores de negócios**

5.  Um administrador permite que a [Gestão de Grupos self-service](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) permita que um utilizador se junte a um grupo ao quais uma aplicação é atribuída **sem aprovação** de negócios

6.  Um administrador permite que a [Gestão de Grupos self-service](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) permita que um utilizador se junte a um grupo ao a que uma aplicação é atribuída, mas apenas **com aprovação prévia de um conjunto selecionado de aprovadores de negócios**

7.  Um administrador atribui uma licença a um utilizador diretamente para uma aplicação de primeira parte, como [o Microsoft Office 365](https://products.office.com/)

8.  Um administrador atribui uma licença a um grupo que o utilizador é membro de uma aplicação de primeira parte, como o [Microsoft Office 365](https://products.office.com/)

9.  Um [administrador consente numa aplicação](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) a ser utilizada por todos os utilizadores e, em seguida, um utilizador insere-se na aplicação

10. Um utilizador [consente com uma aplicação](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) por si mesmo, assinando a aplicação

## <a name="next-steps"></a>Passos seguintes
[Gestão de Aplicações com Diretório Ativo Azure](what-is-application-management.md)
