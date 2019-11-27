---
title: 'Tutorial: integração do Azure Active Directory com o zendesk | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o zendesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4887457f457ff533a0eaf10be7db1627a950f5bd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233272"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Tutorial: integração do Azure Active Directory com o zendesk

Neste tutorial, você aprenderá a integrar o zendesk ao Azure Active Directory (Azure AD).
A integração do zendesk ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao zendesk.
* Você pode permitir que seus usuários sejam automaticamente conectados ao zendesk (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao zendesk, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do zendesk

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O zendesk dá suporte ao SSO iniciado pelo **SP**

* O zendesk dá suporte ao [provisionamento **automatizado** de usuários](zendesk-provisioning-tutorial.md)

## <a name="adding-zendesk-from-the-gallery"></a>Adicionando o zendesk da Galeria

Para configurar a integração do zendesk ao Azure AD, você precisará adicionar o zendesk da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o zendesk da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **zendesk**, selecione **zendesk** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Zendesk na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o zendesk, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do zendesk.

Para configurar e testar o logon único do Azure AD com o zendesk, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do zendesk](#configure-zendesk-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do zendesk](#create-zendesk-test-user)** – para ter um equivalente de Brenda Simon no zendesk que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o zendesk, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **zendesk** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do zendesk](common/sp-identifier.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<subdomain>.zendesk.com`

   b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `https://<subdomain>.zendesk.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contate a [equipe de suporte ao cliente do zendesk](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. O aplicativo zendesk espera as asserções SAML em um formato específico. Não há nenhum atributo SAML obrigatório, mas, opcionalmente, você pode gerenciar na seção **atributos de usuário** na página de integração de aplicativos. Na página **Configurar logon único com SAML** , clique no botão **Editar** para abrir a caixa de diálogo **atributos de usuário** .

    ![image](common/edit-attribute.png)

6. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , configure o atributo de token SAML, conforme mostrado na imagem acima, e execute as seguintes etapas:

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , selecione o valor de atributo apropriado.

    f. Clique em **OK**

    g. Clique em **Guardar**.

    > [!NOTE]
    > Você usa atributos de extensão para adicionar atributos que não estão no Azure AD por padrão. Clique em [atributos de usuário que podem ser definidos no SAML](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) para obter a lista completa de atributos SAML que o **zendesk** aceita.

7. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , copie a **impressão digital** e salve-a no computador.

    ![Copiar valor de impressão digital](common/copy-certificatethumbprint.png)

8. Na seção **Configurar o zendesk** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

9. Há duas maneiras pelas quais o zendesk pode ser configurado-automático e manual.
  
10. Para automatizar a configuração no zendesk, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![image](./media/zendesk-tutorial/install_extension.png)

11. Depois de adicionar a extensão ao navegador, clique em **Configurar o zendesk** irá direcioná-lo para o aplicativo zendesk. A partir daí, forneça as credenciais de administrador para entrar no zendesk. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar a seção **Configurar o logon único do zendesk**.

    ![image](./media/zendesk-tutorial/d2_saml.png)

### <a name="configure-zendesk-single-sign-on"></a>Configurar o logon único do zendesk

1. Se você quiser configurar o zendesk manualmente, abra uma nova janela do navegador da Web e faça logon em seu site de empresa do zendesk como administrador e execute as seguintes etapas:

2. Clique em **administrador**.

3. No painel de navegação esquerdo, clique em **configurações**e em **segurança**.

4. Na página **segurança** , execute as seguintes etapas:

    ![Segurança](././media/zendesk-tutorial/ic773089.png "Segurança")

    ![Início de sessão único](././media/zendesk-tutorial/ic773090.png "Início de sessão único")

    a. Clique na guia **agentes de & de administrador** .

    b. Selecione **SSO (logon único) e SAML**e, em seguida, selecione **SAML**.

    c. Na caixa de texto **URL de SSO do SAML** , Cole o valor da URL de **logon** que você copiou do portal do Azure.

    d. Na caixa de texto **URL de logout remoto** , Cole o valor da **URL de logout** que você copiou do portal do Azure.

    e. Na caixa de texto **impressão digital do certificado** , Cole o valor da **impressão digital** do certificado que você copiou do portal do Azure.

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao zendesk.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **zendesk**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **zendesk**.

    ![O link do zendesk na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-zendesk-test-user"></a>Criar usuário de teste do zendesk

O objetivo desta seção é criar um usuário chamado Brenda Simon no zendesk. O zendesk dá suporte ao provisionamento automático de usuário, que está habilitado por padrão. Você pode encontrar mais detalhes [aqui](Zendesk-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuário.

**Se você precisar criar o usuário manualmente, execute as seguintes etapas:**

> [!NOTE]
> As contas **de usuário final** são provisionadas automaticamente ao entrar. As contas de **agente** e **administrador** precisam ser provisionadas manualmente no **zendesk** antes de entrar.

1. Faça logon em seu locatário do **zendesk** .

2. Selecione a guia **lista de clientes** .

3. Selecione a guia **usuário** e clique em **Adicionar**.

    ![Adicionar usuário](././media/zendesk-tutorial/ic773632.png "Adicionar utilizador")
4. Digite o **nome** e o **email** de uma conta existente do Azure AD que você deseja provisionar e, em seguida, clique em **salvar**.

    ![Novo usuário](././media/zendesk-tutorial/ic773633.png "Novo usuário")

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação de conta de usuário do zendesk ou APIs fornecidas pelo zendesk para provisionar contas de usuário do Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do zendesk no painel de acesso, você deverá ser conectado automaticamente ao zendesk para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar provisionamento de usuário](zendesk-provisioning-tutorial.md)