---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Tableau Server [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d19a7cc8d81f9e6e913f147b24c5cce03ff82027
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76986738"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com o Tableau Server

Neste tutorial, você vai aprender a integrar o Tableau Server com o Azure Ative Directory (Azure AD). Quando integrar o Tableau Server com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Tableau Server.
* Ative que os seus utilizadores sejam automaticamente inscritos no Tableau Server com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada pelo Tableau Server (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Tableau Server suporta **SP** iniciado SSO
* Assim que configurar o Tableau Server, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-server-from-the-gallery"></a>Adicionar Tableau Server da galeria

Para configurar a integração do Tableau Server em Azure AD, precisa adicionar o Tableau Server da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **O Servidor Tableau** na caixa de pesquisa.
1. Selecione **Tableau Server** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tableau-server"></a>Configure e teste Azure AD único sign-on para Tableau Server

Configure e teste Azure AD SSO com Tableau Server utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Tableau Server.

Para configurar e testar o Azure AD SSO com o Tableau Server, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure tableau Server SSO](#configure-tableau-server-sso)** - para configurar as definições de sinal única no lado da aplicação.
    1. **[Create Tableau Server utilizador](#create-tableau-server-test-user)** de teste - para ter uma contraparte de B.Simon no Tableau Server que está ligado à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Tableau Server,** encontre a secção **Gerir** e selecione **um único sinal .**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://azure.<domain name>.link`

    b. Na caixa **de identificador,** digite um URL utilizando o seguinte padrão:`https://azure.<domain name>.link`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Os valores anteriores não são valores reais. Atualize os valores com o URL real e identificador a partir da página de configuração do Tableau Server, o que é explicado mais tarde no tutorial.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção Configurar o **Servidor tableau,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao Tableau Server.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Tableau Server**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-tableau-server-sso"></a>Configure Tableau Server SSO

1. Para configurar o SSO para a sua aplicação, precisa de se inscrever no seu inquilino do Tableau Server como administrador.

2. No separador **CONFIGURAÇÃO,** selecione identidade do **utilizador & Acesso**, e, em seguida, selecione o separador Método de **Autenticação.**

    ![Configurar um único sinal](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Na página **DE CONFIGURAÇÃO,** execute os seguintes passos:

    ![Configurar um único sinal](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. Para obter método de **autenticação,** selecione SAML.

    b. Selecione a caixa de verificação de **Autenticação Ativa sAML para o servidor**.

    c. URL de devolução do Servidor tableau — O URL a <http://tableau_server>que os utilizadores do Tableau Server vão aceder, tais como . A `http://localhost` utilização não é recomendada. Não é suportado utilizar um URL `http://tableau_server/`com um corte de rasto (por exemplo, ) Copy **Tableau Server devolver URL** e colá-lo para assinar caixa de texto **URL** na secção básica **de configuração SAML** no portal Azure

    d. ID da entidade SAML — O ID da entidade identifica exclusivamente a instalação do Seu Servidor Tableau para o IdP. Pode introduzir o url do Tableau Server novamente aqui, se quiser, mas não tem de ser o url do Tableau Server. Copie **o ID da entidade SAML** e cole-o na caixa de texto **identificador** na secção **de configuração Básica SAML** no portal Azure

    e. Clique no Ficheiro de **Metadados XML de download** e abra-o na aplicação do editor de texto. Localize o URL do Serviço de Consumidor de Afirmação com http post e índice 0 e copie o URL. Colá-lo agora na caixa de texto **URL de resposta** na secção de **configuração Básica SAML** no portal Azure

    f. Localize o ficheiro de Metadados da Federação descarregado do portal Azure e, em seguida, carregue-o no ficheiro de **metadados SAML IDp**.

    g. Introduza os nomes dos atributos que o IDP utiliza para manter os nomes dos utilizadores, nomes de visualização e endereços de e-mail.

    h. Clique em **Guardar**

    > [!NOTE]
    > O cliente tem de carregar qualquer certificado na configuração Tableau Server SAML SSO e será ignorado no fluxo SSO. Se precisar de ajuda para configurar o SAML no Tableau Server, consulte este artigo [Configure SAML](https://help.tableau.com/current/online/en-us/saml_config_azure_ad.htm).

### <a name="create-tableau-server-test-user"></a>Criar o utilizador de teste do Tableau Server

O objetivo desta secção é criar um utilizador chamado B.Simon no Tableau Server. Tem de fornecer todos os utilizadores do servidor Tableau.

Este nome de utilizador do utilizador deve corresponder ao valor configurado no atributo personalizado do Nome de **utilizador**Azure AD . Com o mapeamento correto, a integração deve funcionar Configurando o Single Sign-On azure AD.

> [!NOTE]
> Se necessitar de criar um utilizador manualmente, tem de contactar o administrador do Tableau Server na sua organização.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo tableau Server no Painel de Acesso, deve ser automaticamente inscrito no Tableau Server para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Tableau Server com AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)