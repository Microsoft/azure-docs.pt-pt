---
title: 'Tutorial: Integração do Azure Ative Directory com a MobileIron | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o MobileIron.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2021
ms.author: jeedes
ms.openlocfilehash: c47092b1488a79805db69308bcb9a8efde1c0d58
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653067"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Tutorial: Integração do Azure Ative Directory com o MobileIron

 Neste tutorial, você vai aprender a integrar MobileIron com Azure Ative Directory (Azure AD). Quando integrar o MobileIron com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao MobileIron.
* Permita que os seus utilizadores sejam automaticamente inscritos no MobileIron com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* MobileIron assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* MobileIron suporta SSO iniciado **SP e IDP.**

## <a name="add-mobileiron-from-the-gallery"></a>Adicione MobileIron da galeria

Para configurar a integração do MobileIron no Azure AD, é necessário adicionar o MobileIron da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione **Azure Ative Directory**.
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite MobileIron** na caixa de pesquisa.
1. Selecione **MobileIron** a partir dos resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-mobileiron"></a>Configure e teste Azure AD SSO para MobileIron

Configure e teste Azure AD SSO com MobileIron, utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação ligada entre um utilizador Azure AD e o utilizador relacionado no MobileIron.

Para configurar e testar o Azure AD SSO com o MobileIron, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
     1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
2. **[Configure o MobileIron SSO](#configure-mobileiron-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create MobileIron test user](#create-mobileiron-test-user)** - para ter uma contraparte de Britta Simon em MobileIron que está ligada à representação AD AD do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, você ativa Azure AD SSO no portal Azure.
 
1. No portal Azure, na página de integração da aplicação **MobileIron,** encontre a secção **Gerir** e selecione **Single Sign-On**.
1. Na página **'Selecione' Um Método de Sign-On Único,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, selecione o ícone de lápis para **configuração SAML básica** para editar as definições.

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** execute os seguintes passos se desejar configurar a aplicação no modo iniciado pelo **IDP:**

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://www.MobileIron.com/<key>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<host>.MobileIron.com/saml/SSO/alias/<key>`

    c. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

     Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<host>.MobileIron.com/user/login.html`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL Sign-On. Você receberá os valores de chave e anfitrião do portal administrativo do MobileIron que é explicado mais tarde no tutorial.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

Nesta secção, cria-se um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >    >  **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de senha show** e, em seguida, escreva a palavra-passe para baixo.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que B.Simon utilize o Azure single sign-on, concedendo acesso ao MobileIron.

1. No portal Azure, selecione **Aplicações empresariais**  >  **Todas as aplicações**.
1. Na lista de aplicações, selecione **MobileIron**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. Selecione **Adicionar utilizador**. Em seguida, na caixa de diálogo **de atribuição de adicionar,** selecione **Utilizadores e grupos**.
1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Em seguida, escolha **Selecione** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

## <a name="configure-mobileiron-sso"></a>Configurar Mobileiron SSO

1. Numa janela diferente do navegador web, inicie sessão no site da empresa MobileIron como administrador.

2. Vá para a Identidade **de Administrador**  >   e selecione a opção **AAD** no campo **Informação na Configuração do IDP da Nuvem.**

    ![A screenshot mostra o separador Admin do site MobileIron com identidade selecionada.](./media/MobileIron-tutorial/tutorial_MobileIron_admin.png)

3. Copie os valores de **Chave** e **Anfitrião** e cole-os para completar os URLs na secção **de Configuração Básica SAML** no portal Azure.

    ![A screenshot mostra a opção Configuração SAML com uma chave e valor de anfitrião.](./media/MobileIron-tutorial/key.png)

4. No **ficheiro de metadados de exportação a partir de AAD e importado para MobileIron Cloud Field** clique **em Escolher Ficheiro** para carregar os metadados descarregados do portal Azure. Clique em **Fazer** uma vez carregado.

    ![Configure botão de metadados únicos Sign-On de administração](./media/MobileIron-tutorial/tutorial_MobileIron_adminmetadata.png)


### <a name="create-mobileiron-test-user"></a>Criar utilizador de teste MobileIron

Para permitir que os utilizadores de Azure AD iniciem sessão no MobileIron, devem ser a provisionados no MobileIron.  
No caso do MobileIron, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Faça login no seu site da empresa MobileIron como administrador.

1. Vá aos **Utilizadores** e clique em **Adicionar**  >  **Utilizador Único**.

    ![Configure botão único de utilizador Sign-On](./media/MobileIron-tutorial/tutorial_MobileIron_user.png)

1. Na página de diálogo **"Único Utilizador",** execute os seguintes passos:

    ![Configure botão único Sign-On utilizador adicionar](./media/MobileIron-tutorial/tutorial_MobileIron_useradd.png)

    a. Na caixa de texto **do endereço de e-mail,** insira o e-mail do utilizador como brittasimon@contoso.com .

    b. Na caixa de texto **First Name,** insira o primeiro nome do utilizador como Britta.

    c. Na caixa de texto **Last Name,** insira o último nome do utilizador como Simon.

    d. Clique em **Concluído**.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para MobileIron Sign no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de inscrição do MobileIron e inicie o fluxo de login a partir daí.

### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no MobileIron para o qual configura o SSO.

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo MobileIron nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no MobileIron para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o MobileIron, pode impor controlos de sessão, o que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
