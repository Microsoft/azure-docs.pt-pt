---
title: 'Tutorial: Integração do Diretório Ativo Azure com Retransmissão de Imagem [ Relé de Imagem] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo azure e o Image Relay.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fd0637a632b277eae019ac4aebfbc7cdb87e8e2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158968"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Tutorial: Integração do Diretório Ativo Azure com Retransmissão de Imagem

Neste tutorial, aprende-se a integrar o Image Relay com o Azure Ative Directory (Azure AD).
Integrar o Relé de Imagem com a AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao Image Relay.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Image Relay (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Image Relay, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de sinal de retransmissão de imagem ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Image Relay suporta **SP** iniciado SSO

## <a name="adding-image-relay-from-the-gallery"></a>Adicionar retransmissão de imagem da galeria

Para configurar a integração do Image Relay em Azure AD, precisa adicionar O Relé de Imagem da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Image Relay da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite o **Relé**de **Imagem,** selecione Image Relay do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

    ![Retransmissão de imagem na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sinal de Azure AD com Image Relay com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Image Relay.

Para configurar e testar o único sinal de Azure AD com o Image Relay, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure](#configure-image-relay-single-sign-on)** o reinício de imagem de um único sinal - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Image Relay user](#create-image-relay-test-user)** - para ter uma contrapartida de Britta Simon no Image Relay que está ligada à representação azure AD do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sinal de Azure AD com o Image Relay, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **image Relay,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Domínio de retransmissão de imagem e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<companyname>.imagerelay.com/`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de suporte do Cliente de [Retransmissão](http://support.imagerelay.com/) de Imagem para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

4. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **'Retransmissão de Imagem' Configurar,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-image-relay-single-sign-on"></a>Configure o relé de imagem único sign-on

1. Noutra janela do navegador, inscreva-se no site da sua empresa Image Relay como administrador.

2. Na barra de ferramentas em cima, clique na carga de trabalho **de & Permissões** dos Utilizadores.

    ![Configurar um único sinal](./media/imagerelay-tutorial/tutorial_imagerelay_06.png) 

3. Clique **em criar nova permissão**.

    ![Configurar um único sinal](./media/imagerelay-tutorial/tutorial_imagerelay_08.png)

4. Na carga de trabalho de início de tempo de início de **sinal único,** selecione o **Grupo apenas pode iniciar sessão através** da caixa de verificação De Sinal Único e, em seguida, clique em **Guardar**.

    ![Configurar um único sinal](./media/imagerelay-tutorial/tutorial_imagerelay_09.png) 

5. Ir para **as Definições de Conta**.

    ![Configurar um único sinal](./media/imagerelay-tutorial/tutorial_imagerelay_10.png) 

6. Vá ao trabalho de trabalho de **sinal único nas definições.**

    ![Configurar um único sinal](./media/imagerelay-tutorial/tutorial_imagerelay_11.png)

7. No diálogo de **definições SAML,** execute os seguintes passos:

    ![Configurar um único sinal](./media/imagerelay-tutorial/tutorial_imagerelay_12.png)

    a. Na caixa de texto **LOGIN URL,** colá o valor do URL de **Login** que copiou do portal Azure.

    b. Na caixa de texto **logout URL,** colá o valor do URL de **Logout** que copiou do portal Azure.

    c. Como **formato id nome,** selecione **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    d. Como **Opções vinculativas de Pedidos do Prestador de Serviços (Retransmissão**de Imagem) , selecione **post binding**.

    e. Sob **o certificado x.509,** clique no Certificado de **Atualização**.

    ![Configurar um único sinal](./media/imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Abra o certificado descarregado no bloco de notas, copie o conteúdo e, em seguida, cole-o na caixa de texto **x.509 Certificate.**

    ![Configurar um único sinal](./media/imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. Na secção de provisionamento de **utilizadores just-in-time,** selecione o fornecimento de **utilizador justo no tempo**.

    ![Configurar um único sinal](./media/imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Selecione o grupo de permissões (por exemplo, **SSO Basic)** que é permitido iniciar sessão apenas através de um único sinal.

    ![Configurar um único sinal](./media/imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **tipo** de campo de nome utilizador **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao Image Relay.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Image Relay**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Image Relay**.

    ![O link Image Relay na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-image-relay-test-user"></a>Criar o utilizador do teste de retransmissão de imagem

O objetivo desta secção é criar um utilizador chamado Britta Simon em Image Relay.

**Para criar um utilizador chamado Britta Simon em Image Relay, execute os seguintes passos:**

1. Inscreva-se no site da sua empresa De transmissão de imagens como administrador.

2. Vá a **Utilizadores & Permissões** e selecione **Create SSO User**.

    ![Configurar um único sinal](./media/imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. Insira o **email**, **Nome próprio,** **Apelido**e **Empresa** do utilizador que pretende fornecer e selecione o grupo de permissão (por exemplo, SSO Basic) que é o grupo que só pode iniciar sessão através de um único sinal.

    ![Configurar um único sinal](./media/imagerelay-tutorial/tutorial_imagerelay_22.png)

4. Clique em **Criar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo de retransmissão de imagem no Painel de Acesso, deve ser automaticamente inscrito no Relé de Imagem para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)