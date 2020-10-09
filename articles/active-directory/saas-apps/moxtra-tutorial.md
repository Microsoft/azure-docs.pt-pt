---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com a Moxtra Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Moxtra.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: jeedes
ms.openlocfilehash: 7fffe58dfdb63da28edc19c19b56b576f4fbadd3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88544048"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-moxtra"></a>Tutorial: Azure Ative Directory integração única (SSO) com Moxtra

Neste tutorial, você vai aprender a integrar Moxtra com Azure Ative Directory (Azure AD). Quando integrar a Moxtra com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Moxtra.
* Permita que os seus utilizadores sejam automaticamente inscritos na Moxtra com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura única de sso (SSO) ativada pela Moxtra.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Moxtra suporta **SSO** iniciado sp

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-moxtra-from-the-gallery"></a>Adicionar Moxtra da galeria

Para configurar a integração da Moxtra no Azure AD, é necessário adicionar o Moxtra da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** digite **Moxtra** na caixa de pesquisa.
1. Selecione **Moxtra** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-moxtra"></a>Configurar e testar Azure AD único sinal para Moxtra

Configure e teste Azure AD SSO com Moxtra usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Moxtra.

Para configurar e testar o Azure AD SSO com a Moxtra, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Moxtra SSO](#configure-moxtra-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Moxtra test user](#create-moxtra-test-user)** - para ter uma contraparte de B.Simon em Moxtra que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Moxtra,** encontre a secção **Gerir** e selecione um único sinal de **sing.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL:  `https://www.moxtra.com/service/#login`

1. A aplicação Moxtra espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique **em Editar** o ícone para abrir o diálogo dos Atributos do Utilizador.

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação Moxtra espera que alguns mais atributos sejam repercutidos na resposta SAML. Na secção 'Reclamações de Utilizador' no diálogo 'Atributos do Utilizador', execute os seguintes passos para adicionar o atributoken SAML, tal como mostrado na tabela abaixo:

    | Nome | Atributo de origem|
    | ------------------- | -------------------- |    
    | nome de primeiro nome | user.givenname |
    | último nome | utilizador.sobrenome |
    | idpid    | < identificador AD Azure >

    > [!Note]
    > O valor do atributo **Idpid** não é real. Pode obter o valor real da secção **Moxtra** do passo 8. 

    1. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    1. Na caixa de texto **'Nome',** digite o nome do atributo indicado para esta linha.

    1. Deixe o **Espaço Namespace** em branco.

    1. Selecione Fonte como **Atributo**.

    1. A partir da lista **de atributos Source,** digite o valor de atributo mostrado para esta linha.

    1. Clique **em Ok**

    1. Clique em **Guardar**.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Moxtra,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Moxtra.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Moxtra**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-moxtra-sso"></a>Configure Moxtra SSO

1. Em outra janela do navegador, inscreva-se no site da empresa Moxtra como administrador.

2. Na barra de ferramentas à esquerda, clique na **Consola de Admin > S.A. 'Sign-on' único SAML**e, em seguida, clique em **New**.
   
    ![Configurar Sign-On Individuais](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

3. Na página **SAML,** execute os seguintes passos:
   
    ![Configurar Sign-On Individuais](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. Na caixa de texto **Name,** digite um nome para a sua configuração (por exemplo: *SAML*). 
  
    b. Na caixa de texto **IdP Entity ID,** cole o valor do **Identificador AD AZure** que copiou do portal Azure. 
 
    c. Na caixa de texto **URL de login,** cole o valor do URL de **login** que copiou do portal Azure. 
 
    d. Na caixa de texto **AuthnContextClassRef,** **escreva urna:oasis:names:tc:SAML:2.0:ac:classes:Password**. 
 
    e. Na caixa de texto **nameID Format,** **escreva urna:oasis:names:tc:SAML:1.1:nameid-formato:emailAddress**. 
 
    f. Abra o certificado que descarregou do portal Azure no bloco de notas, copie o conteúdo e, em seguida, cole-o na caixa de texto **certificate.**    
 
    exemplo, Na caixa de texto de domínio de e-mail SAML, digite o seu domínio de e-mail SAML.    
  
    >[!NOTE]
    >Para ver os passos para verificar o domínio, clique no "**i**" abaixo.

    h. Clique em **Atualizar**.

### <a name="create-moxtra-test-user"></a>Criar utilizador de teste Moxtra

O objetivo desta secção é criar um utilizador chamado B.simon em Moxtra.

**Para criar um utilizador chamado B.simon em Moxtra, execute os seguintes passos:**

1. Inscreva-se no seu site da empresa Moxtra como administrador.

1. Na barra de ferramentas à esquerda, clique na **Consola de Admin > Gestão do Utilizador**, e, em seguida, Adicione o **Utilizador**.
   
    ![Configurar Sign-On Individuais](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. No diálogo **do Utilizador Adicionar,** execute os seguintes passos:
  
    a. Na caixa de texto **name,** tipo **B**.
  
    b. Na caixa de texto **do último nome,** escreva **Simon.**
  
    c. Na caixa de texto **do Email,** o endereço de e-mail do tipo B.simon é igual ao do portal Azure.
  
    d. Na caixa de texto **da Divisão,** **escreva Dev**.
  
    e. Na caixa de texto do **Departamento,** **digite IT**.
  
    f. Selecione **Administrador**.
  
    exemplo, Clique em **Adicionar**.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Moxtra no Painel de Acesso, deverá ser automaticamente inscrito no Moxtra para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Moxtra com Azure AD](https://aad.portal.azure.com/)

