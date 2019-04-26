---
title: Alterar o nome ou logótipo de uma aplicação empresarial no Azure Active Directory | Documentos da Microsoft
description: Como alterar o nome ou logótipo para uma aplicação empresarial personalizada no Azure Active Directory
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: celested
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62578fe037dc1c9672bd0a4cf28500c658344c53
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60440405"
---
# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory"></a>Alterar o nome ou logótipo de uma aplicação empresarial no Azure Active Directory
É fácil alterar o nome ou logótipo para uma aplicação empresarial personalizada no Azure Active Directory (Azure AD). Tem de ter as permissões adequadas para efetuar estas alterações e tem de ser o criador da aplicação personalizada.

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>Como posso alterar nome de uma aplicação empresarial ou logótipo?
1. Iniciar sessão no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Selecione **todos os serviços**, introduza **Azure Active Directory** na caixa de texto e, em seguida, selecione **Enter**.
3. Sobre o **Azure Active Directory – *directoryname***  painel (ou seja, o Azure AD para o diretório que está a gerir), selecione **aplicações empresariais**.

    ![Aplicações empresariais de abertura](./media/change-name-or-logo-portal/open-enterprise-apps.png)
4. Sobre o **aplicações empresariais** painel, selecione **todos os aplicativos**. Verá uma lista de aplicações que pode gerir.
5. Sobre o **aplicações empresariais - todas as aplicações** painel, selecione uma aplicação.
6. Sobre o ***appname*** painel (ou seja, o painel com o nome da aplicação selecionada no título), selecione **propriedades**.

    ![Selecionar o comando Propriedades](./media/change-name-or-logo-portal/select-app.png)
7. Sobre o ***appname*** **-propriedades** painel, navegue para um ficheiro para utilizar como um logótipo de novo ou editar o nome da aplicação, ou ambos.

    ![Alterar o comando de logótipo ou nameproperties de aplicação](./media/change-name-or-logo-portal/change-logo.png)
8. Selecione o **guardar** comando.

## <a name="next-steps"></a>Passos Seguintes
* [Ver todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](assign-user-or-group-access-portal.md)
* [Remover uma atribuição de utilizador ou grupo a partir de uma aplicação empresarial](remove-user-or-group-access-portal.md)
* [Desativar utilizador-inícios de sessão de uma aplicação empresarial](disable-user-sign-in-portal.md)
