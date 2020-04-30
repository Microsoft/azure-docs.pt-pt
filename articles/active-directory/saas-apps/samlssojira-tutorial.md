---
title: 'Tutorial: Integração do Diretório Ativo Azure com saml SSO para Jira por Resolução GmbH [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SAML SSO para a Jira por resolução GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6f6cb87cf7628c48ce6adf12336c4b712dc0ff9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82202677"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Tutorial: Integração do Diretório Ativo Azure com a SAML SSO para a Jira por resolução GmbH

Neste tutorial, aprende-se a configurar o SAML SSO para a Jira através da resolução GmbH com o Azure Ative Directory (Azure AD).
Integrar o SAML SSO para a Jira por resolução GmbH com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem pode iniciar sessão na Jira com o plugin SAML SSO por resolução GmbH.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Jira com as suas contas Azure AD utilizando o SAML SSO para a Jira através da resolução GmbH (Single Sign-On).
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure e o SAML SSO para a Jira por resolução GmbH, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um julgamento de um mês [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* SAML SSO para Jira por resolução GmbH assinatura única ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* SAML SSO para Jira por resolução GmbH suporta **SP** e **IDP** iniciado SSO

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Adicionar um pedido de empresa para um único sign-on

Para configurar uma única inscrição em Azure AD, você precisa adicionar uma nova aplicação da empresa. Na galeria, existe um predefinido de aplicação pré-configurado para este, **SAML SSO para Jira por resolução GmbH**.

**Para adicionar SAML SSO para Jira por resolução GmbH a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais**e, em seguida, clique em **todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique no novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SAML SSO para Jira por resolução GmbH,** selecione **SAML SSO para Jira por resolução GmbH** a partir do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação. Também pode alterar o nome da aplicação da empresa.

     ![SAML SSO para Jira por resolução GmbH na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>Configure e teste um único sinal com o plugin SAML SSO e Azure AD

Nesta secção, irá testar e configurar uma única inscrição na Jira para um utilizador da AD Azure. Isto será feito para uma utilizadora de teste chamada **Britta Simon.**
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no SAML SSO para a Jira por resolução.

Para configurar e testar um único sinal, é necessário completar os seguintes passos:

1. **[Configure o pedido de empresa azure AD para um único sign-on](#configure-the-azure-ad-enterprise-application-for-single-sign-on)** - Configure o pedido de empresa Azure AD para o único sign-on
2. **[Configure o plugin SAML SSO da sua instância Jira](#configure-the-saml-sso-plugin-of-your-jira-instance)** - Configure as definições de início de sessão individuais no lado da aplicação.
3. Criar um utilizador de **[teste Azure AD](#create-an-azure-ad-test-user)** - Criar um utilizador de teste em Azure AD.
1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - Habilitar o utilizador do teste a utilizar o único sinal no lado Azure.
1. **[Crie o utilizador de teste em Jira](#create-the-test-user-also-in-jira)** - Crie um utilizador de teste de contraparte em Jira para o utilizador de teste Azure AD.
1. **[Teste o único sinal -](#test-single-sign-on)** Verifique se a configuração funciona.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>Configure o pedido de empresa da Azure AD para um único sign-on

Nesta secção, configura o único sinal no portal Azure.

Para configurar o único sign-on com o SAML SSO para jira por resolução GmbH, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), no recém-criado **SAML SSO para jira por resolução A** aplicação empresarial GmbH, selecione **single sign-on** no painel esquerdo.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. Para **selecionar um método de sessão individual,** selecione o modo **SAML** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. Em seguida, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **de Configuração SAML Básica,** se pretender configurar a aplicação no modo iniciado do **IDP,** então execute os seguintes passos:

    ![SAML SSO para Jira por resolução GmbH Domínio e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<server-base-url>/plugins/servlet/samlsso`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<server-base-url>/plugins/servlet/samlsso`

    c. Clique em **Definir URLs adicionais** e executar o seguinte passo, se desejar configurar a aplicação no modo iniciado **sp:**

    ![SAML SSO para Jira por resolução GmbH Domínio e URLs informações únicas de inscrição](common/metadata-upload-additional-signon.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Para o URL identificador, resposta url ** \<** e URL de sinal, substitua o url base do servidor>com o URL base da sua instância Jira. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure. Se tiver algum problema, contacte-nos na [SAML SSO para a Jira através da resolução da equipa de apoio ao cliente da GmbH.](https://www.resolution.de/go/support)

4. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** descarregue o XML de **Metadados da Federação** e guarde-o para o seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>Configure o plugin SAML SSO da sua instância Jira 

1. Numa janela diferente do navegador web, inscreva-se na sua instância Jira como administrador.

2. Paire sobre a engrenagem no lado direito e clique em **Gerir aplicações**.
    
    ![Configurar um único sinal](./media/samlssojira-tutorial/addon1.png)

3. Se for redirecionado para a página de Acesso ao Administrador, introduza a **Palavra-passe** e clique no botão **Confirmar.**

    ![Configurar um único sinal](./media/samlssojira-tutorial/addon2.png)

4. Jira normalmente redireciona-te para o mercado atlassiano. Caso contrário, clique em **Encontrar novas aplicações** no painel esquerdo. Procure por **SAML Single Sign On (SSO) para JIRA** e clique no botão **Instalar** para instalar o plugin SAML.

    ![Configurar um único sinal](./media/samlssojira-tutorial/store.png)

5. A instalação de plugin sairá. Quando estiver feito, clique no botão **Fechar.**

    ![Configurar um único sinal](./media/samlssojira-tutorial/store-2.png)

    ![Configurar um único sinal](./media/samlssojira-tutorial/store-3.png)

6. Em seguida, clique em **Gerir**.

    ![Configurar um único sinal](./media/samlssojira-tutorial/store-4.png)
    
8. Em seguida, clique em **Configurar** para configurar o plugin instalado.

    ![Configurar um único sinal](./media/samlssojira-tutorial/store-5.png)

9. No assistente de **configuração do Plugin SAML SingleSignOn,** clique em **Adicionar novo IdP** para configurar o Azure AD como um novo Fornecedor de Identidade.

    ![Configurar um único sinal](./media/samlssojira-tutorial/addon4.png) 

10. Na página Escolha da sua página de Fornecedor de **Identidade SAML,** execute os seguintes passos:

    ![Configurar um único sinal](./media/samlssojira-tutorial/addon5a.png)
 
    a. Coloque o **AD Azure** como o tipo IDP.
    
    b. Adicione o **nome** do Fornecedor de Identidade (por exemplo, Azure AD).
    
    c. Adicione uma **descrição** (opcional) do Fornecedor de Identidade (por exemplo, Azure AD).
    
    d. Clique em **Seguinte**.
    
11. Na página de configuração do **fornecedor de identidade,** clique em **Seguinte**.
 
    ![Configurar um único sinal](./media/samlssojira-tutorial/addon5b.png)

12. Na página **de metadados IdP Import SAML,** execute os seguintes passos:

    ![Configurar um único sinal](./media/samlssojira-tutorial/addon5c.png)

    a. Clique no botão **de ficheiro Seleto de Metadados XML** e escolha o ficheiro **De dados da Federação De metadados XML** que tenha descarregado antes.

    b. Clique no botão **Import.**
     
    c. Espere brevemente até que a importação tenha sucesso.  
     
    d. Clique no botão **Seguinte.**
    
13. Na página de atribuição e transformação do **Id do Utilizador,** clique no botão **Seguinte.**

    ![Configurar um único sinal](./media/samlssojira-tutorial/addon5d.png)
    
14. Na página de **criação e atualização** do Utilizador, clique em **Guardar & Seguinte** para guardar as definições.
    
    ![Configurar um único sinal](./media/samlssojira-tutorial/addon6a.png)
    
15. Na página **de definições** do Teste, clique em **skip test & configurar manualmente** para saltar o teste do utilizador por enquanto. Isto será realizado na secção seguinte e requer algumas configurações no portal Azure.
    
    ![Configurar um único sinal](./media/samlssojira-tutorial/addon6b.png)
    
16. Clique **em OK** para saltar o aviso.
    
    ![Configurar um único sinal](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon. Com o utilizador, irá testar uma única inscrição.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Escolha **o novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do **Utilizador,** execute os seguintes passos:

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome,** entre **Britta Simon.**
  
    b. No campo de nome <b>BrittaSimon@contoso.com</b>do **Utilizador,** introduza .

    c. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, você adiciona Britta Simon à aplicação da empresa, o que lhe permite usar um único sign-on.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**. 

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, procure a aplicação da empresa que criou no início deste tutorial. Se está a seguir os passos do tutorial, chama-se **SAML SSO para Jira por resolução GmbH**. Se lhe deu outro nome, procure esse nome.

    ![O SAML SSO para Jira por resolução Ligação GmbH na lista de Aplicações](common/all-applications.png)

3. No painel esquerdo, clique em **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role,** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-the-test-user-also-in-jira"></a>Crie o utilizador de teste também em Jira

Para permitir que os utilizadores da AD Azure inscrevam-se no SAML SSO para a Jira por resolução GmbH, devem ser aprovisionados no SAML SSO para a Jira por resolução GmbH. Para o caso deste tutorial, você tem que fazer o provisionamento à mão. No entanto, existem também outros modelos de provisionamento disponíveis para o plugin SAML SSO por resolução, por exemplo, o provisionamento **Just In Time.** Consulte a sua documentação na [SAML SSO por resolução GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Se tiver alguma pergunta sobre o mesmo, contacte o apoio à [resolução.](https://www.resolution.de/go/support)

**Para fornecer manualmente uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se na instância de Jira como administradora.

2. Pairar sobre a engrenagem e selecionar **a gestão**do utilizador.

   ![Adicionar Empregado](./media/samlssojira-tutorial/user1.png)

3. Se for redirecionado para a página de Acesso ao Administrador, introduza a **Palavra-passe** e clique no botão **Confirmar.**

    ![Adicionar Empregado](./media/samlssojira-tutorial/user2.png) 

4. Na secção de separadores de gestão do **utilizador,** clique em **criar o utilizador**.

    ![Adicionar Empregado](./media/samlssojira-tutorial/user3-new.png) 

5. Na página de diálogo **"Criar um novo utilizador",** execute os seguintes passos. Tem de criar o utilizador exatamente como em Azure AD:

    ![Adicionar Empregado](./media/samlssojira-tutorial/user4-new.png) 

    a. Na caixa de texto de **endereço de e-mail,** digite o endereço de e-mail do utilizador: <b>BrittaSimon@contoso.com</b>.

    b. Na caixa de texto **Nome Completo,** digite o nome completo do utilizador: **Britta Simon**.

    c. Na caixa de texto **username,** escreva <b>BrittaSimon@contoso.com</b>o endereço de e-mail do utilizador: . 

    d. Na caixa de texto **Password,** introduza a palavra-passe do utilizador.

    e. Clique em **Criar o utilizador** para terminar a criação do utilizador.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no SAML SSO para Jira por resolução, o azulejo GmbH no Painel de Acesso, deve ser automaticamente inscrito no SAML SSO para jira por resolução GmbH para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

Também pode testar um único sinal, `https://<server-base-url>/plugins/servlet/samlsso`se navegar para . Substitua o ** \<url-base do servidor>** com o URL base da sua instância Jira.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Ativar uma única direção de sinalização para Jira

Como se nota na secção anterior, existem atualmente duas formas de desencadear o único sinal. Quer utilizando o **portal Azure** ou utilizando **um link especial para a sua instância Jira**. O plugin SAML SSO por resolução GmbH também permite ativar um único sinal aceso simplesmente **a qualquer URL que indique a sua instância Jira**.

No essencial, todos os utilizadores que acedam à Jira serão redirecionados para o único sinal depois de ativarem uma opção no plugin.

Para ativar o redirecionamento SSO, faça o seguinte na **sua instância Jira:**

1. Aceda à página de configuração do plugin SAML SSO em Jira.
1. Clique em **Redirecionamento** no painel esquerdo.
![](./media/samlssojira-tutorial/ssore1.png)

1. O carrapato ativa o **redirecionamento SSO**.
![](./media/samlssojira-tutorial/ssore2.png) 

1. Prima o botão **'Definições de Poupança'** no canto superior direito.

Depois de ativar a opção, ainda pode chegar ao pedido de username/password `https://\<server-base-url>/login.jsp?nosso`se a opção Enable **nosso** for assinalada navegando para . Como sempre, ** \<** substitua o>de url base do servidor com o url base.


## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

