---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Splashtop [ Integração de um único sign-on do Azure Ative Diretivo) com o Splashtop [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Splashtop.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c05f63c2-4170-49ce-a967-be1cb1dbcd06
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6ecb03130e26d432f0bd10980c7c3553ce9f8b0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77539787"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-splashtop"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com splashtop

Neste tutorial, você vai aprender a integrar o Splashtop com o Azure Ative Directory (Azure AD). Quando integrar o Splashtop com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a Splashtop.
* Ative que os seus utilizadores sejam automaticamente inscritos no Splashtop com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Splashtop single sign-on (SSO) ativado subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Splashtop suporta **SP** iniciado SSO

* Assim que configurar o Splashtop, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-splashtop-from-the-gallery"></a>Adicionando Splashtop da galeria

Para configurar a integração do Splashtop em Azure AD, precisa de adicionar splashtop da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, **digite Splashtop** na caixa de pesquisa.
1. Selecione **Splashtop** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-splashtop"></a>Configure e teste Azure AD único sign-on para Splashtop

Configure e teste Azure AD SSO com Splashtop utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Splashtop.

Para configurar e testar o Azure AD SSO com splashtop, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o Splashtop SSO](#configure-splashtop-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Create Splashtop test user](#create-splashtop-test-user)** - para ter uma contraparte de B.Simon em Splashtop que está ligada à representação azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Splashtop,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set-up single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    Na caixa de texto **de URL sign-on,** escreva o URL:`https://my.splashtop.com/login/sso`

1. A aplicação Splashtop espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem a seguir mostra a lista de atributos predefinidos, enquanto que o identificador de **nomes** é mapeado com **nome de utilizador.userprincipal .** A aplicação TicketManager espera que o **identificador** de nomes seja mapeado com **user.mail**, por isso precisa editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

1. No **set-up single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** encontre **Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **'Splashtop' de configuração,** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao Splashtop.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Splashtop**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-splashtop-sso"></a>Configure Splashtop SSO

Nesta secção, terá de se candidatar a um novo método SSO a partir do [portal web Splashtop](https://my.splashtop.com/login).
1. No portal web Splashtop, vá ao separador **de informações** / da**Conta,** desloque-se para baixo para encontrar a secção **Single Sign On.** Em seguida, clique **em Aplicar para novo método SSO**.

    ![image](media/splashtop-tutorial/apply-for-new-SSO-method.png)

1. Na janela de aplicação, dê um **nome SSO**. Por exemplo, o New Azure, então selecione **Azure** como o tipo IDP, e insira **url de login** e **identificador de AD Azure** copiado da aplicação Splashtop no portal Azure.

    ![image](media/splashtop-tutorial/azure-sso-1.png)

1. Para obter informações sobre certificados, clique no ficheiro cert descarregado da aplicação Splashtop no portal Azure, edite-o com o Notepad, em seguida, copie o conteúdo, cole-o no campo **Descarregamento (Base64).**

    ![imagem](media/splashtop-tutorial/cert-2.png) ![imagem imagem](media/splashtop-tutorial/cert-1.png) ![](media/splashtop-tutorial/azure-sso-2.png)

1. Já está! Clique em **Guardar** e splashtop A equipa de validação SSO entrará em contacto consigo para obter as informações de verificação e, em seguida, ativará o método SSO.

### <a name="create-splashtop-test-user"></a>Criar o utilizador de teste Splashtop

1. Depois de ativado o método SSO, verifique o método SSO recém-criado para o ativar na secção **Single Sign On.**

    ![image](media/splashtop-tutorial/enable.png)

1. Convide o utilizador `B.Simon@contoso.com` do teste, por exemplo, para a sua equipa Splashtop com o método SSO recém-criado.

    ![image](media/splashtop-tutorial/invite.png)

1. Também pode alterar uma conta Splashtop existente para uma conta SSO, ver [instruções](https://support-splashtopbusiness.splashtop.com/hc/en-us/articles/360038685691-How-to-associate-SSO-method-to-existing-team-admin-member-).

1. Já está! Pode utilizar a conta SSO para iniciar sessão no portal do Splashtop ou na aplicação Splashtop Business.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Splashtop no Painel de Acesso, deve ser automaticamente inscrito no Splashtop para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente splashtop com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o Splashtop com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)