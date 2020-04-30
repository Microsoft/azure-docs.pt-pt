---
title: 'Tutorial: Integração do Diretório Ativo Azure com controlo / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo azure e o Controlo de Continuidade.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1cb7f505-0d06-44b0-95b1-65b470e97092
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5a3383c6c79f3cde901c244eafb39bb0f479176
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67164916"
---
# <a name="tutorial-integrate-continuity-control-with-azure-active-directory"></a>Tutorial: Integrar o Controlo de Continuidade com o Diretório Ativo Azure

Neste tutorial, aprenderá a integrar o Controlo de Continuidade (Controlo) com o Azure Ative Directory (Azure AD). Quando integrar o Controlo com a AD Azure, pode:

* Gerencie em Azure AD quem tenha acesso ao Controlo.
* Ative que os seus utilizadores sejam automaticamente inscritos no Controlo com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode ter um mês de experiência gratuita [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Uma subscrição de um único sinal de controlo (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. Suportes de controlo **SP** iniciadoS SSO.

## <a name="adding-control-from-the-gallery"></a>Adicionar controlo da galeria

Para configurar a integração do Control em Azure AD, precisa adicionar Control da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Controle** na caixa de pesquisa.
1. Selecione **Controlo** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com Controlo utilizando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Control.

Para configurar e testar o Azure AD SSO com Controlo, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o SSO](#configure-control-sso)** de controlo - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Control test user](#create-control-test-user)** - para ter uma contrapartida de Britta Simon no Control que esteja ligada à representação azure AD do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Control,** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na página **basic SAML Configuração,** introduza os valores para o seguinte campo:

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.continuity.net/auth/saml`

    > [!Note]
    > O valor não é real. Atualize o valor com o subdomínio correto. O seu subdomínio SSO pode ser configurado nas Estratégias de [Autenticação de Controlo](https://control.continuity.net/settings/account_profile#tab/security). Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na secção Certificado de **Assinatura SAML,** clique no botão **Editar** para abrir o diálogo do Certificado de **Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

1. Na secção certificado de **assinatura SAML,** copie a **impressão digital** e guarde-a no seu computador.

    ![Copiar valor de impressão digital](common/copy-thumbprint.png)

1. Na secção **'Configurar', copie** o URL de login e guarde-o no computador.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-control-sso"></a>Configure Control SSO

Para configurar um único sinal no lado do **Controlo,** é necessário atualizar as definições de autenticação de entrada única nas Estratégias de [Autenticação de Controlo](https://control.continuity.net/settings/account_profile#tab/security). Atualização **SAML SSO URL** com o **URL de Login** e **impressão digital** do certificado com o valor de **impressão digital** do portal Azure.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado Britta Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que Britta Simon utilize um único sign-on Azure, concedendo acesso ao Controlo.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Controlo**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-control-test-user"></a>Criar o utilizador do teste de controlo

Nesta secção, cria-se uma utilizadora chamada Britta Simon em Controlo. Trabalhe com a equipa de [suporte de Controlo](mailto:help@continuity.net) para adicionar os utilizadores na plataforma Control. Utilize o nome de **utilizador** Azure AD da Britta Simon para preencher o seu ID de utilizador do fornecedor de **identidade** no Controlo. Os utilizadores devem ser criados e o seu conjunto de ID do Utilizador do Fornecedor de **Identidade,** no Controlo antes de poderem utilizar um único sinal.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo de controlo no Painel de Acesso, deve ser automaticamente inscrito no Controlo para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
