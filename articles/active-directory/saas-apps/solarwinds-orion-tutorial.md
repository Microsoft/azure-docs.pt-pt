---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com SolarWinds Orion | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SolarWinds Orion.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: jeedes
ms.openlocfilehash: 4ac5bf2756b82361388ab9f2866b80c63395f90d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104591389"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-solarwinds-orion"></a>Tutorial: Azure Ative Directory integração única (SSO) com SolarWinds Orion

Neste tutorial, você vai aprender a integrar SolarWinds Orion com Azure Ative Directory (Azure AD). Quando integrar o SolarWinds Orion com Azure AD, pode:

* Controle em Azure AD que tem acesso a SolarWinds Orion.
* Capacitar os seus utilizadores a serem automaticamente inscritos no SolarWinds Orion com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* SolarWinds Orion única subscrição ativa (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* SolarWinds Orion suporta **SP e IDP** iniciado SSO.

## <a name="add-solarwinds-orion-from-the-gallery"></a>Adicione SolarWinds Orion da galeria

Para configurar a integração do SolarWinds Orion em Azure AD, você precisa adicionar SolarWinds Orion da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** digite **SolarWinds Orion** na caixa de pesquisa.
1. Selecione **SolarWinds Orion** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-solarwinds-orion"></a>Configurar e testar Azure AD SSO para Orion SolarWinds

Configure e teste Azure AD SSO com SolarWinds Orion usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no SolarWinds Orion.

Para configurar e testar o Azure AD SSO com o SolarWinds Orion, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure SolarWinds Orion SSO](#configure-solarwinds-orion-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create SolarWinds Orion test user](#create-solarwinds-orion-test-user)** - para ter uma contraparte de B.Simon em SolarWinds Orion que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **SolarWinds Orion,** encontre a secção **Gerir** e selecione **um único sinal de sessão.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/SAMLLogin.aspx`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/Login.aspx`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de suporte do Cliente SolarWinds Orion](mailto:technicalsupport@solarwinds.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação SolarWinds Orion espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação SolarWinds Orion espera que alguns mais atributos sejam repercutidos na resposta SAML, que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Name |  Atributo de origem|
    | ----------- | --------- |
    | FirstName | user.givenname |
    | LastName | utilizador.sobrenome |
    | E-mail |user.mail |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configuração do Orion SolarWinds,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Evergreen.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Evergreen**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-solarwinds-orion-sso"></a>Configure SolarWinds Orion SSO

1. Inicie sessão no SolarWinds Orion e vá às **Definições**  ->  **Todas as Definições**.

    ![A screenshot mostra todas as definições selecionadas a partir de Definições.](./media/solarwinds-orion-tutorial/settings.png)

1. Na secção contas do **utilizador,** selecione **Configuração SAML**.

    ![Screenshot mostrar configuração SAML selecionada a partir de contas de utilizador.](./media/solarwinds-orion-tutorial/configure-user-accounts.png)

1. Clique no **FORNECEDOR DE IDENTIDADE ADD**.

    ![A screenshot mostra a configuração SAML onde pode selecionar O FORNECEDOR DE IDENTIDADE ADD.](./media/solarwinds-orion-tutorial/configure-add-identity-provider.png)

1. Execute os seguintes passos na página **'Adicionar Fornecedor de Identidade':**

    ![A screenshot mostra a página 'Adicionar Fornecedor de Identidade' onde pode introduzir os valores descritos.](./media/solarwinds-orion-tutorial/configure-solarwinds.png)

    a. Vá ao **separador Configurar.**

    b. Na caixa de texto **'Nome do Fornecedor de Identidade',** forneça qualquer nome válido como `My SSO service` .

    c. Na caixa de texto **SSO Target URL,** cole o valor URL de **login,** que copiou a partir do portal Azure.

    d.  Na caixa de texto URL do **emitente,** cole o valor do **identificador Azure AD,** que copiou a partir do portal Azure.

    e. Abra o Certificado descarregado **(Base64)** do portal Azure para o Bloco de Notas e cole o conteúdo na caixa de texto **do Certificado de Assinatura X.509.**

    f. Clique em **Guardar**.

### <a name="create-solarwinds-orion-test-user"></a>Criar utilizador de teste SolarWinds Orion

1. Inicie sessão no website da Orion SolarWinds e vá às **Definições**  ->  **Todas as Definições**.

    ![A screenshot mostra todas as definições selecionadas a partir de Definições.](./media/solarwinds-orion-tutorial/settings.png)

1. Na secção **contas de utilizador,** selecione **Gerir contas**.

    ![Screenshot mostrar configuração SAML selecionada.](./media/solarwinds-orion-tutorial/user-accounts.png)

1. No separador **CONTAS INDIVIDUAIS,** clique em **ADD NEW ACCOUNT**.

    ![Screenshot mostra ADD NEW ACCOUNT selecionado em Contas de Gestão.](./media/solarwinds-orion-tutorial/create-user.png)

1. Selecione o tipo de conta, que precisa para criar utilizadores ou grupos individuais SAML.

    ![Screenshot mostra Adicionar Conta Nova onde pode selecionar o tipo de conta.](./media/solarwinds-orion-tutorial/create-user-new-account.png)

1.  Na caixa de texto **name ID,** insira o nome que deve coincidir com o nome de utilizador ou nome de grupo exatamente como em Azure AD.

1.  Clique em **Seguinte** e, em seguida, envie a página.

    ![Screenshot mostra Adicionar Nova Conta onde pode introduzir o nome I D de Azure A D.](./media/solarwinds-orion-tutorial/create-user-name-id.png)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para SolarWinds Orion Sign on URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de sinal de Orion SolarWinds e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no SolarWinds Orion para o qual configura o SSO. 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo SolarWinds Orion nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Orion SolarWinds para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado SolarWinds Orion, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
