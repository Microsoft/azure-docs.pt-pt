---
title: Configuração de um clique e único sinal de sso (SSO) da sua aplicação Azure Marketplace | Microsoft Docs
description: Passos para configuração de SSO de um clique para a sua aplicação a partir do Azure Marketplace.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.reviewer: kenwith
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: e14944bc92b0d728a917402a1bd2f01b8b9012e4
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375649"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Configuração de aplicação de um clique de um único sign-on

 Neste tutorial, aprende-se a executar uma configuração de um clique e um único sinal de sso (SSO) para aplicações de suporte a SAML, Azure Ative Directory (Azure AD) do Azure Marketplace.

## <a name="introduction-to-one-click-sso"></a>Introdução a SSO de um clique

A funcionalidade SSO de um clique foi concebida para configurar um único sinal de sação para aplicações Azure Marketplace que suportam o protocolo SAML. Na página de configuração Azure AD SSO, esta opção permite configurar automaticamente os metadados AD Azure no lado da aplicação. Desta forma, pode configurar rapidamente o SSO com o mínimo de esforço manual.

## <a name="advantages-of-one-click-sso"></a>Vantagens de um clique SSO

- Configuração SSO rápida de aplicações Azure Marketplace que requerem configuração manual no lado da aplicação.
- Configuração SSO mais eficiente e precisa.
- Não é necessária qualquer comunicação ou suporte para a instalação. A aplicação fornece o UI para configuração SAML.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição ativa da aplicação para configurar com SSO. Também precisa de credenciais de administração.
- A **extensão de inscrição secure My Apps** da Microsoft instalada no navegador. Para mais informações, consulte [o Access e utilize aplicações no portal My Apps.](../user-help/my-apps-portal-end-user-access.md)

## <a name="one-click-sso-configuration-steps"></a>Passos de configuração SSO de um clique

1. Adicione a aplicação do Azure Marketplace.

2. Selecione **um único sinal de inscrição**.

3. **Selecione Ative o único sinal de inscrição**.

4. Povoar os valores de configuração obrigatórios na secção **Configuração Básica SAML.**

    > [!NOTE]
    > Se a aplicação tiver alegações personalizadas de que precisa de configurar, manuseie-as antes de realizar um clique SSO.

5. Se a funcionalidade SSO de um clique estiver disponível para a sua aplicação Azure Marketplace, vê o seguinte ecrã. Poderá ter de instalar a extensão do **navegador 'As minhas aplicações' secure'** selecionando **a extensão**.

   ![Instale a extensão do navegador Secure Sign-in das minhas apps](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Depois de adicionar a extensão ao navegador, selecione **Configuração \<Application Name\>**. Depois de ser redirecionado para o portal de administração de aplicações, inscreva-se como administrador.

   ![Nome da aplicação de configuração](./media/one-click-sso-tutorial/setup-sso.png)

7. A extensão do navegador configura automaticamente sSO na aplicação. Confirme selecionando **Sim**.

   ![Guardar os dados auto-povoados](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Se a configuração SSO para a sua aplicação necessitar de etapas adicionais, seguindo as indicações para executar os passos.

8. Depois de terminada a configuração, selecione **OK** para guardar as alterações.

   ![Guardar os dados auto-povoados](./media/one-click-sso-tutorial/save-data.png)

9. Uma janela de confirmação mostra para que saiba que as definições SSO estão configuradas com sucesso.

   ![SSO configurado](./media/one-click-sso-tutorial/sso-configured.png)

10. Depois de a configuração ter sido bem sucedida, é assinado fora da aplicação e devolvido ao portal Azure.

11. Pode selecionar **o Teste** para testar um único sinal de s-on.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com Diretório Ativo Azure](../saas-apps/tutorial-list.md)
* [O que é a extensão do navegador De Inscrição Segura das Minhas Apps?](../user-help/my-apps-portal-end-user-access.md)
