---
title: 'Tutorial: Integração do Diretório Ativo Azure com Andromeda | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Andromeda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 678bb46efb19f3451566306ddbbe372c631c717e
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736042"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Tutorial: Integração do Diretório Ativo Azure com Andromeda

Neste tutorial, aprende-se a integrar Andromeda com o Azure Ative Directory (Azure AD).
A integração de Andromeda com a Ad Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso a Andromeda.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos em Andromeda (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Andromeda, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura ativada por andromeda

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Andromeda apoia **SP e IDP** iniciaM SSO
* Andromeda suporta **provisão** de utilizadores just in time

## <a name="adding-andromeda-from-the-gallery"></a>Adicionar Andromeda da galeria

Para configurar a integração de Andromeda em Azure AD, você precisa adicionar Andromeda da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **digite Andromeda** na caixa de pesquisa.
1. Selecione **Andromeda** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-andromeda"></a>Configure e teste Azure AD SSO para Andromeda

Configure e teste Azure AD SSO com Andromeda usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Andromeda.

Para configurar e testar a Azure AD SSO com a Andromeda, execute os seguintes passos:


1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
2. **[Configure a Andromeda SSO](#configure-andromeda-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create Andromeda test user](#create-andromeda-test-user)** - para ter uma contraparte de Britta Simon em Andromeda que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Andromeda,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<tenantURL>.ngcxpress.com/`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![Screenshot mostra Definir U R Ls adicionais onde pode introduzir um sinal em U R L.](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`

    > [!NOTE]
    > Estes valores não são reais. Irá atualizar o valor com o identificador real, URL de resposta e URL Sign-On, que é explicado mais tarde no tutorial.

6. A aplicação Andromeda espera as afirmações DOL num formato específico. Configure os seguintes pedidos para este pedido. Pode gerir os valores destes atributos a partir da secção **Atributos** do Utilizador na página de integração de aplicações. Na **configuração single Sign-On com** a página SAML, clique no botão **Editar** para abrir o diálogo **de Atributos do Utilizador.**

    ![A screenshot mostra atributos do utilizador como dado nome utilizador.givenname e emailaddress user.mail.](common/edit-attribute.png)

    > [!Important]
    > Limpe as definições do NameSpace enquanto as configura.

7. Na secção **'Reclamações** de Utilizador' no diálogo **'Atributos do Utilizador',** edite as alegações utilizando o **ícone Editar** ou adicione as alegações utilizando **adicionar uma nova alegação** para configurar o atributo de ficha SAML como mostrado na imagem acima e executar os seguintes passos: 

    | Name | Atributo de origem|
    | ------ | -----------|
    | papel        | Papel específico da aplicação |
    | tipo        | Tipo de Aplicação |
    | empresa       | CompanyName |

    > [!NOTE]
    > A Andromeda espera funções para os utilizadores destacados para a aplicação. Por favor, crie estas funções em Azure AD para que os utilizadores possam ser atribuídos as funções apropriadas. Para entender como configurar papéis em Azure AD, consulte [aqui.](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)

    a. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![A Screenshot mostra as reclamações do Utilizador com opções para adicionar uma nova reclamação e guardar.](common/new-save-attribute.png)

    ![Screenshot mostra Gerir as alegações do utilizador onde pode introduzir valores descritos I neste passo.](common/new-attribute-details.png)

    b. Na caixa de texto **'Nome',** digite o nome do atributo indicado para esta linha.

    c. Deixe o **Espaço Namespace** em branco.

    d. Selecione Fonte como **Atributo**.

    e. A partir da lista **de atributos Source,** digite o valor de atributo mostrado para esta linha.

    f. Clique **em Ok**

    exemplo, Clique em **Guardar**.

8. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

9. Na secção **Configuração andromeda,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Andromeda.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Andromeda**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se tiver configurado as funções como explicado no acima, pode selecioná-lo a partir do Dropdown de **função Select.**
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="configure-andromeda-sso"></a>Configurar Andromeda SSO

1. Inscreva-se no site da empresa Andromeda como administrador.

2. No topo da barra de menus clique em **Administração** e navegue para a **Administração.**

    ![Administração Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

3. No lado esquerdo da barra de ferramentas na secção **Interfaces,** clique na **configuração SAML**.

    ![Andromeda saml](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

4. Na página da secção **de configuração SAML,** execute os seguintes passos:

    ![Andromeda config](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Verifique **ativar sSO com SAML**.

    b. Na secção **de Informação de Andromeda,** copie o valor **de identidade SP** e cole-o na caixa de texto **identifier** da secção **de Configuração Básica SAML.**

    c. Copie o valor **url do consumidor** e cole-o na caixa de texto URL de **resposta** da secção **de configuração básica do SAML.**

    d. Copie o valor URL do **logon** e **cole-o na** caixa de texto URL de url de assinatura da secção **de configuração básica do SAML.**

    e. Na secção **SAML Identity Provider,** digite o seu Nome IDP.

    f. Na caixa de texto **Single Sign On End Point,** cole o valor do URL de **login** que, copiou a partir do portal Azure.

    exemplo, Abra o **certificado codificado Base64** descarregado do portal Azure em bloco de notas, cole-o na caixa de texto **do Certificado X 509.**
    
    h. Mapear os seguintes atributos com o respetivo valor para facilitar o login SSO a partir do Azure AD. O atributo **ID do utilizador** é necessário para iniciar sessão. Para provisionamento, são necessários **emails,** **empresas,** **UserType** e **Role.** Nesta secção, definimos atributos de mapeamento (nome e valores) que se relacionam com os definidos dentro do portal Azure

    ![Attbmap andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Clique em **Guardar**.


### <a name="create-andromeda-test-user"></a>Criar utilizador de teste Andromeda

Nesta secção, um utilizador chamado Britta Simon é criado em Andromeda. A Andromeda suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Andromeda, um novo é criado após a autenticação. Se precisar de criar um utilizador manualmente, contacte a equipa de [suporte do Cliente Andromeda.](https://www.ngcsoftware.com/support/)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Andromeda Assinar em URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de assinatura de Andromeda e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Andromeda para o qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo andrómeo nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Andromeda para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Próximos passos

Uma vez configurado Andromeda, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
