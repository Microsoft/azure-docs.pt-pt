---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Zscaler Beta [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Zscaler Beta.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 56b846ae-a1e7-45ae-a79d-992a87f075ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07b0f8112f724c857ffb46378f7aa7ef605b9bbb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "68943295"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Tutorial: Integração do Diretório Ativo Azure com a Zscaler Beta

Neste tutorial, aprende-se a integrar a Zscaler Beta com o Azure Ative Directory (Azure AD).
Quando integrar a Zscaler Beta com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a Zscaler Beta.
* Permita que os seus utilizadores sejam automaticamente inscritos na Zscaler Beta com as suas contas Azure AD. Este controlo de acesso é chamado de inscrição única (SSO).
* Gerencie as suas contas num local central utilizando o portal Azure.

Para obter mais informações sobre software como uma integração de aplicações de serviço (SaaS) com a Azure AD, veja [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Zscaler Beta, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição Zscaler Beta que utiliza um único sinal.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Zscaler Beta suporta SSO iniciado por SP.
* A Zscaler Beta suporta o fornecimento de utilizadores just-in-time.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Adicione Zscaler Beta do Mercado Azure

Para configurar a integração da Zscaler Beta no Azure AD, adicione a Zscaler Beta do Azure Marketplace à sua lista de aplicações geridas do SaaS.

Para adicionar Zscaler Beta do Mercado Azure, siga estes passos.

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![Botão de Diretório Ativo Azure](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione **Nova aplicação** na parte superior da caixa de diálogo.

    ![Novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **a Zscaler Beta**. Selecione **Zscaler Beta** a partir do painel de resultados e, em seguida, selecione **Adicionar**.

     ![Zscaler Beta na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sinal de Azure AD com a Zscaler Beta com base na utilizadora de teste Britta Simon.
Para um único início de sessão, estabeleça uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Zscaler Beta.

Para configurar e testar o único sinal de Azure AD com a Zscaler Beta, complete os seguintes blocos de construção:

- [Configure](#configure-azure-ad-single-sign-on) o único sinal de ad do Azure para permitir que os seus utilizadores utilizem esta funcionalidade.
- [Configure o único sinal de entrada do Zscaler Beta](#configure-zscaler-beta-single-sign-on) para configurar as definições de inscrição únicas no lado da aplicação.
- [Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user) para testar o único sign-on da Azure AD com britta Simon.
- [Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que Britta Simon utilize um único sinal de AD Azure.
- [Crie um utilizador de teste Zscaler Beta](#create-a-zscaler-beta-test-user) para ter uma contrapartida da Britta Simon em Zscaler Beta que esteja ligada à representação da AD Azure do utilizador.
- [Teste um único sinal para](#test-single-sign-on) verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sinal de Azure AD com o Zscaler Beta, siga estes passos.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Zscaler Beta,** selecione **Single sign-on**.

    ![Configurar um link de início de sinal único](common/select-sso.png)

2. Na **seleta de uma única** caixa de diálogo de método de sinalização, selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. Na configuração de um único sign-on com a página **SAML,** selecione **Editar** para abrir a caixa de diálogo **de configuração SAML básica.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** siga este passo:

    ![Domínio Beta Zscaler e informações únicas de inscrição](common/sp-intiated.png)

    - Na caixa **de URL Sign on,** introduza o URL utilizado pelos seus utilizadores para iniciar sessão na sua aplicação Zscaler Beta.

    > [!NOTE]
    > O valor não é real. Atualize o valor com o sinal real no valor URL. Para obter o valor, contacte a equipa de suporte ao [cliente Zscaler Beta.](https://www.zscaler.com/company/contact)

5. A aplicação Zscaler Beta espera as afirmações do SAML num formato específico. Deve adicionar mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Selecione **Editar** para abrir a caixa de diálogo **User Atributos.**

    ![Caixa de diálogo de atributos do utilizador](common/edit-attribute.png)

6. A aplicação Zscaler Beta espera que mais alguns atributos sejam retransmitidos na resposta SAML. Na secção **de reclamações** do Utilizador na caixa de diálogo **User Atributos,** siga estes passos para adicionar o atributo token SAML, como mostrado na tabela seguinte.
    
    | Nome | Atributo de origem | 
    | ---------------| --------------- |
    | membroOf  | user.atribuídos |

    a. **Selecione Adicionar nova alegação** para abrir a caixa de diálogo de reclamações do **utilizador Gerir.**

    ![Caixa de diálogo de reclamações do utilizador](common/new-save-attribute.png)

    ![Gerir a caixa de diálogo de reclamações do utilizador](common/new-attribute-details.png)

    b. Na caixa **nome,** introduza o nome do atributo mostrado para aquela linha.

    c. Deixe a caixa **namespace** em branco.

    d. Para **Origem,** selecione **Attribute**.

    e. Na lista de **atributos Fonte,** insira o valor do atributo mostrado para essa linha.

    f. Selecione **OK**.

    g. Selecione **Guardar**.

    > [!NOTE]
    > Para aprender a configurar funções em Azure AD, consulte [Configurar a alegação de funções](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management).

7. Na **configuração de um único sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** selecione **Download** para descarregar o **Certificado (Base64)**. Guarde no seu computador.

    ![Link de descarregamento de certificado](common/certificatebase64.png)

8. Na secção Beta de **Configurar,** copie os URLs de que necessita para os seus requisitos:

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    - URL de Inicio de Sessão
    - Identificador Azure AD
    - Logout URL

### <a name="configure-zscaler-beta-single-sign-on"></a>Configure Zscaler Beta único sign-on

1. Para automatizar a configuração dentro do Zscaler Beta, instale a **extensão de navegador Secure-in das Minhas Aplicações** selecionando **a extensão**.

    ![Extensão das minhas Apps](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, selecionando **Configurar O Zscaler Beta** direciona-o para a aplicação Zscaler Beta. A partir daí, forneça as credenciais de administração para iniciar sessão na Zscaler Beta. A extensão do navegador configura automaticamente a aplicação para si e automatiza os passos 3 a 6.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Para configurar o Zscaler Beta manualmente, abra uma nova janela do navegador web. Inscreva-se no site da sua empresa Zscaler Beta como administrador e siga estes passos.

4. Vá para as Definições de Autenticação de Autenticação da > **Administração,** > **Authentication Settings**e siga estes passos. **Administration**
   
    ![Administration](./media/zscaler-beta-tutorial/ic800206.png "Administração")

    a. No tipo de **autenticação,** selecione **SAML**.

    b. **Selecione Configure SAML**.

5. Na janela **Edit SAML,** siga estes passos: 
            
    ![Gerir utilizadores & autenticação](./media/zscaler-beta-tutorial/ic800208.png "Gerir utilizadores & autenticação")
    
    a. Na caixa URL do **Portal SAML,** cola no URL de **Login** que copiou do portal Azure.

    b. Na caixa de atribuição de **nome de login,** introduza **o NameID**.

    c. Na caixa **de certificadoS SSL Pública,** selecione **Upload** para carregar o certificado de assinatura Azure SAML que descarregou do portal Azure.

    d. **Toggle Enable SAML Auto-Provisioning**.

    e. Na caixa de atributo de nome do **utilizador,** introduza o **nome do ecrã** se pretender ativar o fornecimento automático sAML para atributos de displayName.

    f. Na caixa de atribuição de **nome de grupo,** introduza **o membroSe** se pretender ativar o fornecimento automático sAML para membros Dos atributos.

    g. Na caixa de atribuição de nome do **departamento,** insira o **departamento** se pretender ativar a prestação automática da SAML para atributos do departamento.

    h. Selecione **Guardar**.

6. Na página de diálogo de autenticação do **utilizador Configurar,** siga estes passos:

    ![Menu de ativação e botão Ativar](./media/zscaler-beta-tutorial/ic800207.png)

    a. Paire sobre o menu **de ativação** na parte inferior esquerda.

    b. **Selecione Ativar**.

## <a name="configure-proxy-settings"></a>Configurar definições de proxy
Para configurar as definições de procuração no Internet Explorer, siga estes passos.

1. Inicie **o Internet Explorer**.

2. Selecione **opções** de Internet a partir do menu **Ferramentas** para abrir a caixa de diálogo opções de **Internet.** 
    
     ![Caixa de diálogo de opções de Internet](./media/zscaler-beta-tutorial/ic769492.png "Opções de Internet")

3. Selecione o separador **Ligações.** 
  
     ![Separador Ligações](./media/zscaler-beta-tutorial/ic769493.png "Ligações")

4. Selecione **definições LAN** para abrir a caixa de diálogo definições da **Rede local (LAN).**

5. Na **secção** proxy, siga estes passos: 
   
    ![Secção de servidor proxy](./media/zscaler-beta-tutorial/ic769494.png "Servidor proxy")

    a. Selecione o servidor proxy Use para a sua caixa de verificação **LAN.**

    b. Na caixa **de endereços,** introduza o **portal. Zscaler Beta.net.**

    c. Na caixa **do Porto,** introduza **80**.

    d. Selecione o **servidor de procuração bypass para endereços locais** verificar caixa.

    e. Selecione **OK** para fechar a caixa de diálogo definições da **Rede de Área Local (LAN).**

6. Selecione **OK** para fechar a caixa de diálogo Opções de **Internet.**

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

Crie um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo,**Users** > selecione Utilizadores de **Diretório** > Ativo Azure**Todos os utilizadores**.

    ![Links de utilizadores e todos os utilizadores](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Na caixa de diálogo **do Utilizador,** siga estes passos:

    ![Caixa de diálogo do utilizador](common/user-properties.png)

    a. Na caixa **de nomes,** entre **brittaSimon.**
  
    b. Na caixa **Nome de utilizador**, introduza `brittasimon@yourcompanydomain.extension`. Um exemplo é BrittaSimon@contoso.com.

    c. Selecione a caixa de verificação de **palavra-passe Mostrar.** Escreva o valor que mostra na caixa **password.**

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Ative britta Simon a usar o único sign-on Azure, concedendo acesso à Zscaler Beta.

1. No portal Azure, selecione **aplicações** > Enterprise**Todas as aplicações** > **Zscaler Beta**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, insira e selecione **Zscaler Beta**.

    ![Ligação Beta Zscaler na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![Ligação de utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar utilizador**. Na caixa de diálogo **Adicionar Atribuição,** selecione **Utilizadores e grupos**.

    ![Adicionar botão de utilizador](common/add-assign-user.png)

5. Na caixa de diálogo **De Utilizadores e grupos,** selecione o utilizador como **Britta Simon** da lista. Em seguida, escolha **Selecionar** na parte inferior do ecrã.

    ![Caixa de diálogo de utilizadores e grupos](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. Na caixa de diálogo **Select Role,** selecione a função de utilizador adequada na lista. Em seguida, escolha **Selecionar** na parte inferior do ecrã.

    ![Selecione caixa de diálogo Role](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

    ![Adicionar caixa de diálogo de atribuição](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Criar um utilizador de teste Zscaler Beta

Nesta secção, a utilizadora Britta Simon é criada em Zscaler Beta. A Zscaler Beta suporta o fornecimento de **utilizadores just-in-time**, que está ativado por padrão. Não há nada para fazer nesta secção. Se um utilizador ainda não existir no Zscaler Beta, um novo é criado após a autenticação.

>[!Note]
>Para criar um utilizador manualmente, contacte a equipa de [suporte Zscaler Beta](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Teste a configuração de um único sinal de acesso do Azure AD utilizando o Painel de Acesso.

Quando selecionar o azulejo Beta Zscaler no Painel de Acesso, deverá ser automaticamente inscrito na Beta Zscaler para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações do SaaS com diretório ativo azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

