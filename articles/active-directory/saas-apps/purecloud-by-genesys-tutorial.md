---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com PureCloud by Genesys | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o PureCloud by Genesys.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 7c6f84ee3bb4920dbe57221b8b0bbf9f5880742b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653032"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Tutorial: Azure Ative Directory single sign-on (SSO) integração com PureCloud by Genesys

Neste tutorial, você vai aprender a integrar PureCloud by Genesys com Azure Ative Directory (Azure AD). Quando integrar PureCloud by Genesys com Azure AD, pode:

* Controle em Azure AD que tem acesso ao PureCloud by Genesys.
* Permita que os seus utilizadores sejam automaticamente inscritos no PureCloud pela Genesys com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura ativada por PureCloud by Genesys (SSO)- ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* PureCloud by Genesys suporta **SP e IDP**– SSO iniciado.

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="add-purecloud-by-genesys-from-the-gallery"></a>Adicione PureCloud por Genesys da galeria

Para configurar a integração do PureCloud by Genesys em AD Azure, você deve adicionar PureCloud by Genesys da galeria à sua lista de aplicações geridas saaS. Para tal, siga estes passos:

1. Inscreva-se no portal Azure utilizando uma conta de trabalho ou escola ou utilizando uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **de galeria,** **escreva PureCloud by Genesys** na caixa de pesquisa.
1. Selecione **PureCloud by Genesys** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-purecloud-by-genesys"></a>Configure e teste Azure AD SSO para PureCloud by Genesys

Configure e teste Azure AD SSO com PureCloud by Genesys usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, deve estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em PureCloud by Genesys.

Para configurar e testar Azure AD SSO com PureCloud by Genesys, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com B.Simon.
    1. **[Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que a B.Simon utilize um único sinal de Ad AD.
1. **[Configure pureCloud by Genesys SSO](#configure-purecloud-by-genesys-sso)** para configurar as definições de inscrição única no lado da aplicação.
    1. **[Crie o utilizador de teste PureCloud by Genesys](#create-purecloud-by-genesys-test-user)** para ter uma contraparte de B.Simon em PureCloud by Genesys que está ligada à representação AD AZure do utilizador.
1. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Para ativar o Azure AD SSO no portal Azure, siga estes passos:

1. No portal Azure, na página de integração de aplicações **PureCloud by Genesys,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página **de método Select a Single Sign-On,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, selecione o ícone de lápis para **configuração SAML básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa **identifier,** insira os URLs que correspondem à sua região:

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.au/saml
    ```

    b. Na caixa **URL de resposta,** insira os URLs que correspondem à sua região:

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.com.au/saml
    ```

1. Selecione **Definir URLs adicionais** e dar o seguinte passo se quiser configurar a aplicação **no** modo iniciado sp:

    Na caixa **URL de inscrição,** insira os URLs que correspondem à sua região:
    
    ```http
    https://login.mypurecloud.com
    https://login.mypurecloud.de
    https://login.mypurecloud.jp
    https://login.mypurecloud.ie
    https://login.mypurecloud.com.au
    ```

1. A aplicação PureCloud by Genesys espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos:

    ![image](common/default-attributes.png)

1. Além disso, a aplicação PureCloud by Genesys espera que mais alguns atributos sejam repercutidos na resposta SAML, como mostra a tabela seguinte. Estes atributos também são pré-povoados, mas pode revê-los conforme necessário.

    | Name | Atributo de origem|
    | ---------------| --------------- |
    | E-mail | user.userprincipalname |
    | OrganizationName | `Your organization name` |

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar PureCloud by Genesys,** copie o URL apropriado (ou URLs), com base nos seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste chamado B.Simon no portal Azure:

1. No painel esquerdo do portal Azure, selecione **O Diretório Ativo Azure,** selecione **Utilizadores,** e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** introduza o nome de utilizador no seguinte formato: username@companydomain.extension . Por exemplo: `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe Show** e, em seguida, tome nota do valor que é apresentado na caixa **palavra-passe.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao PureCloud by Genesys.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **PureCloud by Genesys**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-purecloud-by-genesys-sso"></a>Configure PureCloud by Genesys SSO

1. Numa janela diferente do navegador web, inscreva-se no PureCloud by Genesys como administrador.

1. Selecione **O Administrador** no topo e, em seguida, vá para **Single Sign-on em** **Integrações**.

    ![A screenshot mostra a janela PureCloud Admin onde pode selecionar Single Sign-on.](./media/purecloud-by-genesys-tutorial/configure-1.png)

1. Mude para o separador **ADFS/Azure AD (Premium)** e siga estes passos:

    ![A screenshot mostra a página Integrações onde pode introduzir os valores descritos.](./media/purecloud-by-genesys-tutorial/configure-2.png)

    a. **Selecione Procurar** para carregar o certificado codificado base-64 que descarregou do portal Azure para o **Certificado ADFS.**

    b. Na caixa **URI do Emitente ADFS,** cole o valor do **Identificador AD AZure** que copiou do portal Azure.

    c. Na caixa **Target URI,** cole o valor do URL de **login** que copiou do portal Azure.

    d. Para o valor **do identificador de partidos de suporte,** vá ao portal Azure e, em seguida, na página de integração da aplicação **PureCloud by Genesys,** selecione o separador **Propriedades** e copie o valor do ID da **aplicação.** Cole-o na caixa de **identificação do Partido De Relying.**

    ![A screenshot mostra o painel de propriedades onde pode encontrar o valor I D da aplicação.](./media/purecloud-by-genesys-tutorial/configure-6.png)

    e. Selecione **Guardar**.

### <a name="create-purecloud-by-genesys-test-user"></a>Crie PureCloud by Genesys utilizador de teste

Para permitir que os utilizadores de Azure AD inscrevam-se no PureCloud by Genesys, devem ser aprovisionados no PureCloud pela Genesys. Em PureCloud by Genesys, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, siga estes passos:**

1. Faça login no PureCloud by Genesys como administrador.

1. Selecione **Admin** no topo e vá para **People** under **People & Permisses**.

    ![A screenshot mostra a janela PureCloud Admin onde pode selecionar Pessoas.](./media/purecloud-by-genesys-tutorial/configure-3.png)

1. Na página **Pessoas,** **selecione Adicionar Pessoa.**

    ![A screenshot mostra a página Pessoas onde pode adicionar uma pessoa.](./media/purecloud-by-genesys-tutorial/configure-4.png)

1. Na caixa de diálogo **Add People to the Organization,** siga estes passos:

    ![A screenshot mostra a página onde pode introduzir os valores descritos.](./media/purecloud-by-genesys-tutorial/configure-5.png)

    a. Na caixa **Nome Completo,** insira o nome de um utilizador. Por exemplo: **B.simon**.

    b. Na caixa **de e-mail,** insira o e-mail do utilizador. Por exemplo: **b.simon \@ contoso.com**.

    c. Selecione **Criar**.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para PureCloud by Genesys Sign no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao PureCloud by Genesys Sign-on URL e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no PureCloud by Genesys para o qual configura o SSO. 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo PureCloud by Genesys nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no PureCloud by Genesys para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado PureCloud by Genesys, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).