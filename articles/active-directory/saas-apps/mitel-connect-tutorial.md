---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Mitel Connect Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Mitel Connect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: 26a761708f56ff7aba8daf86d2991579e60291cb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81870199"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>Tutorial: Integração de Diretório Ativo Azure com Mitel MiCloud Connect

Neste tutorial, você vai aprender a integrar Mitel MiCloud Connect com o Azure Ative Directory (Azure AD). Integrar o MiCloud Connect com o Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso a aplicações MiCloud Connect usando as suas credenciais empresariais.
* Pode ativar os utilizadores na sua conta para serem automaticamente inscritos no MiCloud Connect (Single Sign-On) com as suas contas Azure AD.


Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o MiCloud Connect, precisa dos seguintes itens:

* Uma subscrição do Azure

  Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Uma conta Mitel MiCloud Connect

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, irá configurar e testar o único sign-on azure AD (SSO).

* Mitel Connect suporta **SP** iniciado SSO

## <a name="adding-mitel-connect-from-the-gallery"></a>Adicionar Mitel Connect da galeria

Para configurar a integração do Mitel Connect em Azure AD, precisa adicionar o Mitel Connect da galeria à sua lista de aplicações geridas saaS no portal Azure.

**Para adicionar Mitel Connect a partir da galeria, faça os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no **Diretório Ativo Azure**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Clique em **Aplicações Empresariais** e, em seguida, clique em **todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Clique em **Nova aplicação**.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Escreva **Mitel Connect** no campo de pesquisa, clique em **Mitel Connect** a partir do painel de resultados e, em seguida, clique em **Adicionar**.

     ![Mitel Connect na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, irá configurar e testar um único sign-on azure ad com o MiCloud Connect com base num utilizador de teste chamado **Britta Simon**. Para um único início de sessão funcionar, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no MiCloud Connect.

Para configurar e testar o único sinal de Azure AD com o MiCloud Connect, é necessário completar os seguintes passos:

1. **[Configure o MiCloud Connect para SSO com a AD Azure](#configure-micloud-connect-for-sso-with-azure-ad)** - para permitir que os seus utilizadores utilizem esta funcionalidade e configurem as definições de SSO no lado da aplicação.
2. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
3. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
4. **[Crie um utilizador de teste Mitel MiCloud Connect](#create-a-mitel-micloud-connect-test-user)** - para ter uma contrapartida da Britta Simon na sua conta MiCloud Connect que esteja ligada à representação da AD Azure do utilizador.
5. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Configure MiCloud Connect para SSO com AD Azure

Nesta secção, irá ativar o único sinal de entrada do Azure AD para o MiCloud Connect no portal Azure e configurar a sua conta MiCloud Connect para permitir o SSO utilizando o Azure AD.

Para configurar o MiCloud Connect com o SSO para a AD Azure, é mais fácil abrir o portal Azure e o portal Da Conta Mitel lado a lado. Terá de copiar algumas informações do portal Azure para o portal da Conta Mitel e algumas do portal Da Conta Mitel para o portal Azure.


1. Para abrir a página de configuração no [portal Azure,](https://portal.azure.com/)faça o seguinte:

    a. Na página de integração da aplicação **Mitel Connect,** clique **em um único sinal .**

    ![Configurar um único link de sinalização](common/select-sso.png)

    b. No diálogo **Select a Single sign-on,** clique em **SAML**.

    ![Modo de seleção de sinal único](common/select-saml-option.png)
    
    A página de sinalização baseada em SAML é apresentada.

2. Para abrir o diálogo de configuração no portal da Conta Mitel, faça o seguinte:

    a. No menu **Do Sistema Telefónico,** clique em **Funcionalidades add-on**.

    b. À direita do **"Single Sign-On",** clique em **Ativar** ou **Configurações**.
    
    A caixa de diálogo de definições de início de sinal único de ligação aparece.
    
3. Selecione a caixa de verificação **de sinal único ativada.**
    ![imagem](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. No portal Azure, clique no ícone **Editar** na secção **basic SAML Configuração.**
    ![imagem](common/edit-urls.png)

    Aparece a caixa de diálogo basic SAML Configuration.

5.  Copie o URL do campo **Mitel Identifier (Id** da Entidade) no portal da Conta Mitel e cole-o no campo **Identificador (Id da Entidade)** no portal Azure.

6. Copie o URL do campo URL de Resposta (URL do **Serviço de Consumidor de Afirmação)** no portal da Conta Mitel e cole-o no campo URL de Resposta (URL do Serviço de Consumidor de **Afirmação)** no portal Azure.  
   ![imagem](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. No **Sign on URL** text box, escreva um dos seguintes URLs:

    * **https://portal.shoretelsky.com**- utilizar o portal Da Conta Mitel como aplicação padrão da Mitel
    * **https://teamwork.shoretel.com**- usar o Trabalho de Equipa como aplicação padrão da Mitel

    **NOTA**: A aplicação padrão Mitel é a aplicação acedida quando um utilizador clica no azulejo Mitel Connect no Painel de Acesso. Esta é também a aplicação acedida ao fazer uma configuração de teste a partir de Azure AD.

8. Clique em **Guardar** na caixa de diálogo **Basic SAML Configuração** no portal Azure.

9. Na secção de Certificado de **Assinatura SAML** na página de **sinalização baseada em SAML** no portal Azure, clique em **Baixar** ao lado do **Certificado (Base64)** para descarregar o Certificado de **Assinatura** e guardá-lo para o seu computador.
    ![imagem](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. Abra o ficheiro certificado de assinatura num editor de texto, copie todos os dados do ficheiro e, em seguida, cole os dados no campo **de Certificado de Assinatura** no portal da Conta Mitel. 
    ![imagem](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. Na secção **Configuração Mitel Connect** na página **de sinalização baseada em SAML** do portal Azure, faça o seguinte:

    a. Copie o URL do campo URL de **Login** e cole-o no campo URL de **entrada** no portal da Conta Mitel.

    b. Copie o URL do campo **de identificador Azure AD** e cole-o no campo ID da **Entidade** no portal da Conta Mitel.
    ![imagem](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. Clique em **Guardar** na caixa de diálogo de **definições de início de sinal única** no portal da conta Mitel.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

Nesta secção, você vai criar uma utilizadora de teste chamada Britta Simon no portal Azure.

1. No portal Azure, no painel esquerdo, clique no **Diretório Ativo do Azure,** clique em **Utilizadores**e clique em **todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Clique em **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. No diálogo sobre as propriedades do Utilizador, faça os seguintes passos:

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome,** **digite BrittaSimon.**
  
    b. No campo **de nome do Utilizador,** escreva brittasimon@\<domínio da sua empresa .\> \<extensão\>.  
Por exemplo, BrittaSimon@contoso.com.

    c. Selecione a caixa de verificação de **palavra-passe Do Show** e, em seguida, escreva o valor que está exposto na caixa **password.**

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que Britta Simon utilize um único sign-on Azure, concedendo acesso ao Mitel Connect.

1. No portal Azure, clique em **Aplicações Empresariais,** e depois clique em **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, clique em **Mitel Connect**.

    ![O link Mitel Connect na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, clique em **Utilizadores e grupos.**

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique em **Adicionar utilizador**e, em seguida, clique em Utilizadores **e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** na lista de **Utilizadores** e, em seguida, clique em **Selecionar** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, selecione a função adequada para o utilizador a partir da lista no diálogo **Select Role** e, em seguida, clique em **Selecionar** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição,** clique em **Atribuir**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Criar um utilizador de teste Mitel MiCloud Connect

Nesta secção, cria-se uma utilizadora chamada Britta Simon na sua conta MiCloud Connect. Os utilizadores devem ser criados e ativados antes de utilizarem um único sinal.

Para mais detalhes sobre a adição de utilizadores no portal da Conta Mitel, consulte o artigo [adicionar um utilizador](https://oneview.mitel.com/s/article/Adding-a-User-092815) na Base de Conhecimento de Mitel.

Crie um utilizador na sua conta MiCloud Connect com os seguintes detalhes:

  * **Nome:** Britta Simon

* **Endereço de e-mail de negócios:**`brittasimon@<yourcompanydomain>.<extension>`   
(Exemplo: [brittasimon@contoso.com](mailto:brittasimon@contoso.com))

* **Nome de utilizador:**`brittasimon@<yourcompanydomain>.<extension>`  
(Exemplo: [brittasimon@contoso.com](mailto:brittasimon@contoso.com); o nome de utilizador é tipicamente o mesmo que o endereço de e-mail do utilizador)

**NOTA:** O nome de utilizador MiCloud Connect deve ser idêntico ao endereço de e-mail do utilizador no Azure.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, irá testar a configuração de inscrição única do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Mitel Connect no Painel de Acesso, deve ser automaticamente redirecionado para iniciar sessão na aplicação MiCloud Connect configurada como predefinição no campo **Deurl.** Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
