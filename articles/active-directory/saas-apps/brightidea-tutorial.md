---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Brightidea Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Brightidea.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3adae3e0-f43b-492f-b373-6a512d2d6046
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff89f7323ba4cdf14ed1c052b2dd4e4e06356e00
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73157611"
---
# <a name="tutorial-azure-active-directory-integration-with-brightidea"></a>Tutorial: Integração do Diretório Ativo Azure com a Brightidea

Neste tutorial, aprende-se a integrar a Brightidea com o Azure Ative Directory (Azure AD).
Integrar a Brightidea com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Brightidea.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Brightidea (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Brightidea, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de inscrição única Brightidea

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.


* Brightidea suporta **SP e IDP** iniciadoS SSO
* Brightidea suporta o fornecimento de utilizadores **Just In Time**


## <a name="adding-brightidea-from-the-gallery"></a>Adicionando Brightidea da galeria

Para configurar a integração da Brightidea em Azure AD, precisa de adicionar brightidea da galeria à sua lista de aplicações saaS geridas.

**Para adicionar brightidea da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Brightidea**, **selecione Brightidea** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Brightidea na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o single sign-on azure com brightidea com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Brightidea.

Para configurar e testar o único signo da Azure AD com a Brightidea, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On single Brightidea](#configure-brightidea-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Brightidea test user](#create-brightidea-test-user)** - para ter uma contrapartida de Britta Simon em Brightidea que está ligada à representação do utilizador azure AD.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure com brightidea, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Brightidea,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** se tiver ficheiro de **metadados do Prestador** de Serviços e pretender configurar no modo **intilado IDP,** execute os seguintes passos:

    a. Clique no **ficheiro de metadados de upload**.

    ![Upload ficheiro de metadados](common/upload-metadata.png)

    b. Clique no logotipo da **pasta** para selecionar o ficheiro de metadados e clicar em **Carregar**.

    ![escolher ficheiro de metadados](common/browse-upload-metadata.png)

    c. Após o ficheiro de metadados ser carregado com sucesso, os valores de URL do **Identificador** e **da Resposta** ficam povoados automaticamente na caixa de texto da secção Brightidea:

    ![image](common/idp-intiated.png)

    > [!Note]
    > Se os valores de URL do **Identificador** e **da Resposta** não forem automaticamente polerados, preencha os valores manualmente de acordo com a sua exigência.

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    ![image](common/metadata-upload-additional-signon.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.brightidea.com`

4. Na **configuração de um único sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na secção **Configurar brightidea,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-brightidea-single-sign-on"></a>Configure o sinal único brightidea

1. Numa janela de navegador web diferente, inscreva-se no Brightidea usando as credenciais do administrador.

2. Para chegar à funcionalidade SSO no seu sistema Brightidea, navegue para o**Separador**de Autenticação de **Configuração** -> da Empresa . Aí você verá dois separadores sub: Auth Selection & Perfis SAML.

    ![Configuração Brightidea](./media/brightidea-tutorial/configure1.png)

3. Selecione **Auth Selection**. Por padrão, apenas mostra dois métodos padrão: Brightidea Login & Registration. Quando um método SSO adicionado, aparecerá na lista.

    ![Configuração Brightidea](./media/brightidea-tutorial/configure2.png)

4. Selecione **Perfis SAML** e execute os seguintes passos:

    ![Configuração Brightidea](./media/brightidea-tutorial/configure3.png)

    a. Clique nos **Metadados de Descarregamento** e carregue na secção **de configuração Básica SAML** no portal Azure.

    b. Clique no botão **Adicionar Novo** sob a **Definição** do Fornecedor de Identidade e execute os seguintes passos:
    
    ![Configuração Brightidea](./media/brightidea-tutorial/configure4.png)
    
   * Introduza o nome do **perfil SAML** como, por exemplo,`Azure Ad SSO`
    
   * Para **carregar metadados,** clique em escolher ficheiro e fazer upload do ficheiro de metadados descarregado sabotado do portal Azure.

     > [!NOTE]
     > Depois de carregar o ficheiro de metadados, os restantes campos **Single Sign-on Service, Emitente do Fornecedor** de Identidade, Chave Pública de Upload irão povoar automaticamente.

   * Na caixa de texto **e-mail,** insira o valor como `mail`.
     
   * Na caixa de texto **Screen Name,** introduza o valor como `givenName`.
     
   * Clique em **Guardar Alterações**.  

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso à Brightidea.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Brightidea**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Brightidea**.

    ![O link Brightidea na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-brightidea-test-user"></a>Criar utilizador de teste Brightidea

Nesta secção, um utilizador chamado Britta Simon é criado na Brightidea. Brightidea suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Brightidea, um novo é criado após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Brightidea no Painel de Acesso, deve ser automaticamente inscrito no Brightidea para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

