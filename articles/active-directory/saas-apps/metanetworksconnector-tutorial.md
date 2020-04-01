---
title: 'Tutorial: Integração de Diretório Ativo Azure com Conector Meta Networks [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo Azure e o Conector meta networks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4ae5f30d-113b-4261-b474-47ffbac08bf7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: a09eda25e8c7cc087770210cdfbe7e2bc9832acf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160633"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Tutorial: Integração de Diretório Ativo Azure com Conector Meta Networks

Neste tutorial, aprende-se a integrar o Conector meta redes com o Diretório Ativo Azure (Azure AD).
Integrar o Conector meta redes com a AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao Conector meta redes.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Conector meta networks (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Conector meta redes, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de sinal ativado por ligação de meta redes

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* O Conector meta redes suporta **SP** e **IDP** iniciadoS SSO
 
* O Conector de Redes Meta suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Adicionar conector meta redes da galeria

Para configurar a integração do Conector meta redes em Azure AD, precisa adicionar meta Networks Connector da galeria à sua lista de aplicações geridas saaS.

**Para adicionar meta networks Connector da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite o **Conector Meta Networks,** selecione **Meta Networks Connector** do painel de resultados e, em seguida, clique em adicionar o botão **Adicionar** a aplicação.

     ![Conector meta redes na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sinal de Azure AD com meta networks connector com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Conector meta redes.

Para configurar e testar o único sinal de Azure AD com o Conector meta networks, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o conector de metaredes único -](#configure-meta-networks-connector-single-sign-on)** para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Crie o utilizador de teste de **[Conector Meta Networks](#create-meta-networks-connector-test-user)** - para ter uma contrapartida da Britta Simon no Meta Networks Connector que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sinal de Azure AD com o Conector meta networks, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações do **Conector Meta Networks,** selecione **um único sinal.**

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** Se desejar configurar a aplicação no modo iniciado **idp,** execute os seguintes passos:

    ![Meta Networks Connector Domain e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    ![Meta Networks Connector Domain e URLs informações únicas de inscrição](common/both-advanced-urls.png)

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. Na caixa de texto **do Estado relé,** digite um URL utilizando o seguinte padrão:`https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`

    > [!NOTE]
    > Estes valores não são reais. Atualizar estes valores com o url do identificador, resposta e URL de sinal são explicados mais tarde no tutorial.

6. A aplicação Meta Networks Connector espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique no ícone **Editar** para abrir o diálogo **de Atributos do Utilizador.**

    ![image](common/edit-attribute.png)
    
7. Além de acima, a aplicação Meta Networks Connector espera que poucos atributos sejam retransmitidos na resposta SAML. Na secção **Reivindicações** do Utilizador no diálogo **de Atributos** do Utilizador, execute os seguintes passos para adicionar atributo token SAML, conforme indicado no quadro abaixo:
    
    | Nome | Atributo de origem | Espaço de nomes|
    | ---------------| --------------- | -------- |
    | primeiro nome | user.givenname | |
    | apelido | utilizador.sobrenome | |
    | endereço de e-mail| utilizador.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | nome | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | telefone | user.phonenumber | |

    a. Clique **Em adicionar nova alegação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Name,** digite o nome do atributo mostrado para essa linha.

    c. Deixe o espaço de **nome em** branco.

    d. Selecione Origem como **Atributo**.

    e. Na lista de **atributos Fonte,** digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

8. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

9. Na secção De configurar meta redes de **conector,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-meta-networks-connector-single-sign-on"></a>Configure Meta Networks Connector Single Sign-On

1. Abra um novo separador no seu navegador e faça login na sua conta de administrador do Conector meta redes.
    
    > [!NOTE]
    > O Conector meta redes é um sistema seguro. Por isso, antes de aceder ao seu portal, é necessário adicionar o seu endereço IP público a uma lista de licenciamento do seu lado. Para obter o seu endereço IP público, siga o link abaixo especificado [aqui](https://whatismyipaddress.com/). Envie o seu endereço IP para a equipa de suporte do Cliente do [Conector meta redes](mailto:support@metanetworks.com) para adicionar o seu endereço IP a uma lista de suporte.
    
2. Vá ao **Administrador** e selecione **Definições**.
    
    ![Configurar um único sinal](./media/metanetworksconnector-tutorial/configure3.png)
    
3. Certifique-se de que **o Tráfego de Internet e** a Força **VPN MFA** estão prontos para arrancar.
    
    ![Configurar um único sinal](./media/metanetworksconnector-tutorial/configure1.png)
    
4. Vá ao **Administrador** e selecione **SAML**.
    
    ![Configurar um único sinal](./media/metanetworksconnector-tutorial/configure4.png)
    
5. Execute os seguintes passos na página **DETAILS:**
    
    ![Configurar um único sinal](./media/metanetworksconnector-tutorial/configure2.png)
    
    a. Copie o valor **do URL SSO** e cole-o na caixa de texto URL **sign-in** na secção **Domínio e URLs do Conector** de Redes Meta.
    
    b. Copiar valor **DE URL** do destinatário e colar na caixa de texto URL **de resposta** na secção Domínio e URLs do **Conector** de Redes Meta.
    
    c. Copiar O valor **do Público URI (ID da Entidade SP)** e colá-lo na caixa de texto **identificador (Id** da entidade) na secção **Domínio e URLs do Conector** de Redes Meta.
    
    d. Ativar o SAML
    
6. No separador **GENERAL.** Efetuar os seguintes passos:

    ![Configurar um único sinal](./media/metanetworksconnector-tutorial/configure5.png)

    a. No **URL de Assinatura Única**do Fornecedor de Identidade, colá o valor URL de **Login** que copiou do portal Azure.

    b. No **Emitente**do Fornecedor de Identidade, cola o valor do **Identificador AD Azure** que copiou do portal Azure.

    c. Abra o certificado descarregado do portal Azure no bloco de notas, cola-o na caixa de texto **x.509 Certificate.**

    d. Ativar o **provisionamento Just-in-Time**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome,** entre **brittaSimon.**
  
    b. No campo **de nome do Utilizador,** **escreva brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que a Britta Simon utilize um único signo Azure, concedendo acesso ao Meta Networks Connector.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Meta Networks Connector**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Meta Networks Connector**.

    ![O link de conector meta redes na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-meta-networks-connector-test-user"></a>Criar o utilizador de teste de conector de metaredes

Nesta secção, um utilizador chamado Britta Simon é criado no Meta Networks Connector. O Conector meta redes suporta o fornecimento just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Conector Meta Networks, um novo é criado quando tenta aceder ao Conector Meta Networks.

>[!Note]
>Se precisar de criar um utilizador manualmente, contacte a equipa de suporte ao Cliente do [Conector meta redes.](mailto:support@metanetworks.com)

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Conector meta redes no Painel de Acesso, deve ser automaticamente inscrito no Conector meta redes para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

