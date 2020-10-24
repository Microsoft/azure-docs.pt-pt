---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Workplace by Facebook Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Workplace by Facebook.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2020
ms.author: jeedes
ms.openlocfilehash: f20849e4085adcca909d990a2ae9fb7b9751220b
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520114"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>Tutorial: Azure Ative Directory integração única (SSO) com Workplace by Facebook

Neste tutorial, você vai aprender a integrar Workplace by Facebook com Azure Ative Directory (Azure AD). Quando integra o Workplace by Facebook com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Workplace pelo Facebook.
* Permita que os seus utilizadores sejam automaticamente inscritos no Workplace pelo Facebook com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Workplace by Facebook single sign-on (SSO) enabled subscrição.

> [!NOTE]
> O Facebook tem dois produtos, Workplace Standard (gratuito) e Workplace Premium (pago). Qualquer inquilino do Workplace Premium pode configurar a integração SCIM e SSO sem outras implicações para o custo ou licenças necessárias. SSO e SCIM não estão disponíveis em instâncias padrão do local de trabalho.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Workplace by Facebook suporta **SP** iniciado SSO
* Workplace by Facebook suporta **provisão just-in-time**
* Workplace by Facebook suporta ** [fornecimento automático de utilizadores](workplacebyfacebook-provisioning-tutorial.md)**
* Workplace by Facebook Mobile application pode agora ser configurado com Azure AD para ativar SSO. Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.
* Uma vez configurado Workplace by Facebook, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Adicionar Workplace by Facebook da galeria

Para configurar a integração do Workplace pelo Facebook no Azure AD, é necessário adicionar Workplace by Facebook da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva Workplace by Facebook** na caixa de pesquisa.
1. Selecione **Workplace by Facebook** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>Configure e teste Azure AD SSO para o local de trabalho pelo Facebook

Configure e teste Azure AD SSO com Workplace by Facebook usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Workplace pelo Facebook.

