---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a Netvision Compas | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Netvision Compas.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2020
ms.author: jeedes
ms.openlocfilehash: 423ac882c81582f2843eeba37d11c660662ad6e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92519406"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netvision-compas"></a>Tutorial: Azure Ative Directory integração única (SSO) com Netvision Compas

Neste tutorial, você vai aprender a integrar Netvision Compas com Azure Ative Directory (Azure AD). Quando integra o Netvision Compas com a Azure AD, pode:

* Controle em Azure AD que tem acesso a Netvision Compas.
* Permita que os seus utilizadores sejam automaticamente inscritos no Netvision Compas com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Netvision Compas assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Netvision Compas suporta **SP e IDP** iniciado SSO
* Assim que configurar o Netvision Compas, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O Controlo de Sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)


## <a name="adding-netvision-compas-from-the-gallery"></a>Adicionar Netvision Compas da galeria

Para configurar a integração do Netvision Compas no AD Azure, é necessário adicionar o Netvision Compas da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** digite **Netvision Compas** na caixa de pesquisa.
1. Selecione **Netvision Compas** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netvision-compas"></a>Configurar e testar a Azure AD um único sign-on para netvision Compas

Configure e teste Azure AD SSO com Netvision Compas usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Netvision Compas.

Para configurar e testar o Azure AD SSO com a Netvision Compas, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Netvision Compas SSO](#configure-netvision-compas-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Configure o utilizador de teste netvision Compas](#configure-netvision-compas-test-user)** - para ter uma contraparte de B.Simon na Netvision Compas que está ligada à representação AD Ad do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Netvision Compas,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<TENANT>.compas.cloud/Identity/Saml20`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte a [equipa de suporte do Cliente Netvision Compas](mailto:contact@net.vision) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o ficheiro de metadados e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)



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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Netvision Compas.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Netvision Compas**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-netvision-compas-sso"></a>Configurar Netvision Compas SSO

Nesta secção é permitido o SSO SAML na **Netvision Compas**.
1. Inicie sessão na **Netvision Compas** utilizando uma conta administrativa e aceda à área de administração.

    ![Área de administração](media/netvision-compas-tutorial/admin.png)

1. Localize a área **do Sistema** e selecione **Fornecedores de Identidade.**

    ![IDPs de administrador](media/netvision-compas-tutorial/admin-idps.png)

1. Selecione a ação **Add** para registar Azure AD como um novo IDP.

    ![Adicionar IDP](media/netvision-compas-tutorial/idps-add.png)

1. Selecione **SAML** para o **tipo Fornecedor**.
1. Introduza valores significativos para os campos **nome do Visor** e **Descrição.**
1. Atribua os utilizadores **da Netvision Compas** ao IDP selecionando a lista de **utilizadores disponíveis** e selecionando o botão **Add selecionado.** Os utilizadores também podem ser designados para o IDP enquanto seguem o procedimento de provisionamento.
1. Para a opção **MEtadados** SAML clique no botão **Escolher Ficheiro** e selecione o ficheiro de metadados previamente guardado no seu computador.
1. Clique em **Guardar**.

    ![Editar IDP](media/netvision-compas-tutorial/idp-edit.png)


### <a name="configure-netvision-compas-test-user"></a>Configure o utilizador de teste Netvision Compas

Nesta secção, configura um utilizador existente na **Netvision Compas** para utilizar o Azure AD para SSO.
1. Siga o procedimento de provisionamento do utilizador **Netvision Compas,** conforme definido pela sua empresa ou edite uma conta de utilizador existente.
1. Ao definir o perfil do utilizador, certifique-se de que o endereço de **e-mail (pessoal)** do utilizador corresponde ao nome de utilizador Azure AD: username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.

    ![Editar utilizador](media/netvision-compas-tutorial/user-config.png)

Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure.

### <a name="using-the-access-panel-idp-initiated"></a>Utilizar o Painel de Acesso (IDP iniciado).

Quando clicar no azulejo Netvision Compas no Painel de Acesso, deverá ser automaticamente inscrito no Netvision Compas para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

### <a name="directly-accessing-netvision-compas-sp-initiated"></a>Aceda diretamente à Netvision Compas (SP iniciado).

1. Aceda ao URL **Netvision Compas.** Por exemplo, `https://tenant.compas.cloud`.
1. Introduza o nome de utilizador **Netvision Compas** e selecione **Seguinte**.

    ![Utilizador de início de sessão](media/netvision-compas-tutorial/login-user.png)

1. **(opcional)** Se o utilizador receber vários IDPs dentro **do Netvision Compas,** é apresentada uma lista de IDPs disponíveis. Selecione o IDP AD Azure configurado anteriormente no **Netvision Compas**.

    ![Escolha de login](media/netvision-compas-tutorial/login-choose.png)

1. Você é redirecionado para Azure AD para realizar a autenticação. Uma vez autenticado com sucesso, deverá ser automaticamente inscrito no **Netvision Compas** para o qual configura sSO.

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente Netvision Compas com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)