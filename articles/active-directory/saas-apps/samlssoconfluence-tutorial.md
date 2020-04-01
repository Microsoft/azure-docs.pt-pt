---
title: 'Tutorial: Integração do Diretório Ativo Azure com saml SSO para Confluência por resolução GmbH [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SAML SSO para Confluence por resolução GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 6b47d483-d3a3-442d-b123-171e3f0f7486
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9676a71940569b26d6b0b6bfef767108ae57d953
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161213"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Tutorial: Integração do Diretório Ativo Azure com saml SSO para Confluência por resolução GmbH

Neste tutorial, aprende-se a integrar o SAML SSO para confluência por resolução GmbH com o Azure Ative Directory (Azure AD).
Integrar o SAML SSO para confluência por resolução GmbH com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a SAML SSO para Confluence por resolução GmbH.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no SAML SSO para confluência por resolução GmbH (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o SAML SSO para confluência por resolução GmbH, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* SAML SSO for Confluence por resolução GmbH assinatura única ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* SAML SSO for Confluence por resolução GmbH suporta **SP** e **IDP** iniciado SSO

## <a name="adding-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>Adicionar SAML SSO para Confluência por resolução GmbH da galeria

Para configurar a integração do SAML SSO para confluência por resolução GmbH em Azure AD, você precisa adicionar SAML SSO para Confluência por resolução GmbH da galeria para a sua lista de aplicações SaaS geridas.

**Para adicionar SAML SSO para Confluence por resolução GmbH a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SAML SSO para Confluência por resolução GmbH,** selecione **SAML SSO para Confluência por resolução GmbH** do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![SAML SSO para Confluência por resolução GmbH na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com saml SSO para confluência por resolução GmbH com base num utilizador de teste chamado **Britta Simon**.
Para que o início único funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador coligado no SAML SSO para confluência por resolução.

Para configurar e testar o único signo azure ad com o SAML SSO para confluência por resolução GmbH, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure sAML SSO para confluência por resolução GmbH Single Sign-On](#configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on)** - para configurar as definições de início de sessão única no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie SAML SSO para Confluência por resolução O utilizador](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** de teste GmbH - para ter uma contrapartida da Britta Simon na SAML SSO para confluência por resolução GmbH que está ligada à representação azure AD do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com o SAML SSO para confluência por resolução GmbH, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), no **SAML SSO para Confluence por resolução Página** de integração de aplicações GmbH, selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML De configuração** executa os seguintes passos, se pretender configurar a aplicação no modo iniciado **IDP:**

    ![SAML SSO for Confluence por resolução GmbH Domain e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<server-base-url>/plugins/servlet/samlsso`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<server-base-url>/plugins/servlet/samlsso`

    c. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado por SP:

    ![SAML SSO for Confluence por resolução GmbH Domain e URLs informações únicas de inscrição](common/metadata-upload-additional-signon.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta real e URL de sinalização. Contacte a [SAML SSO para confluência por resolução A equipa](https://www.resolution.de/go/support) de suporte ao Cliente gmbH para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

4. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

### <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on"></a>Configure SAML SSO para Confluência por resolução GmbH Single Sign-On

1. Numa janela de navegador web diferente, inicie sessão no seu **SAML SSO para Confluência por resolução do portal de administração GmbH** como administrador.

2. Passe sobre a engrenagem e clique nos **Add-ons**.
    
    ![Configurar um único sinal](./media/samlssoconfluence-tutorial/addon1.png)

3. É redirecionado para a página de Acesso ao Administrador. Introduza a palavra-passe e clique em **Confirmar** o botão.

    ![Configurar um único sinal](./media/samlssoconfluence-tutorial/addon2.png)

4. Sob o **separador ATLASSIAN MARKETPLACE,** clique em **Encontrar novos add-ons**. 

    ![Configurar um único sinal](./media/samlssoconfluence-tutorial/addon.png)

5. Procure o **Sinal Único SAML (SSO) para confluência** e clique em **instalar** o novo plugin SAML.

    ![Configurar um único sinal](./media/samlssoconfluence-tutorial/addon7.png)

6. A instalação de plugin sairá. Clique em **Fechar**.

    ![Configurar um único sinal](./media/samlssoconfluence-tutorial/addon8.png)

    ![Configurar um único sinal](./media/samlssoconfluence-tutorial/addon9.png)

7.  Clique em **Gerir**.

    ![Configurar um único sinal](./media/samlssoconfluence-tutorial/addon10.png)
    
8. Clique em **Configurar** para configurar o novo plugin.

    ![Configurar um único sinal](./media/samlssoconfluence-tutorial/addon11.png)

9. Este novo plugin também pode ser encontrado no separador **DE SEGURANÇA & UTILIZADORES.**

    ![Configurar um único sinal](./media/samlssoconfluence-tutorial/addon3.png)
    
10. Na página de **configuração do Plugin SAML SingleSignOn,** clique em adicionar novo botão **IDP** para configurar as definições do Fornecedor de Identidade.

    ![Configurar um único sinal](./media/samlssoconfluence-tutorial/addon4.png)

11. Escolha a sua página de Fornecedor de **Identidade SAML,** execute os seguintes passos:

    ![Configurar um único sinal](./media/samlssoconfluence-tutorial/addon5a.png)
 
    a. Coloque o **AD Azure** como o tipo IDP.
    
    b. Adicionar **Nome** do Fornecedor de Identidade (por exemplo, Azure AD).
    
    c. Adicionar **Descrição** do Fornecedor de Identidade (por exemplo, Azure AD).
    
    d. Clique em **Seguinte**.
    
12. Na página de configuração do **fornecedor de identidade,** clique no botão **Seguinte.**

    ![Configurar um único sinal](./media/samlssoconfluence-tutorial/addon5b.png)

13. Na página **de metadados IdP Import SAML,** execute os seguintes passos:

    ![Configurar um único sinal](./media/samlssoconfluence-tutorial/addon5c.png)

    a. Clique no botão **'Carregar Ficheiro'** e escolha o ficheiro Metadata XML que descarregou no Passo 5.

    b. Clique no botão **Import.**
    
    c. Espere brevemente até que a importação tenha sucesso.
    
    d. Clique no botão **Seguinte.**
    
14. Na página de atribuição e transformação do **Id do utilizador,** clique no botão **Seguinte.**

    ![Configurar um único sinal](./media/samlssoconfluence-tutorial/addon5d.png)
    
15. Na página de **criação e atualização do Utilizador,** clique em **Guardar & Seguinte** para guardar as definições.   
    
    ![Configurar um único sinal](./media/samlssoconfluence-tutorial/addon6a.png)
    
16. No Teste da sua página de **definições,** clique no **teste Skip & configurar manualmente** para não fazer o teste do utilizador por enquanto. Isto será realizado na secção seguinte e requer algumas configurações no portal Azure. 
    
    ![Configurar um único sinal](./media/samlssoconfluence-tutorial/addon6b.png)
    
17. Na leitura do diálogo que **aparece, saltar os meios de teste...** clique em **OK**.
    
    ![Configurar um único sinal](./media/samlssoconfluence-tutorial/addon6c.png)

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

Nesta secção, permite que a Britta Simon utilize um único signo Azure, concedendo acesso à SAML SSO para confluência por resolução GmbH.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações,** em seguida, selecione **SAML SSO para Confluência por resolução GmbH**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, digite e selecione **SAML SSO para Confluência por resolução GmbH**.

    ![O SAML SSO para Confluência por resolução Ligação GmbH na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>Criar SAML SSO para Confluência por resolução Utilizador de teste GmbH

Para permitir que os utilizadores de AD Azure iniciem sessão no SAML SSO para confluência por resolução GmbH, devem ser aprovisionados no SAML SSO para confluência por resolução GmbH.  
No SAML SSO para Confluence por resolução GmbH, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu SAML SSO para confluência por resolução do site da empresa GmbH como administrador.

2. Passe sobre a engrenagem e clique na gestão do **Utilizador.**

    ![Adicionar Empregado](./media/samlssoconfluence-tutorial/user1.png) 

3. Na secção Utilizadores, clique em adicionar o separador **de utilizadores.** Na página de diálogo **"Adicionar um Utilizador",** execute os seguintes passos:

    ![Adicionar Empregado](./media/samlssoconfluence-tutorial/user2.png) 

    a. Na caixa de texto **Username,** digite o e-mail de utilizador como Britta Simon.

    b. Na caixa de texto **Full Name,** digite o nome completo de utilizador como Britta Simon.

    c. Na caixa de texto **e-mail,** Brittasimon@contoso.comdigite o endereço de e-mail do utilizador como .

    d. Na caixa de texto **Password,** digite a palavra-passe para Britta Simon.

    e. Clique **em Confirmar A Palavra-passe** reintroduza a palavra-passe.
    
    f. Clique no botão **Adicionar.**

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no SAML SSO para confluência por resolução De azulejos GmbH no Painel de Acesso, deve ser automaticamente inscrito no SAML SSO para Confluência por resolução GmbH para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

