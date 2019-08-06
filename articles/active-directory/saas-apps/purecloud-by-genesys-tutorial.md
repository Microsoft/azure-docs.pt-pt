---
title: 'Tutorial: Integração do Azure Active Directory com o PureCloud by Genesys | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o PureCloud by Genesys.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 92df57737b75ab2c9bb9992dd3f223f55dbc39bb
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68826134"
---
# <a name="tutorial-azure-active-directory-integration-with-purecloud-by-genesys"></a>Tutorial: Integração do Azure Active Directory com o PureCloud by Genesys

Neste tutorial, você aprenderá a integrar o PureCloud by Genesys ao Azure Active Directory (Azure AD).
A integração do PureCloud by Genesys ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao PureCloud by Genesys.
* Você pode permitir que seus usuários façam logon automaticamente no PureCloud by Genesys (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao PureCloud by Genesys, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do PureCloud by Genesys

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* PureCloud by Genesys dá suporte ao **SP e** ao SSO iniciado pelo IDP

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>Adicionando o PureCloud by Genesys da Galeria

Para configurar a integração do PureCloud by Genesys ao Azure AD, você precisará adicionar o PureCloud by Genesys da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o PureCloud by Genesys da galeria, execute as seguintes etapas:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **PureCloud by Genesys**, selecione **PureCloud by Genesys** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![PureCloud by Genesys na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o PureCloud by Genesys, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do PureCloud by Genesys.

Para configurar e testar o logon único do Azure AD com o PureCloud by Genesys, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o logon único do PureCloud by Genesys](#configure-purecloud-by-genesys-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar usuário de teste do PureCloud by Genesys](#create-purecloud-by-genesys-test-user)** – para ter um equivalente de Brenda Simon no PureCloud by Genesys que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o PureCloud by Genesys, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **PureCloud by Genesys** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-** enalimentado para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do PureCloud by Genesys](common/idp-intiated.png)

    a. Na caixa de texto **identificador** , digite uma URL de acordo com a sua região:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. Na caixa de texto **URL de resposta** , digite uma URL de acordo com a sua região:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml` |

5. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    ![Informações de logon único de domínio e URLs do PureCloud by Genesys](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon** , digite uma URL de acordo com a sua região:
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

6. O aplicativo PureCloud by Genesys espera que as asserções SAML estejam em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo **atributos de usuário** .

    ![image](common/edit-attribute.png)

7. Além de acima, o aplicativo PureCloud by Genesys espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | Email | user.userprinicipalname |
    | OrganizationName | `Your organization name` |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

8. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download de certificado](common/certificatebase64.png)

9. Na seção **Configurar PureCloud por Genesys** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-purecloud-by-genesys-single-sign-on"></a>Configurar o logon único do PureCloud by Genesys

1. Em uma janela diferente do navegador da Web, faça logon no PureCloud by Genesys como administrador.

2. Clique em **admin** na parte superior e navegue até **logon único** em integrações.

    ![Configurar o início de sessão único](./media/purecloud-by-genesys-tutorial/configure01.png)

3. Alterne para a guia **ADFS/AD do Azure (Premium)** e execute as seguintes etapas:

    ![Configurar o início de sessão único](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Clique em **procurar** para carregar o certificado codificado em base 64 que você baixou do portal do Azure para o **certificado do ADFS**.

    b. Na caixa de texto **URI do emissor do ADFS** , Cole o valor do identificador do **Azure ad** que você copiou do portal do Azure.

    c. Na caixa de texto **URI de destino** , Cole o valor da **URL de logon** que você copiou do portal do Azure.

    d. Para o valor do **identificador** de terceira parte confiável, você precisa ir para a Portal do Azure, na página de integração de aplicativos do **PureCloud by Genesys** , clique na guia **Propriedades** e copie o valor da **ID do aplicativo** . Cole-o na caixa de texto **identificador de terceira parte confiável** . 

    ![Configurar o início de sessão único](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Clique em **Guardar**   

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , **digite\@brendafernandes yourcompanydomain. Extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao PureCloud by Genesys.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **PureCloud por Genesys**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **PureCloud by Genesys**.

    ![O link do PureCloud by Genesys na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-purecloud-by-genesys-test-user"></a>Criar PureCloud por usuário de teste do Genesys

Para permitir que os usuários do Azure AD façam logon no PureCloud by Genesys, eles devem ser provisionados no PureCloud by Genesys. No PureCloud by Genesys, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no PureCloud by Genesys como administrador.

2. Clique em **admin** na parte superior e navegue para **pessoas** em **pessoas & permissões**.

    ![Configurar o início de sessão único](./media/purecloud-by-genesys-tutorial/configure03.png)

3. Na página pessoas, clique em **Adicionar pessoa**.

    ![Configurar o início de sessão único](./media/purecloud-by-genesys-tutorial/configure04.png)

4. No pop-up **adicionar pessoas ao organização** , execute as seguintes etapas:

    ![Configurar o início de sessão único](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. Na caixa de texto **nome completo** , digite o nome do usuário, como **brendafernandes**.

    b. Na caixa de texto **email** , insira o email do usuário, como **brendafernandes\@contoso.com**.
    
    c. Clique em **Criar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco PureCloud by Genesys no painel de acesso, você deverá entrar automaticamente no PureCloud by Genesys para o qual você configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

