---
title: Configuração de um clique, o único início de sessão (SSO) da sua aplicação do Azure Marketplace | Documentos da Microsoft
description: Passos para configuração de um clique do SSO para a sua aplicação no Azure Marketplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a83d27af4fd783b95c53ef3a9169cb72bfc29d34
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67872430"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Configuração de aplicações de um clique de início de sessão único

 Neste tutorial, irá aprender a executar num único clique, o única início de sessão (SSO) configuração para SAML-suportar, aplicações do Azure Active Directory (Azure AD) no Azure Marketplace.

## <a name="introduction-to-one-click-sso"></a>Introdução ao SSO num único clique

A funcionalidade SSO num único clique destina-se para configurar o início de sessão único para aplicações do Azure Marketplace que suportam o protocolo SAML. Na página de configuração do SSO do Azure AD, esta opção permite-lhe configurar automaticamente os metadados do Azure AD no lado do aplicativo. Dessa forma, pode configurar rapidamente a SSO com um mínimo de esforço manual.

## <a name="advantages-of-one-click-sso"></a>Vantagens do SSO num único clique

- Configuração rápida do SSO de aplicações do Azure Marketplace que exigem configuração manual no lado do aplicativo.
- Configuração de SSO mais eficiente e precisa.
- Nenhuma comunicação do parceiro ou suporte necessários à configuração do. O aplicativo fornece a interface do Usuário para a configuração de SAML.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição ativa do aplicativo para configurar com o SSO. Também precisa de credenciais de administrador.
- O **segura de aplicações meu início de sessão extensão** da Microsoft instalado no browser. Para obter mais informações, consulte [aplicações de acesso e a utilização no portal minhas aplicações](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="one-click-sso-configuration-steps"></a>Passos de configuração de SSO num único clique

1. Adicione a aplicação do Azure Marketplace.

2. Selecione **início de sessão único**.

3. Selecione **ativar o início de sessão único**.

4. Preencher os valores de configuração obrigatório na **configuração básica de SAML** secção.

    > [!NOTE]
    > Se a aplicação tiver declarações personalizadas que terá de configurar, manipulá-las antes de executar o SSO num único clique.

5. Se a funcionalidade SSO de um clique está disponível para a sua aplicação do Azure Marketplace, verá seguinte ecrã. Poderá ter de instalar o **segura de aplicações meu início de sessão da extensão de browser** ao selecionar **instalar a extensão**.

   ![Instalar a extensão de browser seguro de aplicações My início de sessão](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Depois de adicionar a extensão para o navegador, selecione **programa de configuração \<nome da aplicação\>** . Após está redirecionado para o portal de administração do aplicativo, inicie sessão como administrador.

   ![Configurar o nome da aplicação](./media/one-click-sso-tutorial/setup-sso.png)

7. A extensão do browser configura automaticamente o SSO no aplicativo. Confirme selecionando **Sim**.

   ![A guardar os dados preenchidos automaticamente](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Se a configuração de SSO para a sua aplicação requer passos adicionais, seguir as instruções para efetuar os passos.

8. Após concluir a configuração, selecione **OK** para guardar as alterações.

   ![Salvar os dados preenchidos automaticamente](./media/one-click-sso-tutorial/save-data.png)

9. Apresenta uma janela de confirmação para informá-lo de que as definições de SSO com êxito estão configuradas.

   ![SSO configurado](./media/one-click-sso-tutorial/sso-configured.png)

10. Após a configuração é concluída, estiver terminada a aplicação e retornado para o portal do Azure.

11. Pode selecionar **testar** para testar o início de sessão único.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [O que é a extensão de browser seguro de aplicações My início de sessão?](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 
