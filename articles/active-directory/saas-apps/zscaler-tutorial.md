---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com zscaler [ Integração de diretório si) com zscaler [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Zscaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 68c453f7-aff1-4614-92d3-9b86f3ad99dc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcf0341e03a5d95abbe8b1a8ce69379fef8251b7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "68989054"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com zscaler

Neste tutorial, você vai aprender a integrar Zscaler com o Azure Ative Directory (Azure AD). Quando integrar o Zscaler com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a Zscaler.
* Ative que os seus utilizadores sejam automaticamente inscritos no Zscaler com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada por um único sinal (SSO) do Zscaler.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Zscaler suporta **SP** iniciado SSO
* Zscaler suporta o fornecimento de utilizadores **Just In Time**

## <a name="adding-zscaler-from-the-gallery"></a>Adicionando Zscaler da galeria

Para configurar a integração do Zscaler em Azure AD, precisa adicionar Zscaler da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** **digite Zscaler** na caixa de pesquisa.
1. **Selecione Zscaler** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler"></a>Configure e teste Azure AD único sign-on para Zscaler

Configure e teste Azure AD SSO com Zscaler utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Zscaler.

Para configurar e testar o Azure AD SSO com o Zscaler, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure zscaler SSO](#configure-zscaler-sso)** - para configurar as definições de início de sessão simples no lado da aplicação.
    1. **[Crie o utilizador de teste Zscaler](#create-zscaler-test-user)** - para ter uma contraparte de B.Simon em Zscaler que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Zscaler,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<companyname>.zscaler.net`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte a equipa de suporte ao [Cliente Zscaler](https://www.zscaler.com/company/contact) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. A sua aplicação Zscaler espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique no ícone **Editar** para abrir o diálogo **de Atributos do Utilizador.**

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação Zscaler espera que poucos atributos sejam retransmitidos na resposta SAML. Na secção **Reivindicações** do Utilizador no diálogo **de Atributos** do Utilizador, execute os seguintes passos para adicionar atributo token SAML, conforme indicado no quadro abaixo:

    | Nome | Atributo fonte |
    | ---------| ------------ |
    | membroOf     | user.atribuídos |

    a. Clique **Em adicionar nova alegação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    b. Na caixa de texto **Name,** digite o nome do atributo mostrado para essa linha.

    c. Deixe o espaço de **nome em** branco.

    d. Selecione Origem como **Atributo**.

    e. Na lista de **atributos Fonte,** digite o valor do atributo mostrado para essa linha.

    f. Clique em **Guardar**.

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) para saber como configurar o papel em Azure AD

1. Na configuração de um único sinal com página **SAML,** na secção certificado de **assinatura SAML,** encontre **certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **'Configurar Zscaler',** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao Zscaler.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Zscaler**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Zscaler**.

    ![O link Zscaler na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. No diálogo **Utilizadores e grupos,** selecione o utilizador como **Britta Simon** da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

    ![image](./media/zscaler-tutorial/tutorial_zscaler_users.png)

6. A partir do diálogo **Select Role,** escolha a função de utilizador apropriada na lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

    ![image](./media/zscaler-tutorial/tutorial_zscaler_roles.png)

7. No diálogo **adicionar atribuição,** selecione o botão **Atribuir.**

    ![image](./media/zscaler-tutorial/tutorial_zscaler_assign.png)

## <a name="configure-zscaler-sso"></a>Configure Zscaler SSO

1. Para automatizar a configuração dentro do Zscaler, é necessário instalar a extensão de **'Sign-in' de Aplicações Seguras,** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar Zscaler** irá direcioná-lo para a aplicação Zscaler. A partir daí, forneça as credenciais de administração para assinar em Zscaler. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração SSO](common/setup-sso.png)

1. Se quiser configurar o Zscaler manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Zscaler como administrador e execute os seguintes passos:

1. Vá à **Administração > Autenticação > Definições** de Autenticação e execute os seguintes passos:

    ![Administration](./media/zscaler-tutorial/ic800206.png "Administração")

    a. No tipo de autenticação, escolha **SAML**.

    b. Clique **em Configurar SAML**.

1. Na janela **Edit SAML,** execute os seguintes passos: e clique em Guardar.  

    ![Gerir utilizadores & autenticação](./media/zscaler-tutorial/ic800208.png "Gerir utilizadores & autenticação")
    
    a. Na caixa de texto URL do **Portal SAML,** colá o URL de **login** que copiou do portal Azure.

    b. Na caixa de texto 'Atribuição de **Nome de login',** introduza **o NameID**.

    c. Clique no **Upload**, para fazer upload do certificado de assinatura Azure SAML que descarregou do portal Azure no **Certificado SSL Público**.

    d. Alternar o **fornecimento automático Enable SAML**.

    e. Na caixa de texto do nome do **utilizador,** introduza o nome do **ecrã** se pretender ativar o fornecimento automático de SAML para atributos displayName.

    f. Na caixa de texto de atribuição de nome de **grupo,** introduza **o membroSe** se pretender ativar o fornecimento automático sAML para membros De atributos.

    g. No **departamento** de atribuição de nome de **departamento,** se pretender ativar o fornecimento automático de SAML para atributos do departamento.

    h. Clique em **Guardar**.

1. Na página de diálogo de autenticação do **utilizador Configurar,** execute os seguintes passos:

    ![Administração](./media/zscaler-tutorial/ic800207.png)

    a. Paire sobre o menu **de ativação** perto do canto inferior esquerdo.

    b. Clique em **Ativar**.

## <a name="configuring-proxy-settings"></a>Definir configurações de proxy

### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para configurar as definições de procuração no Internet Explorer

1. Inicie **o Internet Explorer**.

1. Selecione **opções** de Internet a partir do menu **Ferramentas** para abrir o diálogo internet **Options.**

    ![Opções de Internet](./media/zscaler-tutorial/ic769492.png "Opções de Internet")

1. Clique no separador **Ligações.**
  
    ![Ligações](./media/zscaler-tutorial/ic769493.png "Ligações")

1. Clique nas **definições lan** para abrir o diálogo lan **Definições.**

1. Na secção proxy do servidor, execute os seguintes passos:   

    ![Servidor proxy](./media/zscaler-tutorial/ic769494.png "Servidor proxy")

    a. Selecione **Utilize um servidor proxy para o seu LAN**.

    b. Na caixa de texto 'Endereço', escreva **gateway.zscaler.net**.

    c. Na caixa de texto portuário, tipo **80**.

    d. Selecione **servidor de procuração bypass para endereços locais**.

    e. Clique **em OK** para fechar o diálogo de **definições da Rede local (LAN).**

1. Clique **em OK** para fechar o diálogo das Opções de **Internet.**

### <a name="create-zscaler-test-user"></a>Criar o utilizador de teste Zscaler

Nesta secção, um utilizador chamado Britta Simon é criado em Zscaler. O Zscaler suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Zscaler, um novo é criado após a autenticação.

> [!Note]
> Se precisar de criar um utilizador manualmente, contacte a equipa de [suporte zscaler](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Zscaler no Painel de Acesso, deve ser automaticamente inscrito no Zscaler para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Zscaler com Azure AD](https://aad.portal.azure.com/)
