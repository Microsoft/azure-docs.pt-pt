---
title: 'Tutorial: Integração de Diretório Ativo Azure com consola de administrador mimecast [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Mimecast Admin Console.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 276a1acb5735e3490f331000799d57c329e7fca0
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848561"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mimecast-admin-console"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com a Consola de Admin mimecast

Neste tutorial, aprenderás a integrar a Consola de Admin mimecast com o Azure Ative Directory (Azure AD). Quando integrar a Consola de Admin Mimecast com a AD Azure, pode:

* Controle em Azure AD que tem acesso à Consola de Admin mimecast.
* Ative que os seus utilizadores sejam automaticamente inscritos na Consola de Admin Mimecast com as suas contas AD Azure.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada pela Consola de Administrador Mimecast (SSO) permitiu a subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Consola mimecast Admin suporta **SP e IDP** iniciadoS SSO
* Assim que configurar a Consola de Administrador Mimecast, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>Adicionar consola de administrador mimecast da galeria

Para configurar a integração da Consola de Admin Mimecast em Azure AD, você precisa adicionar a Consola de Admin Mimecast da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **de galeria,** **digite a Consola de Administrador Mimecast** na caixa de pesquisa.
1. Selecione **A Consola de Admin mimecast** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-mimecast-admin-console"></a>Configure e teste Azure AD único sinal para consola de admina Mimecast

Configure e teste Azure AD SSO com consola de admina Mimecast utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Consola de Admin a mimecast.

Para configurar e testar o Azure AD SSO com a Consola de Admin mimecast, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Mimecast Admin Console SSO](#configure-mimecast-admin-console-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
    1. Crie o utilizador de teste da **[Consola de Admin Mimecast](#create-mimecast-admin-console-test-user)** - para ter uma contraparte de B.Simon na Consola de Admin Mimecast que está ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações da **Mimecast Admin Console,** encontre a secção **Gerir** e selecione **um único sinal .**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado idp, execute os seguintes passos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:

    | Região  |  Valor | 
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Estados Unidos da América   | `https://us-api.mimecast.com/sso/<accountcode>`|
    | África do Sul    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Austrália       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Offshore        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    > [!NOTE]
    > Encontrará o `accountcode` valor na consola de administrador **Account**mimecast no código de  >  **conta Definições**de  >  **Conta**. Anexar o `accountcode` identificador.

    b. Na caixa de texto url de **resposta,** escreva um URL: 

    | Região  |  Valor | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Estados Unidos da América   | `https://us-api.mimecast.com/login/saml`|
    | África do Sul    | `https://za-api.mimecast.com/login/saml`|
    | Austrália       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

1. Se desejar configurar a aplicação no modo iniciado **sp:**

    Na caixa de texto **salmôl Sign-on URL,** escreva um URL: 

    | Região  |  Valor | 
    | --------------- | --------------- | 
    | Europa          | `https://login-eu.mimecast.com/administration/app/#/administration-dashboard`|
    | Estados Unidos da América   | `https://login-us.mimecast.com/administration/app/#/administration-dashboard`|
    | África do Sul    | `https://login-za.mimecast.com/administration/app/#/administration-dashboard`|
    | Austrália       | `https://login-au.mimecast.com/administration/app/#/administration-dashboard`|
    | Offshore        | `https://login-jer.mimecast.com/administration/app/#/administration-dashboard`|

1. Clique em **Guardar**.

1. No **set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de **nome do Utilizador,** introduza o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso à Consola de Admin mimecast.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Mimecast Admin Console**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-mimecast-admin-console-sso"></a>Configure Mimecast Admin Console SSO

1. Numa janela de navegador web diferente, inscreva-se na Consola de Administração Mimecast.

1. Navegar para aplicações de serviços de **administração.**  >  **Services**  >  **Applications**

    ![Configuração da consola de admina Mimecast](./media/mimecast-admin-console-tutorial/services.png)

1. Clique no separador **Perfis de Autenticação.**
    
    ![Configuração da consola de admina Mimecast](./media/mimecast-admin-console-tutorial/authentication-profiles.png)

1. Clique no separador **New Authentication Profile.**

    ![Configuração da consola de admina Mimecast](./media/mimecast-admin-console-tutorial/new-authenticatio-profile.png)

1. Forneça uma descrição válida na caixa de texto **descrição** e selecione **Impor a autenticação SAML para caixa** de verificação de consolade administração.

    ![Configuração da consola de admina Mimecast](./media/mimecast-admin-console-tutorial/selecting-admin-consle.png)

1. Na página **SAML Configuração para Consola de Administração,** execute os seguintes passos:

    ![Configuração da consola de admina Mimecast](./media/mimecast-admin-console-tutorial/sso-settings.png)

    a. Para **Fornecedor,** selecione **Azure Ative Directory** a partir do Dropdown.

    b. Na caixa de texto URL de **Metadados,** colá o valor URL de **Metadados da Federação de Aplicações,** que copiou do portal Azure.

    c. Clique **em Importar**. Após a importação do URL de Metadados, os campos serão povoados automaticamente, não precisade realizar qualquer ação nestes campos.

    d. Certifique-se de que desvenda **o contexto protegido da palavra-passe** e utilize caixas de verificação integradas de contexto de **autenticação.**

    e. Clique em **Guardar**.

### <a name="create-mimecast-admin-console-test-user"></a>Criar o utilizador de teste de consola de admina Mimecast

1. Numa janela de navegador web diferente, inscreva-se na Consola de Administração Mimecast.

1. Navegar **Administration**para  >  **Diretórios**  >  **De**Administração.

    ![Configuração da consola de admina Mimecast](./media/mimecast-admin-console-tutorial/internal-directories.png)

1. Selecione no seu domínio, se o domínio for mencionado abaixo, caso contrário, crie um novo domínio clicando no **Novo Domínio**.

    ![Configuração da consola de admina Mimecast](./media/mimecast-admin-console-tutorial/domain-name.png)

1. Clique no separador **New Address.**

    ![Configuração da consola de admina Mimecast](./media/mimecast-admin-console-tutorial/new-address.png)

1. Forneça as informações necessárias ao utilizador na seguinte página:

    ![Configuração da consola de admina Mimecast](./media/mimecast-admin-console-tutorial/user-information.png)

    a. Na caixa de texto endereço de **e-mail,** introduza o endereço de e-mail do utilizador como `B.Simon@yourdomainname.com` .

    b. Na caixa de texto **GLobal Name,** introduza o **nome completo** do utilizador.

    c. Nas caixas de texto **Password** e **Confirme** as caixas de texto, introduza a palavra-passe do utilizador.

    d. Selecione **'Alteração de Força' na** caixa de verificação de login.

    e. Clique em **Guardar**.

    f. Para atribuir funções ao utilizador, clique em **Role Edit** e atribua a função necessária ao utilizador de acordo com o requisito da sua organização.

    ![Configuração da consola de admina Mimecast](./media/mimecast-admin-console-tutorial/assign-role.png)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo mimecast Admin Console no Painel de Acesso, deve ser automaticamente inscrito na Consola de Administrador Mimecast para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a consola de administrador mimecast com a AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a Consola de Admin Mimecast com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
