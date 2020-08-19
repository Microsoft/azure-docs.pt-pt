---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com o ServiceNow Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o ServiceNow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/19/2020
ms.author: jeedes
ms.openlocfilehash: 9748c772466db73fc49b1291ffe2ce7d13da1d8c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88540237"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Tutorial: Azure Ative Directy integração única (SSO) com o ServiceNow

Neste tutorial, você vai aprender a integrar o ServiceNow com o Azure Ative Directory (Azure AD). Quando integra o ServiceNow com AZure AD, pode:

* Controlo em Azure AD que tem acesso ao ServiceNow.
* Ative os seus utilizadores a serem automaticamente inscritos no ServiceNow com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre software como integração de aplicações de serviço (SaaS) com Azure AD, consulte O que é o acesso à [aplicação e um único acesso com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura ativada por ServiceNow (SSO).
* Para o ServiceNow, um caso ou inquilino da versão ServiceNow, Calgary ou posterior.
* Para o ServiceNow Express, uma instância do ServiceNow Express, versão Helsínquia ou posterior.
* O inquilino do ServiceNow deve ter o [sinal único de fornecedor múltiplo no plugin.](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) Pode fazê-lo [enviando um pedido de serviço.](https://hi.service-now.com)
* Para a configuração automática, ative o plugin multi-fornecedor para o ServiceNow.
* Para instalar a aplicação ServiceNow Classic (Mobile), vá à loja apropriada e procure a aplicação ServiceNow Classic. Então, descarregue.

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. 

* O ServiceNow suporta o SSO iniciado pela **SP.**

* O ServiceNow suporta [o fornecimento automatizado de utilizadores.](servicenow-provisioning-tutorial.md)

* Uma vez configurado o ServiceNow, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

* Pode configurar a aplicação ServiceNow Classic (Mobile) com Azure AD para ativar sSO. Suporta utilizadores android e iOS. Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

## <a name="add-servicenow-from-the-gallery"></a>Adicione ServiçoNow da galeria

Para configurar a integração do ServiceNow no AD Azure, é necessário adicionar o ServiceNow da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou escola, ou utilizando uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione o serviço **Azure Ative Directory.**
1. Vá a **Aplicações Empresariais**e selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **insira o ServiceNow** na caixa de pesquisa.
1. Selecione **ServiceNow** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-servicenow"></a>Configurar e testar Azure AD único sinal de inscrição para o ServiceNow

Configure e teste Azure AD SSO com ServiceNow utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no ServiceNow.

Para configurar e testar o Azure AD SSO com o ServiceNow, complete os seguintes blocos de construção:

1. [Configure Azure AD SSO](#configure-azure-ad-sso) para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. [Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user) para testar o Azure AD com B.Simon.
    1. [Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que a B.Simon utilize um único sinal de Ad AD.
    1. [Configure Azure AD SSO para o ServiceNow Express](#configure-azure-ad-sso-for-servicenow-express) para permitir que os seus utilizadores utilizem esta funcionalidade.
2. [Configure o ServiceNow](#configure-servicenow) para configurar as definições SSO no lado da aplicação.
    1. Crie um utilizador de [teste ServiceNow](#create-servicenow-test-user) para ter uma contraparte de B.Simon no ServiceNow, ligada à representação AD AZure do utilizador.
    1. [Configure o ServiceNow Express SSO](#configure-servicenow-express-sso) para configurar as definições de inscrição única no lado da aplicação.  
3. [Teste SSO](#test-sso) para verificar se a configuração funciona.
4. [Teste SSO para ServiceNow Classic (Mobile)](#test-sso-for-servicenow-classic-mobile) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **ServiceNow,** encontre a secção **Gerir.** Selecione **um único sinal de inscrição**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, selecione o ícone de caneta para **configuração SAML básica** para editar as definições.

   ![Screenshot de Configurar Único Sinal com página SAML, com ícone de caneta em destaque](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** execute os seguintes passos:

    a. Em **Iniciar sção no URL,** introduza um URL que utiliza o seguinte padrão: `https://instance.service-now.com/login_with_sso.do?glide_sso_id=<sys_id of the sso configuration>`

    b. No **Identificador (ID da entidade)**, introduza um URL que utiliza o seguinte padrão: `https://<instance-name>.service-now.com`

    c. Para **URL de resposta**, insira um dos seguintes URL:

    | URL de Resposta|
    |----------|
    | `https://instancename.service-now.com/navpage.do` |
    | `https://instancename.service-now.com/customer.do` | 

    > [!NOTE]
    > Estes valores não são reais. Você precisa atualizar estes valores com o URL e identificador de assinatura real, que é explicado mais tarde no tutorial. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sinal com** a página SAML, na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)**. 

   ![Screenshot da secção de Certificado de Assinatura SAML, com Download em destaque](common/certificatebase64.png)

   a. Selecione o botão de cópia para copiar o **Url de metadados da Federação de Aplicações**e cole-o no Bloco de Notas. Esta URL será usada mais tarde no tutorial.

    b. Selecione **Baixar** para baixar **Certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

1. Na secção **set up ServiceNow,** copie os URLs apropriados, com base na sua exigência.

   ![Screenshot da secção set up ServiceNow, com URLs em destaque](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste, chamado B.Simon, no portal Azure.

1. A partir do painel esquerdo no portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >  **Users**  >  **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. Para **nome,** insira `B.Simon` .  
   1. Para **o nome de utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione **a palavra-passe**show e, em seguida, escreva o valor que é mostrado na **caixa de palavra-passe.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao ServiceNow.

1. No portal Azure, selecione **Aplicações empresariais**  >  **Todas as aplicações**.
1. Na lista de candidaturas, selecione **ServiceNow**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![Screenshot da secção Manage, com Utilizadores e grupos em destaque](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Na caixa de diálogo **de atribuição de** adicionar, selecione **Utilizadores e grupos**.

    ![Screenshot de Utilizadores e grupos, com o utilizador Add em destaque](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores e, em seguida, escolha **Select**.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, escolha **Selecionar**.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Configure Azure AD SSO para ServiceNow Express

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **ServiceNow,** selecione **um único sinal de sação**.

    ![Screenshot da página de integração de aplicações ServiceNow, com único sinal de sôs-on em destaque](common/select-sso.png)

2. Na caixa de diálogo **de método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de entrada.

    ![Screenshot de Selecione um único método de inscrição, com SAML realçado](common/select-saml-option.png)

3. No **set-on único com** a página SAML, selecione o ícone da caneta para abrir a caixa de diálogo **de configuração SAML básica.**

    ![Screenshot de Configurar um único sinal com página SAML, com ícone de caneta em destaque](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** execute os seguintes passos:

    a. Para **iniciar s placar no URL,** introduza um URL que utiliza o seguinte padrão: `https://instance.service-now.com/login_with_sso.do?glide_sso_id=<sys_id of the sso configuration>`

    b. Para **identificador (ID de entidade)**, introduza um URL que utiliza o seguinte padrão: `https://<instance-name>.service-now.com`

    c. Para **URL de resposta**, insira um dos seguintes URL:

    | URL de Resposta |
    |-----------|
    | `https://instancename.service-now.com/navpage.do` |
    | `https://instancename.service-now.com/customer.do` |

    > [!NOTE]
    > Estes valores não são reais. Você precisa atualizar estes valores com o URL e identificador de assinatura real, que é explicado mais tarde no tutorial. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração de um único sessão com** a página SAML, na secção **Certificado de Assinatura SAML,** selecione **Descarregamento** para descarregar o **Certificado (Base64)** das opções especificadas, de acordo com o seu requisito. Guarde no seu computador.

    ![Screenshot da secção de Certificado de Assinatura SAML, com Download em destaque](common/certificatebase64.png)

6. Pode ter o Azure AD configurar automaticamente o Serviço Agora para a autenticação baseada em SAML. Para ativar este serviço, aceda à secção **'Configurar',** e selecione **Ver instruções passo a passo** para abrir a janela de **inscrição de configuração.**

    ![Screenshot da secção De Configuração Do ServiceNow, com instruções passo a passo em destaque](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. No formulário **de inscrição de configuração,** insira o nome da sua instância ServiceNow, nome de utilizador de administração e senha de administração. **Selecione Configurar agora**. O nome de utilizador de administração fornecido deve ter a **função security_admin** atribuída no ServiceNow para que este funcione. Caso contrário, para configurar manualmente o ServiceNow para utilizar o AD AD azul como Fornecedor de Identidade SAML, selecione **configurar manualmente um único sinal**. Copie o **URL logout, identificador AD Azure e URL** de login a partir da secção de Referência Rápida.

    ![Screenshot do formulário de inscrição de configuração, com Configure Agora em destaque](./media/servicenow-tutorial/configure.png "URL de aplicativo configurar")

## <a name="configure-servicenow"></a>Configure Serviço Agora

1. Inscreva-se na sua aplicação ServiceNow como administrador.

1. Ativar o plug-in do instalador de **assinaturas Multiple Provider,** seguindo estes passos:

    a. No painel esquerdo, procure a secção **definição** do sistema a partir da caixa de pesquisa e, em seguida, selecione **Plugins**.

    ![Screenshot da secção de Definição do Sistema, com Definição de Sistema e Plugins em destaque](./media/servicenow-tutorial/tutorial_servicenow_03.png "Ativar plugin")

    b. Pesquisa de **Integração - Instalador único de assinatura de fornecedor de múltiplos fornecedores.**

     ![Screenshot da página de Plugins do Sistema, com Integração - Instalador único de sign-on de fornecedor único em destaque](./media/servicenow-tutorial/tutorial_servicenow_04.png "Ativar plugin")

    c. Selecione o plug-in. Clique com o botão direito e **selecione Ativar/Atualizar**.

     ![Screenshot do menu plug-in de clique direito, com Activate/Upgrade em destaque](./media/servicenow-tutorial/tutorial_activate.png "Ativar plugin")

    d. **Selecione Ativar**.

     ![Screenshot da caixa de diálogo Do Plugin Ativa, com a Activate em destaque](./media/servicenow-tutorial/tutorial_activate1.png "Ativar plugin")

1. No painel esquerdo, procure a secção **SSO multi-fornecedor** da barra de pesquisa e, em seguida, selecione **Propriedades**.

    ![Screenshot da secção SSO multi-fornecedor, com SSO e Propriedades Multi-Fornecedores em destaque](./media/servicenow-tutorial/tutorial_servicenow_06.png "URL de aplicativo configurar")

1. Na caixa de diálogo **SSO Properties do Fornecedor Múltiplo,** execute os seguintes passos:

    ![Screenshot da caixa de diálogo SSO Properties de fornecedor de vários fornecedores](./media/servicenow-tutorial/ic7694981.png "URL de aplicativo configurar")

    * Para **ativar o SSO de vários fornecedores,** selecione **Sim**.
  
    * Para **ativar a importação automática de utilizadores de todos os fornecedores de identidade para a tabela de utilizadores**, selecione **Sim**.

    * Para **ativar a registo de depuragem para a integração SSO de vários fornecedores,** selecione **Sim**.

    * Para o campo na mesa de **email** **utilizadores que...**
  
    * Selecione **Guardar**.

1. Pode configurar o ServiceNow automaticamente ou manualmente. Para configurar o ServiceNow automaticamente, siga estes passos:

    1. Volte à página de inscrição única do **ServiceNow** no portal Azure.

    1. O serviço de configuração de um clique é fornecido para o ServiceNow. Para ativar este serviço, aceda à secção **de Configuração 'ServiceNow'** e selecione **Configure ServiceNow** para abrir a janela **de inscrição de configuração.**

        ![Screenshot de Configurar o ServiceNow, com instruções passo a passo em destaque](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    1. No formulário **de inscrição de configuração,** insira o nome da sua instância ServiceNow, nome de utilizador de administração e senha de administração. **Selecione Configurar agora**. O nome de utilizador de administração fornecido deve ter a **função security_admin** atribuída no ServiceNow para que este funcione. Caso contrário, para configurar manualmente o ServiceNow para utilizar o AD AD azul como Fornecedor de Identidade SAML, selecione **configurar manualmente um único sinal**. Copie o **URL de inscrição, o ID da Entidade SAML e o URL de serviço único de inscrição da SAML** a partir da secção de Referência Rápida.

        ![Screenshot do formulário de inscrição de configuração, com Configure Agora em destaque](./media/servicenow-tutorial/configure.png "URL de aplicativo configurar")

    1. Inscreva-se na sua aplicação ServiceNow como administrador.

       * Na configuração automática, todas as definições necessárias estão configuradas no lado **do ServiceNow,** mas o **Certificado X.509** não é ativado por padrão. Tem de mapear manualmente o seu fornecedor de identidade no ServiceNow. Siga estes passos.

         1. No painel esquerdo, procure a secção **SSO multi-fornecedor** da caixa de pesquisa e selecione **Fornecedores de Identidade**.

            ![Screenshot da secção SSO multi-fornecedor, com fornecedores de identidade em destaque](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configurar o início de sessão único")

         1. Selecione o fornecedor de identidade gerado automaticamente.

            ![Screenshot dos fornecedores de identidade, com fornecedor de identidade gerado automaticamente em destaque](./media/servicenow-tutorial/tutorial_servicenow_08.png "Configurar o início de sessão único")

         1.  Na secção **Fornecedor de Identidade,** execute os seguintes passos:

             ![Screenshot da secção de Fornecedor de Identidade](./media/servicenow-tutorial/automatic_config.png "Configurar o início de sessão único")

               * Para **nomes**, introduza um nome para a sua configuração (por exemplo, **Microsoft Azure Federated single sign-on**).

               * Remova o valor **singleLogoutRequest do fornecedor de identidade** povoado da caixa de texto.

               * Copie o valor **da página inicial do ServiceNow** e **cole-o** em URL de inscrição na secção **de Configuração SAML Básica** do Portal Azure.

                  > [!NOTE]
                  > A página inicial do ServiceNow é uma concatenação do seu URL de **inquilino serviceNow** e **/navpage.do** (por exemplo: `https://fabrikam.service-now.com/navpage.do` ).

              * Copie o valor **de ID /Emitente** da Entidade e cole-o em **Identificador** na secção **de Configuração SAML Básica** do Portal Azure.

              * Confirme que **a Política NameID** está definida para `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` valorizar. 

         1. Desloque-se até à secção **de Certificado X.509** e selecione **Editar**.

             ![Screenshot da secção de Certificado X.509, com edição em destaque](./media/servicenow-tutorial/tutorial_servicenow_09.png "Configurar o início de sessão único")

         1. Selecione o certificado e selecione o ícone da seta direita para adicionar o certificado

            ![Screenshot da Coleção, com certificado e ícone de seta direita em destaque](./media/servicenow-tutorial/tutorial_servicenow_11.png "Configurar o início de sessão único")

          1. Selecione **Guardar**.

          1. No canto superior direito da página, selecione **Ligação de Teste**.

             ![Screenshot da página, com a Ligação de Teste em destaque](./media/servicenow-tutorial/tutorial_activate2.png "Ativar plugin")

             > [!NOTE]
             > Se a Ligação de Teste estiver a falhar e não conseguir ativar esta ligação, o ServiceNow oferece o interruptor de substituição. Tem que entrar **Sys_properties. LISTA** na **Navegação de Pesquisa** e abrirá a nova página de Propriedades do Sistema. Aqui tem de criar uma nova propriedade com o nome **glide.authenticate.multisso.test.connection.obrigatório** com **o tipo de dados** como **Verdadeiro/Falso** e, em seguida, definir o **valor** como **Falso**.

             > ![Screenshot da página de resultados do teste](./media/servicenow-tutorial/testconnection-fail.png "Configurar o início de sessão único")
        
          1. Quando pedirem as suas credenciais, insira-as. Verá a seguinte página. Espera-se o erro **de resultados do teste SSO Logout.** Ignore o erro e  **selecione Ativar**.

             ![Screenshot da página de resultados do teste](./media/servicenow-tutorial/servicenowactivate.png "Configurar o início de sessão único")
  
1. Para configurar **o ServiceNow** manualmente, siga estes passos:

    1. Inscreva-se na sua aplicação ServiceNow como administrador.

    1. No painel esquerdo, selecione **Fornecedores de Identidade.**

        ![Screenshot do SSO Multi-Fornecedor, com fornecedores de identidade em destaque](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configurar o início de sessão único")

    1. Na caixa de diálogo **dos Fornecedores de Identidade,** selecione **New**.

        ![Screenshot da caixa de diálogo dos Fornecedores de Identidade, com novo destaque](./media/servicenow-tutorial/ic7694977.png "Configurar o início de sessão único")

    1. Na caixa de diálogo **dos Fornecedores de Identidade,** selecione **SAML**.

        ![Screenshot da caixa de diálogo dos Fornecedores de Identidade, com SAML em destaque](./media/servicenow-tutorial/ic7694978.png "Configurar o início de sessão único")

    1. Nos **Metadados do Fornecedor de Identidade de Importação,** execute os seguintes passos:

        ![Screenshot de Metadados de Fornecedor de Identidade de Importação, com URL e Importação em destaque](./media/servicenow-tutorial/idp.png "Configurar o início de sessão único")

        1. Introduza o **Url de Metadados da Federação** de Aplicações que copiou do portal Azure.

        1. Selecione **Import** (Importar).

    1. Lê o URL de metadados IdP e povoa todas as informações dos campos.

        ![Screenshot do Fornecedor de Identidade](./media/servicenow-tutorial/ic7694982.png "Configurar o início de sessão único")

        * Para **nomes**, introduza um nome para a sua configuração (por exemplo, **Microsoft Azure Federated single sign-on**).

        * Remova o valor **singleLogoutRequest do fornecedor de identidade** povoado da caixa de texto.

        * Copie o valor **da página inicial do ServiceNow.** Cole-o em **URL de inscrição** na secção **de configuração SAML básica** do portal Azure.

            > [!NOTE]
            > A página inicial do ServiceNow é uma concatenação do seu URL de **inquilino serviceNow** e **/navpage.do** (por exemplo: `https://fabrikam.service-now.com/navpage.do` ).

        * Copie o valor **de ID /Emitente.** Cole-o em **Identificador** na secção de **Configuração BÁSICA SAML** do portal Azure.

        * Confirme que **a Política NameID** está definida para `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` valorizar.

        * Selecione **Avançado**. No **Campo do Utilizador,** insira **o e-mail**.

            > [!NOTE]
            > Pode configurar a Azure AD para emitir o ID do utilizador Azure (nome principal do utilizador) ou o endereço de e-mail como o identificador único no token SAML. Faça-o indo **ServiceNow**para a secção de  >  **Attributes**  >  **sinalização ServiceNow Atributos Únicos** do portal Azure e mapeando o campo desejado para o atributo **nameidentifier.** O valor armazenado para o atributo selecionado em Azure AD (por exemplo, nome principal do utilizador) deve corresponder ao valor armazenado no ServiceNow para o campo introduzido (por exemplo, user_name).

        * Selecione **a ligação** de teste no canto superior direito da página.

          > [!NOTE]
          > Se a Ligação de Teste estiver a falhar e não conseguir ativar esta ligação, o ServiceNow oferece o interruptor de substituição. Tem que entrar **Sys_properties. LISTA** na **Navegação de Pesquisa** e abrirá a nova página de Propriedades do Sistema. Aqui tem de criar uma nova propriedade com o nome **glide.authenticate.multisso.test.connection.obrigatório** com **o tipo de dados** como **Verdadeiro/Falso** e, em seguida, definir o **valor** como **Falso**.

          > ![Screenshot da página de resultados do teste](./media/servicenow-tutorial/testconnection-fail.png "Configurar o início de sessão único")

        * Quando pedirem as suas credenciais, insira-as. Verá a seguinte página. Espera-se o erro **de resultados do teste SSO Logout.** Ignore o erro e  **selecione Ativar**.

          ![Screenshot da página de resultados do teste](./media/servicenow-tutorial/servicenowactivate.png "Configurar o início de sessão único")

### <a name="create-servicenow-test-user"></a>Criar utilizador de teste ServiceNow

O objetivo desta secção é criar um utilizador chamado B.Simon in ServiceNow. O ServiceNow suporta o fornecimento automático do utilizador, que é ativado por padrão.

> [!NOTE]
> Se precisar de criar um utilizador manualmente, contacte a equipa de suporte do [Cliente ServiceNow.](https://www.servicenow.com/support/contact-support.html)

### <a name="configure-servicenow-express-sso"></a>Configure serviçoNow Express SSO

1. Inscreva-se na sua aplicação ServiceNow Express como administrador.

2. No painel esquerdo, selecione **Single Sign-On**.

    ![Screenshot da aplicação ServiceNow Express, com sign-on único em destaque](./media/servicenow-tutorial/ic7694980ex.png "URL de aplicativo configurar")

3. Na caixa de diálogo **'Sign-On' único,** selecione o ícone de configuração no canto superior direito e defina as seguintes propriedades:

    ![Screenshot da caixa de diálogo sign-on única](./media/servicenow-tutorial/ic7694981ex.png "URL de aplicativo configurar")

    a. Alternância **Ative o SSO de vários fornecedores** à direita.

    b. Toggle **Enable debug logging para a integração SSO de vários fornecedores** à direita.

    c. **No campo da mesa de utilizadores que user_name...** **user_name**

4. Na **caixa de** diálogo de inscrição única, selecione **Adicionar Novo Certificado**.

    ![Screenshot da caixa de diálogo sign-on única, com adicionar novo certificado em destaque](./media/servicenow-tutorial/ic7694973ex.png "Configurar o início de sessão único")

5. Na caixa de diálogo **X.509 Certificados,** execute os seguintes passos:

    ![Screenshot da caixa de diálogo de certificados X.509](./media/servicenow-tutorial/ic7694975.png "Configurar o início de sessão único")

    a. Para **nomes**, insira um nome para a sua configuração (por exemplo: **TestSAML2.0**).

    b. Selecione **Ative**.

    c. Para **formato**, selecione **PEM**.

    d. Para **tipo**, selecione **Trust Store Cert**.

    e. Abra o seu certificado codificado Base64 descarregado do portal Azure no Bloco de Notas. Copie o conteúdo da sua pasta e, em seguida, cole-o na caixa de texto **do Certificado PEM.**

    f. Selecione **Atualização**

6. Na caixa de diálogo **'Sign-On' única,** selecione **Adicionar Novo IdP**.

    ![Screenshot da caixa de diálogo sign-on única, com add New IdP em destaque](./media/servicenow-tutorial/ic7694976ex.png "Configurar o início de sessão único")

7. Na caixa de diálogo **Add New Identity Provider,** no **âmbito do Configure Identity Provider,** execute os seguintes passos:

    ![Screenshot da caixa de diálogo do fornecedor de identidade adicionar novo fornecedor de identidade](./media/servicenow-tutorial/ic7694982ex.png "Configurar o início de sessão único")

    a. Para **nomes**, insira um nome para a sua configuração (por exemplo: **SAML 2.0**).

    b. Para **URL fornecedor de identidade,** cole o valor do ID do fornecedor de identidade que copiou do portal Azure.

    c. Para **a AuthnRequest do Fornecedor de Identidade,** cole o valor do URL de pedido de autenticação que copiou do portal Azure.

    d. Para **o SingleLogoutRequest do Fornecedor de Identidade,** cole o valor do URL logout que copiou do portal Azure.

    e. Para **Certificado de Fornecedor de Identidade,** selecione o certificado que criou no passo anterior.

8. Selecione **Definições Avançadas**. Em **Propriedades adicionais do Fornecedor de Identidade,** execute os seguintes passos:

    ![Screenshot da caixa de diálogo do Fornecedor de Identidade Adicionar Novo Fornecedor de Identidade, com Definições Avançadas em destaque](./media/servicenow-tutorial/ic7694983ex.png "Configurar o início de sessão único")

    a. Para a ligação do **protocolo para o SingleLogoutRequest do IDP,** insira **urna:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    b. Para **a Política NameID**, insira **urna:oasis:names:tc:SAML:1.1:nameid-formato:não especificado**.

    c. Para **método AuthnContextClassRef,** insira `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` .

    d. Para **criar uma Classe AuthnContext,** alterna-la para off (não selecionado).

9. Em **Propriedades adicionais do Fornecedor de Serviços,** execute os seguintes passos:

    ![Screenshot da caixa de diálogo Add New Identity Provider, com várias propriedades em destaque](./media/servicenow-tutorial/ic7694984ex.png "Configurar o início de sessão único")

    a. Para **a página inicial do ServiceNow,** insira o URL da sua página inicial do ServiceNow.

    > [!NOTE]
    > A página inicial do ServiceNow é uma concatenação do seu URL de **inquilino serviceNow** e **/navpage.do** (por exemplo: `https://fabrikam.service-now.com/navpage.do` ).

    b. Para **o ID/Emitente da Entidade,** insira o URL do seu inquilino ServiceNow.

    c. Para **o Público URI,** insira o URL do seu inquilino ServiceNow.

    d. Para **Clock Skew,** insira **60**.

    e. Para **o Campo do Utilizador,** introduza o **e-mail**.

    > [!NOTE]
    > Pode configurar a Azure AD para emitir o ID do utilizador Azure (nome principal do utilizador) ou o endereço de e-mail como o identificador único no token SAML. Faça-o indo **ServiceNow**para a secção de  >  **Attributes**  >  **sinalização ServiceNow Atributos Únicos** do portal Azure e mapeando o campo desejado para o atributo **nameidentifier.** O valor armazenado para o atributo selecionado em Azure AD (por exemplo, nome principal do utilizador) deve corresponder ao valor armazenado no ServiceNow para o campo introduzido (por exemplo, user_name).

    f. Selecione **Guardar**.

## <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo ServiceNow no Painel de Acesso, deverá ser automaticamente inscrito no ServiceNow para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="test-sso-for-servicenow-classic-mobile"></a>Teste SSO para ServiceNow Classic (Mobile)

1. Abra a sua aplicação **ServiceNow Classic (Mobile)** e execute os seguintes passos:

    a. Selecione o sinal de mais no canto inferior direito.

    ![Screenshot da aplicação ServiceNow Classic, com sinal plus em destaque](./media/servicenow-tutorial/test03.png)

    b. Insira o nome da sua instância ServiceNow e **selecione Continue**.

    ![Screenshot da página Add Instance, com Continue em destaque](./media/servicenow-tutorial/test04.png)

    c. Na página **'Iniciar registo',** execute os seguintes passos:

    ![Screenshot do Log na página, com o início de sessão externo em destaque](./media/servicenow-tutorial/test01.png)

    *  Insira **o nome de utilizador,** como B.simon@contoso.com .

    *  Selecione **USE INÍCIO DE SESSÃO EXTERNO**. Você é redirecionado para a página AD Azure para iniciar sação.

    *  Introduza as suas credenciais. Se houver alguma autenticação de terceiros, ou qualquer outra funcionalidade de segurança ativada, o utilizador deve responder em conformidade. A **página inicial da aplicação** aparece.

        ![Screenshot da página inicial da aplicação](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar apps saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar o aprovisionamento de utilizadores](servicenow-provisioning-tutorial.md)

- [Experimente o ServiceNow com Azure AD](https://aad.portal.azure.com)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/protect-servicenow)

- [Como proteger o ServiceNow com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
