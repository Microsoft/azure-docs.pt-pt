---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Zscaler três | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e Zscaler três.
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
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b23abcf9a39ce7f6d77bc40e7143505bc68e8b72
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554981"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-three"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Zscaler três

Neste tutorial, você aprenderá a integrar o Zscaler três com o Azure Active Directory (Azure AD). Ao integrar o Zscaler três ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Zscaler três.
* Habilite seus usuários a serem conectados automaticamente ao Zscaler três com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Zscaler três assinaturas habilitadas para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Zscaler três dá suporte ao SSO iniciado por **SP**

* Zscaler três dá suporte ao provisionamento **de usuário just in time**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-zscaler-three-from-the-gallery"></a>Adicionando Zscaler três da Galeria

Para configurar a integração do Zscaler três ao Azure AD, você precisará adicionar o Zscaler três da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Zscaler três** na caixa de pesquisa.
1. Selecione **Zscaler três** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler-three"></a>Configurar e testar o logon único do Azure AD para Zscaler três

Configure e teste o SSO do Azure AD com o Zscaler três usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Zscaler três.

Para configurar e testar o SSO do Azure AD com o Zscaler três, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o Zscaler três SSO](#configure-zscaler-three-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Zscaler três](#create-zscaler-three-test-user)** – para ter um equivalente de B. Simon em Zscaler três que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Zscaler três** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon** , digite uma URL: `https://login.zscalerthree.net/sfc_sso`

1. Seu aplicativo Zscaler três espera que as asserções SAML estejam em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/edit-attribute.png)

6. Além de acima, Zscaler três aplicativos esperam que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seu requisito.
    
    | Nome | Atributo de origem |
    | ---------| ------------ |
    | memberOf     | User. assignedroles |

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) para saber como configurar a função no Azure AD

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/certificatebase64.png)

1. Na seção **Configurar Zscaler três** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo usuário** na parte superior da tela.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao Zscaler três.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Zscaler três**.
1. Na caixa de diálogo **usuários e grupos** , selecione o usuário como **Brenda Simon** na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

    ![imagem](./media/zscaler-three-tutorial/tutorial_zscalerthree_users.png)

1. Na caixa de diálogo **selecionar função** , escolha a função de usuário apropriada na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

    ![imagem](./media/zscaler-three-tutorial/tutorial_zscalerthree_roles.png)

1. Na caixa de diálogo **Adicionar atribuição** , selecione o botão **atribuir** .

    ![imagem](./media/zscaler-three-tutorial/tutorial_zscalerthree_assign.png)

## <a name="configure-zscaler-three-sso"></a>Configurar o Zscaler três SSO

1. Para automatizar a configuração dentro de Zscaler três, você precisa instalar a **extensão de navegador de entrada segura de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **instalação Zscaler três** irá direcioná-lo para o aplicativo Zscaler três. A partir daí, forneça as credenciais de administrador para entrar no Zscaler três. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-6.

    ![Configurar](common/setup-sso.png)

3. Se você quiser configurar o Zscaler três manualmente, abra uma nova janela do navegador da Web e entre no site da empresa do Zscaler três como administrador e execute as seguintes etapas:

4. Acesse **administração > autenticação > configurações de autenticação** e execute as seguintes etapas:
   
    ![Administrar](./media/zscaler-three-tutorial/ic800206.png "Administração")

    a. Em tipo de autenticação, escolha **SAML**.

    b. Clique em **Configurar SAML**.

5. Na janela **Editar SAML** , execute as seguintes etapas: e clique em salvar.  
            
    ![Gerenciar usuários & autenticação](./media/zscaler-three-tutorial/ic800208.png "Gerenciar usuários & autenticação")
    
    a. Na caixa de texto **URL do portal do SAML** , Cole a URL de **logon** que você copiou de portal do Azure.

    b. Na caixa de texto **atributo de nome de logon** , digite **NameID**.

    c. Clique em **carregar**para carregar o certificado de autenticação SAML do Azure que você baixou de portal do Azure no **certificado SSL público**.

    d. Ativar/desativar o **provisionamento automático do SAML**.

    e. Na caixa de texto **atributo de nome de exibição do usuário** , digite **DisplayName** se desejar habilitar o provisionamento automático do SAML para atributos DisplayName.

    f. Na caixa de texto **atributo de nome do grupo** , digite **memberOf** se desejar habilitar o provisionamento automático do SAML para atributos memberOf.

    g. No **atributo nome do departamento** , insira **Departamento** se você quiser habilitar o provisionamento automático do SAML para atributos de departamento.

    h. Clique em **Guardar**.

6. Na página da caixa de diálogo **configurar autenticação de usuário** , execute as seguintes etapas:

    ![Administração](./media/zscaler-three-tutorial/ic800207.png)

    a. Passe o mouse sobre o menu de **ativação** próximo à parte inferior esquerda.

    b. Clique em **Ativar**.

## <a name="configuring-proxy-settings"></a>Definindo as configurações de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para definir as configurações de proxy no Internet Explorer

1. Inicie o **Internet Explorer**.

2. Selecione **Opções da Internet** no menu **ferramentas** para abrir a caixa de diálogo **Opções da Internet** .   
    
     ![Opções da Internet](./media/zscaler-three-tutorial/ic769492.png "Opções da Internet")

3. Clique na guia **conexões** .   
  
     ![Ligações](./media/zscaler-three-tutorial/ic769493.png "Ligações")

4. Clique em **configurações de LAN** para abrir a caixa de diálogo Configurações de **LAN** .

5. Na seção servidor proxy, execute as seguintes etapas:   
   
    ![Servidor proxy](./media/zscaler-three-tutorial/ic769494.png "Servidor proxy")

    a. Selecione **usar um servidor proxy para sua LAN**.

    b. Na caixa de texto endereço, digite **Gateway. Zscaler Three.net**.

    c. Na caixa de texto porta, digite **80**.

    d. Selecione **ignorar servidor proxy para endereços locais**.

    e. Clique em **OK** para fechar a caixa de diálogo **configurações de rede local (LAN)** .

6. Clique em **OK** para fechar a caixa de diálogo **Opções da Internet** .

### <a name="create-zscaler-three-test-user"></a>Criar Zscaler três usuários de teste

Nesta seção, um usuário chamado B. Simon é criado em Zscaler três. O Zscaler três dá suporte ao provisionamento just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no Zscaler três, um novo será criado quando você tentar acessar o Zscaler três.

>[!Note]
>Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do Zscaler três](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Testar SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco Zscaler três no painel de acesso, você deverá entrar automaticamente no Zscaler três para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Zscaler três com o Azure AD](https://aad.portal.azure.com/)

