---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Tableau Server Microsoft Docs'
description: Saiba como configurar um único sinal de insuflação entre o Azure Ative Directory e o Tableau Server.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2020
ms.author: jeedes
ms.openlocfilehash: a61321af538ee117828914a209523a1138e74c20
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92483459"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>Tutorial: Azure Ative Directory integração única (SSO) com Tableau Server

Neste tutorial, você vai aprender a integrar tableau Server com Azure Ative Directory (Azure AD). Quando integrar o Tableau Server com AD Azure, pode:

* Controlo em Azure AD que tem acesso ao Tableau Server.
* Ativar os seus utilizadores a serem automaticamente inscritos no Tableau Server com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por Tableau Server (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Tableau Server suporta **SSO** iniciado SP
* Assim que configurar o Tableau Server, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-server-from-the-gallery"></a>Adicionar Tableau Server da galeria

Para configurar a integração do Tableau Server no AZure AD, é necessário adicionar tableau Server da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** escreva **tableau Server** na caixa de pesquisa.
1. Selecione **Tableau Server** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tableau-server"></a>Configurar e testar a Azure AD um único sinal para tableau Server

Configure e teste Azure AD SSO com Tableau Server usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Tableau Server.

Para configurar e testar o Azure AD SSO com tableau Server, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Tableau Server SSO](#configure-tableau-server-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Tableau Server test user](#create-tableau-server-test-user)** - para ter uma contraparte de B.Simon no Tableau Server que está ligada à representação AD Ad do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Tableau Server,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://azure.<domain name>.link`

    b. Na caixa **identifier,** digite um URL utilizando o seguinte padrão: `https://azure.<domain name>.link`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Os valores anteriores não são valores reais. Atualize os valores com o URL real e o identificador da página de configuração do Tableau Server, que é explicado mais tarde no tutorial.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar tableau Server,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use o Azure single sign-on, concedendo acesso ao Tableau Server.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Tableau Server**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-tableau-server-sso"></a>Configurar tableau Server SSO

1. Para obter sSo configurado para a sua aplicação, você precisa iniciar sômedia no seu inquilino Tableau Server como administrador.

2. No **separador CONFIGURAÇÃO,** selecione **Identidade do Utilizador & Aceder**e, em seguida, selecione o separador Método de **Autenticação.**

    ![A screenshot mostra a autenticação selecionada a partir do Acesso & identidade do utilizador.](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Na página **CONFIGURAÇÃO,** execute os seguintes passos:

    ![A screenshot mostra a página de Configuração onde pode introduzir os valores descritos.](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. Para **método de autenticação,** selecione SAML.

    b. Selecione a caixa de verificação de **Ativação DA autenticação SAML para o servidor**.

    c. URL de retorno do Tableau Server - O URL a que os utilizadores do Tableau Server irão aceder, tais como `http://tableau_server` . Não `http://localhost` é aconselhável utilizar. A utilização de um URL com uma barra de fuga (por exemplo, `http://tableau_server/` ) não é suportada. Copiar **Tableau Server devolva URL** e cole-o para **iniciar uma** caixa de texto URL na secção **de configuração SAML básica** no portal Azure

    d. ID da entidade SAML — O ID da entidade identifica exclusivamente a instalação do Tableau Server no IdP. Pode introduzir o URL do seu Tableau Server novamente aqui, se quiser, mas não tem de ser o URL do tableau Server. Copie **o ID da entidade SAML** e cole-o na caixa de texto **identifier** na secção **de Configuração SÉDL Básica** no portal Azure

    e. Clique no **Download XML Metadata File** e abra-o na aplicação do editor de texto. Localize o URL do Serviço de Apoio ao Consumidor de Afirmação com Http Post e Index 0 e copie o URL. Agora cole-o para responder a caixa de texto **URL** na secção **de configuração BÁSICA SAML** no portal Azure

    f. Localize o seu ficheiro de metadados da Federação descarregado a partir do portal Azure e, em seguida, carreve-o no **ficheiro de metadados DOML**.

    exemplo, Introduza os nomes para os atributos que o IdP utiliza para conter os nomes de utilizador, nomes de exibição e endereços de e-mail.

    h. Clique em **Guardar**

    > [!NOTE]
    > O cliente tem de carregar um ficheiro de certificado x509 codificado por PEM com uma extensão .crt e um ficheiro chave RSA ou DSA que tenha a extensão .key, como ficheiro Chave de Certificado. Para mais informações sobre o ficheiro Certificado e o ficheiro Chave de Certificado, consulte [este](https://help.tableau.com/current/server/en-us/saml_requ.htm) documento. Se precisar de ajuda para configurar o SAML no Tableau Server, consulte este artigo [Configure Server Wide SAML](https://help.tableau.com/current/server/en-us/config_saml.htm).

### <a name="create-tableau-server-test-user"></a>Criar utilizador de teste do Tableau Server

O objetivo desta secção é criar um utilizador chamado B.Simon no Tableau Server. É necessário que todos os utilizadores do servidor Tableau.

Este nome de utilizador do utilizador deve corresponder ao valor que configurado no atributo personalizado AZURE AD do nome de **utilizador**. Com o mapeamento correto, a integração deve funcionar configurando o Azure AD Single Sign-On.

> [!NOTE]
> Se precisar de criar um utilizador manualmente, tem de contactar o administrador do Tableau Server na sua organização.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Tableau Server no Painel de Acesso, deverá ser automaticamente inscrito no Tableau Server para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente tableau server com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)