---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com meraki Dashboard | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Meraki Dashboard.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 74009c7e7f2ad28655c9c5322a063a17da96e0c5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99493926"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-meraki-dashboard"></a>Tutorial: Azure Ative Directory integração única (SSO) com o Meraki Dashboard

Neste tutorial, você vai aprender a integrar Meraki Dashboard com Azure Ative Directory (Azure AD). Quando integrar o Meraki Dashboard com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Painel Meraki.
* Permita que os seus utilizadores sejam automaticamente inscritos no Meraki Dashboard com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Meraki Dashboard assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Meraki Dashboard suporta **IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-meraki-dashboard-from-the-gallery"></a>Adicionar Meraki Dashboard da galeria

Para configurar a integração do Meraki Dashboard no Azure AD, é necessário adicionar o Meraki Dashboard da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** digite **Meraki Dashboard** na caixa de pesquisa.
1. Selecione **Meraki Dashboard** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-meraki-dashboard"></a>Configurar e testar Azure AD SSO para o Painel Meraki

Configure e teste Azure AD SSO com Meraki Dashboard usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Meraki Dashboard.

Para configurar e testar o Azure AD SSO com o Meraki Dashboard, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Meraki Dashboard SSO](#configure-meraki-dashboard-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Meraki Dashboard test user](#create-meraki-dashboard-test-user)** - para ter uma contraparte de B.Simon no Painel Meraki que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Meraki Dashboard,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** execute os seguintes passos:
     
    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://n27.meraki.com/saml/login/m9ZEgb/< UNIQUE ID >`

    > [!NOTE]
    > O valor URL de resposta não é real. Atualize este valor com o valor real do URL de resposta, que é explicado mais tarde no tutorial.

1. Clique no botão **Guardar**.

1. A aplicação Meraki Dashboard espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Meraki Dashboard espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Name | Atributo de origem|
    | ---------------| --------- |
    | `https://dashboard.meraki.com/saml/attributes/username` | user.userprincipalname |
    | `https://dashboard.meraki.com/saml/attributes/role` | user.assignedroles |

    > [!NOTE]
    > Para entender como configurar papéis em Azure AD, consulte [aqui.](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)

1. Na secção **Certificado de Assinatura SAML,** clique em Editar o botão **Editar** para abrir o diálogo **do Certificado de Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

1. Na secção **Certificado de Assinatura SAML,** copie o **Valor de Impressão Digital** e guarde-o no seu computador. Este valor precisa de ser convertido para incluir os cólons para que o painel meraki o compreenda. Por exemplo, se a impressão digital do Azure `C2569F50A4AAEDBB8E` for, terá de ser alterada `C2:56:9F:50:A4:AA:ED:BB:8E` para a utilizar mais tarde no Painel Meraki.

    ![Valor da impressão digital do polegar da cópia](common/copy-thumbprint.png)

1. Na secção **'Configurar' Meraki Dashboard,** copie o valor URL logout e guarde-o no seu computador.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Painel Meraki.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Meraki Dashboard**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.

    ![função de utilizador](./media/meraki-dashboard-tutorial/user-role.png)

    > [!NOTE]
    > **Selecione uma** opção de função será desativada e a função predefinitiva é USER para utilizador selecionado.

1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-meraki-dashboard-sso"></a>Configurar meraki dashboard SSO

1. Para automatizar a configuração dentro do Painel Meraki, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar o Painel Meraki** irá direcioná-lo para a aplicação Meraki Dashboard. A partir daí, forneça as credenciais de administração para assinar no Meraki Dashboard. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o Meraki Dashboard manualmente, numa janela diferente do navegador, inscreva-se no site da empresa Meraki Dashboard como administrador.

1. Navegar para  ->  **configurações de organização**.

    ![Separador de definições do painel de instrumentos Meraki](./media/meraki-dashboard-tutorial/configure-1.png)

1. Em Autenticação, altere **o SSO SAML** para **SAML SSO ativado**.

    ![Autenticação do Painel Meraki](./media/meraki-dashboard-tutorial/configure-2.png)

1. Clique **em Adicionar um IdP SAML**.

    ![Meraki Dashboard Adicionar um IdP SAML](./media/meraki-dashboard-tutorial/configure-3.png)

1. Cole o Valor **de Impressão Polegar** convertido, que copiou do portal Azure e convertido em formato especificado, como mencionado no passo 9 da secção anterior em **X.590 cert SHA1 caixa de texto de impressão digital SHA1.** Em seguida, clique em **Guardar**. Depois de poupar, a URL do consumidor aparecerá. Copie o valor do URL do consumidor e cole-o na caixa de texto **URL de resposta** na Secção de **Configuração Básica SAML** no portal Azure.

    ![Configuração do painel meraki](./media/meraki-dashboard-tutorial/configure-4.png)

### <a name="create-meraki-dashboard-test-user"></a>Criar utilizador de teste meraki dashboard

1. Numa janela diferente do navegador web, inscreva-se no painel meraki como administrador.

1. Navegue para  ->  **administradores da** organização.

    ![Administradores do Painel Meraki](./media/meraki-dashboard-tutorial/user-1.png)

1. Na secção funções de administrador SAML, clique no botão **de função Add SAML.**

    ![Meraki Dashboard Adicionar botão de função SAML](./media/meraki-dashboard-tutorial/user-2.png)

1. Introduza o Role **meraki_full_admin**, marque **o acesso da Organização** como **Full** e clique em **Criar papel.** Repita o processo para **meraki_readonly_admin**, desta vez marque **o acesso da Organização** como caixa só de **Leitura.**
 
    ![Meraki Dashboard cria utilizador](./media/meraki-dashboard-tutorial/user-3.png)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no Painel Meraki para o qual configura o SSO

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo meraki dashboard nas Minhas Apps, deverá ser automaticamente inscrito no Painel Meraki para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Meraki Dashboard, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
