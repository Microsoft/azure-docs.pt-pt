---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com serviceNow [ integração de um único sign-on) com o ServiceNow [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o ServiceNow.
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
ms.date: 03/25/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fe6c857e5b0c2f48f27c167c177dbf1f4651986
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80384114"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com o ServiceNow

Neste tutorial, você vai aprender a integrar o ServiceNow com o Azure Ative Directory (Azure AD). Quando integrar o ServiceNow com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao ServiceNow.
* Ative que os seus utilizadores sejam automaticamente inscritos no ServiceNow com as suas contas Azure AD.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre software como uma integração de aplicações de serviço (SaaS) com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição de inscrição única ServiceNow (SSO) ativada.
* Para serviceNow, um exemplo ou inquilino do ServiceNow, versão Calgary ou mais tarde.
* Para o ServiceNow Express, uma instância de ServiceNow Express, versão Helsínquia ou mais tarde.
* O inquilino ServiceNow deve ter o sinal único do [fornecedor múltiplo no Plugin.](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) Pode fazê-lo [submetendo um pedido](https://hi.service-now.com)de serviço.
* Para configuração automática, ative o plugin multi-fornecedor para o ServiceNow.
* Para instalar a aplicação ServiceNow Classic (Mobile), vá à loja apropriada e procure a aplicação ServiceNow Classic. Em seguida, descarregue-o.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. 

* ServiceNow suporta **SP** iniciado SSO.

* ServiceNow suporta [fornecimento automatizado de utilizadores](servicenow-provisioning-tutorial.md).

* Assim que configurar o ServiceNow, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se a partir do Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

* Pode configurar a aplicação ServiceNow Classic (Mobile) com AD Azure para ativar o SSO. Suporta utilizadores Android e iOS. Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

## <a name="add-servicenow-from-the-gallery"></a>Adicione ServiceNow da galeria

Para configurar a integração do ServiceNow em Azure AD, precisa adicionar serviceNow da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou de escola, ou utilizando uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione o serviço **de Diretório Ativo Azure.**
1. Vá a **Aplicações Empresariais**e selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** introduza **ServiceNow** na caixa de pesquisa.
1. Selecione **ServiceNow** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-servicenow"></a>Configure e teste Azure AD único sign-on para ServiceNow

Configure e teste Azure AD SSO com ServiceNow utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no ServiceNow.

Para configurar e testar o Azure AD SSO com o ServiceNow, complete os seguintes blocos de construção:

1. [Configure O SSO AD Azure](#configure-azure-ad-sso) para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. [Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user) para testar o único sinal de Azure AD com B.Simon.
    1. [Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que b.Simon utilize um único sinal de AD Azure.
    1. [Configure Azure AD SSO para serviceNow Express](#configure-azure-ad-sso-for-servicenow-express) para permitir que os seus utilizadores utilizem esta funcionalidade.
2. [Configure](#configure-servicenow) o Serviço Agora para configurar as definições SSO no lado da aplicação.
    1. [Crie um utilizador](#create-servicenow-test-user) de teste ServiceNow para ter uma contraparte de B.Simon no ServiceNow, ligado à representação azure AD do utilizador.
    1. [Configure ServiceNow Express SSO](#configure-servicenow-express-sso) para configurar as definições de inscrição únicas no lado da aplicação.  
3. [Teste sSO](#test-sso) para verificar se a configuração funciona.
4. [Teste SSO para ServiceNow Classic (Mobile)](#test-sso-for-servicenow-classic-mobile) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **ServiceNow,** encontre a secção **Gerir.** Selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set set single sign-on com** a página SAML, selecione o ícone da caneta para **configuração SAML básica** para editar as definições.

   ![Screenshot de configurar um único sign-on com página SAML, com ícone de caneta realçado](common/edit-urls.png)

1. Na secção **De Configuração SAML Básica,** execute os seguintes passos:

    a. Em **Sign on URL,** introduza um URL que utilize o seguinte padrão:`https://instance.service-now.com/login_with_sso.do?glide_sso_id=<sys_id of the sso configuration>`

    b. No **Identificador (ID da Entidade)**, introduza um URL que utilize o seguinte padrão:`https://<instance-name>.service-now.com`

    c. Para **url de resposta,** introduza um dos seguintes URL:

    |||
    |-|-|
    | `https://instancename.service-now.com/navpage.do` |
    | `https://instancename.service-now.com/customer.do` | 

    > [!NOTE]
    > Estes valores não são reais. É necessário atualizar estes valores com o URL e identificador de inscrição real, o que é explicado mais tarde no tutorial. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. No **set set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** encontre **certificado (Base64)**. 

   ![Screenshot da secção certificado de assinatura SAML, com Download em destaque](common/certificatebase64.png)

   a. Selecione o botão de cópia para copiar o Url de **Metadados da Federação**de Aplicações e cole-o no Bloco de Notas. Este URL será usado mais tarde no tutorial.

    b. Selecione **Download** para baixar **Certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

1. Na secção **set up ServiceNow,** copie os URLs apropriados, com base na sua exigência.

   ![Screenshot da secção set Up ServiceNow, com URLs em destaque](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste, chamado B.Simon, no portal Azure.

1. A partir do painel esquerdo no portal Azure,**Users** > selecione **Utilizadores de Diretório** > Ativo Azure**Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. Para **Nome,** insira `B.Simon`.  
   1. Para o nome username@companydomain.extensiondo **utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione **mostrar palavra-passe**e, em seguida, anote o valor que é mostrado na caixa **password.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao ServiceNow.

1. No portal Azure, selecione **Aplicações Empresariais** > **Todas as aplicações**.
1. Na lista de aplicações, selecione **ServiceNow**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![Screenshot da secção Gerir, com utilizadores e grupos em destaque](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Na caixa de diálogo **Adicionar Atribuição,** selecione **Utilizadores e grupos**.

    ![Screenshot de Utilizadores e grupos, com adicionar utilizador em destaque](common/add-assign-user.png)

1. Na caixa de diálogo **De utilizadores e grupos,** selecione **B.Simon** na lista de utilizadores e, em seguida, escolha **Select**.
1. Se estiver à espera de qualquer valor de papel na afirmação do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, escolha **Selecionar**.
1. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Configure Azure AD SSO para ServiceNow Express

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **ServiceNow,** selecione **um único sinal.**

    ![Screenshot da página de integração de aplicações ServiceNow, com um único sign-on em destaque](common/select-sso.png)

2. Na **seleta de uma única** caixa de diálogo de método de sinalização, selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Screenshot de Select um único método de sinalização, com SAML em destaque](common/select-saml-option.png)

3. No **set set single sign-on com** a página SAML, selecione o ícone da caneta para abrir a caixa de diálogo **de configuração Básica SAML.**

    ![Screenshot de Configurar um único sign-on com página SAML, com ícone de caneta realçado](common/edit-urls.png)

4. Na secção **De Configuração SAML Básica,** execute os seguintes passos:

    a. Para **iniciar sessão no URL,** introduza um URL que utilize o seguinte padrão:`https://instance.service-now.com/login_with_sso.do?glide_sso_id=<sys_id of the sso configuration>`

    b. Para **identificador (ID da entidade)**, introduza um URL que utilize o seguinte padrão:`https://<instance-name>.service-now.com`

    c. Para **url de resposta,** introduza um dos seguintes URL:

    |||
    |-|-|
    | `https://instancename.service-now.com/navpage.do` |
    | `https://instancename.service-now.com/customer.do` |

    > [!NOTE]
    > Estes valores não são reais. É necessário atualizar estes valores com o URL e identificador de inscrição real, o que é explicado mais tarde no tutorial. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** selecione **Download** para descarregar o **Certificado (Base64)** das opções especificadas, conforme o seu requisito. Guarde no seu computador.

    ![Screenshot da secção de certificado de assinatura SAML, com Download em destaque](common/certificatebase64.png)

6. Pode ter AD Azure configurar automaticamente o ServiceNow para autenticação baseada em SAML. Para ativar este serviço, vá à secção **'Configurar ServiceNow'** e selecione **Ver instruções passo a passo** para abrir a janela de **sinalização Configure.**

    ![Screenshot da secção ServiceNow configurada, com ver instruções passo a passo destacadas](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. No formulário de **início de sessão Configure,** introduza o nome da sua instância ServiceNow, o nome de utilizador do administrador e a palavra-passe de administrador. **Selecione Configurar agora**. O nome de utilizador administrativo fornecido deve ter a **função security_admin** atribuída no ServiceNow para que este funcione. Caso contrário, para configurar manualmente o ServiceNow para utilizar o Azure AD como fornecedor de identidade SAML, **selecione manualmente configurar um único sinal**. Copie o URL de **Logout, o Identificador AD Azure e** o URL de Login da secção De Referência Rápida.

    ![Screenshot do formulário de inscrição configure, com Configure Now em destaque](./media/servicenow-tutorial/configure.png "Configure url de aplicativo")

## <a name="configure-servicenow"></a>Configure ServiceNow

1. Inscreva-se na sua aplicação ServiceNow como administrador.

1. Ativar a **Integração - Multiple Provider single sign-on Installer plug-in** seguindo estes passos:

    a. No painel esquerdo, procure a secção **Definição** do Sistema a partir da caixa de pesquisa e, em seguida, selecione **Plugins**.

    ![Screenshot da secção Definição de Sistema, com Definição de Sistema e Plugins em destaque](./media/servicenow-tutorial/tutorial_servicenow_03.png "Ativar plugin")

    b. Pesquisa por **Integração - Fornecedor único fornecedor único instalador**.

     ![Screenshot da página plugins do sistema, com integração - Instalador de sinal único de fornecedor múltiplo destacado](./media/servicenow-tutorial/tutorial_servicenow_04.png "Ativar plugin")

    c. Selecione o plug-in. Clique à direita e selecione **Ativar/Atualizar**.

     ![Screenshot do menu plug-in right click, com Activate/Upgrade destacado](./media/servicenow-tutorial/tutorial_activate.png "Ativar plugin")

    d. **Selecione Ativar**.

     ![Screenshot da caixa de diálogo Activate Plugin, com Activate destacado](./media/servicenow-tutorial/tutorial_activate1.png "Ativar plugin")

1. No painel esquerdo, procure a secção **Multi-Provider SSO** a partir da barra de pesquisa e, em seguida, selecione **Propriedades**.

    ![Screenshot da secção Multi-Provider SSO, com Multi-Provider SSO e Propriedades em destaque](./media/servicenow-tutorial/tutorial_servicenow_06.png "Configure url de aplicativo")

1. Na caixa de diálogo **Multiple Provider SSO Properties,** execute os seguintes passos:

    ![Screenshot da caixa de diálogo SSO Properties do fornecedor múltiplo](./media/servicenow-tutorial/ic7694981.png "Configure url de aplicativo")

    * Para **ativar vários fornecedores SSO,** selecione **Sim**.
  
    * Para **ativar a importação automática de utilizadores de todos os fornecedores de identidade para a tabela de utilizadores,** selecione **Sim**.

    * Para **ativar o registo de depuração para a integração SSO de vários fornecedores,** selecione **Sim**.

    * Para **o campo na tabela de utilizadores que...** insira o **e-mail**.
  
    * Selecione **Guardar**.

1. Pode configurar o ServiceNow automaticamente ou manualmente. Para configurar o ServiceNow automaticamente, siga estes passos:

    1. Volte à página de inscrição individual **serviceNow** no portal Azure.

    1. O serviço de configuração de um clique é fornecido para o ServiceNow. Para ativar este serviço, vá à secção de **Configuração ServiceNow** e selecione **Configurar O Serviço Agora** para abrir a janela **de início de 'Configurar'.**

        ![Screenshot de Configurar ServiceNow, com ver instruções passo a passo destacadas](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    1. No formulário de **início de sessão Configure,** introduza o nome da sua instância ServiceNow, o nome de utilizador do administrador e a palavra-passe de administrador. **Selecione Configurar agora**. O nome de utilizador administrativo fornecido deve ter a **função security_admin** atribuída no ServiceNow para que este funcione. Caso contrário, para configurar manualmente o ServiceNow para utilizar o Azure AD como fornecedor de identidade SAML, **selecione manualmente configurar um único sinal**. Copie o URL de **Inscrição, id da entidade SAML e URL** de serviço de inscrição única SAML da secção De Referência Rápida.

        ![Screenshot do formulário de inscrição configure, com Configure Now em destaque](./media/servicenow-tutorial/configure.png "Configure url de aplicativo")

    1. Inscreva-se na sua aplicação ServiceNow como administrador.

       * Na configuração automática, todas as configurações necessárias são configuradas no lado **ServiceNow,** mas o **Certificado X.509** não está ativado por padrão. Tem de mapear manualmente para o seu fornecedor de identidade no ServiceNow. Siga estes passos.

         1. No painel esquerdo, procure a secção **SSO multi-fornecedor** a partir da caixa de pesquisa e selecione **Fornecedores**de Identidade .

            ![Screenshot da secção Multi-Provider SSO, com Fornecedores de Identidade destacados](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configurar o início de sessão único")

         1. Selecione o fornecedor de identidade gerado automaticamente.

            ![Screenshot dos fornecedores de identidade, com fornecedor de identidade gerado automaticamente em destaque](./media/servicenow-tutorial/tutorial_servicenow_08.png "Configurar o início de sessão único")

         1.  Na secção **Fornecedor de Identidade,** execute os seguintes passos:

             ![Screenshot da secção Fornecedor de Identidade](./media/servicenow-tutorial/automatic_config.png "Configurar o início de sessão único")

               * Para **Nome**, introduza um nome para a sua configuração (por exemplo, **Microsoft Azure Federado único sinal on**).

               * Retire do **singleLogoutRequest do Fornecedor** de Identidade preenchido o valor singleLogoutRequest da caixa de texto.

               * Copie o valor da **página inicial do ServiceNow** e **cole-o** em URL de início de cartaz na secção de **configuração SAML Básica serviceNow** do portal Azure.

                  > [!NOTE]
                  > A página inicial da instância ServiceNow é uma concatenação do url`https://fabrikam.service-now.com/navpage.do`do seu **inquilino ServiceNow** e **/navpage.do** (por exemplo: ).

              * Copie o valor **id/emitente da Entidade** e cole-o no **Identificador** na secção de **Configuração SAML Básica serviceNow** do portal Azure.

              * Confirme que a Política `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` **nameID** está definida para valorizar. 

         1. Desloque-se até à secção **de Certificado X.509** e selecione **Editar**.

             ![Screenshot da secção de Certificado X.509, com Editar em destaque](./media/servicenow-tutorial/tutorial_servicenow_09.png "Configurar o início de sessão único")

         1. Selecione o certificado e selecione o ícone da seta direita para adicionar o certificado

            ![Screenshot da Coleção, com certificado e ícone de seta direita em destaque](./media/servicenow-tutorial/tutorial_servicenow_11.png "Configurar o início de sessão único")

          1. Selecione **Guardar**.

          1. No canto superior direito da página, selecione **Test Connection**.

             ![Screenshot da página, com Ligação de Teste em destaque](./media/servicenow-tutorial/tutorial_activate2.png "Ativar plugin")

             > [!NOTE]
             > Se a Ligação de Teste estiver a falhar e não for capaz de ativar esta ligação, o ServiceNow oferece o interruptor de substituição. Tens de entrar **Sys_properties. LISTA** na **Navegação** de Busca e abrirá a nova página de Propriedades do Sistema. Aqui tem de criar uma nova propriedade com o nome **glide.authenticate.multisso.test.connection.obrigatório** com **o tipo** de dados como **True/False** e, em seguida, definir o **valor** como **Falso**.

             > ![Screenshot da página de resultados de testes](./media/servicenow-tutorial/testconnection-fail.png "Configurar o início de sessão único")
        
          1. Quando lhe pedirem as suas credenciais, insira-as. Verá a seguinte página. Espera-se o erro dos resultados do teste de **logout SSO.** Ignore o erro e selecione **Ativar**.

             ![Screenshot da página de resultados de testes](./media/servicenow-tutorial/servicenowactivate.png "Configurar o início de sessão único")
  
1. Para configurar o **ServiceNow** manualmente, siga estes passos:

    1. Inscreva-se na sua aplicação ServiceNow como administrador.

    1. No painel esquerdo, selecione **Fornecedores de Identidade**.

        ![Screenshot de Multi-Provider SSO, com Fornecedores de Identidade em destaque](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configurar o início de sessão único")

    1. Na caixa de diálogo Fornecedores de **Identidade,** selecione **New**.

        ![Screenshot da caixa de diálogo de Fornecedores de Identidade, com novo destaque](./media/servicenow-tutorial/ic7694977.png "Configurar o início de sessão único")

    1. Na caixa de diálogo Fornecedores de **Identidade,** selecione **SAML**.

        ![Screenshot da caixa de diálogo de Fornecedores de Identidade, com SAML em destaque](./media/servicenow-tutorial/ic7694978.png "Configurar o início de sessão único")

    1. Nos Metadados do Fornecedor de **Identidade de Importação,** execute os seguintes passos:

        ![Screenshot de Metadados do Fornecedor de Identidade de Importação, com URL e Importação em destaque](./media/servicenow-tutorial/idp.png "Configurar o início de sessão único")

        1. Introduza o Url de Metadados da Federação de **Aplicações** que copiou do portal Azure.

        1. Selecione **Importar**.

    1. Lê o URL de metadados idp, e povoa todas as informações dos campos.

        ![Screenshot do Fornecedor de Identidade](./media/servicenow-tutorial/ic7694982.png "Configurar o início de sessão único")

        * Para **Nome**, introduza um nome para a sua configuração (por exemplo, **Microsoft Azure Federado único sinal on**).

        * Retire do **singleLogoutRequest do Fornecedor** de Identidade povoado o valor singleLogoutRequest da caixa de texto.

        * Copie o valor **da página inicial do ServiceNow.** **Colá-lo** em URL de início de placa na secção de **configuração SAML Básica serviceNow** do portal Azure.

            > [!NOTE]
            > A página inicial da instância ServiceNow é uma concatenação do url`https://fabrikam.service-now.com/navpage.do`do seu **inquilino ServiceNow** e **/navpage.do** (por exemplo: ).

        * Copiar o **valor ID/Emitente da Entidade.** Cola-o no **Identificador** no **Serviço Now Basic SAML Secção** de Configuração do portal Azure.

        * Confirme que a Política `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` **nameID** está definida para valorizar.

        * Selecione **Advanced**. No **Campo do Utilizador,** introduza **e-mail**.

            > [!NOTE]
            > Pode configurar o Azure AD para emitir o ID do utilizador da AD Azure (nome principal do utilizador) ou o endereço de e-mail como o identificador único no token SAML. Faça-o indo para a secção de**Attributes** > **inscrição única** do **portal** > Azure e mapeando o campo desejado para o atributo identificador de **nome.** O valor armazenado para o atributo selecionado em Azure AD (por exemplo, nome principal do utilizador) deve corresponder ao valor armazenado no ServiceNow para o campo introduzido (por exemplo, user_name).

        * Selecione **ligação** de teste no canto superior direito da página.

          > [!NOTE]
          > Se a Ligação de Teste estiver a falhar e não for capaz de ativar esta ligação, o ServiceNow oferece o interruptor de substituição. Tens de entrar **Sys_properties. LISTA** na **Navegação** de Busca e abrirá a nova página de Propriedades do Sistema. Aqui tem de criar uma nova propriedade com o nome **glide.authenticate.multisso.test.connection.obrigatório** com **o tipo** de dados como **True/False** e, em seguida, definir o **valor** como **Falso**.

          > ![Screenshot da página de resultados de testes](./media/servicenow-tutorial/testconnection-fail.png "Configurar o início de sessão único")

        * Quando lhe pedirem as suas credenciais, insira-as. Verá a seguinte página. Espera-se o erro dos resultados do teste de **logout SSO.** Ignore o erro e selecione **Ativar**.

          ![Screenshot da página de resultados de testes](./media/servicenow-tutorial/servicenowactivate.png "Configurar o início de sessão único")

### <a name="create-servicenow-test-user"></a>Criar o utilizador de teste ServiceNow

O objetivo desta secção é criar um utilizador chamado B.Simon no ServiceNow. O ServiceNow suporta o fornecimento automático de utilizadores, que é ativado por predefinição.

> [!NOTE]
> Se precisar de criar um utilizador manualmente, contacte a equipa de suporte ao [Cliente ServiceNow](https://www.servicenow.com/support/contact-support.html).

### <a name="configure-servicenow-express-sso"></a>Configure ServiceNow Express SSO

1. Inscreva-se na sua aplicação ServiceNow Express como administrador.

2. No painel esquerdo, selecione **Single Sign-On**.

    ![Screenshot da aplicação ServiceNow Express, com um único sign-on em destaque](./media/servicenow-tutorial/ic7694980ex.png "Configure url de aplicativo")

3. Na caixa de diálogo **Single Sign-On,** selecione o ícone de configuração na parte superior direita e detete as seguintes propriedades:

    ![Screenshot da caixa de diálogo de início de sinal único](./media/servicenow-tutorial/ic7694981ex.png "Configure url de aplicativo")

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

    e. Abra o seu certificado codificado Base64 descarregado do portal Azure no Notepad. Copie o conteúdo do mesmo na sua pasta e, em seguida, cole-o na caixa de texto **do Certificado PEM.**

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
    > A página inicial da instância ServiceNow é uma concatenação do url `https://fabrikam.service-now.com/navpage.do`do seu **inquilino ServiceNow** e **/navpage.do** (por exemplo: ).

    b. Para **ID de Entidade / Emitente,** insira o URL do seu inquilino ServiceNow.

    c. Para **O Público URI,** insira o URL do seu inquilino ServiceNow.

    d. Para **o Tempo Skew,** introduza **60**.

    e. Para **o Campo do Utilizador,** insira o **e-mail**.

    > [!NOTE]
    > Pode configurar o Azure AD para emitir o ID do utilizador da AD Azure (nome principal do utilizador) ou o endereço de e-mail como o identificador único no token SAML. Faça-o indo para a secção de**Attributes** > **inscrição única** do **portal** > Azure e mapeando o campo desejado para o atributo identificador de **nome.** O valor armazenado para o atributo selecionado em Azure AD (por exemplo, nome principal do utilizador) deve corresponder ao valor armazenado no ServiceNow para o campo introduzido (por exemplo, user_name).

    f. Selecione **Guardar**.

## <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo ServiceNow no Painel de Acesso, deve ser automaticamente inscrito no ServiceNow para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="test-sso-for-servicenow-classic-mobile"></a>Teste SSO para ServiceNow Classic (Mobile)

1. Abra a sua aplicação **ServiceNow Classic (Mobile)** e execute os seguintes passos:

    a. Selecione o sinal de mais no canto inferior direito.

    ![Screenshot da aplicação ServiceNow Classic, com sinal mais destacado](./media/servicenow-tutorial/test03.png)

    b. Introduza o nome da instância ServiceNow e selecione **Continuar**.

    ![Screenshot da página Add Instance, com Continuar em destaque](./media/servicenow-tutorial/test04.png)

    c. Na página **Iniciar** sessão, execute os seguintes passos:

    ![Screenshot do Log in page, com utilização de login externo destacado](./media/servicenow-tutorial/test01.png)

    *  Introduza o B.simon@contoso.comnome de **utilizador,** como .

    *  Selecione **USE LOGIN EXTERNO**. Foi redirecionado para a página da AD Azure para iniciar sessão.

    *  Introduza as suas credenciais. Se houver alguma autenticação de terceiros, ou qualquer outra funcionalidade de segurança ativada, o utilizador deve responder em conformidade. A **página inicial** da aplicação aparece.

        ![Screenshot da página inicial da aplicação](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar o aprovisionamento de utilizadores](servicenow-provisioning-tutorial.md)

- [Experimente serviceNow com Azure AD](https://aad.portal.azure.com)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/protect-servicenow)

- [Como proteger o ServiceNow com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)