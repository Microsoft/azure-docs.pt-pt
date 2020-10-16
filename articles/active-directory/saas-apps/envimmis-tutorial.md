---
title: 'Tutorial: Integração do Azure Ative Directory com a Envi MMIS Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Envi MMIS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.openlocfilehash: 1eb408025a45f09a6bdb854f4379a560842ef2d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826273"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Tutorial: Integração do Diretório Ativo Azure com a Envi MMIS

Neste tutorial, aprende-se a integrar o Envi MMIS com o Azure Ative Directory (Azure AD).
A integração da Envi MMIS com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Envi MMIS.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Envi MMIS (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a Envi MMIS, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura única ativada envi MMIS

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Envi MMIS apoia **SP** e **IDP** iniciado SSO

## <a name="adding-envi-mmis-from-the-gallery"></a>Adicionar Envi MMIS da galeria

Para configurar a integração da Envi MMIS no AD AZure, é necessário adicionar o Envi MMIS da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Envi MMIS da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Envi MMIS**, selecione **Envi MMIS** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![Envi MMIS na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o Envi MMIS com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Envi MMIS.

Para configurar e testar o Azure AD com o Envi MMIS, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Único Sign-On da Envi MMIS](#configure-envi-mmis-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Envi MMIS test user](#create-envi-mmis-test-user)** - para ter uma contraparte de Britta Simon em Envi MMIS que está ligada à representação AZure AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com a Envi MMIS, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Envi MMIS,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração Single Sign-On com** a página SAML, clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![Screenshot que mostra a configuração "Basic S A M L" com o botão "Identifier", "Answer U R L" e "Save" realçado.](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://www.<CUSTOMER DOMAIN>.com/Account`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://www.<CUSTOMER DOMAIN>.com/Account/Acs`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![Informações únicas de súmis e URLs do Domínio Envi MMIS e URLs](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://www.<CUSTOMER DOMAIN>.com/Account`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte a [equipa de suporte do Cliente Envi MMIS](mailto:support@ioscorp.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. Na **configuração Single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

7. Na secção **Configurar O MMIS da Envi,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-envi-mmis-single-sign-on"></a>Configure Envi MMIS Single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no seu site Envi MMIS como administrador.

2. Clique no **separador My Domain.**

    ![Screenshot que mostra o menu "Utilizador" com "My Domain" selecionado.](./media/envimmis-tutorial/configure1.png)

3. Clique em **Editar**.

    ![Screenshot que mostra o botão "Editar" selecionado.](./media/envimmis-tutorial/configure2.png)

4. Selecione Utilize a caixa de **verificação de autenticação remota** e, em seguida, selecione **HTTP Redirect** a partir do **dropdown do tipo de autenticação.**

    ![Screenshot que mostra o separador "Detalhes" com "Use a autenticação remota" verificado e "H T T P Redirecionamento" selecionado.](./media/envimmis-tutorial/configure3.png)

5. Selecione o separador **Recursos** e, em seguida, clique **em Carregar Metadados**.

    ![Screenshot que mostra o separador "Recursos" com a ação "Upload Metadata" selecionada.](./media/envimmis-tutorial/configure4.png)

6. No **popup de metadados upload,** execute os seguintes passos:

    ![Screenshot que mostra o popup "Upload Metadata" com a opção "Ficheiro" selecionada e o ícone "escolher ficheiro" e o botão "OK" realçado.](./media/envimmis-tutorial/configure5.png)

    a. Selecione a opção **Ficheiro** a partir **do upload de** dropdown.

    b. Faça o upload do ficheiro de metadados descarregado do portal Azure selecionando o ícone de **ficheiro escolher**.

    c. Clique em **OK**.

7. Depois de carregar o ficheiro de metadados descarregado, os campos serão preenchidos automaticamente. Clique **em Atualização**

    ![Configure botão de poupança de Sign-On único](./media/envimmis-tutorial/configure6.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome,** **insira BrittaSimon**.
  
    b. No campo **nome do utilizador,** **escreva brittasimon \@ yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon utilize a Azure um único sinal de acesso, permitindo o acesso à Envi MMIS.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Envi MMIS**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Envi MMIS**.

    ![O link Envi MMIS na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Escolha' de Função,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-envi-mmis-test-user"></a>Criar utilizador de teste Envi MMIS

Para permitir que os utilizadores da Azure AD inscrevam-se na Envi MMIS, devem ser alistados na Envi MMIS. No caso da Envi MMIS, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa Envi MMIS como administrador.

2. Clique no **separador Lista de Utilização.**

    ![Screenshot que mostra o menu "Utilizador" com "Lista de Utilizadores" selecionado.](./media/envimmis-tutorial/user1.png)

3. Clique no botão **Adicionar Utilizador.**

    ![Screenshot que mostra a secção "Utilizadores" com o botão "Adicionar utilizador" selecionado.](./media/envimmis-tutorial/user2.png)

4. Na secção **'Adicionar Utilizador',** execute os seguintes passos:

    ![Adicionar Empregado](./media/envimmis-tutorial/user3.png)

    a. Na caixa de texto **do Nome do Utilizador,** digite o nome de utilizador da conta Britta Simon como ** \@ brittasimon contoso.com**.
    
    b. Na caixa de texto **First Name,** digite o primeiro nome de BrittaSimon como **Britta**.

    c. Na caixa de texto **Do Último Nome,** digite o sobrenome brittaSimon como **Simon.**

    d. Introduza o título do utilizador no **título** da caixa de texto.
    
    e. Na caixa de sms do **Endereço de E-mail,** digite o endereço de e-mail da conta Britta Simon como ** \@ brittasimon contoso.com**.

    f. Na caixa de texto **SSO User Name,** digite o nome de utilizador da conta Britta Simon como ** \@ brittasimon contoso.com**.

    exemplo, Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo DoMMIS Envi no Painel de Acesso, deverá ser automaticamente inscrito no MMIS Da Envi para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

