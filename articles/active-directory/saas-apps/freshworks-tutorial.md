---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Freshworks Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Freshworks.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.openlocfilehash: c953297d4e66f737250451b9a5f42ce7f45dd2e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91821255"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshworks"></a>Tutorial: Azure Ative Directory integração única (SSO) com Freshworks

Neste tutorial, você vai aprender a integrar Freshworks com Azure Ative Directory (Azure AD). Quando integra Freshworks com AZure AD, pode:

* Controlo em Azure AD que tem acesso a Freshworks.
* Permita que os seus utilizadores sejam automaticamente inscritos na Freshworks com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por assinatura única de freshworks (SSO).

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Freshworks suporta SSO iniciado **pela SP**

## <a name="adding-freshworks-from-the-gallery"></a>Adicionando obras frescas da galeria

Para configurar a integração da Freshworks no Azure AD, é necessário adicionar Freshworks da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **de galeria,** escreva **Freshworks** na caixa de pesquisa.
1. Selecione **Freshworks** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshworks"></a>Configurar e testar Azure AD único sign-on para Freshworks

Configure e teste Azure AD SSO com Freshworks usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Freshworks.

Para configurar e testar a Azure AD SSO com Freshworks, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Freshworks SSO](#configure-freshworks-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Freshworks test user](#create-freshworks-test-user)** - para ter uma contraparte de B.Simon em Freshworks que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Freshworks,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.freshworks.com/login`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.freshworks.com/sp/SAML/<MODULE_ID>/metadata`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente Freshworks](mailto:support@freshworks.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Para modificar as opções **de Assinatura**   de acordo com o seu requisito, clique em **Editar**o   botão Editar para abrir o diálogo **do Certificado de Assinatura SAML.**  

     ![image](common/edit-certificate.png)

     ![Screenshot que mostra o diálogo "S A M L Signing Certificate" com o botão "Editar" selecionado.](./media/freshworks-tutorial/response.png)

    a. Selecione **Sign SAML response** como **Opção de Assinatura**.

    b. Clique em **Guardar**.

1. Na secção **Configurar Freshworks,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Freshworks.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Freshworks**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-freshworks-sso"></a>Configure a SSO de Obras Frescas

1. Abra uma nova janela do navegador web e inscreva-se no site da empresa Freshworks como administrador e execute os seguintes passos:

2. A partir do lado esquerdo do menu, clique no ícone **de**   Segurança, em seguida, verifique a opção de **inscrição única** e selecione **SSO SAML** em **métodos de autenticação**.

    ![Screenshot que mostra a secção "Métodos de Segurança - Autenticação" com a opção "Single sign-on" ligada e selecionada "S A M L S S O".](./media/freshworks-tutorial/configure01.png)

3. Na secção **de inscrição única,** execute os seguintes passos:

    ![Configuração de obras frescas](./media/freshworks-tutorial/configure02.png)

    a. Clique em **Copiar** para copiar o **ID da Entidade fornecedora de serviços (SP)** para o seu exemplo e cole-o na caixa de texto **Identifier (Entity ID)** na secção **de Configuração SAML Básica** no portal Azure.

    b. No ID de Entidade fornecido pela caixa de texto **IdP,** cole o valor **identificador Azure AD,** que copiou a partir do portal Azure.

    c. Na caixa de texto **URL SSO SAML,** cole o valor **URL de login,** que copiou a partir do portal Azure.

    d. Abra o certificado codificado Base64 no bloco de notas, copie o seu conteúdo e cole-o na caixa de texto do **certificado de Segurança.**

    e. Clique em **Guardar**.

### <a name="create-freshworks-test-user"></a>Criar utilizador de testes freshworks

Nesta secção, cria-se um utilizador chamado B.Simon in Freshworks. Trabalhe com [a equipa de suporte do Cliente Freshworks](mailto:support@freshworks.com) para adicionar os utilizadores na plataforma Freshworks. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação. 

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Freshworks no Painel de Acesso, deverá ser automaticamente inscrito no Freshworks para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente freshworks com Azure AD](https://aad.portal.azure.com/)

