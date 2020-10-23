---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Claromentis Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Claromentis.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: jeedes
ms.openlocfilehash: 92b068ee9b8aaf4c462002354bbb6490f4888a80
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455907"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-claromentis"></a>Tutorial: Azure Ative Directy integração única (SSO) com Claromentis

Neste tutorial, você vai aprender a integrar Claromentis com Azure Ative Directory (Azure AD). Quando integrar Claromentis com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Claromentis.
* Permita que os seus utilizadores sejam automaticamente inscritos no Claromentis com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Claromentis uma única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Claromentis apoia **SP e IDP** iniciado SSO
* Claromentis suporta **o fornecimento de utilizadores Just In Time**

## <a name="adding-claromentis-from-the-gallery"></a>Adicionar Claromentis da galeria

Para configurar a integração de Claromentis no AD Azure, é necessário adicionar Claromentis da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **escreva Claromentis** na caixa de pesquisa.
1. **Selecione Claromentis** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-claromentis"></a>Configurar e testar Azure AD único sign-on para Claromentis

Configure e teste Azure AD SSO com Claromentis usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Claromentis.

Para configurar e testar a Azure AD SSO com Claromentis, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Claromentis SSO](#configure-claromentis-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Claromentis test user](#create-claromentis-test-user)** - ter uma contraparte de B.Simon em Claromentis que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Claromentis,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier,** insira o valor do identificador de acordo com o seu requisito de organização.

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<customer_site_url>/custom/loginhandler/simplesaml/www/module.php/saml/sp/saml2-acs.php/claromentis`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:

    ```https
    https://<customer_site_url>/login
    https://<customer_site_url>/login?no_auto=0
    ```

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição, que é explicado mais tarde no turorial.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar Claromentis,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Claromentis.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, **selecione Claromentis**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-claromentis-sso"></a>Configurar Claromentis SSO

1. Numa janela de navegador diferente, inscreva-se no website Claromentis como administrador.

1. Clique no ícone de **aplicações** e selecione **Administrador.**

    ![A screenshot mostra o site Claromentis com a Administração selecionada.](./media/claromentis-tutorial/config1.png)

1. Selecione **o separador 'Personal's's's's Handler de Login.**

    ![A screenshot mostra a página de Administração com o Manipulador de Login personalizado selecionado.](./media/claromentis-tutorial/config2.png)

1. Selecione **SAML Config**.

    ![A screenshot mostra a página de configuração para SAML.](./media/claromentis-tutorial/config3.png)

1. No separador **SAML Config,** desloque-se até à secção **Config** e execute os seguintes passos:

    ![A screenshot mostra a secção Config da página onde pode introduzir as informações descritas neste passo.](./media/claromentis-tutorial/config4.png)

    a. Na caixa de texto **nome de contacto técnico,** insira o nome da pessoa de contacto técnico.

    b. Na caixa de sms **do Email de Contacto Técnico,** insira o endereço de e-mail da pessoa de contacto técnico.

    c. Forneça a palavra-passe na caixa de texto **da palavra-passe Auth Admin.**

1. Percorra até **Auth Sources** e execute os seguintes passos:

    ![A screenshot mostra a secção Auth Sources onde pode introduzir as informações descritas neste passo.](./media/claromentis-tutorial/config5.png)

    a. Na caixa de texto **IDP,** insira o valor do **identificador Azure AD,** que copiou a partir do portal Azure.

    b. Na caixa de texto iD da **Entidade,** insira o valor de ID da Entidade.

    c. Faça o upload do ficheiro **XML dos metadados da Federação,** que descarregou a partir do portal Azure.

    d. Clique em **Guardar**.

1. Irá agora notar que todos os URLs foram povoados dentro da secção **fornecedor de identidade** na secção **SAML Config.**

    ![A screenshot mostra a página do Fornecedor de Identidade povoada com U R Ls.](./media/claromentis-tutorial/config6.png)

    a. Copiar o valor **do Identificador (ID da Entidade),** cole este valor na caixa de texto **identifier** na secção **configuração de SAML básico** no portal Azure.

    b. Copiar **Resposta valor URL,** colar este valor na caixa de texto **URL de resposta** na secção **configuração BÁSICA SAML** no portal Azure.

    c. Sinal de cópia No valor **URL,** cole este valor na caixa de texto **URL de inscrição** na secção **de Configuração Básica SAML** no portal Azure.

### <a name="create-claromentis-test-user"></a>Criar utilizador de teste Claromentis

Nesta secção, um utilizador chamado B.Simon é criado em Claromentis. O claromentis suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Claromentis, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Claromentis no Painel de Acesso, deverá ser automaticamente inscrito no Claromentis para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente Claromentis com Azure AD](https://aad.portal.azure.com/)