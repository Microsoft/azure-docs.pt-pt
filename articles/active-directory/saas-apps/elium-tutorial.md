---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Elium [ Diretório Ativo) integração com Elium [ SSO) integração com Elium [ Diretório Ativo) integração com Elium [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Elium.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0900f730c287586725722f0b8baaeb0c22f850c2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "72791230"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-elium"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com Elium

Neste tutorial, você vai aprender a integrar Elium com o Azure Ative Directory (Azure AD). Quando integrar o Elium com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Elium.
* Permita que os seus utilizadores sejam automaticamente inscritos no Elium com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada por elium single sign-on (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Elium apoia **SP e IDP** iniciado SSO
* Elium suporta o fornecimento de utilizadores **Just In Time**

## <a name="adding-elium-from-the-gallery"></a>Adicionando Elium da galeria

Para configurar a integração do Elium em Azure AD, você precisa adicionar Elium da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite **Elium** na caixa de pesquisa.
1. Selecione **Elium** no painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-elium"></a>Configure e teste Azure AD único sign-on para Elium

Configure e teste Azure AD SSO com Elium utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Elium.

Para configurar e testar o Azure AD SSO com o Elium, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Elium SSO](#configure-elium-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie o utilizador](#create-elium-test-user)** do teste Elium - para ter uma contrapartida de B.Simon em Elium que esteja ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Elium,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<platform-domain>.elium.com/login/saml2/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<platform-domain>.elium.com/login/saml2/acs`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE]
    > Estes valores não são reais. Obterá estes valores a partir do ficheiro `https://<platform-domain>.elium.com/login/saml2/metadata`de **metadados SP** descarregado em , o que é explicado mais tarde neste tutorial.

1. A aplicação Elium espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos saml. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Elium espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome | Atributo fonte|
    | ---------------| ----------------|
    | e-mail   |utilizador.mail |
    | first_name| user.givenname |
    | last_name| utilizador.sobrenome|
    | job_title| user.jobtitle|
    | empresa| user.companyname|

    > [!NOTE]
    > Estas são as alegações predefinidas. **Só é necessária uma reclamação de e-mail**. Para o fornecimento de JIT também apenas a reclamação de e-mail é obrigatória. Outras reclamações personalizadas podem variar de uma plataforma de cliente para outra plataforma de cliente.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção Configurar o **Elium,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que B.Simon use o único sign-on Azure, concedendo acesso ao Elium.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Elium**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-elium-sso"></a>Configure Elium SSO

1. Para automatizar a configuração dentro do Elium, é necessário instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar Elium** irá direcioná-lo para a aplicação Elium. A partir daí, forneça as credenciais de administração para assinar no Elium. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração de configuração](common/setup-sso.png)

1. Se quiser configurar o Elium manualmente, abra uma nova janela do navegador web e inscreva-se no site da sua empresa Elium como administrador e execute os seguintes passos:

1. Clique no **perfil do Utilizador** a partir do canto superior direito e, em seguida, selecione **Administration**.

    ![Configurar um único sinal](./media/elium-tutorial/user1.png)

1. Selecione o separador **Segurança**.

    ![Configurar um único sinal](./media/elium-tutorial/user2.png)

1. Desloque-se até à secção **de inscrição única (SSO)** e execute os seguintes passos:

    ![Configurar um único sinal](./media/elium-tutorial/user3.png)

    a. Copie o valor de Verificar se a **autenticação SAML2 funciona para a sua conta** e **cole-a** na caixa de texto url signon na secção **basic SAML Configuração** no portal Azure.

    > [!NOTE]
    > Depois de configurar o SSO, pode sempre aceder à página de login remoto predefinido no seguinte URL:`https://<platform_domain>/login/regular/login` 

    b. **Selecione Enable SAML2 federation** checkbox.

    c. Selecione **caixa de verificação jIT Provisioning.**

    d. Abra os **Metadados SP** clicando no botão **Descarregar.**

    e. Procure a **entidadeID** no ficheiro **SP Metadata,** copie o valor **ididato** da entidade e cole-o na caixa de texto **do Identificador** na secção **de Configuração Básica SAML** no portal Azure. 

    ![Configurar um único sinal](./media/elium-tutorial/user4.png)

    f. Procure o **Serviço de Crédito Consumidor** no ficheiro **SP Metadata,** copie o valor de **localização** e cole-o na caixa de texto URL de **resposta** na secção **basic SAML Configuração** no portal Azure.

    ![Configurar um único sinal](./media/elium-tutorial/user5.png)

    g. Abra o ficheiro de metadados descarregado do portal Azure para o bloco de notas, copie o conteúdo e cole-o na caixa de texto **idP Metadata.**

    h. Clique em **Guardar**.

### <a name="create-elium-test-user"></a>Criar o utilizador do teste Elium

Nesta secção, um utilizador chamado B.Simon é criado em Elium. Elium apoia **o fornecimento just-in-time**, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Elium, um novo é criado quando se tenta aceder ao Elium.

> [!Note]
> Se precisar de criar um utilizador manualmente, contacte a equipa de [suporte da Elium.](mailto:support@elium.com)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Elium no Painel de Acesso, deve ser automaticamente inscrito no Elium para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Elium com Azure AD](https://aad.portal.azure.com/)