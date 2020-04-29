---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Everbridge Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Everbridge.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 60463a00c6864bed7b3a18e816ef0143d3573782
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67103260"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Tutorial: Integração do Diretório Ativo Azure com a Everbridge

Neste tutorial, aprende-se a integrar a Everbridge com o Azure Ative Directory (Azure AD).
Quando integrar a Everbridge com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Everbridge.
* Permita que os seus utilizadores sejam automaticamente inscritos na Everbridge com as suas contas Azure AD. Este controlo de acesso é chamado de inscrição única (SSO).
* Gerencie as suas contas num local central utilizando o portal Azure.
Para obter mais informações sobre software como uma integração de aplicações de serviço (SaaS) com a Azure AD, veja [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Everbridge, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição everbridge que usa um único sinal.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Everbridge suporta SSO iniciado com IDP.

## <a name="add-everbridge-from-the-azure-marketplace"></a>Adicione Everbridge do Mercado Azure

Para configurar a integração da Everbridge em Azure AD, adicione a Everbridge do Azure Marketplace à sua lista de aplicações geridas do SaaS.

Para adicionar Everbridge do Azure Marketplace, siga estes passos.

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![Botão de Diretório Ativo Azure](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione **Nova aplicação** na parte superior da caixa de diálogo.

    ![Novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, entre em **Everbridge.** Selecione **Everbridge** a partir do painel de resultados e selecione **Adicionar**.

     ![Everbridge na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sinal de Azure AD com a Everbridge com base na utilizadora de teste Britta Simon.
Para que o único início de sessão funcione, estabeleça uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Everbridge.

Para configurar e testar o único sinal de Azure AD com a Everbridge, complete os seguintes blocos de construção:

- [Configure](#configure-azure-ad-single-sign-on) o único sinal de ad do Azure para permitir que os seus utilizadores utilizem esta funcionalidade.
- [Configure everbridge como portal único](#configure-everbridge-as-everbridge-manager-portal-single-sign-on) do portal Everbridge para configurar as definições de inscrição única seleções no lado da aplicação.
- [Configure everbridge como portal único](#configure-everbridge-as-everbridge-member-portal-single-sign-on) do everbridge para configurar as definições de inscrição única seleções no lado da aplicação.
- [Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user) para testar o único sign-on da Azure AD com britta Simon.
- [Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que Britta Simon utilize um único sinal de AD Azure.
- [Crie um utilizador](#create-an-everbridge-test-user) de teste Everbridge para ter uma contrapartida de Britta Simon em Everbridge que esteja ligada à representação da AD Azure do utilizador.
- [Teste um único sinal para](#test-single-sign-on) verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o single sign-on azure com a Everbridge, siga estes passos.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **everbridge,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. Na **seleta de uma única** caixa de diálogo de método de sinalização, selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. Na configuração de um único sign-on com a página **SAML,** selecione **Editar** para abrir a caixa de diálogo **de configuração SAML básica.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

    >[!NOTE]
    >Configure a aplicação quer como portal *gestor, quer* como portal membro, tanto no portal Azure como no portal Everbridge.

4. Para configurar a aplicação **Everbridge** como portal **do gestor Everbridge,** na secção **basic SAML De Configuração,** siga estes passos:

    ![Algumas informações sobre o domínio everbridge e URLs](common/idp-intiated.png)

    a. Na caixa **de identificador,** introduza um URL que siga o padrão`https://sso.everbridge.net/<API_Name>`

    b. Na caixa **DEURL resposta,** introduza um URL que siga o padrão`https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com os valores reais do Identificador e do URL de resposta. Para obter estes valores, contacte a equipa de [apoio everbridge.](mailto:support@everbridge.com) Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Para configurar a aplicação **Everbridge** como **portal membro Everbridge,** na secção **basic SAML Configuration,** siga estes passos:

  * Se pretender configurar a aplicação no modo iniciado pelo IDP, siga estes passos:

     ![Domínio Everbridge e URLs informações únicas de inscrição para o modo iniciado pelo IDP](common/idp-intiated.png)

    a. Na caixa **de identificador,** introduza um URL que siga o padrão`https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. Na caixa **DEURL resposta,** introduza um URL que siga o padrão`https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Se pretender configurar a aplicação no modo iniciado por SP, selecione **Definir URLs adicionais** e siga este passo:

     ![Domínio Everbridge e URLs informações únicas de inscrição para modo iniciado por SP](common/both-signonurl.png)

     a. No **Sign on URL** box, introduza um URL que siga o padrão`https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e assine os valores de URL. Para obter estes valores, contacte a equipa de [apoio everbridge.](mailto:support@everbridge.com) Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

6. Na **configuração de um único sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** selecione **Download** para descarregar os **Metadados da Federação XML**. Guarde no seu computador.

    ![Link de descarregamento de certificado](common/metadataxml.png)

7. Na secção **Configurar Everbridge,** copie os URLs de que necessita para os seus requisitos:

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    - URL de Inicio de Sessão
    - Identificador Azure AD
    - Logout URL

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Configure Everbridge como gerente do Everbridge portal único sign-on

Para configurar o SSO em **Everbridge** como uma aplicação **do portal do gestor Everbridge,** siga estes passos.
 
1. Numa janela de navegador web diferente, inscreva-se no Everbridge como administrador.

1. No menu em cima, selecione o separador **Definições.** Em **Segurança,** selecione **Single Sign-On**.
   
     ![Configurar o início de sessão único](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. Na caixa **nome,** insira o nome do fornecedor identificador. Um exemplo é o nome da sua empresa.
   
     b. Na caixa **de nome SPI,** insira o nome da API.
   
     c. Selecione **Escolher Ficheiro** para fazer o upload do ficheiro de metadados que descarregou a partir do portal Azure.
   
     d. Para **a localização da identidade SAML,** selecione Identidade está no elemento **Identificador de Nome da declaração do Assunto**.
   
     e. Na caixa URL do Fornecedor de **Identidade,** colá o valor URL de **Login** que copiou do portal Azure.
   
     f. Para **o Prestador de Serviços iniciado Pedido vinculativo,** selecione HTTP **Redirect**.

     g. Selecione **Guardar**.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Configure Everbridge como membro do Everbridge

Para configurar um único sign-on em **Everbridge** como um **portal membro everbridge,** envie o **Demôdice XML da Federação** descarregado para a equipa de suporte [everbridge](mailto:support@everbridge.com). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

Para criar a utilizadora de teste Britta Simon no portal Azure, siga estes passos.

1. No portal Azure, no painel esquerdo,**Users** > selecione Utilizadores de **Diretório** > Ativo Azure**Todos os utilizadores**.

    ![Links de utilizadores e todos os utilizadores](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Na caixa de diálogo **do Utilizador,** siga estes passos.

    ![Caixa de diálogo do utilizador](common/user-properties.png)

    a. Na caixa **de nomes,** entre **brittaSimon.**
  
    b. Na caixa **Nome de utilizador**, introduza `brittasimon@yourcompanydomain.extension`. Um exemplo é BrittaSimon@contoso.com.

    c. Selecione a caixa de verificação **de palavra-passe do show.** Escreva o valor que mostra na caixa **password.**

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Permitir que Britta Simon use o único sign-on Azure, concedendo acesso a Everbridge.

1. No portal Azure, selecione **aplicações** > Da Empresa**Todas as aplicações** >**Everbridge**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Everbridge**.

    ![Ligação Everbridge na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![Ligação de utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar utilizador**. Na caixa de diálogo **Adicionar Atribuição,** selecione **Utilizadores e grupos**.

    ![Adicionar caixa de diálogo de atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **de utilizadores e grupos,** selecione **Britta Simon** na lista de utilizadores. Escolha **Selecione** na parte inferior do ecrã.

6. Se esperar algum valor de papel na afirmação do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Escolha **Selecione** na parte inferior do ecrã.

7. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

### <a name="create-an-everbridge-test-user"></a>Criar um utilizador de teste Everbridge

Nesta secção, cria-se a utilizadora de teste Britta Simon em Everbridge. Para adicionar utilizadores na plataforma Everbridge, trabalhe com a equipa de [suporte everbridge.](mailto:support@everbridge.com) Os utilizadores devem ser criados e ativados em Everbridge antes de utilizar um único sinal. 

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Teste a configuração de um único sinal de acesso do Azure AD utilizando o Painel de Acesso.

Ao selecionar o azulejo Everbridge no Painel de Acesso, deverá ser automaticamente inscrito na conta Everbridge para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações do SaaS com diretório ativo azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

