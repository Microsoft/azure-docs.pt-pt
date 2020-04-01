---
title: Configuração de um clique, única inscrição (SSO) da sua aplicação Azure Marketplace / Microsoft Docs
description: Passos para configuração de um clique de SSO para a sua aplicação a partir do Mercado Azure.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67872430"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Configuração de aplicativo de um clique de um único sinal

 Neste tutorial, aprende-se a executar uma configuração de um clique e um único sinal (SSO) para aplicações de suporte a SAML, azure Ative Directory (Azure AD) a partir do Azure Marketplace.

## <a name="introduction-to-one-click-sso"></a>Introdução ao SSO de um clique

A funcionalidade SSO de um clique foi concebida para configurar um único sinal para aplicações do Azure Marketplace que suportam o protocolo SAML. Na página de configuração Azure AD SSO, esta opção permite configurar automaticamente os metadados Azure AD no lado da aplicação. Desta forma, pode configurar rapidamente o SSO com o mínimo de esforço manual.

## <a name="advantages-of-one-click-sso"></a>Vantagens do SSO de um clique

- Configuração Rápida SSO de aplicações Azure Marketplace que requerem configuração manual no lado da aplicação.
- Configuração SSO mais eficiente e precisa.
- Não é necessária nenhuma comunicação ou apoio de parceiros para a configuração. A aplicação fornece o UI para a configuração SAML.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição ativa da aplicação para configurar com SSO. Também precisa de credenciais de administração.
- A **extensão de 'Sign-in'** Da Microsoft instalada no navegador. Para mais informações, consulte [O Acesso e utilize aplicações no portal My Apps](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="one-click-sso-configuration-steps"></a>Passos de configuração SSO de um clique

1. Adicione a aplicação do Azure Marketplace.

2. Selecione **um único sinal .**

3. **Selecione Ativar um único sinal .**

4. Povoe os valores de configuração obrigatórios na secção **de configuração Básica SAML.**

    > [!NOTE]
    > Se a aplicação tiver alegações personalizadas de que precisa de configurar, manuseie-as antes de realizar um clique de SSO.

5. Se a funcionalidade SSO de um clique estiver disponível para a sua aplicação Azure Marketplace, consulte o ecrã seguinte. Poderá ter de instalar a extensão de **navegador 'Sessão' "My Apps Secure",** selecionando **a extensão**.

   ![Instale a extensão de navegador de sessão de sinais de segurança das minhas aplicações](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Depois de adicionar a extensão ao navegador, **selecione \<'Configurar Nome de\>Aplicação 'Configurar'.** Depois de ser redirecionado para o portal de administração de aplicações, inscreva-se como administrador.

   ![Nome da aplicação de configuração](./media/one-click-sso-tutorial/setup-sso.png)

7. A extensão do navegador configura automaticamente o SSO na aplicação. Confirme selecionando **Sim**.

   ![Salvar os dados auto-povoados](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Se a configuração SSO para a sua aplicação necessitar de passos adicionais, seguindo as instruções para executar os passos.

8. Depois de terminar a configuração, selecione **OK** para guardar as alterações.

   ![Guarde os dados auto-povoados](./media/one-click-sso-tutorial/save-data.png)

9. Uma janela de confirmação mostra para que saiba que as definições SSO estão configuradas com sucesso.

   ![SSO configurado](./media/one-click-sso-tutorial/sso-configured.png)

10. Depois de a configuração ter sucesso, é assinado fora da aplicação e devolvido ao portal Azure.

11. Pode selecionar **o Teste** para testar um único sinal.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações do SaaS com diretório ativo azure](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [O que é a extensão de navegador De Sintetizador as Minhas Aplicações?](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 
