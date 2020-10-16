---
title: 'Tutorial: Integração do Azure Ative Directory com a ArcGIS Enterprise Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a ArcGIS Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2018
ms.author: jeedes
ms.openlocfilehash: fc0303cc4ba00f873ecfea7b02047ebd1bff7da1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91713876"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Tutorial: Integração do Azure Ative Directory com a ArcGIS Enterprise

Neste tutorial, aprende-se a integrar a ArcGIS Enterprise com o Azure Ative Directory (Azure AD).
A integração da ArcGIS Enterprise com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso à ArcGIS Enterprise.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na ArcGIS Enterprise (Sign-On Única) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com a ArcGIS Enterprise, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada pela ArcGIS Enterprise

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* ArcGIS Enterprise apoia **SP e IDP** iniciado SSO
* A Empresa ArcGIS suporta o fornecimento de utilizadores **just in time**


## <a name="adding-arcgis-enterprise-from-the-gallery"></a>Adicionar ArcGIS Enterprise da galeria

Para configurar a integração da ArcGIS Enterprise em Azure AD, é necessário adicionar a ArcGIS Enterprise da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar a ArcGIS Enterprise à galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite ArcGIS Enterprise**, selecione **ArcGIS Enterprise** do painel de resultados e clique em **Adicionar** botão adicionar a aplicação.

     ![Empresa ArcGIS na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com [Nome de aplicação] com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em [Nome de aplicação].

Para configurar e testar o único sinal de Azure AD com [Nome da aplicação], é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único da Empresa ArcGIS](#configure-arcgis-enterprise-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create ArcGIS Enterprise test user](#create-arcgis-enterprise-test-user)** - para ter uma contraparte de Britta Simon na ArcGIS Enterprise que está ligada à representação AD AD Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com [Nome da aplicação], execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **da ArcGIS Enterprise,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** execute os seguintes passos, se desejar configurar a aplicação no modo iniciado pelo **IDP:**

    ![A screenshot mostra a Configuração BÁSICA SAML, onde pode introduzir o Identificador, Responder U R L e selecionar Guardar.](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `<EXTERNAL_DNS_NAME>.portal`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

    c. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![Screenshot mostra Definir U R Ls adicionais onde pode introduzir um sinal em U R L.](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de suporte ao Cliente da ArcGIS Enterprise](mailto:support@esri.com) para obter estes valores. Você receberá o valor identificador da secção De Fornecedor de **Identidade,** que é explicado mais tarde neste tutorial.

5. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

### <a name="configure-arcgis-enterprise-single-sign-on"></a>Configurar a empresa ArcGIS Single Sign-On

1. Para automatizar a configuração dentro do ArcGIS Enterprise, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar a ArcGIS Enterprise** irá direcioná-lo para a aplicação ArcGIS Enterprise. A partir daí, forneça as credenciais de administração para assinar na ArcGIS Enterprise. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar a ArcGIS Enterprise manualmente, inicie sessão no site da empresa ArcGIS Enterprise como administrador.


1. Selecione **Configurações de EDIÇÃO >Organização.**

    ![A screenshot mostra o separador ArcGIS Enterprise Organization com definições de Edição chamadas.](./media/arcgisenterprise-tutorial/configure1.png)

1. Selecione o separador **Segurança**.

    ![A screenshot mostra o separador de Segurança selecionado.](./media/arcgisenterprise-tutorial/configure2.png)

1. Desloque-se até aos Logins da Empresa através da secção **SAML** e selecione **SET ENTERPRISE LOGIN**.

    ![O Screenshot mostra logins da Empresa via SAML onde pode selecionar set Enterprise Login.](./media/arcgisenterprise-tutorial/configure3.png)

1. Na secção **'Fornecedor de Identidade' set,** execute os seguintes passos:

    ![A screenshot mostra o Fornecedor de Identidade Definido onde executa os passos descritos aqui.](./media/arcgisenterprise-tutorial/configure4.png)

    a. Por favor, forneça um nome como **Azure Ative Directory Test** na caixa de texto **Name.**

    b. Na caixa de texto **URL,** cole o valor url de **metadados da Federação de Aplicações** que copiou a partir do portal Azure.

    c. Clique em **Mostrar definições avançadas** e copie o valor **de ID** da Entidade e cole-o na caixa de texto **identifier** na secção **Domínio Empresarial e URLs arcgis** no portal Azure.
    
    ![A screenshot mostra onde obter a Entidade I D e atualizar o fornecedor de identificação.](./media/arcgisenterprise-tutorial/configure5.png)

    d. CLIQUE **EM UPDATE IDENTITY PROVIDER**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador** **brittasimon \@ yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso à ArcGIS Enterprise.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **ArcGIS Enterprise**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, escreva e selecione **ArcGIS Enterprise**.

    ![O link Da Empresa ArcGIS na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-arcgis-enterprise-test-user"></a>Criar utilizador de teste arcgis enterprise

Nesta secção, um utilizador chamado Britta Simon é criado na ArcGIS Enterprise. A ArcGIS Enterprise suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na ArcGIS Enterprise, um novo é criado após a autenticação.

> [!Note]
> Se precisar de criar um utilizador manualmente, contacte a equipa de [suporte da ArcGIS Enterprise.](mailto:support@esri.com)

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo da Empresa ArcGIS no Painel de Acesso, deverá ser automaticamente inscrito na Empresa ArcGIS para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

