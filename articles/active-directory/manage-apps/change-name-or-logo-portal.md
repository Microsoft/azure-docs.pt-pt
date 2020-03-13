---
title: Alterar o nome ou logotipo de uma aplicação empresarial em Azure AD
description: Como alterar o nome ou logotipo para uma aplicação empresarial personalizada no Diretório Ativo azure
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
ms.openlocfilehash: d23849df75d1ab239eb269b462abb21df196e7e5
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79138506"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Alterar o nome ou logotipo de uma aplicação empresarial no Diretório Ativo azure

É fácil alterar o nome ou logotipo para uma aplicação empresarial personalizada no Azure Ative Directory (Azure AD). Deve ter as permissões adequadas para efazer estas alterações, e deve ser o criador da aplicação personalizada.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Como posso alterar o nome ou logotipo de uma aplicação da empresa?

1. Inscreva-se no [portal azure Ative Diretório](https://aad.portal.azure.com/) com uma conta que é uma administração global para o diretório. Aparece a página do centro de **administração do Azure Ative Directory.**
2. No painel esquerdo, selecione **Aplicações empresariais**. A lista das suas aplicações empresariais aparece.
3. Selecione uma aplicação. A página geral da aplicação aparece.
4. No painel de visão geral da aplicação, sob a rubrica **Gerir,** selecione **Propriedades**. A página **Propriedades** aparece.
5. Se quiser alterar o nome, selecione a caixa **nome,** escreva o novo nome e prima **Enter**.
6. Se quiser alterar o logótipo, encontre o campo **Logo** e selecione o ícone da pasta ao lado da caixa de **ficheiros Select,** que fica abaixo da imagem atual do logotipo da aplicação.

   ![Selecionando o comando de propriedades](./media/change-name-or-logo-portal/change-logo.png)

   Caso contrário, se não estiver a mudar o logótipo, vá para o passo 8.
7. No marcador de ficheiros, selecione o ficheiro que pretende como novo logótipo. O nome do ficheiro aparece na caixa abaixo da imagem do logotipo atual.

   > [!NOTE]
   > O Azure requer que a imagem do logotipo seja um ficheiro PNG, e aplica limites no tamanho da largura, altura e arquivo. Os logótipos personalizados devem ter exatamente 215 &times; 215 pixels de tamanho e estar em formato PNG. Recomendamos que utilize um fundo de cores sólidas sem transparência no logotipo da sua aplicação para aparecer melhor para os utilizadores.
8. Selecione **Guardar**. Se escolheu um novo logótipo, a imagem do campo **Logo** muda para refletir o novo ficheiro de logotipo.

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Veja os grupos e membros da sua organização no Diretório Ativo Azure](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](assign-user-or-group-access-portal.md)
* [Remova uma atribuição de utilizador ou grupo de uma aplicação empresarial](remove-user-or-group-access-portal.md)
* [Desativar os sintetantes do utilizador para uma aplicação empresarial](disable-user-sign-in-portal.md)
