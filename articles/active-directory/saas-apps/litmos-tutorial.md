---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Litmos [ Integração de diretório sonorsocial com Litmos ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Litmos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a935ef6d14b4de67964c555e0ffa610bbe992459
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "70171565"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmos"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com Litmos

Neste tutorial, você vai aprender a integrar Litmos com o Azure Ative Directory (Azure AD). Quando integrar litmos com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Litmos.
* Permita que os seus utilizadores sejam automaticamente inscritos na Litmos com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada por um único sinal (SSO) da Litmos.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Litmos suporta **IDP** iniciado SSO
* Litmos suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-litmos-from-the-gallery"></a>Adicionando Litmos da galeria

Para configurar a integração da Litmos em Azure AD, precisa de adicionar Litmos da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Litmos** na caixa de pesquisa.
1. Selecione **Litmos** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-litmos"></a>Configure e teste Azure AD único sinal para Litmos

Configure e teste Azure AD SSO com Litmos utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Litmos.

Para configurar e testar o Azure AD SSO com litmos, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure litmos SSO](#configure-litmos-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie o utilizador](#create-litmos-test-user)** de teste Litmos - para ter uma contrapartida de B.Simon em Litmos que esteja ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Litmos,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na configuração de um único sessão com a página **SAML,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<companyname>.litmos.com/account/Login`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<companyname>.litmos.com/integration/samllogin`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Identificação e Resposta real, que são explicados posteriormente no tutorial ou contacte a equipa de suporte do [Cliente Litmos](https://www.litmos.com/contact-us) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **set up Litmos,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso a Litmos.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Litmos**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-litmos-sso"></a>Configure Litmos SSO

1. Numa janela de navegador diferente, inscreva-se no site da empresa Litmos como administrador.

2. Na barra de navegação do lado esquerdo, clique em **Contas**.

    ![Secção de Contas no Lado da Aplicação][22]

3. Clique no separador **Integrações.**

    ![Guia de Integração][23]

4. No separador **Integrações,** desloque-se até **3º Party Integrations**, e, em seguida, clique no separador **SAML 2.0.**

    ![Secção SAML 2.0][24]

5. Copiar o valor sob o ponto final do **SAML para litmos é:** e cole-o na caixa de texto URL de **resposta** na secção **De domínio litmos e URLs** no portal Azure.

    ![Ponto final do SAML][26]

6. Na sua aplicação **Litmos,** execute os seguintes passos:

    ![Aplicação Litmos][25]

    a. Clique em **ativar saml**.

    b. Abra o seu certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo na sua área de sobrercopia e, em seguida, cole-o na caixa de texto **do Certificado SAML X.509.**

    c. Clique em **Guardar Alterações**.

### <a name="create-litmos-test-user"></a>Criar o utilizador de teste Litmos

O objetivo desta secção é criar um utilizador chamado Britta Simon em Litmos. A aplicação Litmos suporta o fornecimento Just-in-Time. Isto significa que uma conta de utilizador é criada automaticamente, se necessário, durante uma tentativa de acesso à aplicação utilizando o Painel de Acesso.

**Para criar um utilizador chamado Britta Simon em Litmos, execute os seguintes passos:**

1. Numa janela de navegador diferente, inscreva-se no site da empresa Litmos como administrador.

2. Na barra de navegação do lado esquerdo, clique em **Contas**.

    ![Secção de Contas No lado da aplicação][22]

3. Clique no separador **Integrações.**

    ![Guia de Integrações][23]

4. No separador **Integrações,** desloque-se até **3º Party Integrations**, e, em seguida, clique no separador **SAML 2.0.**

    ![SAML 2.0][24]

5. Selecione **Utilizadores Autogenerate**
  
    ![Utilizadores autogerados][27]

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Litmos no Painel de Acesso, deve ser automaticamente inscrito no Litmos para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Litmos com Azure AD](https://aad.portal.azure.com/)

[21]: ./media/litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/litmos-tutorial/tutorial_litmos_66.png