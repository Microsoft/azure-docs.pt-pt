---
title: 'Tutorial: Integração do Diretório Ativo Azure com o | de Controlo de Acesso da Genea Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Genea Access Control.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 82c05f77781abdaea3b2c84aa1071656c206439a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104669868"
---
# <a name="tutorial-azure-active-directory-integration-with-genea-access-control"></a>Tutorial: Integração do Diretório Ativo Azure com o Controlo de Acesso da Genea

Neste tutorial, você vai aprender a integrar o Controlo de Acesso da Genea com o Azure Ative Directory (Azure AD). Quando integrar o Controlo de Acesso da Genea com a AD Azure, pode:

* Controle em Azure AD que tem acesso ao Controlo de Acesso da Genea.
* Capacitar os seus utilizadores a serem automaticamente inscritos no Controlo de Acesso da Genea com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o Genea Access Control, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura ativada pelo Controlo de Acesso genea

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Genea Access Control suporta **SP e IDP** iniciado SSO.
> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.


## <a name="adding-genea-access-control-from-the-gallery"></a>Adicionar o Controlo de Acesso genea da galeria

Para configurar a integração do Genea Access Control em Azure AD, é necessário adicionar o Genea Access Control da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **de galeria,** **digite o Controlo de Acesso genea** na caixa de pesquisa.
1. Selecione **Genea Access Control** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-genea-access-control"></a>Configurar e testar Azure AD SSO para o Controlo de Acesso da Genea

Configure e teste Azure AD SSO com o Genea Access Control usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Controlo de Acesso de Genea.

Para configurar e testar o Azure AD SSO com o Controlo de Acesso da Genea, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o SSO do Controlo de Acesso da Genea](#configure-genea-access-control-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Genea Access Control test user](#create-genea-access-control-test-user)** - para ter uma contraparte de B.Simon no Genea Access Control que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Genea Access Control,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute o seguinte passo:

    Na caixa de texto **identifier,** digite o URL:  `https://login.sequr.io`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    a. Na caixa de texto **URL de entrada de inscrição,** digite o URL: `https://login.sequr.io`

    b. Na caixa de texto **do Estado de Retransmissão,** você receberá este valor, que é explicado mais tarde no tutorial.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

7. Na secção Configurar o **Controlo de Acesso genea,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Controlo de Acesso de Genea.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, **selecione Genea Access Control**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**
## <a name="configure-genea-access-control-sso"></a>Configurar o Controlo de Acesso genea SSO

1. Numa janela diferente do navegador, inscreva-se no site da empresa Genea Access Control como administrador.

1. Clique nas **Integrações** a partir do painel de navegação esquerdo.

    ![Screenshot mostra integração selecionada a partir do painel de navegação.](./media/sequr-tutorial/configure-1.png)

1. Desloque-se para baixo para a secção **'Iniciar's** e clique em **Gerir**.

    ![A screenshot mostra a secção 'Sign-on' única com o botão 'Gerir' selecionado.](./media/sequr-tutorial/configure-2.png)

1. Na secção **Gestão de Um Único Sinal,** execute os seguintes passos:

    ![A screenshot mostra a secção 'Gerir Sign-On' onde pode introduzir os valores descritos.](./media/sequr-tutorial/configure-3.png)

    a. Na caixa de texto **de URL do Fornecedor de Identidade Single Sign-On,** cole o valor URL de **login,** que copiou a partir do portal Azure.

    b. Arraste e deixe cair o ficheiro **Certificado,** que descarregou a partir do portal Azure ou introduza manualmente o conteúdo do certificado.

    c. Após a poupança da configuração, o valor do estado do retransmissão será gerado. Copie o valor **do estado do retransmissor** e cole-o na caixa de texto do Estado de **Retransmissão** da secção **de Configuração Básica SAML** no portal Azure.

    d. Clique em **Guardar**.

### <a name="create-genea-access-control-test-user"></a>Criar utilizador de teste de controlo de acesso genea

Nesta secção, cria-se um utilizador chamado Britta Simon no Controlo de Acesso da Genea. Trabalhe com [a equipa de suporte do Cliente do Genea Access Control](mailto:support@sequr.io) para adicionar os utilizadores na plataforma Genea Access Control. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o Sinal de Controlo de Acesso genea no URL, onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de acesso genea e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Controlo de Acesso da Genea para o qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo do Controlo de Acesso Genea nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Controlo de Acesso genea para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Controlo de Acesso da Genea, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).