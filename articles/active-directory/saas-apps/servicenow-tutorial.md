---
title: 'Tutorial: Integração de logon único (SSO) do Azure Active Directory com o ServiceNow | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 431d20c4c5ae5355d456ca3453b832e590cbb199
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558954"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Tutorial: Integração de SSO (logon único) do Azure Active Directory com o ServiceNow

Neste tutorial, você aprenderá a integrar o ServiceNow ao Azure Active Directory (Azure AD). Ao integrar o ServiceNow ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao ServiceNow.
* Habilite seus usuários a serem conectados automaticamente ao ServiceNow com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do ServiceNow.
* Para o ServiceNow, uma instância ou locatário do ServiceNow, versão Calgary ou superior
* Para o ServiceNow Express, uma instância do ServiceNow Express, versão Helsinque ou superior
* O locatário do ServiceNow deve ter o [plug-in de logon único do provedor múltiplo](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) habilitado. Isso pode ser feito [enviando uma solicitação de serviço](https://hi.service-now.com).
* Para configuração automática, habilite o plug-in de vários provedores para o ServiceNow.
* Para instalar o aplicativo do ServiceNow clássico (móvel), você precisa ir para o repositório apropriado e procurar o aplicativo do ServiceNow clássico e clicar em baixar.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O ServiceNow dá suporte ao SSO iniciado pelo **SP** e dá suporte ao [ provisionamento automatizado de usuários](servicenow-provisioning-tutorial.md).

O aplicativo do ServiceNow clássico (móvel) agora pode ser configurado com o Azure AD para habilitar o SSO e ele dá suporte a usuários do **Android** e **Ios** . Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

## <a name="adding-servicenow-from-the-gallery"></a>Adicionando o ServiceNow da Galeria

Para configurar a integração do ServiceNow ao Azure AD, você precisará adicionar o ServiceNow da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **ServiceNow** na caixa de pesquisa.
1. Selecione **ServiceNow** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-servicenow"></a>Configurar e testar o logon único do Azure AD para o ServiceNow

Configure e teste o SSO do Azure AD com o ServiceNow usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no ServiceNow.

Para configurar e testar o SSO do Azure AD com o ServiceNow, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure ad](#configure-azure-ad-sso)** para permitir que seus usuários usem esse recurso.
    1. **[Crie um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Simon.
    1. **[Atribua o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** para habilitar B. Simon para usar o logon único do Azure AD.
    1. **[Configurar o SSO do Azure ad para o ServiceNow Express](#configure-azure-ad-sso-for-servicenow-express)** – para permitir que os usuários usem esse recurso.
2. **[Configure o ServiceNow](#configure-servicenow)** para definir as configurações de SSO no lado do aplicativo.
    1. **[Crie um usuário de teste do ServiceNow](#create-servicenow-test-user)** para ter um equivalente de B. Simon no ServiceNow que esteja vinculado à representação de usuário do Azure AD.
    1. **[Configurar o SSO do ServiceNow Express](#configure-servicenow-express-sso)** – para configurar as configurações de logon único no lado do aplicativo.    
3. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.
4. **[Teste o SSO para o ServiceNow clássico (móvel)](#test-sso-for-servicenow-classic-mobile)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ServiceNow** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://<instance-name>.service-now.com/navpage.do`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão:`https://<instance-name>.service-now.com`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de entrada e o identificador reais que são explicados posteriormente no tutorial. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

   ![O link de download de certificado](common/certificatebase64.png)

   a. Clique no botão Copiar para copiar a **URL de metadados de Federação do aplicativo** e cole-a no bloco de notas, pois essa URL de metadados de Federação do aplicativo será usada posteriormente no tutorial.

    b. Clique em **baixar** para baixar o **certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

1. Na seção **Configurar o ServiceNow** , copie as URLs apropriadas com base em seu requisito.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao ServiceNow.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **ServiceNow**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Configurar o SSO do Azure AD para o ServiceNow Express

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ServiceNow** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-** enalimentado para habilitar o logon único.

    ![modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://<instance-name>.service-now.com/navpage.do`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão:`https://<instance-name>.service-now.com`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de entrada e o identificador reais que são explicados posteriormente no tutorial. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Um clique em configurar serviço é fornecido para o ServiceNow ou seja, para que o Azure AD configure automaticamente o ServiceNow para autenticação baseada em SAML. Para habilitar este serviço, vá para a seção **Configurar o ServiceNow** , clique em **exibir instruções** passo a passo para abrir a janela Configurar logon.

    ![Configurar o início de sessão único](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Insira o nome da instância do ServiceNow, o nome de usuário do administrador e a senha de administrador no formulário **Configurar logon** e clique em **Configurar agora**. Verifique se o nome de usuário do administrador fornecido deve ter a função **Security_Admin** atribuída no ServiceNow para que isso funcione. Caso contrário, para configurar manualmente o ServiceNow para usar o Azure AD como um provedor de identidade SAML, clique em **configurar manualmente o logon único** e copie a **URL de logout, o identificador do Azure AD e a URL de logon** da seção referência rápida.

    ![Configurar URL do aplicativo](./media/servicenow-tutorial/configure.png "Configurar URL do aplicativo")

## <a name="configure-servicenow"></a>Configurar o ServiceNow

1. Faça logon em seu aplicativo ServiceNow como administrador.

2. Ative o plug-in de **instalação de logon único do provedor de integração múltipla** seguindo as próximas etapas:

    a. No painel de navegação à esquerda, pesquise a seção de **definição do sistema** na barra de pesquisa e clique em **plugins**.

    ![Ativar plug-in](./media/servicenow-tutorial/tutorial_servicenow_03.png "Ativar plug-in")

    b. Procurar **integração – instalador de logon único de vários provedores**.

     ![Ativar plug-in](./media/servicenow-tutorial/tutorial_servicenow_04.png "Ativar plug-in")

    c. Selecione o plug-in. Clique com o botão direito do mouse e selecione **Ativar/atualizar**.

     ![Ativar plug-in](./media/servicenow-tutorial/tutorial_activate.png "Ativar plug-in")

    d. Clique no botão **Ativar** .

     ![Ativar plug-in](./media/servicenow-tutorial/tutorial_activate1.png "Ativar plug-in")

3. No painel de navegação no lado esquerdo, pesquise a seção **SSO de vários provedores** na barra de pesquisa e clique em **Propriedades**.

    ![Configurar URL do aplicativo](./media/servicenow-tutorial/tutorial_servicenow_06.png "Configurar URL do aplicativo")

4. Na caixa de diálogo **Propriedades de SSO de vários provedores** , execute as seguintes etapas:

    ![Configurar URL do aplicativo](./media/servicenow-tutorial/ic7694981.png "Configurar URL do aplicativo")

    * Como **habilitar o SSO de vários provedores**, selecione **Sim**.
  
    * Como **habilitar a importação automática de usuários de todos os provedores de identidade para a tabela de usuário**, selecione **Sim**.

    * Como **habilitar o log de depuração para a integração de SSO de vários provedores**, selecione **Sim**.

    * No campo, na caixa de texto **tabela de usuário que...** , digite **user_name**.
  
    * Clique em **Guardar**.

5. Há duas maneiras pelas quais o **ServiceNow** pode ser configurado-automático e manual.

6. Para configurar o **ServiceNow** automaticamente, siga as etapas abaixo:

    * Retorne para a página de logon único do **ServiceNow** no portal do Azure.

    * Um clique em configurar serviço é fornecido para o ServiceNow ou seja, para que o Azure AD configure automaticamente o ServiceNow para autenticação baseada em SAML. Para habilitar este serviço, vá para a seção **configuração do servicenow** , clique em **Configurar o servicenow** para abrir a janela Configurar logon.

        ![Configurar o início de sessão único](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    * Insira o nome da instância do ServiceNow, o nome de usuário do administrador e a senha de administrador no formulário **Configurar logon** e clique em **Configurar agora**. Verifique se o nome de usuário do administrador fornecido deve ter a função **Security_Admin** atribuída no ServiceNow para que isso funcione. Caso contrário, para configurar manualmente o ServiceNow para usar o Azure AD como um provedor de identidade SAML, clique em **configurar manualmente o logon único** e copie a **URL de saída, a ID da entidade SAML e a URL do serviço de logon único SAML** da seção referência rápida.

        ![Configurar URL do aplicativo](./media/servicenow-tutorial/configure.png "Configurar URL do aplicativo")

    * Faça logon em seu aplicativo ServiceNow como administrador.

    * Na configuração automática, todas as configurações necessárias são configuradas no lado do **ServiceNow** , mas o **certificado X. 509** não está habilitado por padrão. Você precisa mapeá-lo manualmente para seu provedor de identidade no ServiceNow. Siga as etapas abaixo para o mesmo:

    * No painel de navegação no lado esquerdo, pesquise a seção **SSO de vários provedores** na barra de pesquisa e clique em **provedores de identidade**.

        ![Configurar logon único](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configurar logon único")

    * Clique no provedor de identidade gerado automaticamente

        ![Configurar logon único](./media/servicenow-tutorial/tutorial_servicenow_08.png "Configurar logon único")

    *  Na seção **provedor de identidade** , execute as seguintes etapas:

        ![Configurar logon único](./media/servicenow-tutorial/automatic_config.png "Configurar logon único")

        * Na caixa de texto **nome** , digite um nome para a sua configuração (por exemplo, **Microsoft Azure logon único federado**).

        * Remova o valor de **SingleLogoutRequest do provedor de identidade** preenchido da caixa de texto.

        * Copie o valor da **Home Page do servicenow** , Cole-o na caixa de texto **URL de logon** na seção de **configuração SAML básica do servicenow** em portal do Azure.

            > [!NOTE]
            > A Home Page da instância do ServiceNow é uma concatenação de sua **URL de locatário do servicenow** e **/navpage.do** (por exemplo:`https://fabrikam.service-now.com/navpage.do`).

        * Copie a **ID da entidade/** valor do emissor, Cole-o na caixa de texto **identificador** na seção **configuração do SAML básica do ServiceNow** em portal do Azure.

        * Verifique se a **política NameID** está definida como `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` valor. 

    * Role para baixo até a seção **certificado X. 509** , selecione **Editar**.

        ![Configurar logon único](./media/servicenow-tutorial/tutorial_servicenow_09.png "Configurar logon único")

    * Selecione no certificado e clique no ícone de seta para a direita para adicionar o certificado

        ![Configurar logon único](./media/servicenow-tutorial/tutorial_servicenow_11.png "Configurar logon único")

    * Clique em **Guardar**.

    * Clique em **testar conexão** no canto superior direito da página.

        ![Ativar plug-in](./media/servicenow-tutorial/tutorial_activate2.png "Ativar plug-in")

    * Depois de clicar na **conexão de teste**, você obterá a janela pop-up onde precisa inserir as credenciais e a página abaixo com os resultados é mostrada. O erro de **resultados de teste de logoff de SSO** é esperado. ignore o erro e clique no botão **Ativar** .

        ![Configurar logon único](./media/servicenow-tutorial/servicenowactivate.png "Configurar logon único")
  
7. Para configurar o **ServiceNow** manualmente, siga as etapas abaixo:

    * Faça logon em seu aplicativo ServiceNow como administrador.

    * No painel de navegação no lado esquerdo, clique em **provedores de identidade**.

        ![Configurar logon único](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configurar logon único")

    * Na caixa de diálogo **provedores de identidade** , clique em **novo**.

        ![Configurar logon único](./media/servicenow-tutorial/ic7694977.png "Configurar logon único")

    * Na caixa de diálogo **provedores de identidade** , clique em **SAML**.

        ![Configurar logon único](./media/servicenow-tutorial/ic7694978.png "Configurar logon único")

    * No pop-up **importar metadados do provedor de identidade** , execute as seguintes etapas:

        ![Configurar logon único](./media/servicenow-tutorial/idp.png "Configurar logon único")

        * Insira a **URL de metadados de Federação do aplicativo** que você copiou de portal do Azure.

        * Clique em **Importar**.

    * Ele lê a URL de metadados do IdP e popula todas as informações de campos.

        ![Configurar logon único](./media/servicenow-tutorial/ic7694982.png "Configurar logon único")

        * Na caixa de texto **nome** , digite um nome para a sua configuração (por exemplo, **Microsoft Azure logon único federado**).

        * Remova o valor de **SingleLogoutRequest do provedor de identidade** preenchido da caixa de texto.

        * Copie o valor da **Home Page do servicenow** , Cole-o na caixa de texto **URL de logon** na seção de **configuração SAML básica do servicenow** em portal do Azure.

            > [!NOTE]
            > A Home Page da instância do ServiceNow é uma concatenação de sua **URL de locatário do servicenow** e **/navpage.do** (por exemplo:`https://fabrikam.service-now.com/navpage.do`).

        * Copie a **ID da entidade/** valor do emissor, Cole-o na caixa de texto **identificador** na seção **configuração do SAML básica do ServiceNow** em portal do Azure.

        * Verifique se a **política NameID** está definida como `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` valor.

        * Clique em **avançado**. Na caixa de texto **campo de usuário** , digite **email** ou **user_name**, dependendo de qual campo é usado para identificar exclusivamente os usuários em sua implantação do ServiceNow.

            > [!NOTE]
            > Você pode configurar o Azure AD para emitir a ID de usuário do Azure AD (nome UPN) ou o endereço de email como o identificador exclusivo no token SAML acessando os **atributos do ServiceNow > > seção logon único** do portal do Azure e mapeando o campo desejado para o atributo **nameidentifier** . O valor armazenado para o atributo selecionado no Azure AD (por exemplo, nome principal do usuário) deve corresponder ao valor armazenado no ServiceNow para o campo inserido (por exemplo, user_name)

        * Clique em **testar conexão** no canto superior direito da página.

        * Depois de clicar na **conexão de teste**, você obterá a janela pop-up onde precisa inserir as credenciais e a página abaixo com os resultados é mostrada. O erro de **resultados de teste de logoff de SSO** é esperado. ignore o erro e clique no botão **Ativar** .

          ![Configurar logon único](./media/servicenow-tutorial/servicenowactivate.png "Configurar logon único")

### <a name="create-servicenow-test-user"></a>Criar usuário de teste do ServiceNow

O objetivo desta seção é criar um usuário chamado Brenda Simon no ServiceNow. O ServiceNow dá suporte ao provisionamento automático de usuário, que está habilitado por padrão. Você pode encontrar mais detalhes [aqui](servicenow-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuário.

> [!NOTE]
> Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte ao cliente do ServiceNow](https://www.servicenow.com/support/contact-support.html)

### <a name="configure-servicenow-express-sso"></a>Configurar o SSO do ServiceNow Express

1. Faça logon em seu aplicativo ServiceNow Express como administrador.

2. No painel de navegação no lado esquerdo, clique em **logon único**.

    ![Configurar URL do aplicativo](./media/servicenow-tutorial/ic7694980ex.png "Configurar URL do aplicativo")

3. Na caixa de diálogo **logon único** , clique no ícone de configuração no canto superior direito e defina as seguintes propriedades:

    ![Configurar URL do aplicativo](./media/servicenow-tutorial/ic7694981ex.png "Configurar URL do aplicativo")

    a. Alterne **habilitar SSO de vários provedores** à direita.

    b. Alterne **habilitar log de depuração para a integração de SSO de vários provedores** à direita.

    c. No campo, na caixa de texto **tabela de usuário que...** , digite **user_name**.

4. Na caixa de diálogo **logon único** , clique em **Adicionar novo certificado**.

    ![Configurar logon único](./media/servicenow-tutorial/ic7694973ex.png "Configurar logon único")

5. Na caixa de diálogo **certificados X. 509** , execute as seguintes etapas:

    ![Configurar logon único](./media/servicenow-tutorial/ic7694975.png "Configurar logon único")

    a. Na caixa de texto **nome** , digite um nome para a sua configuração (por exemplo: **TestSAML2.0**).

    b. Selecione **ativo**.

    c. Como **formato**, selecione **PEM**.

    d. Como **tipo**, selecione **confiar no certificado de repositório**.

    e. Abra o certificado codificado em base64 baixado de portal do Azure no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **certificado PEM** .

    f. Clique em **Atualizar**

6. Na caixa de diálogo **logon único** , clique em **Adicionar novo IDP**.

    ![Configurar logon único](./media/servicenow-tutorial/ic7694976ex.png "Configurar logon único")

7. Na caixa de diálogo **Adicionar novo provedor de identidade** , em **Configurar provedor de identidade**, execute as seguintes etapas:

    ![Configurar logon único](./media/servicenow-tutorial/ic7694982ex.png "Configurar logon único")

    a. Na caixa de texto **nome** , digite um nome para a sua configuração (por exemplo: **SAML 2.0**).

    b. No campo **URL do provedor de identidade** , Cole o valor da **ID do provedor de identidade**que você copiou do portal do Azure.

    c. No campo **AuthnRequest do provedor de identidade** , Cole o valor da **URL de solicitação de autenticação**que você copiou do portal do Azure.

    d. No campo **SingleLogoutRequest do provedor de identidade** , Cole o valor da **URL de logout**copiado de portal do Azure

    e. Como **certificado do provedor de identidade**, selecione o certificado que você criou na etapa anterior.

8. Clique em **Configurações avançadas**e, em **Propriedades adicionais do provedor de identidade**, execute as seguintes etapas:

    ![Configurar logon único](./media/servicenow-tutorial/ic7694983ex.png "Configurar logon único")

    a. Na **Associação de protocolo para a caixa de texto SingleLogoutRequest do IDP** , digite **urn: Oasis: names: TC: SAML: 2.0: Bindings: http-Redirect**.

    b. Na caixa de texto **política NameID** , digite **urn: Oasis: names: TC: SAML: 1.1: NameID-Format: não especificado**.

    c. No **método AuthnContextClassRef**, digite `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Desmarque **criar um AuthnContextClass**.

9. Em **Propriedades adicionais do provedor de serviços**, execute as seguintes etapas:

    ![Configurar logon único](./media/servicenow-tutorial/ic7694984ex.png "Configurar logon único")

    a. Na caixa de texto **Home Page do servicenow** , digite a URL da sua página inicial da instância do servicenow.

    > [!NOTE]
    > A Home Page da instância do ServiceNow é uma concatenação de sua **URL de locatário do servicenow** e **/navpage.do** (por exemplo: `https://fabrikam.service-now.com/navpage.do`).

    b. Na caixa de texto **ID da entidade/emissor** , digite a URL do seu locatário do ServiceNow.

    c. Na caixa de texto **URI do público-alvo** , digite a URL do seu locatário do ServiceNow.

    d. Na caixa de texto distorção do **relógio** , digite **60**.

    e. Na caixa de texto **campo de usuário** , digite **email** ou **user_name**, dependendo de qual campo é usado para identificar exclusivamente os usuários em sua implantação do ServiceNow.

    > [!NOTE]
    > Você pode configurar o Azure AD para emitir a ID de usuário do Azure AD (nome UPN) ou o endereço de email como o identificador exclusivo no token SAML acessando os **atributos do ServiceNow > > seção logon único** do portal do Azure e mapeando o campo desejado para o atributo **nameidentifier** . O valor armazenado para o atributo selecionado no Azure AD (por exemplo, nome principal do usuário) deve corresponder ao valor armazenado no ServiceNow para o campo inserido (por exemplo, user_name)

    f. Clique em **Guardar**.

## <a name="test-sso"></a>Testar SSO

Ao selecionar o bloco do ServiceNow no painel de acesso, você deverá ser conectado automaticamente ao ServiceNow para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-servicenow-classic-mobile"></a>Testar SSO para ServiceNow clássico (móvel)

1. Abra seu aplicativo do **ServiceNow clássico (móvel)** e execute as seguintes etapas:

    a. Clique no símbolo **Adicionar** abaixo da tela.

    ![A entrada](./media/servicenow-tutorial/test03.png)

    b. Digite o nome da instância do ServiceNow e clique em **continuar**.

    ![A entrada](./media/servicenow-tutorial/test04.png)

    c. Na tela de **logon** , execute as seguintes etapas:

    ![A entrada](./media/servicenow-tutorial/test01.png)

    *  Digite **nome** de B.simon@contoso.comusuário como.

    *  Clique em **usar logon externo** e você será redirecionado para a página do Azure ad para entrar.
    
    *  Insira suas credenciais e, se houver alguma autenticação de terceiros ou qualquer outro recurso de segurança habilitado, o usuário terá que responder de acordo e a **página inicial** do aplicativo será exibida, conforme mostrado abaixo:

        ![A Home Page](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar provisionamento de usuário](servicenow-provisioning-tutorial.md)

- [Experimente o ServiceNow com o Azure AD](https://aad.portal.azure.com)