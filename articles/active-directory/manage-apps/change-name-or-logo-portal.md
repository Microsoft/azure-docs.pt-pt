---
title: Alterar o nome ou logotipo de uma aplicação da empresa em Azure AD
description: Como alterar o nome ou logotipo para uma aplicação de empresa personalizada no Azure Ative Directory
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/05/2019
ms.author: kenwith
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f8902f306f50659725bc4b12fbbb6fee7563c4b
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763674"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Alterar o nome ou logotipo de uma aplicação da empresa no Azure Ative Directory

É fácil alterar o nome ou logotipo para uma aplicação de empresa personalizada no Azure Ative Directory (Azure AD). Você deve ter as permissões apropriadas para fazer estas alterações, e você deve ser o criador da aplicação personalizada.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Como posso alterar o nome ou logotipo de uma aplicação empresarial?

1. Inscreva-se no [portal Azure Ative Directory](https://aad.portal.azure.com/) com uma conta que é um administrador global para o diretório. Aparece a página **do centro de administração Azure Ative Directory.**
2. No painel esquerdo, selecione **Aplicações empresariais**. A lista das suas aplicações empresariais aparece.
3. Selecione uma aplicação. A página geral da aplicação aparece.
4. No painel de visão geral da aplicação, no título **Manage,** selecione **Propriedades**. A página **Propriedades** aparece.
5. Se pretender alterar o nome, selecione a caixa **Nome,** digite o novo nome e prima **Enter**.
6. Se pretender alterar o logótipo, encontre o campo **Logo e** selecione o ícone da pasta ao lado da caixa **de ficheiros Select,** que está abaixo da imagem atual do logotipo da aplicação.

   ![Selecionando o comando de propriedades](./media/change-name-or-logo-portal/change-logo.png)

   Caso contrário, se não estiver a mudar o logótipo, vá para o passo 8.
7. No selecionador de ficheiros, selecione o ficheiro que pretende como novo logótipo. O nome do ficheiro aparece na caixa abaixo da imagem do logotipo atual.

   > [!NOTE]
   > O Azure requer que a imagem do logotipo seja um ficheiro PNG, e aplica limites de largura, altura e tamanho do ficheiro. Os logótipos personalizados devem ter exatamente 215 &times; 215 pixels de tamanho e estar em formato PNG. Recomendamos que utilize um fundo de cor sólido sem transparência no logotipo da sua aplicação para aparecer melhor para os utilizadores.
8. Selecione **Guardar**. Se escolheu um novo logótipo, **a** imagem do campo logo muda para refletir o novo ficheiro de logotipo.

## <a name="next-steps"></a>Passos seguintes

* [Início Rápido: ver os grupos e membros da sua organização no Azure Active Directory](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](assign-user-or-group-access-portal.md)
* [Remova uma atribuição de utilizador ou grupo de uma aplicação da empresa](remove-user-or-group-access-portal.md)
* [Desativar as ins ins ins do utilizador para uma aplicação da empresa](disable-user-sign-in-portal.md)
