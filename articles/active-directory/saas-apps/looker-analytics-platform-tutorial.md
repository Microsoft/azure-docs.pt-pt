---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com a Plataforma Looker Analytics Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Plataforma De Análise Do Looker.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/10/2020
ms.author: jeedes
ms.openlocfilehash: a903bbd27b1f7e6890b0dcb6088cc843788207b5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94667605"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-looker-analytics-platform"></a>Tutorial: Azure Ative Directory integração única (SSO) com a Plataforma Looker Analytics

Neste tutorial, você vai aprender a integrar a Plataforma De Análise Looker com o Azure Ative Directory (Azure AD). Quando integrar a Plataforma De Análise do Looker com AZure AD, pode:

* Controle em Azure AD que tem acesso à Plataforma De Análise do Looker.
* Permita que os seus utilizadores sejam automaticamente inscritos na Plataforma Looker Analytics com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada pela Plataforma Looker Analytics (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Plataforma Looker Analytics suporta SSO iniciado **SP e IDP**
* Plataforma Looker Analytics suporta **provisão de** utilizadores just in time


## <a name="adding-looker-analytics-platform-from-the-gallery"></a>Adicionar Plataforma De Análise Looker da galeria

Para configurar a integração da Plataforma Looker Analytics no Azure AD, é necessário adicionar a Plataforma Looker Analytics da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva Plataforma De Análise do Looker** na caixa de pesquisa.
1. Selecione **a Plataforma De Análise Looker** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-looker-analytics-platform"></a>Configurar e testar Azure AD SSO para a plataforma Looker Analytics

Configure e teste Azure AD SSO com a Plataforma Looker Analytics utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Plataforma Looker Analytics.

Para configurar e testar o Azure AD SSO com a Plataforma Looker Analytics, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Plataforma SSO do Looker Analytics](#configure-looker-analytics-platform-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Looker Analytics Platform test user](#create-looker-analytics-platform-test-user)** - para ter uma contraparte de B.Simon na Plataforma De Análise Looker que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações da **Plataforma Looker Analytics,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `<SPN>_looker`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.looker.com/samlcallback`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<SUBDOMAIN>.looker.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de suporte do Cliente da Plataforma Looker Analytics](mailto:support@looker.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Plataforma De Análise de Configuração,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Plataforma Looker Analytics.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Looker Analytics Platform**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-looker-analytics-platform-sso"></a>Configurar a plataforma de análise looker SSO

1. Numa janela diferente do navegador web, inscreva-se no website da Plataforma Looker Analytics como administrador.

1. Vá ao **Admin**  ->  **Authentication**  ->  **SAML**

    ![screenshot para opção SAML](./media/looker-analytics-platform-tutorial/admin.png)

1. Cole as informações dos **Metadados** da Federação que copiou do portal Azure para a caixa de texto **de metadados IDP** e clique em **Carregar**.

    ![screenshot para upload de metadados](./media/looker-analytics-platform-tutorial/metadata.png)

1. Execute os seguintes passos na secção **Definições de Atributos do Utilizador.**

    ![screenshot para Definições de Atributos do Utilizador](./media/looker-analytics-platform-tutorial/user-attribute-settings.png)

    a. Adicione o seguinte valor no campo Email Attr: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    b. Adicione o seguinte valor ao campo Fname Attr: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    c. Adicione o seguinte valor ao campo Lname Attr: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    d. Na **Autenticação** do Utilizador de Teste, clique em **Test SAML Authentication**. Se a página que carrega diz "Resposta do servidor validada com sucesso", configura com sucesso o exemplo para integração SAML.
    
    e. Em **Configurações de Guardar e Aplicar**, Verifique a caixa que **confirmei a configuração acima e pretenda aplicá-la globalmente**.

    f. Clique no botão **'Definições de actualização'.**

### <a name="create-looker-analytics-platform-test-user"></a>Criar utilizador de teste da Plataforma Looker Analytics

Nesta secção, um utilizador chamado Britta Simon é criado na Plataforma Looker Analytics. A Plataforma Looker Analytics suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na Plataforma Looker Analytics, uma nova é criada após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de entrada da plataforma Looker Analytics, onde pode iniciar o fluxo de login.  

* Vá ao Sinal da Plataforma Looker Analytics no URL diretamente e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na Plataforma de Análise do Looker para a qual configura o SSO 

Também pode utilizar o Microsoft Access Panel para testar a aplicação em qualquer modo. Quando clicar no azulejo da Plataforma Looker Analytics no Painel de Acesso, se configurado no modo SP, será redirecionado para a página de início do login para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na Plataforma De Análise do Looker para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Próximos passos

Uma vez configurado a Plataforma Looker Analytics, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


