---
title: 'Tutorial: integração do Azure Active Directory com o FreshDesk | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o FreshDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e75a45bc4ccd2614520fda39e09996c2286bdbd0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227621"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Tutorial: integração do Azure Active Directory com o FreshDesk

Neste tutorial, você aprenderá a integrar o FreshDesk com o Azure Active Directory (Azure AD).
A integração do FreshDesk ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao FreshDesk.
* Você pode permitir que seus usuários sejam conectados automaticamente ao FreshDesk (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao FreshDesk, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do FreshDesk

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O FreshDesk dá suporte ao SSO iniciado por **SP**

## <a name="adding-freshdesk-from-the-gallery"></a>Adicionando o FreshDesk da Galeria

Para configurar a integração do FreshDesk ao Azure AD, você precisará adicionar o FreshDesk da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o FreshDesk da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **FreshDesk**, selecione **FreshDesk** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![FreshDesk na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o FreshDesk, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no FreshDesk.

Para configurar e testar o logon único do Azure AD com o FreshDesk, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do FreshDesk](#configure-freshdesk-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do FreshDesk](#create-freshdesk-test-user)** – para ter um equivalente de Brenda Simon no FreshDesk que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o FreshDesk, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **FreshDesk** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do FreshDesk](common/sp-identifier.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<tenant-name>.freshdesk.com` ou qualquer outro valor que Freshdesk tenha sugerido.

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `https://<tenant-name>.freshdesk.com` ou qualquer outro valor que Freshdesk tenha sugerido.

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contate a [equipe de suporte ao cliente do FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. O aplicativo FreshDesk espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra um exemplo disso. O valor padrão do **identificador de usuário exclusivo** é **User. UserPrincipalName** , mas FreshDesk espera que isso seja mapeado com o endereço de email do usuário. Para o, você pode usar o atributo **User. mail** da lista ou usar o valor de atributo apropriado com base na configuração da sua organização. 

    ![image](common/edit-attribute.png)

6. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML, conforme mostrado na imagem acima, e execute as seguintes etapas:
    
    | Nome | Atributo de origem |
    | ---------------| --------------- |
    | Identificador de usuário exclusivo | user.mail |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **OK**

    g. Clique em **Guardar**.

7. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download de certificado](common/certificatebase64.png)

8. Abra o **prompt de comando** e execute os seguintes comandos:

    a. Insira `certutil.exe -dump FreshDesk.cer` valor no prompt de comando.

    > [!NOTE]
    > Aqui **FreshDesk. cer** é o certificado que você baixou do portal do Azure.

    b. Copie o valor de **hash de certificado (SHA256)** e cole-o no bloco de notas. 

9. Na seção **Configurar FreshDesk** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-freshdesk-single-sign-on"></a>Configurar o logon único do FreshDesk

1. Em uma janela diferente do navegador da Web, faça logon em seu site de empresa do Freshdesk como administrador.

2. Selecione o **ícone de configurações** e, na seção **segurança** , execute as seguintes etapas:

    ![Início de Sessão Único](./media/freshdesk-tutorial/IC776770.png "Início de Sessão Único")
  
    a. Para **SSO (logon único)** , selecione **ativado**.

    b. Selecione **SSO do SAML**.

    c. Na caixa de texto **URL de logon do SAML** , Cole o valor da URL de **logon** copiado do portal do Azure.

    d. Na caixa de texto **URL de logout** , Cole o valor da **URL de logout** copiado do portal do Azure.

    e. Na caixa de texto **impressão digital do certificado de segurança** , Cole o valor **hash de certificado (SHA256)** que você obteve anteriormente.
  
    f. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite **brendafernandes\@yourcompanydomain. Extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao FreshDesk.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **FreshDesk**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **FreshDesk**.

    ![O link do FreshDesk na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-freshdesk-test-user"></a>Criar usuário de teste do FreshDesk

Para permitir que os usuários do AD do Azure façam logon no FreshDesk, eles devem ser provisionados no FreshDesk.  
No caso do FreshDesk, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon no seu locatário do **Freshdesk** .

2. No menu na parte superior, clique em **admin**.

    ![ADM](./media/freshdesk-tutorial/IC776772.png "administrador")

3. Na guia **configurações gerais** , clique em **agentes**.
  
    ![Representantes](./media/freshdesk-tutorial/IC776773.png "Agentes")

4. Clique em **novo agente**.

    ![Novo agente](./media/freshdesk-tutorial/IC776774.png "Novo agente")

5. Na caixa de diálogo informações do agente, execute as seguintes etapas:

    ![Informações do agente](./media/freshdesk-tutorial/IC776775.png "Informações do agente")

    a. Na caixa de texto **email** , digite o endereço de email do Azure ad da conta do Azure AD que você deseja provisionar.

    b. Na caixa de texto **nome completo** , digite o nome da conta do Azure AD que você deseja provisionar.

    c. Na caixa de texto **título** , digite o título da conta do Azure AD que você deseja provisionar.

    d. Clique em **Guardar**.

    >[!NOTE]
    >O titular da conta do Azure AD receberá um email que inclui um link para confirmar a conta antes que ela seja ativada.
    >
    >[!NOTE]
    >Você pode usar qualquer outra ferramenta de criação de conta de usuário Freshdesk ou APIs fornecidas pelo Freshdesk para provisionar contas de usuário do Azure AD para o FreshDesk.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do FreshDesk no painel de acesso, você deverá ser conectado automaticamente ao FreshDesk para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

