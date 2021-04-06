---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com Litmos | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Litmos.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.openlocfilehash: cfdcef2b9adf4e7ce500a9a89a45678a60afffc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458458"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmos"></a>Tutorial: Azure Ative Directory integração única (SSO) com Litmos

Neste tutorial, você vai aprender a integrar Litmos com Azure Ative Direy (Azure AD). Quando integra Litmos com AZure AD, pode:

* Controlo em Azure AD que tem acesso a Litmos.
* Ative os seus utilizadores a serem automaticamente inscritos em Litmos com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Inscrição única (SSO) ativada por litmos.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Litmos suporta **IDP** iniciado SSO
* Litmos suporta **provisão do** utilizador Just In Time

## <a name="adding-litmos-from-the-gallery"></a>Adicionar Litmos da galeria

Para configurar a integração de Litmos em Azure AD, você precisa adicionar Litmos da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Litmos** na caixa de pesquisa.
1. Selecione **Litmos** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-litmos"></a>Configurar e testar Azure AD único sinal para Litmos

Configure e teste Azure AD SSO com Litmos usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Litmos.

Para configurar e testar o Azure AD SSO com Litmos, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure litmos SSO](#configure-litmos-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Litmos test user](#create-litmos-test-user)** - para ter uma contraparte de B.Simon em Litmos que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Litmos,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. No **set-on único com** a página SAML, insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<companyname>.litmos.com/account/Login`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<companyname>.litmos.com/integration/samllogin`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real, que são explicados mais tarde em tutorial ou contacte a [equipa de suporte do Cliente Litmos](https://www.litmos.com/contact-us) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Litmos,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Litmos.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Litmos**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-litmos-sso"></a>Configurar litmos SSO

1. Numa janela de navegador diferente, inscreva-se no site da empresa Litmos como administrador.

2. Na barra de navegação do lado esquerdo, clique em **Contas**.

    ![Secção de Contas no Lado da Aplicação][22]

3. Clique no separador **Integrações.**

    ![Separador de Integração][23]

4. No separador **Integrações,** desloque-se até à **3ª Integrações partidárias** e, em seguida, clique no separador **SAML 2.0.**

    ![Secção SAML 2.0][24]

5. Copie o valor no **ponto final do SAML para litmos é:** e cole-o na caixa de texto **URL de resposta** na secção De Domínio **e URLs de Litmos** no portal Azure.

    ![Ponto final SAML][26]

6. Na sua aplicação **Litmos,** execute os seguintes passos:

    ![Aplicação Litmos][25]

    a. Clique **em Ativar o SAML**.

    b. Abra o certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **do Certificado SAML X.509.**

    c. Clique em **Guardar Alterações**.

### <a name="create-litmos-test-user"></a>Criar utilizador de teste de Litmos

O objetivo desta secção é criar um utilizador chamado Britta Simon em Litmos. A aplicação Litmos suporta o provisionamento just-in-time. Isto significa que uma conta de utilizador é criada automaticamente se necessário durante uma tentativa de aceder à aplicação utilizando o Painel de Acesso.

**Para criar um utilizador chamado Britta Simon em Litmos, execute os seguintes passos:**

1. Numa janela de navegador diferente, inscreva-se no site da empresa Litmos como administrador.

2. Na barra de navegação do lado esquerdo, clique em **Contas**.

    ![Secção de Contas No Lado da Aplicação][22]

3. Clique no separador **Integrações.**

    ![Separador de Integrações][23]

4. No separador **Integrações,** desloque-se até à **3ª Integrações partidárias** e, em seguida, clique no separador **SAML 2.0.**

    ![SAML 2.0][24]

5. Selecione **Utilizadores de Autogerados**
  
    ![Utilizadores de autogerados][27]

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Litmos no Painel de Acesso, deverá ser automaticamente inscrito nos Litmos para os quais configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente Litmos com Azure AD](https://aad.portal.azure.com/)

[21]: ./media/litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/litmos-tutorial/tutorial_litmos_66.png