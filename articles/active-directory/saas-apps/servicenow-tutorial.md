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
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c18613233d6dec59c76db120ed7f089dfbb5fbac
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046727"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o ServiceNow

Neste tutorial, você aprenderá a integrar o ServiceNow ao Azure Active Directory (Azure AD). Ao integrar o ServiceNow ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao ServiceNow.
* Habilite seus usuários a serem conectados automaticamente ao ServiceNow com suas contas do Azure AD.
* Gerencie suas contas em um local central: o portal do Azure.

Para saber mais sobre software como uma integração de aplicações de serviço (SaaS) com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura habilitada para SSO (logon único) do ServiceNow.
* Para o ServiceNow, uma instância ou locatário do ServiceNow, versão Calgary ou posterior.
* Para o ServiceNow Express, uma instância do ServiceNow Express, a versão Helsinque ou posterior.
* O inquilino ServiceNow deve ter o sinal único do [fornecedor múltiplo no Plugin.](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) Pode fazê-lo [submetendo um pedido](https://hi.service-now.com)de serviço.
* Para configuração automática, habilite o plug-in de vários provedores para o ServiceNow.
* Para instalar o aplicativo do ServiceNow clássico (móvel), vá para o repositório apropriado e procure o aplicativo do ServiceNow clássico. Em seguida, baixe-o.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. 

* ServiceNow suporta **SP** iniciado SSO.

* ServiceNow suporta [fornecimento automatizado de utilizadores](servicenow-provisioning-tutorial.md).

* Assim que configurar o ServiceNow, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Controlo de Sessão estende-se a partir de Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

* Você pode configurar o aplicativo do ServiceNow clássico (móvel) com o Azure AD para habilitar o SSO. Ele dá suporte a usuários Android e iOS. Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

## <a name="add-servicenow-from-the-gallery"></a>Adicionar o ServiceNow da Galeria

Para configurar a integração do ServiceNow ao Azure AD, você precisará adicionar o ServiceNow da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou de escola, ou utilizando uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione o serviço **de Diretório Ativo Azure.**
1. Vá a **Aplicações Empresariais**e selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** introduza **ServiceNow** na caixa de pesquisa.
1. Selecione **ServiceNow** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-servicenow"></a>Configurar e testar o logon único do Azure AD para o ServiceNow

Configure e teste Azure AD SSO com ServiceNow utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no ServiceNow.

Para configurar e testar o SSO do Azure AD com o ServiceNow, conclua os seguintes blocos de construção:

1. [Configure O SSO AD Azure](#configure-azure-ad-sso) para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. [Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user) para testar o único sinal de Azure AD com B.Simon.
    1. [Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que b.Simon utilize um único sinal de AD Azure.
    1. [Configure Azure AD SSO para serviceNow Express](#configure-azure-ad-sso-for-servicenow-express) para permitir que os seus utilizadores utilizem esta funcionalidade.
2. [Configure](#configure-servicenow) o Serviço Agora para configurar as definições SSO no lado da aplicação.
    1. [Crie um utilizador](#create-servicenow-test-user) de teste ServiceNow para ter uma contraparte de B.Simon no ServiceNow, ligado à representação azure AD do utilizador.
    1. [Configure ServiceNow Express SSO](#configure-servicenow-express-sso) para configurar as definições de inscrição únicas no lado da aplicação.  
3. [Teste sSO](#test-sso) para verificar se a configuração funciona.
4. [Teste SSO para ServiceNow Classic (Mobile)](#test-sso-for-servicenow-classic-mobile) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **ServiceNow,** encontre a secção **Gerir.** Selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set set single sign-on com** a página SAML, selecione o ícone da caneta para **configuração SAML básica** para editar as definições.

   ![Captura de tela da página Configurar logon único com SAML, com o ícone de caneta realçado](common/edit-urls.png)

4. Na secção **De Configuração SAML Básica,** execute os seguintes passos:

    a. Em **Sign on URL,** introduza um URL que utilize o seguinte padrão: `https://<instance-name>.service-now.com/navpage.do`

    b. No **Identificador (ID da Entidade)** introduza um URL que utilize o seguinte padrão: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de entrada e o identificador reais, que são explicados posteriormente no tutorial. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. No **set set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** encontre **certificado (Base64)** . 

   ![Captura de tela da seção certificado de autenticação SAML, com download realçado](common/certificatebase64.png)

   a. Selecione o botão de cópia para copiar o Url de **Metadados da Federação**de Aplicações e cole-o no Bloco de Notas. Essa URL será usada posteriormente no tutorial.

    b. Selecione **Download** para baixar **Certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

1. Na secção **set up ServiceNow,** copie os URLs apropriados, com base na sua exigência.

   ![Captura de tela da seção configurar ServiceNow, com URLs realçadas](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste, chamado B. Simon, na portal do Azure.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory** > **Utilizadores** > Todos **os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. Para **Nome,** insira `B.Simon`.  
   1. Para **o nome de utilizador,** introduza o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione **mostrar palavra-passe**e, em seguida, anote o valor que é mostrado na caixa **password.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao ServiceNow.

1. No portal Azure, selecione **Aplicações Empresariais** > **Todas as aplicações.**
1. Na lista de aplicações, selecione **ServiceNow**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![Captura de tela da seção Gerenciar, com usuários e grupos realçados](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Na caixa de diálogo **Adicionar Atribuição,** selecione **Utilizadores e grupos**.

    ![Captura de tela de usuários e grupos, com Adicionar usuário realçado](common/add-assign-user.png)

1. Na caixa de diálogo **De utilizadores e grupos,** selecione **B.Simon** na lista de utilizadores e, em seguida, escolha **Select**.
1. Se estiver à espera de qualquer valor de papel na afirmação do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, escolha **Selecionar**.
1. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Configurar o SSO do Azure AD para o ServiceNow Express

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **ServiceNow,** selecione **um único sinal.**

    ![Captura de tela da página de integração de aplicativos do ServiceNow, com logon único realçado](common/select-sso.png)

2. Na **seleta de uma única** caixa de diálogo de método de sinalização, selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Captura de tela de selecionar um método de logon único, com SAML realçado](common/select-saml-option.png)

3. No **set set single sign-on com** a página SAML, selecione o ícone da caneta para abrir a caixa de diálogo **de configuração Básica SAML.**

    ![Captura de tela da página Configurar logon único com SAML, com o ícone de caneta realçado](common/edit-urls.png)

4. Na secção **De Configuração SAML Básica,** execute os seguintes passos:

    a. Para **iniciar sessão no URL,** introduza um URL que utilize o seguinte padrão: `https://<instance-name>.service-now.com/navpage.do`

    b. Para **identificador (ID da entidade)** introduza um URL que utilize o seguinte padrão: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de entrada e o identificador reais, que são explicados posteriormente no tutorial. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** selecione **Download** para descarregar o **Certificado (Base64)** das opções especificadas, conforme o seu requisito. Salve-o em seu computador.

    ![Captura de tela da seção certificado de autenticação SAML, com download realçado](common/certificatebase64.png)

6. Você pode fazer com que o Azure AD configure automaticamente o ServiceNow para autenticação baseada em SAML. Para ativar este serviço, vá à secção **'Configurar ServiceNow'** e selecione **Ver instruções passo a passo** para abrir a janela de **sinalização Configure.**

    ![Captura de tela da seção configurar ServiceNow, com a exibição de instruções passo a passo realçada](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. No formulário de **início de sessão Configure,** introduza o nome da sua instância ServiceNow, o nome de utilizador do administrador e a palavra-passe de administrador. **Selecione Configurar agora**. O nome de utilizador administrativo fornecido deve ter a **função security_admin** atribuída no ServiceNow para que este funcione. Caso contrário, para configurar manualmente o ServiceNow para utilizar o Azure AD como fornecedor de identidade SAML, **selecione manualmente configurar um único sinal**. Copie o URL de **Logout, o Identificador AD Azure e** o URL de Login da secção De Referência Rápida.

    ![Screenshot do formulário de inscrição configure, com Configure Now em destaque](./media/servicenow-tutorial/configure.png "Configurar URL do aplicativo")

## <a name="configure-servicenow"></a>Configurar o ServiceNow

1. Faça logon em seu aplicativo ServiceNow como administrador.

2. Ativar a **Integração - Multiple Provider single sign-on Installer plug-in** seguindo estes passos:

    a. No painel esquerdo, procure a secção **Definição** do Sistema a partir da caixa de pesquisa e, em seguida, selecione **Plugins**.

    ![Screenshot da secção Definição de Sistema, com Definição de Sistema e Plugins em destaque](./media/servicenow-tutorial/tutorial_servicenow_03.png "Ativar plug-in")

    b. Pesquisa por **Integração - Fornecedor único fornecedor único instalador**.

     ![Screenshot da página plugins do sistema, com integração - Instalador de sinal único de fornecedor múltiplo destacado](./media/servicenow-tutorial/tutorial_servicenow_04.png "Ativar plug-in")

    c. Selecione o plug-in. Clique à direita e selecione **Ativar/Atualizar**.

     ![Screenshot do menu plug-in right click, com Activate/Upgrade destacado](./media/servicenow-tutorial/tutorial_activate.png "Ativar plug-in")

    d. **Selecione Ativar**.

     ![Screenshot da caixa de diálogo Activate Plugin, com Activate destacado](./media/servicenow-tutorial/tutorial_activate1.png "Ativar plug-in")

3. No painel esquerdo, procure a secção **Multi-Provider SSO** a partir da barra de pesquisa e, em seguida, selecione **Propriedades**.

    ![Screenshot da secção Multi-Provider SSO, com Multi-Provider SSO e Propriedades em destaque](./media/servicenow-tutorial/tutorial_servicenow_06.png "Configurar URL do aplicativo")

4. Na caixa de diálogo **Multiple Provider SSO Properties,** execute os seguintes passos:

    ![Screenshot da caixa de diálogo SSO Properties do fornecedor múltiplo](./media/servicenow-tutorial/ic7694981.png "Configurar URL do aplicativo")

    * Para **ativar vários fornecedores SSO,** selecione **Sim**.
  
    * Para **ativar a importação automática de utilizadores de todos os fornecedores de identidade para a tabela de utilizadores,** selecione **Sim**.

    * Para **ativar o registo de depuração para a integração SSO de vários fornecedores,** selecione **Sim**.

    * Para **o campo na tabela de utilizadores que...** , entre **user_name**.
  
    * Selecione **Guardar**.

6. Você pode configurar o ServiceNow automaticamente ou manualmente. Para configurar o ServiceNow automaticamente, siga estas etapas:

    1. Volte à página de inscrição individual **serviceNow** no portal Azure.

    1. O serviço de configuração de um clique é fornecido para o ServiceNow. Para ativar este serviço, vá à secção de **Configuração ServiceNow** e selecione **Configurar O Serviço Agora** para abrir a janela **de início de 'Configurar'.**

        ![Captura de tela de configurar o ServiceNow, com a exibição de instruções passo a passo realçadas](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    1. No formulário de **início de sessão Configure,** introduza o nome da sua instância ServiceNow, o nome de utilizador do administrador e a palavra-passe de administrador. **Selecione Configurar agora**. O nome de utilizador administrativo fornecido deve ter a **função security_admin** atribuída no ServiceNow para que este funcione. Caso contrário, para configurar manualmente o ServiceNow para utilizar o Azure AD como fornecedor de identidade SAML, **selecione manualmente configurar um único sinal**. Copie o URL de **Inscrição, id da entidade SAML e URL** de serviço de inscrição única SAML da secção De Referência Rápida.

        ![Screenshot do formulário de inscrição configure, com Configure Now em destaque](./media/servicenow-tutorial/configure.png "Configurar URL do aplicativo")

    1. Faça logon em seu aplicativo ServiceNow como administrador.

       * Na configuração automática, todas as configurações necessárias são configuradas no lado **ServiceNow,** mas o **Certificado X.509** não está ativado por padrão. Você precisa mapeá-lo manualmente para seu provedor de identidade no ServiceNow. Siga estes passos.

         1. No painel esquerdo, procure a secção **SSO multi-fornecedor** a partir da caixa de pesquisa e selecione **Fornecedores**de Identidade .

            ![Screenshot da secção Multi-Provider SSO, com Fornecedores de Identidade destacados](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configurar o início de sessão único")

         1. Selecione o provedor de identidade gerado automaticamente.

            ![Screenshot dos fornecedores de identidade, com fornecedor de identidade gerado automaticamente em destaque](./media/servicenow-tutorial/tutorial_servicenow_08.png "Configurar o início de sessão único")

         1.  Na secção **Fornecedor de Identidade,** execute os seguintes passos:

             ![Screenshot da secção Fornecedor de Identidade](./media/servicenow-tutorial/automatic_config.png "Configurar o início de sessão único")

               * Para **Nome**, introduza um nome para a sua configuração (por exemplo, **Microsoft Azure Federado único sinal on**).

               * Retire do **singleLogoutRequest do Fornecedor** de Identidade preenchido o valor singleLogoutRequest da caixa de texto.

               * Copie o valor da **página inicial do ServiceNow** e **cole-o** em URL de início de cartaz na secção de **configuração SAML Básica serviceNow** do portal Azure.

                  > [!NOTE]
                  > A página inicial da instância ServiceNow é uma concatenação do seu URL de **inquilino ServiceNow** e **/navpage.do** (por exemplo:`https://fabrikam.service-now.com/navpage.do`).

              * Copie o valor **id/emitente da Entidade** e cole-o no **Identificador** na secção de **Configuração SAML Básica serviceNow** do portal Azure.

              * Confirme que a **Política nameID** está definida para `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` valor. 

         1. Desloque-se até à secção **de Certificado X.509** e selecione **Editar**.

             ![Screenshot da secção de Certificado X.509, com Editar em destaque](./media/servicenow-tutorial/tutorial_servicenow_09.png "Configurar o início de sessão único")

         1. Selecione o certificado e selecione o ícone de seta para a direita para adicionar o certificado

            ![Screenshot da Coleção, com certificado e ícone de seta direita em destaque](./media/servicenow-tutorial/tutorial_servicenow_11.png "Configurar o início de sessão único")

          1. Selecione **Guardar**.

          1. No canto superior direito da página, selecione **Test Connection**.

             ![Screenshot da página, com Ligação de Teste em destaque](./media/servicenow-tutorial/tutorial_activate2.png "Ativar plug-in")

          1. Quando solicitado por suas credenciais, insira-as. Você verá a página a seguir. Espera-se o erro dos resultados do teste de **logout SSO.** Ignore o erro e selecione **Ativar**.

             ![Screenshot da página de resultados de testes](./media/servicenow-tutorial/servicenowactivate.png "Configurar o início de sessão único")
  
6. Para configurar o **ServiceNow** manualmente, siga estes passos:

    1. Faça logon em seu aplicativo ServiceNow como administrador.

    1. No painel esquerdo, selecione **Fornecedores de Identidade**.

        ![Screenshot de Multi-Provider SSO, com Fornecedores de Identidade em destaque](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configurar o início de sessão único")

    1. Na caixa de diálogo Fornecedores de **Identidade,** selecione **New**.

        ![Screenshot da caixa de diálogo de Fornecedores de Identidade, com novo destaque](./media/servicenow-tutorial/ic7694977.png "Configurar o início de sessão único")

    1. Na caixa de diálogo Fornecedores de **Identidade,** selecione **SAML**.

        ![Screenshot da caixa de diálogo de Fornecedores de Identidade, com SAML em destaque](./media/servicenow-tutorial/ic7694978.png "Configurar o início de sessão único")

    1. Nos Metadados do Fornecedor de **Identidade de Importação,** execute os seguintes passos:

        ![Screenshot de Metadados do Fornecedor de Identidade de Importação, com URL e Importação em destaque](./media/servicenow-tutorial/idp.png "Configurar o início de sessão único")

        1. Introduza o Url de Metadados da Federação de **Aplicações** que copiou do portal Azure.

        1. Selecione **Import**.

    1. Ele lê a URL de metadados do IdP e popula todas as informações de campos.

        ![Screenshot do Fornecedor de Identidade](./media/servicenow-tutorial/ic7694982.png "Configurar o início de sessão único")

        * Para **Nome**, introduza um nome para a sua configuração (por exemplo, **Microsoft Azure Federado único sinal on**).

        * Retire do **singleLogoutRequest do Fornecedor** de Identidade povoado o valor singleLogoutRequest da caixa de texto.

        * Copie o valor **da página inicial do ServiceNow.** **Colá-lo** em URL de início de placa na secção de **configuração SAML Básica serviceNow** do portal Azure.

            > [!NOTE]
            > A página inicial da instância ServiceNow é uma concatenação do seu URL de **inquilino ServiceNow** e **/navpage.do** (por exemplo:`https://fabrikam.service-now.com/navpage.do`).

        * Copiar o **valor ID/Emitente da Entidade.** Cola-o no **Identificador** no **Serviço Now Basic SAML Secção** de Configuração do portal Azure.

        * Confirme que a **Política nameID** está definida para `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` valor.

        * Selecione **Avançadas**. No **Campo do Utilizador,** introduza **e-mail** ou **user_name,** dependendo do campo utilizado para identificar exclusivamente os utilizadores na sua implementação ServiceNow.

            > [!NOTE]
            > Você pode configurar o Azure AD para emitir a ID de usuário do Azure AD (nome principal do usuário) ou o endereço de email como o identificador exclusivo no token SAML. Faça isso indo para o **ServiceNow** > **Atributos** > **secção de inscrição única** do portal Azure, e mapeando o campo desejado para o atributo identificador de **nome.** O valor armazenado para o atributo selecionado em Azure AD (por exemplo, nome principal do utilizador) deve corresponder ao valor armazenado no ServiceNow para o campo introduzido (por exemplo, user_name).

        * Selecione **ligação** de teste no canto superior direito da página.

        * Quando solicitado por suas credenciais, insira-as. Você verá a página a seguir. Espera-se o erro dos resultados do teste de **logout SSO.** Ignore o erro e selecione **Ativar**.

          ![Screenshot da página de resultados de testes](./media/servicenow-tutorial/servicenowactivate.png "Configurar o início de sessão único")

### <a name="create-servicenow-test-user"></a>Criar usuário de teste do ServiceNow

O objetivo desta seção é criar um usuário chamado B. Simon no ServiceNow. O ServiceNow dá suporte ao provisionamento automático de usuário, que é habilitado por padrão.

> [!NOTE]
> Se precisar de criar um utilizador manualmente, contacte a equipa de suporte ao [Cliente ServiceNow](https://www.servicenow.com/support/contact-support.html).

### <a name="configure-servicenow-express-sso"></a>Configurar o SSO do ServiceNow Express

1. Faça logon em seu aplicativo ServiceNow Express como administrador.

2. No painel esquerdo, selecione **Single Sign-On**.

    ![Screenshot da aplicação ServiceNow Express, com um único sign-on em destaque](./media/servicenow-tutorial/ic7694980ex.png "Configurar URL do aplicativo")

3. Na caixa de diálogo **Single Sign-On,** selecione o ícone de configuração na parte superior direita e detete as seguintes propriedades:

    ![Screenshot da caixa de diálogo de início de sinal único](./media/servicenow-tutorial/ic7694981ex.png "Configurar URL do aplicativo")

    a. Toggle **Enable multiple provider SSO** à direita.

    b. Toggle **Enable debuglogging para a integração SSO do fornecedor múltiplo** à direita.

    c. No **campo na tabela de utilizadores que... entram** **user_name**.

4. Na caixa de diálogo **Single Sign-On,** selecione **Adicionar novo certificado**.

    ![Screenshot da caixa de diálogo de início de sessão individual, com adicionar novo certificado em destaque](./media/servicenow-tutorial/ic7694973ex.png "Configurar o início de sessão único")

5. Na caixa de diálogo **X.509 Certificates,** execute os seguintes passos:

    ![Screenshot da caixa de diálogo de certificados X.509](./media/servicenow-tutorial/ic7694975.png "Configurar o início de sessão único")

    a. Para **Nome**, introduza um nome para a sua configuração (por exemplo: **TestSAML2.0**).

    b. Selecione **Ative**.

    c. Para **formato,** selecione **PEM**.

    d. Para **Tipo,** selecione **Trust Store Cert**.

    e. Abra o certificado codificado em base64 baixado de portal do Azure no bloco de notas. Copie o conteúdo do mesmo na sua pasta e, em seguida, cole-o na caixa de texto **do Certificado PEM.**

    f. Selecione **Atualização**

6. Na caixa de diálogo **Single Sign-On,** selecione **Adicionar novo IDP**.

    ![Screenshot da caixa de diálogo de início de sessão individual, com adicionar novo IDP em destaque](./media/servicenow-tutorial/ic7694976ex.png "Configurar o início de sessão único")

7. Na caixa de diálogo **Add New Identity Provider,** no âmbito do **Configure Identity Provider,** execute os seguintes passos:

    ![Screenshot de adicionar nova caixa de diálogo fornecedor de identidade](./media/servicenow-tutorial/ic7694982ex.png "Configurar o início de sessão único")

    a. Para **Nome**, introduza um nome para a sua configuração (por exemplo: **SAML 2.0**).

    b. Para URL do Fornecedor de **Identidade,** colhe o valor do ID do fornecedor de identidade que copiou do portal Azure.

    c. Para o **AuthnRequest do Fornecedor**de Identidade, colhe o valor do URL de pedido de autenticação que copiou do portal Azure.

    d. Para o **SingleLogoutRequest do Fornecedor**de Identidade, colhe o valor do URL de logout que copiou do portal Azure.

    e. Para certificado de **fornecedor de identidade,** selecione o certificado que criou na etapa anterior.

8. Selecione **Definições Avançadas**. No âmbito das **propriedades adicionais**do fornecedor de identidade, execute os seguintes passos:

    ![Screenshot de Adicionar nova caixa de diálogo fornecedor de identidade, com Configurações Avançadas em destaque](./media/servicenow-tutorial/ic7694983ex.png "Configurar o início de sessão único")

    a. Para a vinculação do **protocolo para o SingleLogoutRequest do IDP,** insira **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    b. Para **a política nameID,** insira **urn:oasis:nomes:tc:SAML:1.1:nameid-format:não especificado**.

    c. Para **o método AuthnContextClassRef,** introduza `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Para **criar uma Classe AuthnContext,** alterna-a para desligar (não selecionada).

9. No âmbito das **propriedades adicionais**do prestador de serviços, execute os seguintes passos:

    ![Screenshot de Adicionar nova caixa de diálogo fornecedor de identidade, com várias propriedades em destaque](./media/servicenow-tutorial/ic7694984ex.png "Configurar o início de sessão único")

    a. Para **a página inicial do ServiceNow,** introduza o URL da sua página inicial da sua instância ServiceNow.

    > [!NOTE]
    > A página inicial da instância ServiceNow é uma concatenação do seu URL de **inquilino ServiceNow** e **/navpage.do** (por exemplo: `https://fabrikam.service-now.com/navpage.do`).

    b. Para **ID de Entidade / Emitente,** insira o URL do seu inquilino ServiceNow.

    c. Para **O Público URI,** insira o URL do seu inquilino ServiceNow.

    d. Para **o Tempo Skew,** introduza **60**.

    e. Para o **Campo do Utilizador,** introduza **e-mail** ou **user_name,** dependendo do campo utilizado para identificar exclusivamente os utilizadores na sua implementação ServiceNow.

    > [!NOTE]
    > Você pode configurar o Azure AD para emitir a ID de usuário do Azure AD (nome principal do usuário) ou o endereço de email como o identificador exclusivo no token SAML. Faça isso indo para o **ServiceNow** > **Atributos** > **secção de inscrição única** do portal Azure, e mapeando o campo desejado para o atributo identificador de **nome.** O valor armazenado para o atributo selecionado em Azure AD (por exemplo, nome principal do utilizador) deve corresponder ao valor armazenado no ServiceNow para o campo introduzido (por exemplo, user_name).

    f. Selecione **Guardar**.

## <a name="test-sso"></a>Testar SSO

Ao selecionar o bloco do ServiceNow no painel de acesso, você deverá ser conectado automaticamente ao ServiceNow para o qual você configurou o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="test-sso-for-servicenow-classic-mobile"></a>Testar SSO para ServiceNow clássico (móvel)

1. Abra a sua aplicação **ServiceNow Classic (Mobile)** e execute os seguintes passos:

    a. Selecione o sinal de adição no canto inferior direito.

    ![Captura de tela do aplicativo do ServiceNow clássico, com sinal de adição realçado](./media/servicenow-tutorial/test03.png)

    b. Introduza o nome da instância ServiceNow e selecione **Continuar**.

    ![Captura de tela da página Adicionar instância, com continuar realçado](./media/servicenow-tutorial/test04.png)

    c. Na página **Iniciar** sessão, execute os seguintes passos:

    ![Captura de tela da página de logon, com usar o logon externo realçado](./media/servicenow-tutorial/test01.png)

    *  Introduza **o nome de utilizador,** como B.simon@contoso.com.

    *  Selecione **USE LOGIN EXTERNO**. Você será redirecionado para a página do Azure AD para entrada.
    
    *  Introduza as suas credenciais. Se houver alguma autenticação de terceiros ou qualquer outro recurso de segurança habilitado, o usuário deverá responder de acordo. A **página inicial** da aplicação aparece.

        ![Captura de tela do home page do aplicativo](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar o fornecimento de utilizadores](servicenow-provisioning-tutorial.md)

- [Experimente serviceNow com Azure AD](https://aad.portal.azure.com)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o ServiceNow com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/protect-servicenow)