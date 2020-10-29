---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Workware / Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o Workware.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: f3f48695f0ef26b6e54073dbfc4bb0c61802fe58
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905725"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workware"></a>Tutorial: Azure Ative Directory integração única (SSO) com workware

Neste tutorial, você vai aprender a integrar Workware com Azure Ative Directory (Azure AD). Quando integra o Workware com AZure AD, pode:

* Controle em Azure AD que tem acesso a Workware.
* Permita que os seus utilizadores sejam automaticamente inscritos no Workware com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada por um único sso (SSO) do workware.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Workware suporta **IDP** iniciado SSO

## <a name="adding-workware-from-the-gallery"></a>Adicionar artigos de trabalho da galeria

Para configurar a integração do Workware no AZure AD, é necessário adicionar Workware da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações** .
1. Para adicionar nova aplicação, selecione **Nova aplicação** .
1. Na secção Adicionar a partir da secção **de galeria,** **escreva workware** na caixa de pesquisa.
1. Selecione **Workware** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-workware"></a>Configure e teste Azure AD SSO para workware

Configure e teste Azure AD SSO com workware usando um utilizador de teste chamado **B.Simon** . Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Workware.

Para configurar e testar a Azure AD SSO com workware, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Workware SSO](#configure-workware-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Workware test user](#create-workware-test-user)** - para ter uma contraparte de B.Simon em Workware que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Workware,** encontre a secção **Gerir** e selecione **um único sinal de sação** .
1. Na página de método **de inscrição** única, selecione **SAML** .
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. No **set-on único com** a página SAML, insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `<WORKWARE_URL>/WW/AuthServices`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `<WORKWARE_URL>/WW/AuthServices/Acs`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte [a equipa de suporte do Cliente Workware](mailto:support@activeops.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar o Workware,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory** , selecione **Utilizadores** , e, em seguida, selecione **Todos os utilizadores** .
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome** , introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Workware.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações** .
1. Na lista de aplicações, selecione **Workware** .
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos** .
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-workware-sso"></a>Configurar o SSO de Workware

Para utilizar a função SSO no Workware, a seguinte configuração tem de ser concluída:

#### <a name="enable-sso-permissions-for-workware-system-administrators"></a>Ativar permissões SSO para administradores de sistemas de software de trabalho

* Para permitir que os administradores do sistema de workware criem a autenticação SSO, a permissão de autenticação SSO (na **categoria de permissões de configuração do Sistema de administração > na definição de sistema nas Definições do Sistema > Permissões para** o ecrã de funções) tem de ser ativada para administradores de sistemas de workware.

    ![Permissão de autenticação SSO](./media/workware-tutorial/permission.png)

#### <a name="set-up-sso-authentication-in-workware"></a>Configurar a autenticação SSO em workware

1. Aceda à página **de Definições do Sistema** e clique na **Autenticação SSO**

1. Na secção **autenticação SSO,** clique no botão **Adicionar Autenticação SSO** e execute os seguintes passos: 

    ![Autenticação SSO](./media/workware-tutorial/authentication.png)

    1. No **Fornecedor de Identidade Externa,** forneça o nome do IDP.
    1. Selecione **SAML2.0** como **Tipo de Autenticação**
    1. Na caixa de texto **URL do Fornecedor de Identidade SignIn,** introduza o valor URL de **login,** que copiou a partir do portal Azure.
    1. Na caixa de texto **URL do Fornecedor de Identidade,** insira o valor do **identificador Azure AD,** que copiou a partir do portal Azure.
    1. Na caixa de texto **URL do Fornecedor de Identidade,** introduza o valor URL **logout,** que copiou a partir do portal Azure.
    1. Clique em **Enable** .
    1. Faça o upload do **Certificado** de Produto De identidade descarregado no **Certificado de Fornecedor** de Identidade a partir do portal Azure.
    1. Clique em **Guardar**


### <a name="create-workware-test-user"></a>Criar utilizador de teste de workware

1. Inscreva-se no site do Workware como administrador.

1. Selecione **Admin > Criar / Ver > Contas de Utilizador > Adicionar Novos**

1. Execute os seguintes passos na página seguinte.

    ![Utilizador de teste](./media/workware-tutorial/create-user.png)

    a. Dê um nome válido no campo **Nome.**

    b. Selecione **o Tipo de Autenticação** como **SSO** .

    c. Introduza os campos necessários e clique em **Guardar** .

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no Workware para o qual configura o SSO

* Pode utilizar o Microsoft Access Panel. Quando clicar no telha workware no Painel de Acesso, deverá ser automaticamente inscrito no Workware para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Próximas etapas

Uma vez configurado o Workware, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


