---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com a RightCrowd Workforce Management Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a RightCrowd Workforce Management.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/24/2020
ms.author: jeedes
ms.openlocfilehash: 0300d6216862cf1533c431a13977dbeea52d4414
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355205"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-rightcrowd-workforce-management"></a>Tutorial: Azure Ative Directory integração única (SSO) com a RightCrowd Workforce Management

Neste tutorial, você vai aprender a integrar a RightCrowd Workforce Management com o Azure Ative Directory (Azure AD). Quando integrar a RightCrowd Workforce Management com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à RightCrowd Workforce Management.
* Capacitar os seus utilizadores a serem automaticamente inscritos na RightCrowd Workforce Management com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada pela RightCrowd Workforce Management (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.



* RightCrowd Workforce Management apoia **SP e IDP** iniciado SSO
* RightCrowd Workforce Management suporta **provisão de** utilizadores just in time


## <a name="adding-rightcrowd-workforce-management-from-the-gallery"></a>Adicionar RightCrowd Workforce Management da galeria

Para configurar a integração da RightCrowd Workforce Management em Azure AD, precisa adicionar a RightCrowd Workforce Management da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite RightCrowd Workforce Management** na caixa de pesquisa.
1. Selecione **a RightCrowd Workforce Management** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-rightcrowd-workforce-management"></a>Configurar e testar Azure AD SSO para a RightCrowd Workforce Management

Configure e teste Azure AD SSO com RightCrowd Workforce Management usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na RightCrowd Workforce Management.

Para configurar e testar a Azure AD SSO com a RightCrowd Workforce Management, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o RightCrowd Workforce Management SSO](#configure-rightcrowd-workforce-management-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create RightCrowd Workforce Management test user](#create-rightcrowd-workforce-management-test-user)** - para ter uma contrapartida de B.Simon na RightCrowd Workforce Management que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **RightCrowd Workforce Management,** encontre a secção **Gerir** e selecione **um único sinal de saúde**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier,** digite um dos seguintes URLs: `http://<SUBDOMAIN>.rightcrowdcustomerdomain.com`

    b. Na caixa de texto **URL de resposta,** digite um dos seguintes URLs: `https://<SUBDOMAIN>.rightcrowdcustomerdomain.com/RightCrowd/Saml2/Auth/AssertionComsumerService`


1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de assinatura,** digite um dos seguintes URLs:  `http://<SUBDOMAIN>.rightcrowdcustomerdomain.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real em URL, Identifier e URL de resposta. Contacte [a equipa de apoio à RightCrowd Workforce Management](mailto:info@rightcrowd.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Clique em **Guardar**.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção Configurar a **Direção da Força de Trabalho RightCrowd,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à RightCrowd Workforce Management.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **RightCrowd Workforce Management**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-rightcrowd-workforce-management-sso"></a>Configurar a RightCrowd Workforce Management SSO

Para configurar um único sign-on no lado **da RightCrowd Workforce Management,** você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipe de [suporte rightCrowd Workforce Management](mailto:info@rightcrowd.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-rightcrowd-workforce-management-test-user"></a>Criar utilizador de teste de Gestão da Força de Trabalho RightCrowd

Nesta secção, um utilizador chamado Britta Simon é criado na RightCrowd Workforce Management. A RightCrowd Workforce Management suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na RightCrowd Workforce Management, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o Signo de Gestão da Força de Trabalho RightCrowd no URL, onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de Gestão da Força de Trabalho RightCrowd e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na RightCrowd Workforce Management para a qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo da RightCrowd Workforce Management nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na RightCrowd Workforce Management para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado a RightCrowd Workforce Management, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


