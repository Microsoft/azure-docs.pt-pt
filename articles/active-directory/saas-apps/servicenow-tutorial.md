---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o ServiceNow | Microsoft Docs'
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
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79f5284f95f6157c70461179177179a9fbee4fe0
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293696"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o ServiceNow

Neste tutorial, você aprenderá a integrar o ServiceNow ao Azure Active Directory (Azure AD). Ao integrar o ServiceNow ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao ServiceNow.
* Habilite seus usuários a serem conectados automaticamente ao ServiceNow com suas contas do Azure AD.
* Gerencie suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS (software como serviço) com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do ServiceNow.
* Para o ServiceNow, uma instância ou locatário do ServiceNow, versão Calgary ou posterior.
* Para o ServiceNow Express, uma instância do ServiceNow Express, a versão Helsinque ou posterior.
* O locatário do ServiceNow deve ter o [plug-in de logon único do provedor múltiplo](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) habilitado. Você pode fazer isso [enviando uma solicitação de serviço](https://hi.service-now.com).
* Para configuração automática, habilite o plug-in de vários provedores para o ServiceNow.
* Para instalar o aplicativo do ServiceNow clássico (móvel), vá para o repositório apropriado e procure o aplicativo do ServiceNow clássico. Em seguida, baixe-o.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. 

* O ServiceNow dá suporte ao SSO iniciado pelo **SP** .

* O ServiceNow dá suporte ao [provisionamento automatizado de usuários](servicenow-provisioning-tutorial.md).

* Depois de configurar o ServiceNow, você pode impor controles de sessão, que protegem vazamento e pós-infiltração dos dados confidenciais de sua organização em tempo real. Os controles de sessão se estendem do acesso condicional. [Saiba como impor o controle de sessão com Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

* Você pode configurar o aplicativo do ServiceNow clássico (móvel) com o Azure AD para habilitar o SSO. Ele dá suporte a usuários Android e iOS. Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

## <a name="add-servicenow-from-the-gallery"></a>Adicionar o ServiceNow da Galeria

Para configurar a integração do ServiceNow ao Azure AD, você precisará adicionar o ServiceNow da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou usando uma conta Microsoft pessoal.
1. No painel esquerdo, selecione o serviço **Azure Active Directory** .
1. Vá para **aplicativos empresariais**e selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **ServiceNow** na caixa de pesquisa.
1. Selecione **ServiceNow** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-servicenow"></a>Configurar e testar o logon único do Azure AD para o ServiceNow

Configure e teste o SSO do Azure AD com o ServiceNow usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no ServiceNow.

Para configurar e testar o SSO do Azure AD com o ServiceNow, conclua os seguintes blocos de construção:

1. [Configure o SSO do Azure ad](#configure-azure-ad-sso) para permitir que seus usuários usem esse recurso.
    1. [Crie um usuário de teste do Azure ad](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com B. Simon.
    1. [Atribua o usuário de teste do Azure ad](#assign-the-azure-ad-test-user) para habilitar B. Simon para usar o logon único do Azure AD.
    1. [Configure o SSO do Azure ad para o ServiceNow Express](#configure-azure-ad-sso-for-servicenow-express) para permitir que os usuários usem esse recurso.
2. [Configure o ServiceNow](#configure-servicenow) para definir as configurações de SSO no lado do aplicativo.
    1. [Crie um usuário de teste do ServiceNow](#create-servicenow-test-user) para ter um equivalente de B. Simon no ServiceNow, vinculado à representação do usuário no Azure AD.
    1. [Configure o SSO do ServiceNow Express](#configure-servicenow-express-sso) para configurar as configurações de logon único no lado do aplicativo.  
3. [Teste o SSO](#test-sso) para verificar se a configuração funciona.
4. [Teste o SSO para o ServiceNow clássico (móvel)](#test-sso-for-servicenow-classic-mobile) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ServiceNow** , localize a seção **gerenciar** . Selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , selecione o ícone de caneta para a **configuração básica do SAML** para editar as configurações.

   ![Captura de tela da página Configurar logon único com SAML, com o ícone de caneta realçado](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    a. Na **URL de logon**, insira uma URL que usa o seguinte padrão: `https://<instance-name>.service-now.com/navpage.do`

    b. Em **identificador (ID da entidade)** , insira uma URL que usa o seguinte padrão: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de entrada e o identificador reais, que são explicados posteriormente no tutorial. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** . 

   ![Captura de tela da seção certificado de autenticação SAML, com download realçado](common/certificatebase64.png)

   a. Selecione o botão Copiar para copiar a **URL de metadados de Federação do aplicativo**e cole-a no bloco de notas. Essa URL será usada posteriormente no tutorial.

    b. Selecione **baixar** para baixar o **certificado (Base64)** e salve o arquivo de certificado em seu computador.

1. Na seção **Configurar o ServiceNow** , copie as URLs apropriadas com base em seu requisito.

   ![Captura de tela da seção configurar ServiceNow, com URLs realçadas](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste, chamado B. Simon, na portal do Azure.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory** > **usuários** > todos os **usuários**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. Para **nome**, insira `B.Simon`.  
   1. Para **nome de usuário**, insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione **Mostrar senha**e, em seguida, anote o valor mostrado na caixa **senha** .
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao ServiceNow.

1. Na portal do Azure, selecione **aplicativos empresariais** > **todos os aplicativos**.
1. Na lista de aplicativos, selecione **ServiceNow**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![Captura de tela da seção Gerenciar, com usuários e grupos realçados](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Na caixa de diálogo **Adicionar atribuição** , selecione **usuários e grupos**.

    ![Captura de tela de usuários e grupos, com Adicionar usuário realçado](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e escolha **selecionar**.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista. Em seguida, selecione **Selecionar**.
1. Na caixa de diálogo **Adicionar atribuição** , selecione **atribuir**.

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Configurar o SSO do Azure AD para o ServiceNow Express

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ServiceNow** , selecione **logon único**.

    ![Captura de tela da página de integração de aplicativos do ServiceNow, com logon único realçado](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Captura de tela de selecionar um método de logon único, com SAML realçado](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , selecione o ícone de caneta para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Captura de tela da página Configurar logon único com SAML, com o ícone de caneta realçado](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    a. Para **URL de logon**, insira uma URL que usa o seguinte padrão: `https://<instance-name>.service-now.com/navpage.do`

    b. Para o **identificador (ID da entidade)** , insira uma URL que usa o seguinte padrão: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de entrada e o identificador reais, que são explicados posteriormente no tutorial. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , selecione **baixar** para baixar o **certificado (Base64)** das opções especificadas, de acordo com seu requisito. Salve-o em seu computador.

    ![Captura de tela da seção certificado de autenticação SAML, com download realçado](common/certificatebase64.png)

6. Você pode fazer com que o Azure AD configure automaticamente o ServiceNow para autenticação baseada em SAML. Para habilitar esse serviço, vá para a seção **Configurar o ServiceNow** e selecione **exibir instruções** passo a passo para abrir a janela **Configurar logon** .

    ![Captura de tela da seção configurar ServiceNow, com a exibição de instruções passo a passo realçada](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. No formulário **Configurar logon** , insira o nome da instância do ServiceNow, o nome de usuário do administrador e a senha do administrador. Selecione **Configurar agora**. O nome de usuário do administrador fornecido deve ter a função **Security_Admin** atribuída no ServiceNow para que isso funcione. Caso contrário, para configurar manualmente o ServiceNow para usar o Azure AD como um provedor de identidade SAML, selecione **configurar manualmente o logon único**. Copie a **URL de logout, o identificador do Azure AD e a URL de logon** da seção referência rápida.

    ![Captura de tela de configurar formulário de logon, com configurar agora realçado](./media/servicenow-tutorial/configure.png "Configurar URL do aplicativo")

## <a name="configure-servicenow"></a>Configurar o ServiceNow

1. Faça logon em seu aplicativo ServiceNow como administrador.

2. Ative o plug-in de **instalação de logon único do provedor de integração múltipla** seguindo estas etapas:

    a. No painel esquerdo, procure a seção **definição do sistema** na caixa de pesquisa e, em seguida, selecione **plug-ins**.

    ![Captura de tela da seção de definição do sistema, com definição do sistema e plugins realçados](./media/servicenow-tutorial/tutorial_servicenow_03.png "Ativar plug-in")

    b. Procurar **integração – instalador de logon único de vários provedores**.

     ![Captura de tela da página plug-ins do sistema, com a integração – instalador de logon único de vários provedores realçado](./media/servicenow-tutorial/tutorial_servicenow_04.png "Ativar plug-in")

    c. Selecione o plug-in. Clique com o botão direito do mouse e selecione **Ativar/atualizar**.

     ![Captura de tela do menu de atalho do plug-in, com ativar/atualizar realçado](./media/servicenow-tutorial/tutorial_activate.png "Ativar plug-in")

    d. Selecione **Ativar**.

     ![Captura de tela da caixa de diálogo ativar plug-in, com ativar realçado](./media/servicenow-tutorial/tutorial_activate1.png "Ativar plug-in")

3. No painel esquerdo, procure a seção **SSO de vários provedores** na barra de pesquisa e, em seguida, selecione **Propriedades**.

    ![Captura de tela da seção de SSO de vários provedores, com SSO e propriedades de vários provedores realçadas](./media/servicenow-tutorial/tutorial_servicenow_06.png "Configurar URL do aplicativo")

4. Na caixa de diálogo **Propriedades de SSO de vários provedores** , execute as seguintes etapas:

    ![Captura de tela da caixa de diálogo Propriedades de SSO de vários provedores](./media/servicenow-tutorial/ic7694981.png "Configurar URL do aplicativo")

    * Para **habilitar o SSO de vários provedores**, selecione **Sim**.
  
    * Para **habilitar a importação automática de usuários de todos os provedores de identidade para a tabela de usuário**, selecione **Sim**.

    * Para **habilitar o log de depuração para a integração de SSO de vários provedores**, selecione **Sim**.

    * Para **o campo na tabela de usuário que...** , insira **user_name**.
  
    * Selecione **Guardar**.

6. Você pode configurar o ServiceNow automaticamente ou manualmente. Para configurar o ServiceNow automaticamente, siga estas etapas:

    1. Retorne à página de logon único do **ServiceNow** no portal do Azure.

    1. O serviço de configuração de um clique é fornecido para o ServiceNow. Para habilitar esse serviço, vá para a seção de **configuração do servicenow** e selecione **Configurar o servicenow** para abrir a janela **Configurar logon** .

        ![Captura de tela de configurar o ServiceNow, com a exibição de instruções passo a passo realçadas](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    1. No formulário **Configurar logon** , insira o nome da instância do ServiceNow, o nome de usuário do administrador e a senha do administrador. Selecione **Configurar agora**. O nome de usuário do administrador fornecido deve ter a função **Security_Admin** atribuída no ServiceNow para que isso funcione. Caso contrário, para configurar manualmente o ServiceNow para usar o Azure AD como um provedor de identidade SAML, selecione **configurar manualmente o logon único**. Copie a **URL de saída, a ID da entidade SAML e a URL do serviço de logon único SAML** da seção de referência rápida.

        ![Captura de tela de configurar formulário de logon, com configurar agora realçado](./media/servicenow-tutorial/configure.png "Configurar URL do aplicativo")

    1. Faça logon em seu aplicativo ServiceNow como administrador.

       * Na configuração automática, todas as configurações necessárias são configuradas no lado do **ServiceNow** , mas o **certificado X. 509** não está habilitado por padrão. Você precisa mapeá-lo manualmente para seu provedor de identidade no ServiceNow. Siga estes passos.

         1. No painel esquerdo, procure a seção **SSO de vários provedores** na caixa de pesquisa e selecione **provedores de identidade**.

            ![Captura de tela da seção de SSO de vários provedores, com provedores de identidade realçado](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configurar o início de sessão único")

         1. Selecione o provedor de identidade gerado automaticamente.

            ![Captura de tela de provedores de identidade, com o provedor de identidade gerado automaticamente realçado](./media/servicenow-tutorial/tutorial_servicenow_08.png "Configurar o início de sessão único")

         1.  Na seção **provedor de identidade** , execute as seguintes etapas:

             ![Captura de tela da seção do provedor de identidade](./media/servicenow-tutorial/automatic_config.png "Configurar o início de sessão único")

               * Para **nome**, insira um nome para a sua configuração (por exemplo, **Microsoft Azure logon único federado**).

               * Remova o valor de **SingleLogoutRequest do provedor de identidade** preenchido da caixa de texto.

               * Copie o valor da **Home Page do servicenow** e cole-o na **URL de logon** na seção de **configuração de SAML básica do servicenow** do portal do Azure.

                  > [!NOTE]
                  > A Home Page da instância do ServiceNow é uma concatenação de sua **URL de locatário do servicenow** e **/navpage.do** (por exemplo:`https://fabrikam.service-now.com/navpage.do`).

              * Copie a **ID da entidade/** valor do emissor e cole-o no **identificador** na seção de **configuração de SAML básica do ServiceNow** do portal do Azure.

              * Confirme se a **política NameID** está definida para `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` valor. 

         1. Role para baixo até a seção **certificado X. 509** e selecione **Editar**.

             ![Captura de tela da seção do certificado X. 509, com editar realçado](./media/servicenow-tutorial/tutorial_servicenow_09.png "Configurar o início de sessão único")

         1. Selecione o certificado e selecione o ícone de seta para a direita para adicionar o certificado

            ![Captura de tela da coleção, com o certificado e o ícone de seta à direita realçados](./media/servicenow-tutorial/tutorial_servicenow_11.png "Configurar o início de sessão único")

          1. Selecione **Guardar**.

          1. No canto superior direito da página, selecione **testar conexão**.

             ![Captura de tela da página, com conexão de teste realçada](./media/servicenow-tutorial/tutorial_activate2.png "Ativar plug-in")

          1. Quando solicitado por suas credenciais, insira-as. Você verá a página a seguir. O erro de **resultados de teste de logoff de SSO** é esperado. Ignore o erro e selecione **Ativar**.

             ![Captura de tela da página de Resultados de Teste](./media/servicenow-tutorial/servicenowactivate.png "Configurar o início de sessão único")
  
6. Para configurar o **ServiceNow** manualmente, siga estas etapas:

    1. Faça logon em seu aplicativo ServiceNow como administrador.

    1. No painel esquerdo, selecione **provedores de identidade**.

        ![Captura de tela do SSO de vários provedores, com provedores de identidade realçado](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configurar o início de sessão único")

    1. Na caixa de diálogo **provedores de identidade** , selecione **novo**.

        ![Captura de tela da caixa de diálogo provedores de identidade, com novo realçado](./media/servicenow-tutorial/ic7694977.png "Configurar o início de sessão único")

    1. Na caixa de diálogo **provedores de identidade** , selecione **SAML**.

        ![Captura de tela da caixa de diálogo provedores de identidade com SAML realçado](./media/servicenow-tutorial/ic7694978.png "Configurar o início de sessão único")

    1. Em **importar metadados do provedor de identidade**, execute as seguintes etapas:

        ![Captura de tela de metadados do provedor de identidade de importação, com URL e importação realçadas](./media/servicenow-tutorial/idp.png "Configurar o início de sessão único")

        1. Insira a **URL de metadados de Federação do aplicativo** que você copiou do portal do Azure.

        1. Selecione **Import** (Importar).

    1. Ele lê a URL de metadados do IdP e popula todas as informações de campos.

        ![Captura de tela do provedor de identidade](./media/servicenow-tutorial/ic7694982.png "Configurar o início de sessão único")

        * Para **nome**, insira um nome para a sua configuração (por exemplo, **Microsoft Azure logon único federado**).

        * Remova o valor de **SingleLogoutRequest do provedor de identidade** preenchido da caixa de texto.

        * Copie o valor da **Home Page do ServiceNow** . Cole-o na **URL de logon** na seção de **configuração SAML básica do ServiceNow** do portal do Azure.

            > [!NOTE]
            > A Home Page da instância do ServiceNow é uma concatenação de sua **URL de locatário do servicenow** e **/navpage.do** (por exemplo:`https://fabrikam.service-now.com/navpage.do`).

        * Copie a **ID da entidade/** valor do emissor. Cole-o no **identificador** na seção de **configuração SAML básica do ServiceNow** do portal do Azure.

        * Confirme se a **política NameID** está definida para `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` valor.

        * Selecione **Avançadas**. No **campo usuário**, insira **email** ou **user_name**, dependendo de qual campo é usado para identificar exclusivamente os usuários em sua implantação do ServiceNow.

            > [!NOTE]
            > Você pode configurar o Azure AD para emitir a ID de usuário do Azure AD (nome principal do usuário) ou o endereço de email como o identificador exclusivo no token SAML. Para fazer isso, vá para a seção **atributos** do **ServiceNow** >  > de **logon único** do portal do Azure e mapeie o campo desejado para o atributo **nameidentifier** . O valor armazenado para o atributo selecionado no Azure AD (por exemplo, nome principal do usuário) deve corresponder ao valor armazenado no ServiceNow para o campo inserido (por exemplo, user_name).

        * Selecione **testar conexão** no canto superior direito da página.

        * Quando solicitado por suas credenciais, insira-as. Você verá a página a seguir. O erro de **resultados de teste de logoff de SSO** é esperado. Ignore o erro e selecione **Ativar**.

          ![Captura de tela da página de Resultados de Teste](./media/servicenow-tutorial/servicenowactivate.png "Configurar o início de sessão único")

### <a name="create-servicenow-test-user"></a>Criar usuário de teste do ServiceNow

O objetivo desta seção é criar um usuário chamado B. Simon no ServiceNow. O ServiceNow dá suporte ao provisionamento automático de usuário, que é habilitado por padrão.

> [!NOTE]
> Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte ao cliente do ServiceNow](https://www.servicenow.com/support/contact-support.html).

### <a name="configure-servicenow-express-sso"></a>Configurar o SSO do ServiceNow Express

1. Faça logon em seu aplicativo ServiceNow Express como administrador.

2. No painel esquerdo, selecione **logon único**.

    ![Captura de tela do aplicativo ServiceNow Express, com logon único realçado](./media/servicenow-tutorial/ic7694980ex.png "Configurar URL do aplicativo")

3. Na caixa de diálogo **logon único** , selecione o ícone de configuração no canto superior direito e defina as seguintes propriedades:

    ![Captura de tela da caixa de diálogo logon único](./media/servicenow-tutorial/ic7694981ex.png "Configurar URL do aplicativo")

    a. Alterne **habilitar SSO de vários provedores** à direita.

    b. Alterne **habilitar log de depuração para a integração de SSO de vários provedores** à direita.

    c. No **campo da tabela de usuário que...** , insira **user_name**.

4. Na caixa de diálogo **logon único** , selecione **Adicionar novo certificado**.

    ![Captura de tela da caixa de diálogo logon único, com Adicionar novo certificado realçado](./media/servicenow-tutorial/ic7694973ex.png "Configurar o início de sessão único")

5. Na caixa de diálogo **certificados X. 509** , execute as seguintes etapas:

    ![Captura de tela da caixa de diálogo certificados X. 509](./media/servicenow-tutorial/ic7694975.png "Configurar o início de sessão único")

    a. Para **nome**, insira um nome para sua configuração (por exemplo: **testsaml 2.0**).

    b. Selecione **ativo**.

    c. Para **formato**, selecione **PEM**.

    d. Para **tipo**, selecione **confiar no certificado de repositório**.

    e. Abra o certificado codificado em base64 baixado de portal do Azure no bloco de notas. Copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **certificado PEM** .

    f. Selecionar **atualização**

6. Na caixa de diálogo **logon único** , selecione **Adicionar novo IDP**.

    ![Captura de tela da caixa de diálogo logon único, com Adicionar novo IdP realçado](./media/servicenow-tutorial/ic7694976ex.png "Configurar o início de sessão único")

7. Na caixa de diálogo **Adicionar novo provedor de identidade** , em **Configurar provedor de identidade**, execute as seguintes etapas:

    ![Captura de tela da caixa de diálogo Adicionar novo provedor de identidade](./media/servicenow-tutorial/ic7694982ex.png "Configurar o início de sessão único")

    a. Para **nome**, insira um nome para sua configuração (por exemplo: **SAML 2,0**).

    b. Para **URL do provedor de identidade**, Cole o valor da ID do provedor de identidade que você copiou do portal do Azure.

    c. Para o **AuthnRequest do provedor de identidade**, Cole o valor da URL de solicitação de autenticação que você copiou do portal do Azure.

    d. Para o **SingleLogoutRequest do provedor de identidade**, Cole o valor da URL de logout que você copiou do portal do Azure.

    e. Para **certificado do provedor de identidade**, selecione o certificado que você criou na etapa anterior.

8. Selecione **Configurações avançadas**. Em **Propriedades adicionais do provedor de identidade**, execute as seguintes etapas:

    ![Captura de tela da caixa de diálogo Adicionar novo provedor de identidade, com configurações avançadas realçadas](./media/servicenow-tutorial/ic7694983ex.png "Configurar o início de sessão único")

    a. Para **a associação de protocolo para o SingleLogoutRequest do IDP**, insira **urn: Oasis: names: TC: SAML: 2.0: Bindings: http-Redirect**.

    b. Para a **política NameID**, insira **urn: Oasis: names: TC: SAML: 1.1: NameID-Format: não especificado**.

    c. Para o **método AuthnContextClassRef**, insira `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Para **criar um AuthnContextClass**, alterne-o para desativado (desmarcado).

9. Em **Propriedades adicionais do provedor de serviços**, execute as seguintes etapas:

    ![Captura de tela da caixa de diálogo Adicionar novo provedor de identidade, com várias propriedades realçadas](./media/servicenow-tutorial/ic7694984ex.png "Configurar o início de sessão único")

    a. Para a **Home Page do servicenow**, insira a URL da sua página inicial da instância do ServiceNow.

    > [!NOTE]
    > A Home Page da instância do ServiceNow é uma concatenação de sua **URL de locatário do servicenow** e **/navpage.do** (por exemplo: `https://fabrikam.service-now.com/navpage.do`).

    b. Para **ID da entidade/emissor**, insira a URL do seu locatário do ServiceNow.

    c. Para **URI de público-alvo**, insira a URL do seu locatário do ServiceNow.

    d. Para **distorção de relógio**, insira **60**.

    e. Para o **campo usuário**, insira **email** ou **user_name**, dependendo de qual campo é usado para identificar exclusivamente os usuários em sua implantação do ServiceNow.

    > [!NOTE]
    > Você pode configurar o Azure AD para emitir a ID de usuário do Azure AD (nome principal do usuário) ou o endereço de email como o identificador exclusivo no token SAML. Para fazer isso, vá para a seção **atributos** do **ServiceNow** >  > de **logon único** do portal do Azure e mapeie o campo desejado para o atributo **nameidentifier** . O valor armazenado para o atributo selecionado no Azure AD (por exemplo, nome principal do usuário) deve corresponder ao valor armazenado no ServiceNow para o campo inserido (por exemplo, user_name).

    f. Selecione **Guardar**.

## <a name="test-sso"></a>Testar SSO

Ao selecionar o bloco do ServiceNow no painel de acesso, você deverá ser conectado automaticamente ao ServiceNow para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-servicenow-classic-mobile"></a>Testar SSO para ServiceNow clássico (móvel)

1. Abra seu aplicativo do **ServiceNow clássico (móvel)** e execute as seguintes etapas:

    a. Selecione o sinal de adição no canto inferior direito.

    ![Captura de tela do aplicativo do ServiceNow clássico, com sinal de adição realçado](./media/servicenow-tutorial/test03.png)

    b. Insira o nome da instância do ServiceNow e selecione **continuar**.

    ![Captura de tela da página Adicionar instância, com continuar realçado](./media/servicenow-tutorial/test04.png)

    c. Na página de **logon** , execute as seguintes etapas:

    ![Captura de tela da página de logon, com usar o logon externo realçado](./media/servicenow-tutorial/test01.png)

    *  Insira o **nome de usuário**, como B.simon@contoso.com.

    *  Selecione **usar logon externo**. Você será redirecionado para a página do Azure AD para entrada.
    
    *  Introduza as suas credenciais. Se houver alguma autenticação de terceiros ou qualquer outro recurso de segurança habilitado, o usuário deverá responder de acordo. A **Home Page** do aplicativo é exibida.

        ![Captura de tela do home page do aplicativo](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar provisionamento de usuário](servicenow-provisioning-tutorial.md)

- [Experimente o ServiceNow com o Azure AD](https://aad.portal.azure.com)

- [O que é o controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/protect-servicenow)

- [Como proteger o ServiceNow com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
