---
title: 'Tutorial: Integração do Azure Active Directory com o Zscaler One | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e Zscaler um.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: d23fc94fba9a9b750f2977fd9c5d8297e4da8d5c
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68825911"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-one"></a>Tutorial: Integração do Azure Active Directory com o Zscaler One

Neste tutorial, você aprenderá a integrar o Zscaler One ao Azure Active Directory (Azure AD).
A integração do Zscaler One ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Zscaler One.
* Você pode permitir que seus usuários façam logon automaticamente no Zscaler One (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Zscaler One, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Zscaler uma assinatura habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* Zscaler One dá suporte ao SSO iniciado por **SP**

* O Zscaler One dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-zscaler-one-from-the-gallery"></a>Adicionando o Zscaler One da Galeria

Para configurar a integração do Zscaler One ao Azure AD, você precisará adicionar o Zscaler One da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Zscaler One da galeria, execute as seguintes etapas:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Zscaler One**, selecione **Zscaler One** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Zscaler um na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o Zscaler One, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Zscaler One.

Para configurar e testar o logon único do Azure AD com o Zscaler One, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o Zscaler um logon único](#configure-zscaler-one-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar um usuário de teste do Zscaler One](#create-zscaler-one-test-user)** – para ter um equivalente de Brenda Simon no Zscaler um que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Zscaler One, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Zscaler One** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-** enalimentado para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Zscaler](common/sp-signonurl.png)

    Na caixa de texto **URL de logon** , digite a URL usada pelos usuários para fazer logon no seu aplicativo Zscaler One.

    > [!NOTE]
    > Você atualiza o valor com a URL de logon real. Contate a [equipe de suporte ao cliente do Zscaler One](https://www.zscaler.com/company/contact) para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Seu aplicativo Zscaler One espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo **atributos de usuário** .

    ![image](common/edit-attribute.png)

6. Além de acima, Zscaler um aplicativo espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:
    
    | Nome | Atributo de origem |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.
    
    f. Clique em **Guardar**.

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) para saber como configurar a função no Azure AD

7. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download de certificado](common/certificatebase64.png)

8. Na seção **Configurar Zscaler uma** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-zscaler-one-single-sign-on"></a>Configurar o Zscaler um logon único

1. Para automatizar a configuração no Zscaler One, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar Zscaler um** direcionará você para o aplicativo Zscaler One. A partir daí, forneça as credenciais de administrador para entrar no Zscaler One. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-6.

    ![Configurar SSO](common/setup-sso.png)

3. Se você quiser configurar o Zscaler um manualmente, abra uma nova janela do navegador da Web e entre no site da empresa do Zscaler One como administrador e execute as seguintes etapas:

4. Acesse **administração > autenticação > configurações de autenticação** e execute as seguintes etapas:
   
    ![Administração] do (./media/zscaler-one-tutorial/ic800206.png "Administração") do

    a. Em tipo de autenticação, escolha **SAML**.

    b. Clique em **Configurar SAML**.

5. Na janela **Editar SAML** , execute as seguintes etapas: e clique em salvar.  
            
    ![Gerenciar usuários & autenticação](./media/zscaler-one-tutorial/ic800208.png "Gerenciar usuários & autenticação")
    
    a. Na caixa de texto **URL do portal do SAML** , Cole a URL de **logon** que você copiou de portal do Azure.

    b. Na caixa de texto **atributo de nome de logon** , digite **NameID**.

    c. Clique em **carregar**para carregar o certificado de autenticação SAML do Azure que você baixou de portal do Azure no **certificado SSL público**.

    d. Ativar/desativar o **provisionamento automático do SAML**.

    e. Na caixa de texto **atributo de nome de exibição do usuário** , digite **DisplayName** se desejar habilitar o provisionamento automático do SAML para atributos DisplayName.

    f. Na caixa de texto **atributo de nome do grupo** , digite **memberOf** se desejar habilitar o provisionamento automático do SAML para atributos memberOf.

    g. No **atributo nome do departamento** , insira **Departamento** se você quiser habilitar o provisionamento automático do SAML para atributos de departamento.

    h. Clique em **Guardar**.

6. Na página da caixa de diálogo **configurar autenticação de usuário** , execute as seguintes etapas:

    ![Administração](./media/zscaler-one-tutorial/ic800207.png)

    a. Passe o mouse sobre o menu de **ativação** próximo à parte inferior esquerda.

    b. Clique em **Ativar**.

## <a name="configuring-proxy-settings"></a>Definindo as configurações de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para definir as configurações de proxy no Internet Explorer

1. Inicie o **Internet Explorer**.

2. Selecione **Opções da Internet** no menu **ferramentas** para abrir a caixa de diálogo **Opções da Internet** .   
    
     ![Opções da Internet](./media/zscaler-one-tutorial/ic769492.png "Opções da Internet")

3. Clique na guia **conexões** .   
  
     ![Conexões] do (./media/zscaler-one-tutorial/ic769493.png "Conexões") do

4. Clique em **configurações de LAN** para abrir a caixa de diálogo Configurações de **LAN** .

5. Na seção servidor proxy, execute as seguintes etapas:   
   
    ![Servidor proxy](./media/zscaler-one-tutorial/ic769494.png "Servidor proxy")

    a. Selecione **usar um servidor proxy para sua LAN**.

    b. Na caixa de texto endereço, digite **Gateway. Zscaler One.net**.

    c. Na caixa de texto porta, digite **80**.

    d. Selecione **ignorar servidor proxy para endereços locais**.

    e. Clique em **OK** para fechar a caixa de diálogo **configurações de rede local (LAN)** .

7. Clique em **OK** para fechar a caixa de diálogo **Opções da Internet** .

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , brittasimon@yourcompanydomain.extensiondigite. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao Zscaler One.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Zscaler One**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Zscaler One**.

    ![O link Zscaler One na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione o usuário como **Brenda Simon** na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

    ![image](./media/zscaler-one-tutorial/tutorial_zscalerone_users.png)

6. Na caixa de diálogo **selecionar função** , escolha a função de usuário apropriada na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

    ![image](./media/zscaler-one-tutorial/tutorial_zscalerone_roles.png)

7. Na **adicionar atribuição** caixa de diálogo select a **atribuir** botão.

    ![image](./media/zscaler-one-tutorial/tutorial_zscalerone_assign.png)

### <a name="create-zscaler-one-test-user"></a>Criar um usuário de teste do Zscaler One

Nesta seção, um usuário chamado Brenda Simon é criado no Zscaler One. O Zscaler One dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no Zscaler um, um novo será criado após a autenticação.

>[!Note]
>Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do Zscaler One](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco Zscaler One no painel de acesso, você deverá entrar automaticamente no Zscaler um para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

