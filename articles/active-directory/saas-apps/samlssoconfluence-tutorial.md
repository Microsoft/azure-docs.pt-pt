---
title: 'Tutorial: Integração do Azure Ative Directory com a SAML SSO for Confluence por resolução GmbH Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SAML SSO for Confluence por resolução GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: 87c24cf61974c284772aae23e48ffc907792895b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88543487"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Tutorial: Integração do Azure Ative Directory com a SAML SSO for Confluence por resolução GmbH

Neste tutorial, aprende-se a integrar o SAML SSO para a Confluência através da resolução GmbH com o Azure Ative Directory (Azure AD).
A integração do SSO SAML para a Confluência por resolução a GmbH com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a SAML SSO para Confluência por resolução GmbH.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no SSO SAML para Confluência através da resolução GmbH (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração Azure AD com a SAML SSO para Confluência por resolução GmbH, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* SAML SSO para Confluência por resolução Assinatura ativada pela GmbH

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* SAML SSO for Confluence por resolução GmbH apoia **SP** e **IDP** iniciado SSO

## <a name="adding-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>Adicionar SSO SAML para Confluência por resolução GmbH da galeria

Para configurar a integração do SSO SAML para Confluência através da resolução GmbH em Azure AD, você precisa adicionar SAML SSO para Confluence por resolução GmbH da galeria à sua lista de aplicações geridas saaS.

**Para adicionar SSO SAML para Confluência por resolução GmbH da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva SAML SSO para Confluência por resolução GmbH,** selecione **SAML SSO para Confluência por resolução GmbH** do painel de resultados e clique em **Adicionar** botão para adicionar a aplicação.

     ![SAML SSO para Confluência por resolução GmbH na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com sSo sso para confluência por resolução GmbH com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no SSO saml para confluência por resolução, a GmbH.

Para configurar e testar o Azure AD com sSo sso para confluência por resolução GmbH, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o SSO SAML para Confluência por resolução GmbH Single Sign-On](#configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Criar SAML SSO para Confluência por resolução Utilizador](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** de teste GmbH - para ter uma contraparte de Britta Simon em SAML SSO para Confluência por resolução GmbH que está ligada à representação AZure AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD um único sinal de inscrição com o SSO SAML para Confluência por resolução GmbH, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), no **SSO saml para confluência por resolução página** de integração de aplicações GmbH, selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML** execute os seguintes passos, se desejar configurar a aplicação no modo iniciado pelo **IDP:**

    ![SAML SSO para Confluência por resolução GmbH Domain e URLs informações únicas de sinal-on](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado sp:

    ![SAML SSO para Confluência por resolução GmbH Domain e URLs informações únicas de sinal-on](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a SAML SSO para a Confluence por resolução a equipa de suporte do cliente gmbH](https://www.resolution.de/go/support) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

### <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on"></a>Configure SAML SSO para a Confluência por resolução GmbH Single Sign-On

1. Numa janela diferente do navegador web, inicie sessão no seu **SSO SAML para Confluence por resolução O portal de administração GmbH** como administrador.

2. Hover on cog e clique nos **Add-ons**.
    
    ![Configurar Sign-On Individuais](./media/samlssoconfluence-tutorial/addon1.png)

3. É redirecionado para a página de Acesso ao Administrador. Introduza a palavra-passe e clique no botão **Confirmar.**

    ![Configurar Sign-On Individuais](./media/samlssoconfluence-tutorial/addon2.png)

4. No **separador ATLASSIAN MARKETPLACE,** clique **em Localizar novos addons**. 

    ![Configurar Sign-On Individuais](./media/samlssoconfluence-tutorial/addon.png)

5. Procure **o sinal único SAML On (SSO) para confluência** e clique no botão **Instalar** para instalar o novo plugin SAML.

    ![Configurar Sign-On Individuais](./media/samlssoconfluence-tutorial/addon7.png)

6. A instalação plugin irá arrancar. Clique em **Close** (Fechar).

    ![Configurar Sign-On Individuais](./media/samlssoconfluence-tutorial/addon8.png)

    ![Configurar Sign-On Individuais](./media/samlssoconfluence-tutorial/addon9.png)

7.  Clique em **Gerir**.

    ![Configurar Sign-On Individuais](./media/samlssoconfluence-tutorial/addon10.png)
    
8. Clique **em Configurar** para configurar o novo plugin.

    ![Configurar Sign-On Individuais](./media/samlssoconfluence-tutorial/addon11.png)

9. Este novo plugin também pode ser encontrado no separador & SEGURANÇA dos **UTILIZADORES.**

    ![Configurar Sign-On Individuais](./media/samlssoconfluence-tutorial/addon3.png)
    
10. Na página **de configuração do plugin singleSignOn SAML,** clique em Adicionar novo botão **IdP** para configurar as definições do Fornecedor de Identidade.

    ![Configurar Sign-On Individuais](./media/samlssoconfluence-tutorial/addon4.png)

11. Na escolha da sua página **de Fornecedor de Identidade SAML,** execute os seguintes passos:

    ![Configurar Sign-On Individuais](./media/samlssoconfluence-tutorial/addon5a.png)
 
    a. Desagrafe **o AD AD** como o tipo IdP.
    
    b. Adicionar **Nome** do Fornecedor de Identidade (por exemplo, Azure AD).
    
    c. Adicionar **Descrição** do Fornecedor de Identidade (por exemplo, Azure AD).
    
    d. Clique em **Seguinte**.
    
12. Na página **de configuração do fornecedor de identidade,** clique no botão **Seguinte.**

    ![Configurar Sign-On Individuais](./media/samlssoconfluence-tutorial/addon5b.png)

13. Na página **de Metadados IdP de Importação SAML,** execute os seguintes passos:

    ![Configurar Sign-On Individuais](./media/samlssoconfluence-tutorial/addon5c.png)

    a. Clique no botão **'Ficheiro de Carga'** e escolha o ficheiro XML dos metadados que descarregou no Passo 5.

    b. Clique **no botão Importar.**
    
    c. Aguarde brevemente até que a importação tenha sucesso.
    
    d. Clique **no próximo** botão.
    
14. Na página de id do utilizador e página **de transformação,** clique no botão **Seguinte.**

    ![Configurar Sign-On Individuais](./media/samlssoconfluence-tutorial/addon5d.png)
    
15. Na **página de criação e atualização do Utilizador,** clique em **Guardar & Seguinte** para guardar as definições.   
    
    ![Configurar Sign-On Individuais](./media/samlssoconfluence-tutorial/addon6a.png)
    
16. Na página **de Teste das definições,** clique em **fazer o teste de saltar & configurar manualmente** para saltar o teste do utilizador por enquanto. Isto será realizado na secção seguinte e requer algumas configurações no portal Azure. 
    
    ![Configurar Sign-On Individuais](./media/samlssoconfluence-tutorial/addon6b.png)
    
17. Na leitura do diálogo que **aparece, ignorando o teste significa...** clique **em OK**.
    
    ![Configurar Sign-On Individuais](./media/samlssoconfluence-tutorial/addon6c.png)

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

Nesta secção, você permite que Britta Simon utilize a Azure um único sinal, concedendo acesso ao SSO SAML para Confluência por resolução GmbH.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **SAML SSO para Confluência por resolução GmbH**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, escreva e selecione **SAML SSO para Confluência por resolução GmbH**.

    ![O SSO SAML para Confluência por resolução Ligação GmbH na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>Criar SSO SAML para Confluência por resolução Utilizador de teste GmbH

Para permitir que os utilizadores da Azure AD iniciem sessão no SSO SAML para Confluência por resolução GmbH, devem ser alojadas na SAML SSO para Confluence por resolução GmbH.  
No SSO SAML for Confluence por resolução GmbH, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu SSO SAML para Confluence por resolução do site da empresa GmbH como administrador.

2. Hover on cog e clique na **gestão**do Utilizador .

    ![Adicionar Empregado](./media/samlssoconfluence-tutorial/user1.png) 

3. Na secção Utilizadores, clique no **separador Adicionar utilizadores.** Na página de diálogo **"Adicionar um Utilizador",** execute os seguintes passos:

    ![Adicionar Empregado](./media/samlssoconfluence-tutorial/user2.png) 

    a. Na caixa de texto **username,** escreva o e-mail de um utilizador como Britta Simon.

    b. Na caixa de texto **'Nome Completo',** digite o nome completo do utilizador como Britta Simon.

    c. Na caixa de texto **por e-mail,** digite o endereço de e-mail do utilizador como Brittasimon@contoso.com .

    d. Na caixa de texto **da palavra-passe,** digite a palavra-passe para Britta Simon.

    e. Clique **em Confirmar A Palavra-passe** reentre na palavra-passe.
    
    f. Clique no botão **Adicionar.**

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no SSO SAML para Confluência por resolução, o azulejo gmbH no Painel de Acesso, deverá ser automaticamente inscrito no SSO SAML for Confluence por resolução, para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

