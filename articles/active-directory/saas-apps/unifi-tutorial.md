---
title: 'Tutorial: Integração do Diretório Ativo Azure com | DA Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o UNIFI.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 1fa1c40c96694b71bc762050998257102b687c18
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650638"
---
# <a name="tutorial-azure-active-directory-integration-with-unifi"></a>Tutorial: Integração do Diretório Ativo Azure com a UNIFI

Neste tutorial, você vai aprender a integrar a UNIFI com o Azure Ative Directory (Azure AD). Quando integrar a UNIFI com a AD Azure, pode:

* Controlo em Azure AD que tem acesso à UNIFI.
* Capacitar os seus utilizadores a serem automaticamente inscritos na UNIFI com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Unifi assinatura única ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* A UNIFI apoia **o SP e o IDP** iniciado sSO.
* A UNIFI suporta o fornecimento **automatizado** de utilizadores.

## <a name="add-unifi-from-the-gallery"></a>Adicione UNIFI da galeria

Para configurar a integração da UNIFI no AD Azure, precisa adicionar a UNIFI da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite UNIFI** na caixa de pesquisa.
1. Selecione **UNIFI** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-unifi"></a>Configure e teste Azure AD SSO para a UNIFI

Configure e teste Azure AD SSO com a UNIFI utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na UNIFI.

Para configurar e testar a Azure AD SSO com a UNIFI, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o SSO UNIFI](#configure-unifi-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create UNIFI test user](#create-unifi-test-user)** - para ter uma contraparte de B.Simon em UNIFI que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **DA UNIFI,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    Na caixa de texto **identifier,** digite o URL: `INVIEWlabs`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite o URL:  `https://app.discoverunifi.com/login`

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

7. Na secção **Configurar a SEÇÃO UNIFI,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à UNIFI.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **UNIFI**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-unifi-sso"></a>Configurar sso unificador

1. Numa janela diferente do navegador web, inscreva-se no site da empresa **UNIFI** como administrador.

2. Clique nos **Utilizadores.**

    ![A screenshot mostra os utilizadores selecionados a partir do site DA UNIFI.](./media/unifi-tutorial/app-1.png)

3. Clique no **Fornecedor de Novas Identidades.**

    ![A screenshot mostra o novo fornecedor de identidade de anúncios selecionado.](./media/unifi-tutorial/app-2.png)

4. Na secção **Add Identity Provider,** execute os seguintes passos:

    ![A screenshot mostra o Fornecedor de Identidade Adicionar onde pode introduzir os valores descritos.](./media/unifi-tutorial/app-3.png) 

    a. Na caixa de texto **'Nome provedor',** digite o nome do Fornecedor de Identidade..

    b. Na caixa de texto **URL do Fornecedor,** cole o valor URL do **Login,** que copiou do portal Azure.

    c. Abra o Certificado que descarregou do portal Azure no bloco de notas, remova o **certificado ---BEGIN---** e **---END CERTIFICATE---** etiqueta e, em seguida, cole o conteúdo restante na caixa de texto **certificate.**

    d. Selecione a caixa de verificação **default Provider.**

### <a name="create-unifi-test-user"></a>Criar utilizador de teste UNIFI

Nesta secção, cria-se um utilizador chamado Britta Simon. **A UNIFI** suporta o fornecimento automático do utilizador para que não sejam necessárias etapas manuais. Os utilizadores são criados automaticamente após a autenticação bem sucedida do AD AZure.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para UNIFI Iniciar o URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de inscrição da UNIFI e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na UNIFI para a qual configura o SSO. 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo DA UNIFI nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na UNIFI para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado a UNIFI, pode impor o controlo da sessão, que protege a exfiltração e a infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).