---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Zendesk Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Zendesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/26/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f70e7067a78b439d3dcaf0b83460296cad7b4485
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585808"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zendesk"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com a Zendesk

Neste tutorial, você vai aprender a integrar zendesk com o Azure Ative Directory (Azure AD). Quando integrar a Zendesk com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Zendesk.
* Ative que os seus utilizadores sejam automaticamente inscritos na Zendesk com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada por um único sinal (SSO) da Zendesk.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Zendesk suporta **SP** iniciado SSO
* Zendesk suporta fornecimento [ **automatizado** de utilizadores](zendesk-provisioning-tutorial.md)
* Assim que configurar o Zendesk, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-zendesk-from-the-gallery"></a>Adicionando Zendesk da galeria

Para configurar a integração da Zendesk em Azure AD, precisa adicionar Zendesk da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite **Zendesk** na caixa de pesquisa.
1. Selecione **Zendesk** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zendesk"></a>Configure e teste Azure AD único sign-on para Zendesk

Configure e teste Azure AD SSO com Zendesk utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Zendesk.

Para configurar e testar o Azure AD SSO com a Zendesk, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure zendesk SSO](#configure-zendesk-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie o utilizador](#create-zendesk-test-user)** de teste Zendesk - para ter uma contrapartida de B.Simon em Zendesk que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Zendesk,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<subdomain>.zendesk.com`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<subdomain>.zendesk.com`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<subdomain>.zendesk.com/access/saml`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL, Identifier e URL de Resposta real. Contacte a equipa de suporte do [Cliente Zendesk](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. A aplicação Zendesk espera as afirmações da SAML num formato específico. Não existem atributos SAML obrigatórios, mas opcionalmente pode gerir a partir da secção **Deatributos** do Utilizador na página de integração de aplicações. Na configuração do Open Sign-On com a página **SAML,** clique no botão **Editar** para abrir o diálogo **de Atributos do Utilizador.**

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Utiliza atributos de extensão para adicionar atributos que não estão em AD Azure por padrão. Clique [em atributos de utilizador que podem ser definidos no SAML](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) para obter a lista completa de atributos SAML que **zendesk** aceita.

1. Na secção Certificado de **Assinatura SAML,** clique no botão **Editar** para abrir o diálogo do Certificado de **Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

1. Na secção certificado de **assinatura SAML,** copie o **Valor da Impressão Digital** e guarde-o no seu computador.

    ![Copiar valor de impressão digital](common/copy-thumbprint.png)

1. Na secção Configurar a **Zendesk,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso à Zendesk.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Zendesk**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-zendesk-sso"></a>Configure Zendesk SSO

1. Se quiser configurar o Zendesk manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Zendesk como administrador e execute os seguintes passos:

2. Clique **em Admin**.

3. No painel de navegação à esquerda, clique em **Definições**, e, em seguida, clique em **Segurança**.

4. Na página **de Segurança,** execute os seguintes passos:

    ![Segurança](./media/zendesk-tutorial/ic773089.png "Segurança")

    ![Início de sessão único](./media/zendesk-tutorial/ic773090.png "Início de sessão único")

    a. Clique no separador **Agentes de & de Administração.**

    b. Selecione **um único sinal (SSO) e SAML,** e, em seguida, selecione **SAML**.

    c. Na caixa de texto **URL SAML SSO,** colá o valor do URL de **Login** que copiou do portal Azure.

    d. Na caixa de texto url de **logout remoto,** colá o valor do URL de **Logout** que copiou do portal Azure.

    e. Na caixa de texto **Impressões Digitais de Certificado,** cola o valor de **impressão digital** do certificado que copiou do portal Azure.

    f. Clique em **Guardar**.

### <a name="create-zendesk-test-user"></a>Criar o utilizador de teste Zendesk

O objetivo desta secção é criar uma utilizadora chamada Britta Simon em Zendesk. A Zendesk suporta o fornecimento automático de utilizadores, que é por defeito. Pode encontrar mais detalhes [aqui](Zendesk-provisioning-tutorial.md) sobre como configurar o fornecimento automático de utilizadores.

**Se necessitar de criar manualmente o utilizador, execute os seguintes passos:**

> [!NOTE]
> **As** contas de utilizador final são automaticamente aprovisionadas ao iniciar a sua sessão. **As** contas do Agente e **da Admin** têm de ser aprovisionadas manualmente na **Zendesk** antes de iniciarem a sua inscrição.

1. Inscreva-se no seu inquilino **Zendesk.**

2. Selecione o separador Lista de **Clientes.**

3. Selecione o separador **Utilizador** e clique em **Adicionar**.

    ![Adicionar utilizador](./media/zendesk-tutorial/ic773632.png "Adicionar utilizador")
4. Digite o **Nome** e **O E-mail** de uma conta AD Azure existente que pretende fornecer e, em seguida, clique em **Guardar**.

    ![Novo utilizador](./media/zendesk-tutorial/ic773633.png "Novo utilizador")

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador Zendesk ou APIs fornecidas pela Zendesk para fornecer contas de utilizador da Azure AD.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Zendesk no Painel de Acesso, deverá ser automaticamente inscrito no Zendesk para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente zendesk com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger zendesk com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Configurar o fornecimento de utilizadores](zendesk-provisioning-tutorial.md)