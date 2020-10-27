---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com CheckProof Microsoft Docs'
description: Saiba como configurar um único sinal de entrada entre o Azure Ative Directory e o CheckProof.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/23/2020
ms.author: jeedes
ms.openlocfilehash: 5a5b10d61d3381389b08e5df7e9e70c92494dd75
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548667"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-checkproof"></a>Tutorial: Azure Ative Directy integração única (SSO) com CheckProof

Neste tutorial, você vai aprender a integrar CheckProof com Azure Ative Directory (Azure AD). Quando integrar o CheckProof com AZure AD, pode:

* Controlo em Azure AD que tem acesso à CheckProof.
* Ativar os seus utilizadores a serem automaticamente inscritos no CheckProof com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada por check-in (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* CheckProof suporta **IDP** iniciado SSO

## <a name="adding-checkproof-from-the-gallery"></a>Adicionar CheckProof da galeria

Para configurar a integração do CheckProof no Azure AD, é necessário adicionar CheckProof da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações** .
1. Para adicionar nova aplicação, selecione **Nova aplicação** .
1. Na secção Adicionar a partir da secção **de galeria,** escreva **CheckProof** na caixa de pesquisa.
1. Selecione **CheckProof** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-checkproof"></a>Configure e teste Azure AD SSO para checkproof

Configure e teste Azure AD SSO com CheckProof usando um utilizador de teste chamado **B.Simon** . Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no CheckProof.

Para configurar e testar o Azure AD SSO com CheckProof, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o CheckProof SSO](#configure-checkproof-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create CheckProof test user](#create-checkproof-test-user)** - para ter uma contraparte de B.Simon em CheckProof que está ligada à representação AD AZure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **CheckProof,** encontre a secção **Gerir** e selecione **um único sinal de sação** .
1. Na página de método **de inscrição** única, selecione **SAML** .
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. No **set-on único com** a página SAML, insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://api.checkproof.com/api/v1/saml/<ID>/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://api.checkproof.com/api/v1/saml/<ID>/acs`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte [a equipa de suporte do Cliente CheckProof](mailto:support@checkproof.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar a Verificação de Verificação,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à CheckProof.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações** .
1. Na lista de aplicações, selecione **CheckProof** .
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos** .
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-checkproof-sso"></a>Configurar sso à prova de verificação

1. Numa janela diferente do navegador web, inscreva-se no site CheckProof como administrador.

1. Aceda às **Definições > Configurações da Empresa > página DE CONFIGURAÇÕES SAML** e Carreque a **Federação Metadados XML** na caixa de texto **da Federação XML.**

    ![página de configurações saml](./media/checkproof-tutorial/saml-settings.png)

### <a name="create-checkproof-test-user"></a>Criar utilizador de teste CheckProof

1. Numa janela diferente do navegador web, inscreva-se no site CheckProof como administrador.

1. Clique no **Perfil** e selecione **O meu perfil** .

    ![Página de utilizador de teste CheckProof](./media/checkproof-tutorial/create-user.png)

1. Clique no **UTILIZADOR CREATE** .

1. Na página **CREATE USER,** preencha os campos necessários e clique em **SAVE** .

    ![Página do utilizador do teste CheckProof1](./media/checkproof-tutorial/create-user-2.png)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

1. Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no CheckProof para o qual configura o SSO

1. Pode utilizar o Microsoft Access Panel. Quando clicar no azulejo CheckProof no Painel de Acesso, deverá ser automaticamente inscrito no CheckProof para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado CheckProof, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


