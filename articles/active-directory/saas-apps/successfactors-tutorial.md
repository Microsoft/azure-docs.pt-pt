---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com SuccessFactors [ Integração de Diretórios Ativos) azure com SuccessFactors [ SuccessFactors] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SuccessFactors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d49915271580b5665981bf7e212f3d5712c86456
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76292984"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-successfactors"></a>Tutorial: Azure Ative Diretório integração de um único sign-on (SSO) com SuccessFactors

Neste tutorial, você vai aprender a integrar SuccessFactors com O Diretório Ativo Azure (Azure AD). Quando integra o SuccessFactors com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a SuccessFactors.
* Ative que os seus utilizadores sejam automaticamente inscritos no SuccessFactors com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada por um único sign-on (SSO) de SuccessFactors.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* SuccessFactors suporta **SP** iniciado SSO.
* Assim que configurar os SuccessFactors, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se a partir do Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-successfactors-from-the-gallery"></a>Adicionar SuccessFactors da galeria

Para configurar a integração dos SuccessFactors no Azure AD, é necessário adicionar SuccessFactors da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **SuccessFactors** na caixa de pesquisa.
1. Selecione **SuccessFactors** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-successfactors"></a>Configure e teste Azure AD SSO para SuccessFactors

Configure e teste Azure AD SSO com SuccessFactors utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em SuccessFactors.

Para configurar e testar o Azure AD SSO com SuccessFactors, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
2. **[Configure SuccessFactors SSO](#configure-successfactors-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
    1. **[Create SuccessFactors test user](#create-successfactors-test-user)** - para ter uma contrapartida de B.Simon em SuccessFactors que está ligado à representação do Utilizador azure AD.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de **aplicações SuccessFactors,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    a. Na caixa de texto **"Sign-on URL",** escreva um URL utilizando o seguinte padrão:

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:

    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL, Identificador e Resposta real. Contacte a equipa de suporte do [Cliente SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html) para obter estes valores.

4. Na configuração de um único sinal com página **SAML,** na secção certificado de **assinatura SAML,** encontre **certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **Configurar SuccessFactors,** copie os URL(s) adequados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
    1. No campo **Nome**, introduza `B.Simon`.  
    1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
    1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
    1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso aos SuccessFactors.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **SuccessFactors**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-successfactors-sso"></a>Configure SuccessFactors SSO

1. Numa janela de navegador web diferente, inicie sessão no seu portal de **administração SuccessFactors** como administrador.

2. Visite a segurança da **aplicação** e nativa de **um único sinal na funcionalidade**.

3. Coloque qualquer valor no **Token reset** e clique em **Guardar Token** para ativar o SAML SSO.

    ![Configurar um único sinal no lado da aplicação][11]

    > [!NOTE]
    > Este valor é utilizado como interruptor de ligar/desligar. Se algum valor for guardado, o SAML SSO está ON. Se um valor em branco for guardado, o SAML SSO está DESLIGADO.

4. Nativo para abaixo screenshot e executar as seguintes ações:

    ![Configurar um único sinal no lado da aplicação][12]
  
    a. Selecione o botão de rádio **SAML v2 SSO**
  
    b. Defino o **Nome da Festa de Assertão SAML**(por exemplo, emitente SAML + nome da empresa).

    c. Na caixa de texto **URL emitente,** cola o valor do **Identificador AD Azure** que copiou do portal Azure.

    d. Selecione **Afirmação** como **exigir assinatura obrigatória**.

    e. Selecione **ativado** como **ativar a bandeira SAML**.

    f. Selecione **Não** como Assinatura de Pedido de **Login (SF Gerado/SP/RP)**.

    g. Selecione **Browser/Post Profile** como **Perfil SAML**.

    h. Selecione **Não** como **Prazo Válido de Aplicação**do Certificado .

    i. Copie o conteúdo do ficheiro de certificado descarregado do portal Azure e, em seguida, cole-o na caixa de texto do Certificado de **Verificação SAML.**

    > [!NOTE] 
    > O conteúdo do certificado deve ter etiquetas de certificado e certificado final.

5. Navegue para SAML V2 e, em seguida, execute os seguintes passos:

    ![Configurar um único sinal no lado da aplicação][13]

    a. **Selecione Sim** como **Suporte SP iniciado Global Logout**.

    b. Na caixa de texto **GLOBAL Logout Service URL (logoutRequest destination),** colá o valor URL de **Sign-Out** que copiou forma o portal Azure.

    c. Selecione **Não** como **Exigir que o Sp deva encriptar todos os elementos NameID**.

    d. Selecione **não especificado** como **Formato NameID**.

    e. Selecione **Sim** como **Ativar o login iniciado sp (AuthnRequest)**.

    f. No pedido de envio como caixa de texto **emitentes a nível da empresa,** colá-cola o valor URL de **Login** que copiou do portal Azure.

6. Execute estes passos se pretender tornar os nomes de utilizador de login Case Insensitive.

    ![Configurar um único sinal][29]

    a. Visite as **Definições**da Empresa (perto do fundo).

    b. Selecione caixa de verificação perto de **ativar o nome de utilizador não sensível a casos**.

    c. Clique em **Guardar**.

    > [!NOTE]
    > Se tentar ativar isto, o sistema verifica se cria um nome de login SAML duplicado. Por exemplo, se o cliente tiver nomes de utilizador User1 e user1. Tirar a sensibilidade ao caso faz com que estes duplicados. O sistema dá-lhe uma mensagem de erro e não ativa a funcionalidade. O cliente precisa de alterar um dos nomes de utilizador para que seja escrito diferente.

### <a name="create-successfactors-test-user"></a>Criar o utilizador de teste SuccessFactors

Para permitir que os utilizadores de Anúncios Azure assinem o SuccessFactors, devem ser aprovisionados em SuccessFactors. No caso dos SuccessFactors, o provisionamento é uma tarefa manual.

Para obter utilizadores criados em SuccessFactors, é necessário contactar a equipa de suporte do [SuccessFactors.](https://www.successfactors.com/content/ssf-site/en/support.html)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SuccessFactors no Painel de Acesso, deve ser automaticamente inscrito nos SuccessFactors para os quais configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente SuccessFactors com Anúncio Azure](https://aad.portal.azure.com)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger os SuccessFactors com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
