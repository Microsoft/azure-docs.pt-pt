---
title: Alterar o nome ou logótipo de um aplicativo empresarial no Azure Active Directory | Documentos da Microsoft
description: Como alterar o nome ou logótipo para uma aplicação empresarial personalizada no Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30da8d6843c27c42d4d99adef50b9ad98a131c95
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65780929"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Alterar o nome ou logótipo de um aplicativo empresarial no Azure Active Directory

É fácil alterar o nome ou logótipo para uma aplicação empresarial personalizada no Azure Active Directory (Azure AD). Tem de ter as permissões adequadas para efetuar estas alterações e tem de ser o criador do aplicativo personalizado.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Como posso alterar nome de uma aplicação empresarial ou logótipo?

1. Inicie sessão para o [portal do Azure Active Directory](https://aad.portal.azure.com/) com uma conta que seja um administrador global do diretório. O **Centro de administração do Azure Active Directory** é apresentada a página.
2. No painel esquerdo, selecione **Aplicações empresariais**. É apresentada a lista de aplicações empresariais.
3. Selecione uma aplicação. É apresentada a página de descrição geral do aplicativo.
4. No painel de descrição geral da aplicação, sob o **Manage** cabeçalho, selecione **propriedades**. O **propriedades** é apresentada a página.
5. Se quiser alterar o nome, selecione o **Name** caixa, escreva o nome do novo e prima **Enter**.
6. Se quiser alterar o logótipo, encontrar o **logótipo** campo e selecione o ícone de pasta junto aos **selecionar um ficheiro** caixa, o que é inferior a imagem de logótipo atual do aplicativo.

   ![Selecionar o comando Propriedades](./media/change-name-or-logo-portal/change-logo.png)

   Caso contrário, se não estiver a alterar o logotipo, avance para o passo 8.
7. No Seletor de ficheiros, selecione o ficheiro que pretende como novo logótipo. O nome do ficheiro é apresentado na caixa abaixo da imagem de logótipo atual.

   > [!NOTE]
   > O Azure requer a imagem de logotipo a ser um ficheiro PNG e aplica-se limites em largura, altura e tamanho de ficheiro.
8. Selecione **Guardar**. Se optou por um novo logótipo, o **logótipo** alterações na imagem para refletir o novo ficheiro do logótipo do campo.

## <a name="next-steps"></a>Passos Seguintes

* [Quickstart: Ver grupos e membros da sua organização no Azure Active Directory](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](assign-user-or-group-access-portal.md)
* [Remover uma atribuição de utilizador ou grupo a partir de uma aplicação empresarial](remove-user-or-group-access-portal.md)
* [Desativar utilizador-inícios de sessão de uma aplicação empresarial](disable-user-sign-in-portal.md)
