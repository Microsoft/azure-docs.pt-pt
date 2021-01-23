---
title: 'Tutorial: Integração única de sign-on (SSO) do Check Point CloudGuard Dome9 Arc | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Check Point CloudGuard Dome9 Arc.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: e5bb8a32cfd73e67141a25531594e8a3b6f793c6
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732232"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-cloudguard-dome9-arc"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Check Point CloudGuard Dome9 Arc

Neste tutorial, você vai aprender a integrar Check Point CloudGuard Dome9 Arc com Azure Ative Directory (Azure AD). Quando integrar o Check Point CloudGuard Dome9 Arc com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Check Point CloudGuard Dome9 Arc.
* Ativar os seus utilizadores a serem automaticamente inscritos no Check Point CloudGuard Dome9 Arc com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Verifique o sinal de entrada única do CloudGuard Dome9 Arc (SSO) ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Check Point CloudGuard Dome9 Arc suporta **SP e IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Adicionando check point CloudGuard Dome9 Arc da galeria

Para configurar a integração do Check Point CloudGuard Dome9 Arc em AD Azure, é necessário adicionar Check Point CloudGuard Dome9 Arc da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **escreva Check Point CloudGuard Dome9 Arc** na caixa de pesquisa.
1. Selecione **Check Point CloudGuard Dome9 Arc** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-check-point-cloudguard-dome9-arc"></a>Configure e teste Azure AD SSO para check point CloudGuard Dome9 Arc

Configure e teste Azure AD SSO com Check Point CloudGuard Dome9 Arc usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Check Point CloudGuard Dome9 Arc.

Para configurar e testar O SSO Azure AD com Check Point CloudGuard Dome9 Arc, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Check Point CloudGuard Dome9 Arc SSO](#configure-check-point-cloudguard-dome9-arc-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Check Point CloudGuard Dome9 Arc test user](#create-check-point-cloudguard-dome9-arc-test-user)** - para ter uma contraparte de B.Simon em Check Point CloudGuard Dome9 Arc que está ligada à representação AD AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Check Point CloudGuard Dome9 Arc,** encontre a secção **'Gerir'** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://secure.dome9.com/sso/saml/<yourcompanyname>`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de resposta real e URL de inscrição. Você receberá o `<company name>` valor da secção **Configure Check Point CloudGuard Dome9 Arc SSO,** que é explicada mais tarde no tutorial. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação Check Point CloudGuard Dome9 Arc espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação Check Point CloudGuard Dome9 Arc espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com o seu requisito.
    
    | Name |  Atributo de origem|
    | ---------------| --------------- |
    | membros de | user.assignedroles |

    >[!NOTE]
    >Clique [aqui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) para saber como criar papéis em Azure AD.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **set up Check Point CloudGuard Dome9 Arc,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Check Point CloudGuard Dome9 Arc.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Check Point CloudGuard Dome9 Arc**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se tiver configurado as funções como explicado no acima, pode selecioná-lo a partir do Dropdown de **função Select.**
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-check-point-cloudguard-dome9-arc-sso"></a>Configurar ponto de verificação CloudGuard Dome9 Arc SSO

1. Para automatizar a configuração no Quadro de Verificação CloudGuard Dome9 Arc, é necessário instalar a **extensão do navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar Check Point CloudGuard Dome9 Arc** irá direcioná-lo para a aplicação Check Point CloudGuard Dome9 Arc. A partir daí, forneça as credenciais de administração para assinar no Check Point CloudGuard Dome9 Arc. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar manualmente o Check Point CloudGuard Dome9 Arc, abra manualmente uma nova janela do navegador web e inscreva-se no site da empresa Check Point CloudGuard Dome9 Arc como administrador e execute os seguintes passos:

2. Clique nas **Definições** de Perfil no canto superior direito e, em seguida, clique em **Definições de Conta**. 

    ![Screenshot que mostra o menu "Definições de perfil" com "Definições de conta" selecionadas.](./media/dome9arc-tutorial/configure1.png)

3. Navegue para **SSO** e clique em **ENABLE**.

    ![Screenshot que mostra o separador "S O" e "Enable" selecionados.](./media/dome9arc-tutorial/configure2.png)

4. Na secção de Configuração SSO, execute os seguintes passos:

    ![Verifique a configuração do arco do CloudGuard Dom9](./media/dome9arc-tutorial/configure3.png)

    a. Introduza o nome da empresa na caixa de texto **ID conta.** Este valor deve ser utilizado na **Resposta** e **Assinar no** URL mencionado na secção **de Configuração SAML Básica** do portal Azure.

    b. Na caixa de texto **emitente,** cole o valor do **Identificador AD AZure,** que copiou no portal Azure.

    c. Na url textbox **do Idp endpoint,** cole o valor do URL de **Login,** que copiou para formar o portal Azure.

    d. Abra o certificado codificado Base64 descarregado no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto do **certificado X.509.**

    e. Clique em **Guardar**.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Criar check point CloudGuard Dome9 Arc utilizador

Para permitir que os utilizadores de Azure AD inscrevam-se no Check Point CloudGuard Dome9 Arc, devem ser aprovisionados na aplicação. Check Point CloudGuard Dome9 Arc suporta provisões just-in-time mas para que funcione corretamente, o utilizador tem de selecionar uma **função** específica e atribuir o mesmo ao utilizador.

   >[!Note]
   >Para a criação **de funções** e outros detalhes [contacte a equipa de suporte do Cliente Check Point CloudGuard Dome9 Arc](mailto:Dome9@checkpoint.com).

**Para providenciar manualmente uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa Check Point CloudGuard Dome9 Arc como administrador.

2. Clique nos **Utilizadores & Funções** e, em seguida, clique em **Utilizadores**.

    ![Screenshot que mostra "Utilizadores & Roles" com a ação "Utilizadores" selecionada.](./media/dome9arc-tutorial/user1.png)

3. Clique **em ADD USER**.

    ![Screenshot que mostra "Utilizadores & Roles" com o botão "ADD USER" selecionado.](./media/dome9arc-tutorial/user2.png)

4. Na secção **Criar Utilizador,** execute os seguintes passos:

    ![Adicionar Empregado](./media/dome9arc-tutorial/user3.png)

    a. Na caixa de texto **por e-mail,** escreva o e-mail do utilizador como B.Simon@contoso.com .

    b. Na caixa de texto **Name First,** escreva o primeiro nome do utilizador como B.

    c. Na caixa de texto **Do Último Nome,** escreva o apelido do utilizador como Simon.

    d. Faça **o SSO User** como **On**.

    e. Clique **em CREATE**.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Check Point CloudGuard Dome9 Arc Sign no URL onde pode iniciar o fluxo de login.  

* Vá ao Teste de Ponto CloudGuard Dome9 Arc Sign-on URL diretamente e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Check Point CloudGuard Dome9 Arc para o qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo do 'Check Point CloudGuard Dome9' nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Arco CloudGuard Dome9 para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Próximos passos

Assim que configurar o Check Point CloudGuard Dome9 Arc, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
