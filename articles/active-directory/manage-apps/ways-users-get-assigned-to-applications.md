---
title: Como atribuir utilizadores a aplicações Microsoft Docs
description: Entenda como os utilizadores são designados para uma aplicação no seu inquilino
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 516bffa7057f8fee3b8e38d46f3b2da905880044
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88639941"
---
# <a name="how-to-assign-users-to-applications"></a>Como atribuir utilizadores a aplicações

Este artigo ajuda-o a entender como os utilizadores são designados para uma aplicação no seu inquilino.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Como é que os utilizadores são designados para uma aplicação no Azure AD?

Para que um utilizador aceda a uma aplicação, deve primeiro ser-lhe atribuído de alguma forma. A atribuição pode ser realizada por um administrador, um delegado de negócios, ou, por vezes, o próprio utilizador. Abaixo descreve a forma como os utilizadores podem ser designados para aplicações:

1.  Um administrador [atribui um utilizador](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) à aplicação diretamente

2.  Um administrador [atribui um grupo](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) ao qual o utilizador é membro da aplicação, incluindo:

    * Um grupo que foi sincronizado a partir do local

    * Um grupo de segurança estática criado na nuvem

    * Um [grupo de segurança dinâmico](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) criado na nuvem

    * Um grupo do Office 365 criado na nuvem

    * O grupo [de todos os utilizadores](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups)

3.  Um administrador permite o Acesso a [Aplicações self-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) para permitir que um utilizador adicione uma aplicação usando a funcionalidade [My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Add App** sem **aprovação do negócio**

4.  Um administrador permite o Acesso a [Aplicações self-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) para permitir que um utilizador adicione uma aplicação utilizando a funcionalidade [My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Add App,** mas apenas w**aprovação prévia de um conjunto selecionado de aprovadores de negócios**

5.  Um administrador permite que a [Self-service Group Management](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) permita que um utilizador se junte a um grupo a que uma aplicação é atribuída **sem aprovação do negócio**

6.  Um administrador permite que a [Self-service Group Management](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) permita que um utilizador se junte a um grupo ao qual uma aplicação é atribuída, mas **apenas com aprovação prévia de um conjunto selecionado de aprovadores de negócios**

7.  Um administrador atribui uma licença a um utilizador diretamente para uma primeira aplicação de partido, como [o Microsoft Office 365](https://products.office.com/)

8.  Um administrador atribui uma licença a um grupo do qual o utilizador é membro de uma aplicação de primeira parte, como [o Microsoft Office 365](https://products.office.com/)

9.  Um [administrador consente que uma aplicação](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) seja usada por todos os utilizadores e, em seguida, um utilizador assina a aplicação

10. Um utilizador [consente com uma aplicação](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) por si mesmo, inscrevendo-se na aplicação

## <a name="next-steps"></a>Passos seguintes
[Gestão de Aplicações com Diretório Ativo Azure](what-is-application-management.md)
