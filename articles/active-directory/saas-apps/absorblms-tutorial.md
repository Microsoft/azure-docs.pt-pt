---
title: 'Tutorial: Integração do Diretório Ativo Azure com | de Absorção de LMS Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o Absorb LMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 268943463002ddd1dbdbf67df9587758f81f537f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646525"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Tutorial: Integração do Diretório Ativo Azure com Absorver LMS

Neste tutorial, você vai aprender a integrar Absorver LMS com Azure Ative Directory (Azure AD). Quando integrar absorver LMS com AD Azure, pode:

* Controlo em Azure AD que tem acesso a Absorver LMS.
* Ative os seus utilizadores a serem automaticamente inscritos para absorver LMS com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD Azure com Absorver LMS, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Absorva a assinatura ativada por LMS.

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Absorver LMS suporta **IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="add-absorb-lms-from-the-gallery"></a>Adicione Absorva LMS da galeria

Para configurar a integração do Absorb LMS em AD Azure, é necessário adicionar O Absorvão LMS da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Absorver LMS** na caixa de pesquisa.
1. **Selecione Absorver LMS** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-absorb-lms"></a>Configure e teste Azure AD SSO para absorver LMS

Configure e teste Azure AD SSO com Absorb LMS usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Absorver LMS.

Para configurar e testar o Azure AD SSO com Absorver LMS, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Absorva O LMS SSO](#configure-absorb-lms-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Absorb LMS test user](#create-absorb-lms-test-user)** - para ter uma contraparte de B.Simon in Absorb LMS que está ligada à representação AD AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Absorva LMS,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na **configuração single Sign-On com página SAML,** clique no botão **Editar** para abrir o diálogo **básico de configuração SAML.**

    Se estiver a utilizar **Absorva 5 - UI** utilize a seguinte configuração:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:: `https://<SUBDOMAIN>.myabsorb.com/account/saml`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:: `https://<SUBDOMAIN>.myabsorb.com/account/saml`

    Se estiver a utilizar **o Absorb 5 - New Learner Experience,** utilize a seguinte configuração:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte [a equipa de suporte do Cliente absorver LMS](https://support.absorblms.com/hc/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. A imagem seguinte mostra a lista de atributos predefinidos, onde como **identificador** de nome é mapeado com **user.userprincipalname**.

    ![image](common/edit-attribute.png)

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

7. Na secção **Configurar absorver LMS,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Absorb LMS.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, **selecione Absorb LMS**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-absorb-lms-sso"></a>Configure absorver LMS SSO

1. Numa nova janela do navegador web, inscreva-se no site da empresa Absorção LMS como administrador.

2. Selecione o botão **Conta** no topo direito.

    ![O botão Conta](./media/absorblms-tutorial/account.png)

3. No painel 'Conta', selecione **Definições do Portal**.

    ![A ligação de Definições do Portal](./media/absorblms-tutorial/portal.png)

4. Selecione o **separador 'Gestão de Definições SSO'.**

    ![O separador Utilizadores](./media/absorblms-tutorial/sso.png)

5. Na página **'Gerir as Definições Sign-On' Únicas,** faça o seguinte:

    ![A única página de configuração de sign-on](./media/absorblms-tutorial/settings.png)

    a. Na caixa de texto **Name,** insira o nome como Azure AD Marketplace SSO.

    b. Selecione **SAML** como **método**.

    c. No Bloco de Notas, abra o certificado que descarregou a partir do portal Azure. Remova as **etiquetas ---BEGIN Certificate---** e **---END Certificate---.** Em seguida, na caixa **chave,** cole o conteúdo restante.

    d. Na caixa **Modo,** selecione **Identity Provider Iniciado**.

    e. Na caixa **Id Property,** selecione o atributo que configura como o identificador do utilizador em Azure AD. Por exemplo, se *o identificador de nomes* for selecionado em Azure AD, selecione **o nome de utilizador**.

    f. Selecione **Sha256** como **tipo de assinatura**.

    exemplo, Na caixa **URL de login,** cole o URL de Acesso ao **Utilizador** a partir da página **Propriedades** da aplicação do portal Azure.

    h. No **URL logo,** cole o valor **URL de inscrição** que copiou a partir da janela **de inscrição** de configuração do portal Azure.

    i. Toggle **Redirecionar automaticamente** para **on**.

6. **Selecione Save.**

    ![O único permite que o SSO Login altere](./media/absorblms-tutorial/save.png)

### <a name="create-absorb-lms-test-user"></a>Criar absorver o utilizador de teste LMS

Para que os utilizadores de Azure AD inscrevam-se para absorver LMS, devem ser configurados em Absorver LMS. No caso do Absorver LMS, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento do utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa Absorva LMS como administrador.

2. No painel **de Utilizadores,** selecione **Utilizadores**.

    ![A ligação dos Utilizadores](./media/absorblms-tutorial/users.png)

3. Selecione **o separador Utilizador.**

    ![A lista de lançamentos adicionais](./media/absorblms-tutorial/add.png)

4. Na página **'Adicionar Utilizador',** faça o seguinte:

    ![A página do Utilizador adicionar](./media/absorblms-tutorial/user.png)

    a. Na caixa **First Name,** escreva o primeiro nome, como **Britta.**

    b. Na caixa Do **Último Nome,** escreva o apelido, tal como **Simão.**

    c. Na caixa **username,** escreva um nome completo, como **Britta Simon**.

    d. Na **caixa palavra-passe,** escreva a palavra-passe do utilizador.

    e. Na caixa **'Confirmar palavra-passe',** retipe a palavra-passe.

    f. Desa esta medida **é ativa** para **ative**.

5. **Selecione Save.**

    ![O único permite que o SSO Login altere](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > Por predefinição, o Provisionamento do Utilizador não está ativado em SSO. Se o cliente quiser ativar esta funcionalidade, tem de a configurar como mencionado [nesta](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) documentação. Note também que a Provisioing do Utilizador só está disponível no **Absorb 5 - New Learner Experience** com URL ACS-`https://company.myabsorb.com/api/rest/v2/authentication/saml`

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no Absorção LMS para o qual configura o SSO.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Absorver LMS nas Minhas Apps, deverá ser automaticamente inscrito no Absorver LMS para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Absorver LMS, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).