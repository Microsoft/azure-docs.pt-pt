---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com consola de administrador netskope Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e a Consola de Administrador netskope.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2020
ms.author: jeedes
ms.openlocfilehash: c29a1b409a2deb3695470f5bbec4865afdbae8e1
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97913996"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-administrator-console"></a>Tutorial: Azure Ative Directory integração única (SSO) com consola de administrador netskope

Neste tutorial, você vai aprender a integrar a Consola de Administrador netskope com Azure Ative Directory (Azure AD). Quando integrar a Consola de Administrador netskope com Azure AD, pode:

* Controle em Azure AD que tem acesso à Consola de Administrador netskope.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Consola de Administrador netskope com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Netskope Administrator Console única sign-on (SSO) ativada subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Consola de administrador da Netskope suporta **SP e IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.


## <a name="adding-netskope-administrator-console-from-the-gallery"></a>Adicionar consola de administrador netskope da galeria

Para configurar a integração da Consola administradora netskope em AD Azure, precisa adicionar a Consola de Administrador netskope da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite consola administrador netskope** na caixa de pesquisa.
1. Selecione **a Consola de Administrador netskope** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-netskope-administrator-console"></a>Configurar e testar Azure AD SSO para consola administradora de Netskope

Configure e teste Azure AD SSO com consola administrador netskope usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Consola de Administrador netskope.

Para configurar e testar O Azure AD SSO com consola administrador netskope, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a consola SSO do administrador de Netskope](#configure-netskope-administrator-console-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Crie o utilizador de teste de consola de administrador netskope](#create-netskope-administrator-console-test-user)** - para ter uma contraparte de B.Simon na Consola administrador de Netskope que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **da consola netskope,** encontre a secção **Gerir** e selecione um único sinal **de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://<tenant_host_name>/saml/acs`

    > [!NOTE]
    > O valor não é real. Atualizar o valor com o URL de resposta real. Receberá o valor explicado mais tarde no tutorial.

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<tenantname>.goskope.com`

    > [!NOTE]
    > Os valores de URL de inscrição não são reais. Atualizar o valor url de inscrição com o URL de inscrição real. Contacte a [equipa de suporte do cliente da consola de administrador netskope](mailto:support@netskope.com) para obter o valor de URL de inscrição. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação netskope Administrator Console espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação netskope Administrator Console espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name |  Atributo de origem|
    | ---------| --------- |
    | papel de administração | user.assignedroles |

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui) para saber como criar papéis em Azure AD.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção Configurar a consola **do administrador netskope,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Consola de Administrador netskope.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **consola de administrador netskope**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se tiver configurado as funções como explicado no acima, pode selecioná-lo a partir do Dropdown de **função Select.**
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-netskope-administrator-console-sso"></a>Configurar consola de administrador de Netskope SSO

1. Abra um novo separador no seu navegador e inscreva-se no site da empresa netskope Administrator Console como administrador.

1. Clique no **separador Definições** a partir do painel de navegação esquerdo.

    ![Screenshot mostra Definição selecionada no painel de navegação.](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Clique no separador **Administração.**

    ![A screenshot mostra a Administração selecionada a partir de Definições.](./media/netskope-cloud-security-tutorial/config-administration.png)

1. Clique no separador **SSO.**

    ![A screenshot mostra S S O selecionado na Administração.](./media/netskope-cloud-security-tutorial/config-sso.png)

1. Na secção **Definições de Rede,** execute os seguintes passos:
    
    ![A screenshot mostra definições de rede onde pode introduzir os valores descritos.](./media/netskope-cloud-security-tutorial/config-pasteurls.png)

    a. Copiar O valor URL do Serviço de Apoio ao Consumidor de **Afirmação** e colá-lo na caixa de texto **URL de resposta** na secção **configuração básica do SAML** no portal Azure.

    b. Copiar **Serviço Fornecedor de ID valor** e colá-lo na caixa de texto **identifier** na secção **configuração de SAML básico** no portal Azure.

1. Clique nas **definições de EDITO** na secção **Definições SSO/SLO.**

    ![A screenshot mostra as definições S S O / S L O onde pode selecionar DEFINIÇÕES DE EDIÇÃO.](./media/netskope-cloud-security-tutorial/config-editsettings.png)

1. Na janela popup **Definições,** execute os seguintes passos;

    ![A screenshot mostra a caixa de diálogo Definições onde pode introduzir os valores descritos.](./media/netskope-cloud-security-tutorial/configuration.png)

    a. **Selecione Ativar SSO**.

    b. Na caixa de texto **do URL do IDP,** cole o valor URL de **login,** que copiou a partir do portal Azure.

    c. Na caixa de texto **IDPENTITY ID,** cole o valor do **identificador Azure AD,** que copiou do portal Azure.

    d. Abra o certificado codificado Base64 descarregado no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **IDP CERTIFICATE.**

    e. **Selecione Ativar SSO**.

    f. Na caixa de texto **URL do IDP SLO,** cole o valor **URL logout,** que copiou a partir do portal Azure.

    exemplo, Clique **em SUBMETER.**

### <a name="create-netskope-administrator-console-test-user"></a>Criar utilizador de teste de consola de administrador de Netskope

1. Abra um novo separador no seu navegador e inscreva-se no site da empresa netskope Administrator Console como administrador.

1. Clique no **separador Definições** a partir do painel de navegação esquerdo.

    ![A screenshot mostra definições selecionadas.](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Clique no separador **Plataforma Ativa.**

    ![A screenshot mostra plataforma ativa selecionada a partir de Definições.](./media/netskope-cloud-security-tutorial/user1.png)

1. Clique **no separador Utilizadores.**

    ![A screenshot mostra utilizadores selecionados a partir da Plataforma Ativa.](./media/netskope-cloud-security-tutorial/add-user.png)

1. CLIQUE **EM UTILIZADORES DE ADD**.

    ![A screenshot mostra a caixa de diálogo do Utilizadores onde pode selecionar utilizadores ADD.](./media/netskope-cloud-security-tutorial/user-add.png)

1. Introduza o endereço de e-mail do utilizador que pretende adicionar e clique em **ADD**.

    ![O Screenshot mostra adicionar utilizadores onde pode introduzir uma lista de utilizadores.](./media/netskope-cloud-security-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o Sinal de Consola do Administrador netskope no URL, onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de assinatura da consola do administrador netskope e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na Consola de Administrador netskope para a qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo da Consola de Administrador netskope nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na Consola de Administrador netskope para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurar a Consola de Administrador netskope, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).