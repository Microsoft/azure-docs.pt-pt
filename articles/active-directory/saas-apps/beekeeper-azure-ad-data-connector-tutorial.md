---
title: 'Tutorial: Azure Ative Directory integração individual de inscrição (SSO) com o Apicultor Azure AD Data Connector  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Apicultor Azure AD Data Connector.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 26c8789a-1bfe-4371-94d5-febe34f0b0e4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7484d71c6032b97341537a0564d8d52b3996cc8e
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77050327"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beekeeper-azure-ad-data-connector"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com o Apicultor Azure AD Data Connector

Neste tutorial, você aprenderá a integrar o Apicultor Azure AD Data Connector com o Azure Ative Directory (Azure AD). Quando integrar o Apicultor Azure AD Data Connector com AD Azure, pode:

* Controlo em Azure AD que tem acesso ao Apicultor Azure AD Data Connector.
* Ative que os seus utilizadores sejam automaticamente inscritos no Aekeeper Azure AD Data Connector com as suas contas Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Aapicultor Azure AD Data Connector único (SSO) ativado subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Apicultor Azure AD Data Connector suporta **SP e IDP** iniciadoS SSO
* O conector de dados apicultor Azure AD suporta o fornecimento de utilizadores **justo no tempo**
* Assim que configurar o Apicultor Azure AD Data Connector, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controles de sessão se estendem do acesso condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-beekeeper-azure-ad-data-connector-from-the-gallery"></a>Adicionar conector de dados apícola Azure AD da galeria

Para configurar a integração do Apicultor Azure AD Data Connector em Azure AD, você precisa adicionar o Ad Data Connector abekeeper Azure da galeria à sua lista de aplicações geridas saaS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite o **Apicultor Azure AD Data Connector** na caixa de pesquisa.
1. Selecione **Apicultor Azure AD Data Connector** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-beekeeper-azure-ad-data-connector"></a>Configure e teste Azure AD único sinal para apicultor Azure AD Data Connector

Configure e teste Azure AD SSO com aapicultor Azure AD Data Connector utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no AD Data Connector abekeeper Azure.

Para configurar e testar o Azure AD SSO com o Apicultor Azure AD Data Connector, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o Apicultor Azure AD Data Connector SSO](#configure-beekeeper-azure-ad-data-connector-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
    * Crie o utilizador do teste de **[conector de dados AD AD AD AD](#create-beekeeper-azure-ad-data-connector-test-user)** - para ter uma contraparte de B.Simon no Apicultor Azure AD Data Connector que está ligado à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de dados do **AD AD AD AD,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** se tiver **ficheirode metadados do Prestador** de Serviços e pretender configurar no modo iniciado do **IDP,** execute os seguintes passos:

    a. Clique no **ficheiro de metadados de upload**.

    ![Carregar arquivo de metadados](common/upload-metadata.png)

    b. Clique no logotipo da **pasta** para selecionar o ficheiro de metadados e clicar em **Carregar**.

    ![escolher arquivo de metadados](common/browse-upload-metadata.png)

    c. Após o ficheiro de metadados ser carregado com sucesso, os valores de URL do **Identificador** e **da Resposta** ficam povoados automaticamente na secção de Configuração Básica do SAML.

    ![image](common/idp-intiated.png)

    > [!Note]
    > Se os valores de URL do **Identificador** e **da Resposta** não forem automaticamente polerados, preencha os valores manualmente de acordo com a sua exigência.

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão: `https://<your_company>.beekeeper.io/login`

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize esse valor com a URL de logon real. Contacte a equipa de suporte do Cliente do [AD AD AD](mailto:support@beekeeper.io) Para obter este valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. A aplicação apitadora Azure AD Data Connector espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Aekeeper Azure AD Data Connector espera que poucos atributos sejam passados na resposta SAML que são mostradas abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de origem|
    | ------------ | --------- |
    | FirstName | user.givenname |
    | LastName | User. sobrenome |
    | e-mail | user.mail |
    | o nome de utilizador | user.mainname |
    | Posição | user.jobtitle |

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na secção **de Conector de Dados AD AD do Ad Seta, copie** os URL(s) adequados com base na sua exigência.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **de nome do utilizador,** introduza o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, permitirá que a B.Simon utilize um único sign-on Azure, concedendo acesso ao Apicultor Azure AD Data Connector.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Aekeeper Azure AD Data Connector**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![O link Adicionar usuário](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-beekeeper-azure-ad-data-connector-sso"></a>Configure Beekeeper Azure AD Data Connector SSO

Para configurar um único sinal no lado do **AD Data Connector do AD AD,** é necessário enviar os metadados da **Federação descarregados XML** e URLs copiados apropriados do portal Azure para a equipa de suporte do [Adkeeper Azure AD Data Connector](mailto:support@beekeeper.io). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-beekeeper-azure-ad-data-connector-test-user"></a>Criar o utilizador do teste do conector de dados apicultor Azure AD

Nesta secção, um utilizador chamado Britta Simon é criado no Apicultor Azure AD Data Connector. O Apicultor Azure AD Data Connector suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para você nesta seção. Se um utilizador já não existir no Apicultor Azure AD Data Connector, um novo é criado após a autenticação.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no teléreo de dados AD AD apicultor no Painel de Acesso, deve ser automaticamente inscrito no Conector de Dados AD AD apicultor para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Conector de Dados AD AD Azure com AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
