---
title: 'Tutorial: Integração de Diretório Ativo Azure com o RStudio Connect Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o RStudio Connect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9bc78022-6d38-4476-8f03-e3ca2551e72e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bb5dd845b03bd94f0a94db50c01b804cf6f55c2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81407105"
---
# <a name="tutorial-azure-active-directory-integration-with-rstudio-connect"></a>Tutorial: Integração de Diretório Ativo Azure com rStudio Connect

Neste tutorial, aprende-se a integrar o RStudio Connect com o Azure Ative Directory (Azure AD).
Integrar o RStudio Connect com o Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao RStudio Connect.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no RStudio Connect (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o RStudio Connect, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* RStudio Connect. Há uma avaliação gratuita de [45 dias.](https://www.rstudio.com/products/connect/)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* RStudio Connect suporta **SP e IDP** iniciadoS SSO

* RStudio Connect suporta o provisionamento de utilizadores **justo no tempo**

## <a name="adding-rstudio-connect-from-the-gallery"></a>Adicionar RStudio Connect da galeria

Para configurar a integração do RStudio Connect no Azure AD, precisa de adicionar o RStudio Connect da galeria à sua lista de aplicações saaS geridas.

**Para adicionar RStudio Connect a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite RStudio Connect**, selecione **RStudio Connect** a partir do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

    ![RStudio Connect na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o single sign-on azure ad com o RStudio Connect com base num utilizador de teste chamado **Britta Simon**.
Para um único início de sessão funcionar, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no RStudio Connect.

Para configurar e testar o único sign-on azure ad com o RStudio Connect, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure rStudio Connect Single Sign-On](#configure-rstudio-connect-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create RStudio Connect test user](#create-rstudio-connect-test-user)** - para ter uma contrapartida de Britta Simon no RStudio Connect que está ligada à representação do Utilizador azure AD.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com o RStudio Connect, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **RStudio Connect,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **basic SAML Configuration,** se desejar configurar a aplicação no modo iniciado `<example.com>` **idp,** execute os seguintes passos, substituindo o endereço do servidor de ligação rStudio e a porta:

    ![RStudio Connect Domain e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<example.com>/__login__/saml`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<example.com>/__login__/saml/acs`

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    ![RStudio Connect Domain e URLs informações únicas de inscrição](common/metadata-upload-additional-signon.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<example.com>/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta real e URL de sinalização. São determinados a partir do`https://example.com` Endereço de Servidor de Ligação RStudio (nos exemplos acima). Contacte a equipa de [suporte RStudio Connect](mailto:support@rstudio.com) se tiver problemas. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

6. A aplicação RStudio Connect espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem a seguir mostra a lista de atributos predefinidos, onde o identificador de **nomes** é mapeado com **nome de utilizador.userprincipal .** A aplicação RStudio Connect espera que o **identificador** de nomes seja mapeado com **user.mail**, por isso precisa editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

7. Na configuração do Single Sign-On com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

### <a name="configure-rstudio-connect-single-sign-on"></a>Configure RStudio Connect Single Sign-On

Para configurar um único início de sessão para **rStudio Connect,** precisa de utilizar o Url de Metadados da Federação de **Aplicações** e **o Endereço de Servidor** usado acima. Isto é feito no ficheiro de `/etc/rstudio-connect.rstudio-connect.gcfg`configuração RStudio Connect em .

Este é um ficheiro de configuração de exemplo:

```
[Server]
SenderEmail =

; Important! The user-facing URL of your RStudio Connect server.
Address = 

[Http]
Listen = :3939

[Authentication]
Provider = saml

[SAML]
Logging = true

; Important! The URL where your IdP hosts the SAML metadata or the path to a local copy of it placed in the RStudio Connect server.
IdPMetaData = 

IdPAttributeProfile = azure
SSOInitiated = IdPAndSP
```

Guarde **Server Address** o `Server.Address` seu Endereço de Servidor no valor e o Url de Metadados da Federação de **Aplicações** no `SAML.IdPMetaData` valor. Note que esta configuração da amostra utiliza uma ligação HTTP não encriptada, enquanto o Azure AD requer a utilização de uma ligação HTTPS encriptada. Pode utilizar um [proxy inverso](https://docs.rstudio.com/connect/admin/proxy/) em frente ao RStudio Connect ou configurar o RStudio Connect para utilizar o HTTPS [diretamente](https://docs.rstudio.com/connect/admin/appendix/configuration/#HTTPS). 

Se tiver problemas com a configuração, pode ler o [Guia de Administrador rStudio Connect](https://docs.rstudio.com/connect/admin/authentication/saml/) ou enviar um e-mail à equipa de suporte do [RStudio](mailto:support@rstudio.com) para obter ajuda.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **User name** tipo `brittasimon@yourcompanydomain.extension`de campo do nome do utilizador . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao RStudio Connect.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **RStudio Connect**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **RStudio Connect**.

    ![O link RStudio Connect na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-rstudio-connect-test-user"></a>Criar o utilizador de teste RStudio Connect

Nesta secção, um utilizador chamado Britta Simon é criado no RStudio Connect. O RStudio Connect suporta o fornecimento just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador ainda não existir no RStudio Connect, um novo é criado quando tenta aceder ao RStudio Connect.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo RStudio Connect no Painel de Acesso, deve ser automaticamente inscrito no RStudio Connect para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

