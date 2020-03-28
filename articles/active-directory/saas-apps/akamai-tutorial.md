---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com a Akamai [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Akamai.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1b7e0d7a-e78f-43a5-af93-b626186e2376
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 042dd242285081001ca48c9f17e4d42c2294c0ff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74979596"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com a Akamai

Neste tutorial, você vai aprender a integrar a Akamai com o Azure Ative Directory (Azure AD). Quando integrar a Akamai com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Akamai.
* Permita que os seus utilizadores sejam automaticamente inscritos na Akamai com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura de um único sinal (SSO) da Akamai.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

- Slack suporta IDP iniciado SSO

## <a name="adding-akamai-from-the-gallery"></a>Adicionando Akamai da galeria

Para configurar a integração da Akamai em Azure AD, você precisa adicionar Akamai da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite **Akamai** na caixa de pesquisa.
1. Selecione **Akamai** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-akamai"></a>Configure e teste Azure AD único sign-on para Akamai

Configure e teste Azure AD SSO com Akamai utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Akamai.

Para configurar e testar o Azure AD SSO com a Akamai, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o Akamai SSO](#configure-akamai-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie o utilizador](#create-akamai-test-user)** de teste akamai - para ter uma contraparte de B.Simon na Akamai que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **da Akamai,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<Yourapp>.login.go.akamai-access.com/sp/response`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https:// <Yourapp>.login.go.akamai-access.com/sp/response`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte a equipa de suporte ao [Cliente da Akamai](https://www.akamai.com/us/en/contact-us/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção Configurar a **Akamai,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso à Akamai.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Akamai**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-akamai-sso"></a>Configure Akamai SSO

### <a name="setting-up-idp"></a>Configuração do IDP

1. Assine a consola de acesso à **aplicação da empresa Akamai.**
1. Na **consola Akamai EAA**, Selecione**Fornecedores**de **Identidade de Identidade** > .

    ![Configurar akamai](./media/header-akamai-tutorial/configure01.png)

1. Clique em **Adicionar Fornecedor de Identidade**.

    ![Configurar akamai](./media/header-akamai-tutorial/configure02.png)

    a. Especifique o **Nome Único**.
    
    b. Escolha **O SAML** de terceiros e clique em Criar Fornecedor de **Identidade e Configurar**.

### <a name="general-settings"></a>Definições Gerais

1. **Identity Intercept** - Especifique o nome do (URL base SP - será utilizado para configuração de AD Azure)

    > [!NOTE]
    > Pode optar por ter o seu próprio domínio personalizado (exigirá uma entrada DNS e um Certificado). Neste exemplo, vamos usar o Domínio Akamai.

1. **Akamai Cloud Zone** - Selecione a zona de nuvem apropriada.
1. **Validação do Certificado** - Verifique documentação akamai (opcional)

    ![Configurar akamai](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>Configuração de autenticação

1. URL – Especifique o URL da mesma forma que a sua interceção de identidade (é aqui que os utilizadores são redirecionados após a autenticação).
2. URL de início de início : Atualize o URL de logout.
3. Sinal De Pedido SAML: padrão sem verificação.
4. Para o ficheiro IDP Metadata, adicione a aplicação na consola Azure AD.

    ![Configurar akamai](./media/header-akamai-tutorial/configure04.png)

### <a name="header-based-authentication"></a>Autenticação baseada em cabeçalho

Autenticação baseada em cabeçalho akamai

1. Escolha o formulário **CUSTOM HTTP** do Assistente de Aplicações Adicionar.

    ![Configurar akamai](./media/header-akamai-tutorial/configure05.png)

    ![Configurar akamai](./media/header-akamai-tutorial/configure06.png)

    ![Configurar akamai](./media/header-akamai-tutorial/configure07.png)

    ![Configurar akamai](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>Autenticação

![Configurar akamai](./media/header-akamai-tutorial/configure09.png)

![Configurar akamai](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>Serviços

1. Clique em Guardar e ir para autenticação.

![Configurar akamai](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>Definições Avançadas

1. No âmbito dos **cabeçalhos HTTP**do Cliente, especifique o **CustomerHeader** e o **Atributo SAML**.

    ![Configurar akamai](./media/header-akamai-tutorial/configure12.png)

1. Clique em Guardar e vá para o botão **de implantação.**

    ![Configurar akamai](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>Implementar a Aplicação

1. Clique no botão implementação de **aplicação.**

    ![Configurar akamai](./media/header-akamai-tutorial/configure14.png)

1. Verifique se a Aplicação foi implementada com sucesso.

    ![Configurar akamai](./media/header-akamai-tutorial/configure15.png)

### <a name="kerberos-authentication"></a>Autenticação Kerberos

#### <a name="remote-desktop"></a>Ambiente de Trabalho Remoto

1. Escolha **RDP** do Assistente de Aplicações ADD.

    ![Configurar akamai](./media/header-akamai-tutorial/configure16.png)

    ![Configurar akamai](./media/header-akamai-tutorial/configure17.png)

    ![Configurar akamai](./media/header-akamai-tutorial/configure18.png)

1. Especifique o Conector que estará a servir isto.

    ![Configurar akamai](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>Autenticação

Clique em **Guardar e ir aos Serviços**.

![Configurar akamai](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>Serviços

Clique em **Guardar e vá para Definições Avançadas**.

![Configurar akamai](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>Definições Avançadas

Clique em **Guardar e ir para a Implementação**.

![Configurar akamai](./media/header-akamai-tutorial/configure22.png)

![Configurar akamai](./media/header-akamai-tutorial/configure23.png)

![Configurar akamai](./media/header-akamai-tutorial/configure24.png)

### <a name="deployment"></a>Implementação

#### <a name="ssh"></a>SSH

1. Ir adicionar aplicações, escolher **SSH**.

    ![Configurar akamai](./media/header-akamai-tutorial/configure25.png)

    ![Configurar akamai](./media/header-akamai-tutorial/configure26.png)

1. Configurar identidade de aplicação.

    ![Configurar akamai](./media/header-akamai-tutorial/configure27.png)

    a. Especificar Nome /Descrição.

    b. Especifique o servidor de aplicação IP/FQDN e a porta para SSH.

    c. Especifique o nome de utilizador /palavra-passe do SSH *Verifique a Akamai EAA.

    d. Especifique o nome do hospedeiro externo.

    e. Especifique a localização do conector e escolha o conector.

#### <a name="authentication"></a>Autenticação

Clique em **Guardar e ir aos Serviços**.

![Configurar akamai](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>Serviços

Clique em **Guardar e vá para Definições Avançadas**.

![Configurar akamai](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>Definições Avançadas

Clique em Guardar e para ir Implantação

![Configurar akamai](./media/header-akamai-tutorial/configure30.png)

![Configurar akamai](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>Implementação

Clique na **aplicação Implementar**.

![Configurar akamai](./media/header-akamai-tutorial/configure32.png)

### <a name="kerberos-applications"></a>Aplicações Kerberos

#### <a name="adding-directory"></a>Diretório de Adição

![Configurar akamai](./media/header-akamai-tutorial/configure33.png)

![Configurar akamai](./media/header-akamai-tutorial/configure34.png)

![Configurar akamai](./media/header-akamai-tutorial/configure35.png)

![Configurar akamai](./media/header-akamai-tutorial/configure36.png)

### <a name="create-akamai-test-user"></a>Criar o utilizador de teste akamai

Nesta secção, cria-se um utilizador chamado B.Simon na Akamai. Trabalhe com a equipa de suporte ao [Cliente Akamai](https://www.akamai.com/us/en/contact-us/) para adicionar os utilizadores na plataforma Akamai. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal. 

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Akamai no Painel de Acesso, deve ser automaticamente inscrito no Akamai para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente akamai com Azure AD](https://aad.portal.azure.com/)
