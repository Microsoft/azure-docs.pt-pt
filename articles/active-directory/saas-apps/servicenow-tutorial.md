---
title: 'Tutorial: Integração do Active Directory do Azure com o ServiceNow | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e ao ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74cc66119eeb82eeb5448a14f2f1762822a9bc80
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56876573"
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Tutorial: Integração do Active Directory do Azure com ServiceNow

Neste tutorial, saiba como integrar o ServiceNow com o Azure Active Directory (Azure AD).
Integração do ServiceNow com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao ServiceNow.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada ao ServiceNow (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ServiceNow, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* ServiceNow logon único habilitado subscrição
* Para o ServiceNow, uma instância ou um inquilino do ServiceNow, versão Calgary ou superior
* Para o ServiceNow Express, uma instância do ServiceNow Express, versão Helsínquia ou superior
* O inquilino do ServiceNow tem de ter o [vários fornecedor único início de sessão no plug-in](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) ativada. Isso pode ser feito [submeter um pedido de serviço](https://hi.service-now.com).
* Para a configuração automática, ative o plug-in de multi fornecedor do ServiceNow.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta do ServiceNow **SP** iniciada SSO

* Suporta do ServiceNow [ **automatizada** aprovisionamento de utilizadores](servicenow-provisioning-tutorial.md)

## <a name="adding-servicenow-from-the-gallery"></a>Adicionando o ServiceNow da Galeria

Para configurar a integração do ServiceNow com o Azure AD, terá de adicionar ServiceNow a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar ServiceNow a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **ServiceNow**, selecione **ServiceNow** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![ServiceNow na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com ServiceNow com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no ServiceNow deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com ServiceNow, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único do ServiceNow](#configure-azure-ad-single-sign-on-for-servicenow)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o ServiceNow Single Sign-On](#configure-servicenow-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Configurar o Azure AD início de sessão único para Express do ServiceNow](#configure-azure-ad-single-sign-on-for-servicenow-express)**  - para permitir que os utilizadores utilizar esta funcionalidade.
4. **[Configurar o ServiceNow Express início de sessão único](#configure-servicenow-express-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
5. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
6. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
7. **[Criar utilizador de teste do ServiceNow](#create-servicenow-test-user)**  - para ter um equivalente da Eduarda Almeida na ServiceNow que está ligado à representação de utilizador do Azure AD.
8. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>Configurar o Azure AD início de sessão único do ServiceNow

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com ServiceNow, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **ServiceNow** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![URLs de domínio do ServiceNow e únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<instance-name>.service-now.com/navpage.do`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Estes valores não são reais. Terá de atualizar estes valores de URL de início de sessão real e o identificador que é explicado mais tarde no tutorial. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

    a. Clique no botão de cópia para copiar **Url de metadados de Federação de aplicação** e cole-o no bloco de notas, como este Url de metadados de Federação da aplicação irá ser utilizado mais tarde no tutorial.

    b. Clique em **baixe** para transferir **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

6. Sobre o **configurar ServiceNow** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-servicenow-single-sign-on"></a>Configurar o ServiceNow Single Sign-On

1. Inicie sessão sua aplicação ServiceNow como administrador.

2. Ativar a **integração - vários único início de sessão instalador de fornecedor** Plug-in ao seguir os passos seguintes:

    a. No painel de navegação no lado esquerdo, pesquise **System Definition** secção da barra de pesquisa e, em seguida, clique em **plug-ins**.

    ![Ativar o plug-in](./media/servicenow-tutorial/tutorial_servicenow_03.png "ativar Plug-in")

    b. Procure **integração - vários único início de sessão instalador de fornecedor**.

     ![Ativar o plug-in](./media/servicenow-tutorial/tutorial_servicenow_04.png "ativar Plug-in")

    c. Selecione o plug-in. Clique com o botão direito do rato e selecione **Activate/atualização**.

     ![Ativar o plug-in](./media/servicenow-tutorial/tutorial_activate.png "ativar Plug-in")

    d. Clique nas **Activate** botão.

     ![Ativar o plug-in](./media/servicenow-tutorial/tutorial_activate1.png "ativar Plug-in")

3. No painel de navegação no lado esquerdo, pesquise **SSO de multi fornecedor** secção da barra de pesquisa e, em seguida, clique em **propriedades**.

    ![Configurar o URL da aplicação](./media/servicenow-tutorial/tutorial_servicenow_06.png "configurar o URL da aplicação")

4. Sobre o **várias propriedades do fornecedor de SSO** caixa de diálogo, execute os seguintes passos:

    ![Configurar o URL da aplicação](./media/servicenow-tutorial/ic7694981.png "configurar o URL da aplicação")

    * Como **ativar vários provedor SSO**, selecione **Sim**.
  
    * Como **ativar automaticamente a importação de utilizadores de todos os fornecedores de identidade para a tabela de utilizador**, selecione **Sim**.

    * Como **depuração de ativar o registo para o fornecedor de vários integração de SSO**, selecione **Sim**.

    * No **o campo no utilizador de tabela que...**  caixa de texto, tipo **user_name**.
  
    * Clique em **Guardar**.

5. Existem duas formas em que **ServiceNow** pode ser configurado - automática e Manual.

6. Para configurar **ServiceNow** automaticamente, siga os passos abaixo:

    * Retorno para o **ServiceNow** início de sessão único na página no portal do Azure.

    * Um clique configurar o serviço é fornecido para o ServiceNow ou seja, para que o Azure AD configurar automaticamente ServiceNow para autenticação baseada no SAML. Para ativar este serviço, aceda ao **configuração do ServiceNow** secção, clique em **configurar ServiceNow** para abrir a janela de início de sessão de configurar.

        ![Configurar o início de sessão único](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    * Introduza o nome da instância do ServiceNow, o nome de utilizador administrador e a palavra-passe de administrador no **configurar o início de sessão** formam e clique em **configurar agora**. Tenha em atenção que o nome de utilizador do administrador fornecido tem de ter o **security_admin** função atribuída no ServiceNow para que isso funcione. Caso contrário, para configurar manualmente o ServiceNow ao utilizar o Azure AD como fornecedor de identidade de SAML, clique em **configurar manualmente o início de sessão único** e copie o **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** da secção de referência rápida.

        ![Configurar o URL da aplicação](./media/servicenow-tutorial/configure.png "configurar o URL da aplicação")

    * Inicie sessão sua aplicação ServiceNow como administrador.

    * Na configuração automática todas as definições são configuradas no **ServiceNow** lado, mas a **certificado X.509** não está ativada por predefinição. Tem para mapeá-lo manualmente para o fornecedor de identidade no ServiceNow. Siga os passos abaixo para o mesmo:

    * No painel de navegação no lado esquerdo, pesquise **SSO de multi fornecedor** secção da barra de pesquisa e, em seguida, clique em **fornecedores de identidade**.

        ![Configurar o início de sessão único](./media/servicenow-tutorial/tutorial_servicenow_07.png "configurar início de sessão único")

    * Clique no fornecedor de identidade gerada automaticamente

        ![Configurar o início de sessão único](./media/servicenow-tutorial/tutorial_servicenow_08.png "configurar início de sessão único")

    *  Sobre o **fornecedor de identidade** secção, execute os seguintes passos:

        ![Configurar o início de sessão único](./media/servicenow-tutorial/automatic_config.png "configurar início de sessão único")

        * Na **Name** caixa de texto, escreva um nome para a sua configuração (por exemplo, **Microsoft Azure federado início de sessão único**).

        * Remova o preenchida **SingleLogoutRequest do fornecedor de identidade** valor a partir da caixa de texto.

        * Cópia **home page do ServiceNow** valor, cole-a no **URL de início de sessão** caixa de texto no **ServiceNow domínio e URLs** secção no portal do Azure.

            > [!NOTE]
            > Home page de instância do ServiceNow é uma concatenação das suas **URL de inquilino do ServiceNow** e **/navpage.do** (por exemplo:`https://fabrikam.service-now.com/navpage.do`).

        * Cópia **ID de entidade / emissor** valor, cole-o na **identificador** caixa de texto no **ServiceNow domínio e URLs** secção no portal do Azure.

        * Certifique-se de que **política de NameID** está definida como `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` valor. 

    * Desloque para baixo para o **certificado X.509** secção, selecione **editar**.

        ![Configurar o início de sessão único](./media/servicenow-tutorial/tutorial_servicenow_09.png "configurar início de sessão único")

    * Selecione o certificado e clique em ícone de seta para a direita para adicionar o certificado

        ![Configurar o início de sessão único](./media/servicenow-tutorial/tutorial_servicenow_11.png "configurar início de sessão único")

    * Clique em **Guardar**.

    * Clique em **Testar ligação** no canto superior direito da página.

        ![Ativar o plug-in](./media/servicenow-tutorial/tutorial_activate2.png "ativar Plug-in")

    * Depois de clicar no **Testar ligação**, irá obter a janela de pop-up, onde tem de introduzir credenciais e a página com resultados é mostrado. O **resultados de teste do fim de sessão de SSO** erro é esperado a ignorar o erro e clique em **ativar** botão.

        ![Configurar o início de sessão único](./media/servicenow-tutorial/servicenowactivate.png "configurar início de sessão único")
  
7. Para configurar **ServiceNow** manualmente, siga os passos abaixo:

    * Inicie sessão sua aplicação ServiceNow como administrador.

    * No painel de navegação no lado esquerdo, clique em **fornecedores de identidade**.

        ![Configurar o início de sessão único](./media/servicenow-tutorial/tutorial_servicenow_07.png "configurar início de sessão único")

    * Sobre o **fornecedores de identidade** caixa de diálogo, clique em **New**.

        ![Configurar o início de sessão único](./media/servicenow-tutorial/ic7694977.png "configurar início de sessão único")

    * Sobre o **fornecedores de identidade** caixa de diálogo, clique em **SAML**.

        ![Configurar o início de sessão único](./media/servicenow-tutorial/ic7694978.png "configurar início de sessão único")

    * Sobre o **metadados de fornecedor de identidade de importação** pop-up, execute os seguintes passos:

        ![Configurar o início de sessão único](./media/servicenow-tutorial/idp.png "configurar início de sessão único")

        * Introduza o **Url de metadados de Federação de aplicação** que copiou do portal do Azure.

        * Clique em **importar**.

    * Lê o URL de metadados de IdP e preenche a todas as informações de campos.

        ![Configurar o início de sessão único](./media/servicenow-tutorial/ic7694982.png "configurar início de sessão único")

        * Na **Name** caixa de texto, escreva um nome para a sua configuração (por exemplo, **Microsoft Azure federado início de sessão único**).

        * Remova o preenchida **SingleLogoutRequest do fornecedor de identidade** valor a partir da caixa de texto.

        * Cópia **home page do ServiceNow** valor, cole-a no **URL de início de sessão** caixa de texto no **ServiceNow domínio e URLs** secção no portal do Azure.

            > [!NOTE]
            > Home page de instância do ServiceNow é uma concatenação das suas **URL de inquilino do ServiceNow** e **/navpage.do** (por exemplo:`https://fabrikam.service-now.com/navpage.do`).

        * Cópia **ID de entidade / emissor** valor, cole-o na **identificador** caixa de texto no **ServiceNow domínio e URLs** secção no portal do Azure.

        * Certifique-se de que **política de NameID** está definida como `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` valor.

        * Clique em **Advanced**. Na **campo de utilizador** caixa de texto, tipo **e-mail** ou **user_name**, dependendo do que campo é utilizado para identificar exclusivamente os utilizadores na sua implementação do ServiceNow.

            > [!NOTE]
            > Pode configurar o Azure AD para emitir o ID de utilizador do Azure AD (nome principal de utilizador) ou o endereço de e-mail como o identificador exclusivo no SAML token ao aceder a **ServiceNow > atributos > início de sessão único** seção do portal do Azure e para mapear o campo desejado para o **nameidentifier** atributo. O valor armazenado para o atributo selecionado no Azure AD (por exemplo, nome principal de utilizador) tem de corresponder ao valor armazenado no ServiceNow para o campo introduzido (por exemplo, user_name)

        * Clique em **Testar ligação** no canto superior direito da página.

        * Depois de clicar no **Testar ligação**, irá obter a janela de pop-up, onde tem de introduzir credenciais e a página com resultados é mostrado. O **resultados de teste do fim de sessão de SSO** erro é esperado a ignorar o erro e clique em **ativar** botão.

          ![Configurar o início de sessão único](./media/servicenow-tutorial/servicenowactivate.png "configurar início de sessão único")

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>Configurar o Azure AD Single Sign-On para Express do ServiceNow

1. Na [portal do Azure](https://portal.azure.com/), na **ServiceNow** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![URLs de domínio do ServiceNow e únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<instance-name>.service-now.com/navpage.do`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Estes valores não são reais. Terá de atualizar estes valores de URL de início de sessão real e o identificador que é explicado mais tarde no tutorial. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Um clique configurar o serviço é fornecido para o ServiceNow ou seja, para que o Azure AD configurar automaticamente ServiceNow para autenticação baseada no SAML. Para ativar este serviço, aceda ao **configurar ServiceNow** secção, clique em **ver instruções passo a passo** para abrir a janela de início de sessão de configurar.

    ![Configurar o início de sessão único](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Introduza o nome da instância do ServiceNow, o nome de utilizador administrador e a palavra-passe de administrador no **configurar o início de sessão** formam e clique em **configurar agora**. Tenha em atenção que o nome de utilizador do administrador fornecido tem de ter o **security_admin** função atribuída no ServiceNow para que isso funcione. Caso contrário, para configurar manualmente o ServiceNow ao utilizar o Azure AD como fornecedor de identidade de SAML, clique em **configurar manualmente o início de sessão único** e copie o **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** da secção de referência rápida.

    ![Configurar o URL da aplicação](./media/servicenow-tutorial/configure.png "configurar o URL da aplicação")

### <a name="configure-servicenow-express-single-sign-on"></a>Configurar o ServiceNow Express início de sessão único

1. Inicie sessão sua aplicação ServiceNow Express como administrador.

2. No painel de navegação no lado esquerdo, clique em **Single Sign-On**.

    ![Configurar o URL da aplicação](./media/servicenow-tutorial/ic7694980ex.png "configurar o URL da aplicação")

3. Sobre o **Single Sign-On** caixa de diálogo, clique no ícone de configuração no canto superior direito e defina as propriedades seguintes:

    ![Configurar o URL da aplicação](./media/servicenow-tutorial/ic7694981ex.png "configurar o URL da aplicação")

    a. Botão de alternar **ativar vários provedor SSO** à direita.

    b. Botão de alternar **depuração de ativar o registo para o fornecedor de vários integração de SSO** à direita.

    c. No **o campo no utilizador de tabela que...**  caixa de texto, tipo **user_name**.

4. Sobre o **início de sessão único** caixa de diálogo, clique em **adicionar novo certificado**.

    ![Configurar o início de sessão único](./media/servicenow-tutorial/ic7694973ex.png "configurar início de sessão único")

5. Sobre o **certificados X.509** caixa de diálogo, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/servicenow-tutorial/ic7694975.png "configurar início de sessão único")

    a. Na **nome** caixa de texto, escreva um nome para a sua configuração (por exemplo: **TestSAML2.0**).

    b. Selecione **Active Directory**.

    c. Como **formato**, selecione **PEM**.

    d. Como **tipo**, selecione **confiar no certificado de Store**.

    e. Abra o seu certificado codificado Base64 transferido a partir do portal do Azure no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado PEM** caixa de texto.

    f. Clique em **Update**

6. Sobre o **início de sessão único** caixa de diálogo, clique em **adicionar IdP novo**.

    ![Configurar o início de sessão único](./media/servicenow-tutorial/ic7694976ex.png "configurar início de sessão único")

7. Sobre o **adicionar novo fornecedor de identidade** caixa de diálogo, em **configurar o fornecedor de identidade**, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/servicenow-tutorial/ic7694982ex.png "configurar início de sessão único")

    a. Na **nome** caixa de texto, escreva um nome para a sua configuração (por exemplo: **SAML 2.0**).

    b. Na **URL de fornecedor de identidade** campo, cole o valor de **ID do fornecedor de identidade**, que copiou do portal do Azure.

    c. Na **AuthnRequest do fornecedor de identidade** campo, cole o valor de **URL de pedido de autenticação**, que copiou do portal do Azure.

    d. Na **SingleLogoutRequest do fornecedor de identidade** campo, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure

    e. Como **certificado do fornecedor de identidade**, selecione o certificado que criou no passo anterior.

8. Clique em **definições avançadas**e, em **propriedades adicionais do fornecedor de identidade**, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/servicenow-tutorial/ic7694983ex.png "configurar início de sessão único")

    a. Na **protocolo de enlace para o IDP SingleLogoutRequest** caixa de texto, tipo **urn: oasis: nomes: tc: SAML:2.0:bindings:HTTP-redirecionar**.

    b. Na **política NameID** caixa de texto, tipo **urn: oasis: nomes: tc: SAML:1.1:nameid-formato: não especificado**.

    c. Na **método AuthnContextClassRef**, tipo `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Desmarcar **criar um AuthnContextClass**.

9. Sob **propriedades adicionais do fornecedor de serviço**, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/servicenow-tutorial/ic7694984ex.png "configurar início de sessão único")

    a. Na **home page do ServiceNow** caixa de texto, escreva o URL da sua home page de instância de ServiceNow.

    > [!NOTE]
    > Home page de instância do ServiceNow é uma concatenação das suas **URL de inquilino do ServiceNow** e **/navpage.do** (por exemplo: `https://fabrikam.service-now.com/navpage.do`).

    b. Na **ID de entidade / emissor** caixa de texto, escreva o URL do seu inquilino do ServiceNow.

    c. Na **URI de audiência** caixa de texto, escreva o URL do seu inquilino do ServiceNow.

    d. Na **relógio inclinar** caixa de texto, tipo **60**.

    e. Na **campo de utilizador** caixa de texto, tipo **e-mail** ou **user_name**, dependendo do que campo é utilizado para identificar exclusivamente os utilizadores na sua implementação do ServiceNow.

    > [!NOTE]
    > Pode configurar o Azure AD para emitir o ID de utilizador do Azure AD (nome principal de utilizador) ou o endereço de e-mail como o identificador exclusivo no SAML token ao aceder a **ServiceNow > atributos > início de sessão único** seção do portal do Azure e para mapear o campo desejado para o **nameidentifier** atributo. O valor armazenado para o atributo selecionado no Azure AD (por exemplo, nome principal de utilizador) tem de corresponder ao valor armazenado no ServiceNow para o campo introduzido (por exemplo, user_name)

    f. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao ServiceNow.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **ServiceNow**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **ServiceNow**.

    ![A ligação do ServiceNow na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-servicenow-test-user"></a>Criar utilizador de teste do ServiceNow

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no ServiceNow. ServiceNow suporta o aprovisionamento automático de utilizadores, que está por predefinição, ativada. Pode encontrar mais detalhes [aqui](servicenow-provisioning-tutorial.md) sobre como configurar o aprovisionamento automático de utilizadores.

> [!NOTE]
> Se precisar de criar manualmente um utilizador, terá de contactar o [equipa de suporte de cliente do ServiceNow](https://www.servicenow.com/support/contact-support.html)

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de ServiceNow, no painel de acesso, deve ser automaticamente conectado ao ServiceNow para a qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar o aprovisionamento do utilizador](servicenow-provisioning-tutorial.md)
