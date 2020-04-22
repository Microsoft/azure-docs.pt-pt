---
title: 'Tutorial: Integração do Diretório Ativo Azure com pagerDuty [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o PagerDuty.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 99d9988a02b046562c2517df8a81b8ef5c778ef2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683520"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pagerduty"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com pagerDuty

Neste tutorial, você aprenderá a integrar pagerDuty com o Azure Ative Directory (Azure AD). Quando integrar o PagerDuty com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao PagerDuty.
* Ative que os seus utilizadores sejam automaticamente inscritos no PagerDuty com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* PagerDuty single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* PagerDuty suporta **SP** iniciado SSO
* Assim que configurar o PagerDuty, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-pagerduty-from-the-gallery"></a>Adicionando PagerDuty da galeria

Para configurar a integração do PagerDuty em Azure AD, precisa adicionar pagerDuty da galeria à sua lista de aplicações SaaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **PagerDuty** na caixa de pesquisa.
1. Selecione **PagerDuty** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-pagerduty"></a>Configure e teste Azure AD único sign-on para PagerDuty

Configure e teste Azure AD SSO com PagerDuty utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no PagerDuty.

Para configurar e testar o Azure AD SSO com pagerDuty, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure PagerDuty SSO](#configure-pagerduty-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. Crie o utilizador do **[teste PagerDuty](#create-pagerduty-test-user)** - para ter uma contraparte de B.Simon no PagerDuty que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **PagerDuty,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<tenant-name>.pagerduty.com`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<tenant-name>.pagerduty.com`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL, Identifier e URL de Resposta real. Contacte a equipa de [suporte ao Cliente PagerDuty](https://www.pagerduty.com/support/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **Configurar PagerDuty,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao PagerDuty.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **PagerDuty**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-pagerduty-sso"></a>Configure PagerDuty SSO

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa Pagerduty como administrador.

2. No menu em cima, clique em **Definições de Conta**.

    ![Definições de conta](./media/pagerduty-tutorial/ic778535.png "Definições de conta")

3. Clique **em Iniciar Um Único Sinal**.

    ![Início de sessão único](./media/pagerduty-tutorial/ic778536.png "Início de sessão único")

4. Na página **Enable Single Sign-on (SSO),** execute os seguintes passos:

    ![Ativar um único sinal](./media/pagerduty-tutorial/ic778537.png "Ativar um único sinal")

    a. Abra o seu certificado codificado base-64 descarregado do portal Azure no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **x.509 Certificate**
  
    b. Na caixa de texto **login URL,** cola **o URL de Login** que copiou do portal Azure.
  
    c. Na caixa de texto **logout URL,** colhe o **URL de Logout** que copiou do portal Azure.

    d. **Selecione Permitir o nome de utilizador/início da palavra-passe**.

    e. Selecione Exigir caixa de verificação de comparação de contexto de **autenticação EXACT.**

    f. Clique em **Guardar Alterações**.

### <a name="create-pagerduty-test-user"></a>Criar o utilizador do teste PagerDuty

Para permitir que os utilizadores de Anúncios Azure assinem no PagerDuty, devem ser aprovisionados no PagerDuty. No caso do PagerDuty, o provisionamento é uma tarefa manual.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador pagerduty ou APIs fornecidas pela Pagerduty para fornecer contas de utilizador do Diretório Ativo Azure.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu inquilino **pagerduty.**

2. No menu em cima, clique em **Utilizadores.**

3. Clique em **Adicionar Utilizadores**.
   
    ![Add Users](./media/pagerduty-tutorial/ic778539.png "Adicionar Utilizadores")

4.  No diálogo **Convidar a sua equipa,** execute os seguintes passos:
   
    ![Convide a sua equipa](./media/pagerduty-tutorial/ic778540.png "Convide a sua equipa")

    a. Digite o **Primeiro e Último Nome** do utilizador como **B.Simon**. 
   
    b. Introduza o endereço de **e-mail** do utilizador como **o contoso.com\@b.simon**.
   
    c. Clique em **Adicionar,** e depois clique em **Enviar Convites**.
   
    > [!NOTE]
    > Todos os utilizadores adicionados receberão um convite para criar uma conta PagerDuty.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo PagerDuty no Painel de Acesso, deverá ser automaticamente inscrito no PagerDuty para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente pagerDuty com Anúncio Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o PagerDuty com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