Para configurar e testar O SSO Azure AD com Workplace pelo Facebook, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
2. **[Configure Workplace by Facebook SSO](#configure-workplace-by-facebook-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    * **[Create Workplace by Facebook test user](#create-workplace-by-facebook-test-user)** - ter uma contrapartida de B.Simon no Local de Trabalho pelo Facebook que está ligada à representação AD AZure do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações workplace **by Facebook,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<instancename>.facebook.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://www.facebook.com/company/<instanceID>`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Sign-On real, identifier e responder URL. Consulte a página de autenticação do Painel de Trabalho para obter os valores corretos para a sua comunidade workplace, isto é explicado mais tarde no tutorial.

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar o Local de Trabalho pelo Facebook,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Workplace pelo Facebook.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Workplace by Facebook**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-workplace-by-facebook-sso"></a>Configurar o Local de Trabalho pelo Facebook SSO

1. Para automatizar a configuração no Local de Trabalho pelo Facebook, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar o Workplace pelo Facebook** irá direcioná-lo para o Workplace por aplicação do Facebook. A partir daí, forneça as credenciais de administração para assinar no Workplace pelo Facebook. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-5.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar o Workplace pelo Facebook manualmente, abra manualmente uma nova janela do navegador web e inscreva-se no seu Site de Trabalho pelo site da empresa do Facebook como administrador e execute os seguintes passos:

    > [!NOTE]
    > Como parte do processo de autenticação SAML, o Workplace pode utilizar cadeias de consulta de até 2,5 quilobytes de tamanho para passar parâmetros para Azure AD.

1. No painel de navegação **Security**esquerdo, navegue para o  >  **separador autenticação de** segurança.

    ![Painel de Administração](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

    a. Verifique a opção de sinal único na opção **SSO.**
    
    b. Clique em **+Adicionar novo Provedor SSO**.
    > [!NOTE]
    > Certifique-se de que também verifica a caixa de verificação de início de sessão de palavra-passe. Os administradores podem precisar desta opção para fazer login durante a reversão do certificado para evitar que se bloqueiem.

1. No **separador autenticação,** selecione **Single-Sign On (SSO)** e execute os seguintes passos:

    ![Separador de autenticação](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. Em **nome do Provedor SSO,** insira o nome de instância SSO como Azureadsso.

    b. Na caixa de texto **URL SAML,** cole o valor do URL de **login,** que copiou do portal Azure.

    c. Na caixa de texto **URL DO EMITENTE SAML,** cole o valor do **Identificador AD AZure,** que copiou do portal Azure.

    d. Abra o **certificado codificado base-64** no bloco de notas descarregado do portal Azure, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **do Certificado SAML.**

    e. Copie o **URL do Público** para o seu exemplo e cole-o na caixa de texto **Identifier (Entity ID)** na secção **de Configuração SAML Básica** no portal Azure.

    f. Copie o **URL do destinatário** para o seu exemplo e cole-o em Assinar na caixa de texto **URL** na secção **de configuração SAML básica** no portal Azure.

    exemplo, Copie o **URL ACS (Serviço de Apoio ao Consumidor de Afirmação)** para o seu exemplo e cole-o na caixa de texto **URL de resposta** na secção de **configuração SAML básica** no portal Azure.

    h. Percorra a parte inferior da secção e clique no botão **SSO de teste.** Isto resulta numa janela popup que aparece com a página de login AZure AD apresentada. Insira as suas credenciais normalmente para autenticar.

    **Resolução de problemas:** Certifique-se de que o endereço de e-mail devolvido do AZure AD é o mesmo que a conta Workplace com a qual está a iniciar sessão.

    i. Uma vez concluído o teste com sucesso, desloque-se até à parte inferior da página e clique no botão **Guardar.**

    j. Todos os utilizadores que utilizem o Workplace serão agora apresentados com a página de login Azure AD para autenticação.

1. **Redirecionamento de logout SAML (opcional)** -

    Pode optar por configurar opcionalmente um Url de logout SAML, que pode ser usado para apontar para a página de logout do Azure AD. Quando esta definição estiver ativada e configurada, o utilizador deixará de ser direcionado para a página de início de saúde do Local de Trabalho. Em vez disso, o utilizador será redirecionado para o url que foi adicionado na definição de redirecionamento de logotes SAML.

### <a name="configuring-reauthentication-frequency"></a>Configuração da frequência de reautora

Pode configurar o Workplace para solicitar uma verificação SAML todos os dias, três dias, semana, duas semanas, mês ou nunca.

> [!NOTE]
> O valor mínimo para a verificação do SAML nas aplicações móveis está definido para uma semana.

Também pode forçar um reset SAML para todos os utilizadores que utilizem o botão: Exija agora a autenticação SAML para todos os utilizadores.

### <a name="create-workplace-by-facebook-test-user"></a>Criar Workplace por utilizador de teste do Facebook

Nesta secção, um utilizador chamado B.Simon é criado no Workplace pelo Facebook. O local de trabalho pelo Facebook suporta o provisionamento just-in-time, que é ativado por padrão.

Não há ação para ti nesta secção. Se um utilizador não existir no Workplace pelo Facebook, um novo é criado quando tenta aceder ao Workplace pelo Facebook.

>[!Note]
>Se precisar de criar um utilizador manualmente, contacte [o Workplace pela equipa de suporte do Cliente do Facebook](https://www.workplace.com/help/work/)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no Azulejo do Facebook no Painel de Acesso, deverá ser automaticamente inscrito no Workplace pelo Facebook para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>Teste SSO para Local de Trabalho pelo Facebook (móvel)

1. Local de trabalho aberto por aplicação Facebook Mobile. Na placa na página, clique em **LOG IN**.

    ![O sinal em](./media/workplacebyfacebook-tutorial/test05.png)

2. Insira o seu email de negócios e clique **em CONTINUE.**

    ![O e-mail](./media/workplacebyfacebook-tutorial/test02.png)

3. CLIQUE **APENAS UMA VEZ**.

    ![O tempo](./media/workplacebyfacebook-tutorial/test04.png)

4. Clique **em Permitir**.

    ![O Permitir](./media/workplacebyfacebook-tutorial/test03.png)

5. Finalmente, após o início do sôm.    

    ![A página inicial](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Configurar o Provisionamento do Utilizador](workplacebyfacebook-provisioning-tutorial.md)

- [Experimente o Workplace by Facebook com Azure AD](https://aad.portal.azure.com)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)