---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Profit.co App SAML [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e Profit.co App SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 27f42934-c636-43dd-9c20-a3c6316f2763
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 564ca97210d85c5118901f30261abe3de9df1053
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81770931"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-profitco-saml-app"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com Profit.co App SAML

Neste tutorial, você aprenderá a integrar Profit.co App SAML com o Azure Ative Directory (Azure AD). Quando integrar Profit.co App SAML com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Profit.co App SAML.
* Ative que os seus utilizadores sejam automaticamente inscritos na Profit.co App SAML com as suas contas Azure AD.
* Gerencie as suas contas num local central, o portal Azure.

Para saber mais sobre software como uma integração de aplicações de serviço (SaaS) com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Profit.co assinatura de inscrição única da SAML App (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Profit.co SAML App suporta IDP iniciado SSO.

* Depois de configurar Profit.co App SAML, pode impor o controlo da sessão. Isto protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o acesso condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="add-profitco-saml-app-from-the-gallery"></a>Adicione Profit.co App SAML da galeria

Para configurar a integração da Profit.co Aplicação SAML em Azure AD, precisa adicionar Profit.co App SAML da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou de escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Vá a **Aplicações Empresariais**e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Profit.co SAML App** na caixa de pesquisa.
1. Selecione **Profit.co SAML App** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-profitco-saml-app"></a>Configure e teste Azure AD único sinal para Profit.co App SAML

Configure e teste Azure AD SSO com Profit.co App SAML utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, estabeleça uma relação ligada entre um utilizador da AD Azure e o utilizador relacionado na Profit.co App SAML.

Aqui estão os passos gerais para configurar e testar Azure AD SSO com Profit.co App SAML:

1. **[Configure O SSO AD Azure](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de Azure AD com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Profit.co SSO da aplicação SAML](#configure-profitco-saml-app-sso)** para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie Profit.co utilizador de testes da Aplicação SAML](#create-a-profitco-saml-app-test-user)** para ter uma contrapartida de B.Simon na Profit.co App SAML. Esta contrapartida está ligada à representação da AD Azure do utilizador.
1. **[Teste sSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de **aplicações da Aplicação SAML Profit.co,** encontre a secção **Gerir.** Selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set set single sign-on com** a página SAML, selecione o ícone do lápis para **configuração SAML básica** para editar as definições.

   ![Screenshot de Configurar um único sign-on com página SAML, com ícone de lápis realçado](common/edit-urls.png)

1. Na secção **de Configuração Básica do SAML,** a aplicação é pré-configurada e os URLs necessários já estão pré-povoados em Azure. Os utilizadores precisam de guardar a configuração selecionando o botão **Guardar.**

1. No **set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** selecione o botão **Copiar.** Isto copia o Url de **Metadados da Federação** de Aplicações e guarda-o no seu computador.

    ![Screenshot do Certificado de Assinatura SAML, com o botão de cópia realçado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, cria-se um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure,**Users** > selecione **Utilizadores de Diretório** > Ativo Azure**Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está mostrado no campo **Password.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que a B.Simon utilize um único sign-on Azure, concedendo acesso a Profit.co App SAML.

1. No portal Azure, selecione **Aplicações Empresariais** > **Todas as aplicações**.
1. Na lista de aplicações, selecione **Profit.co App SAML**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![Screenshot da secção Gerir, com Utilizadores e grupos em destaque](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Na caixa de diálogo **Adicionar Atribuição,** selecione **Utilizadores e grupos**.

    ![Screenshot de Utilizadores e página de grupos, com adicionar utilizador destacado](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Em seguida, escolha o botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de papel na afirmação do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, escolha o botão **Select** na parte inferior do ecrã.
1. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

## <a name="configure-profitco-saml-app-sso"></a>Configure Profit.co SAML App SSO

Para configurar um único login no lado da aplicação SAML Profit.co, precisa enviar o URL de Metadados da Federação de Aplicações para a equipa de suporte da [Aplicação SAML Profit.co](mailto:support@profit.co). Configuram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-a-profitco-saml-app-test-user"></a>Criar um utilizador de teste de aplicação SAML Profit.co

Nesta secção, cria-se um utilizador chamado B.Simon em Profit.co SAML App. Trabalhe com a equipa de suporte da [Profit.co SAML App](mailto:support@profit.co) para adicionar os utilizadores na plataforma de aplicações SAML Profit.co. Não pode utilizar um único sinal até criar e ativar os utilizadores.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de acesso do Azure AD utilizando o Painel de Acesso.

Ao selecionar o azulejo Profit.co App SAML no Painel de Acesso, deve ser automaticamente inscrito na aplicação SAML Profit.co para a qual configura o SSO. Para mais informações, consulte [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Profit.co App SAML com AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger Profit.co App SAML com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
