---
title: 'Tutorial: Integração do Diretório Ativo Azure com a OpsGenie Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o OpsGenie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 8cf82827258b3888f4c30ad39a395d697a518a32
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81261108"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-opsgenie"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com opsGenie

Neste tutorial, você vai aprender a integrar opsGenie com o Azure Ative Directory (Azure AD). Quando integrar a OpsGenie com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à OpsGenie.
* Ative que os seus utilizadores sejam automaticamente inscritos na OpsGenie com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* OpsGenie single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* OpsGenie suporta **IDP** iniciado SSO
* Assim que configurar a OpsGenie, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-opsgenie-from-the-gallery"></a>Adicionando OpsGenie da galeria

Para configurar a integração da OpsGenie no Azure AD, precisa adicionar opsGenie da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **OpsGenie** na caixa de pesquisa.
1. Selecione **OpsGenie** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-opsgenie"></a>Configure e teste Azure AD único sign-on para OpsGenie

Configure e teste Azure AD SSO com OpsGenie utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em OpsGenie.

Para configurar e testar o Azure AD SSO com opsGenie, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure OpsGenie SSO](#configure-opsgenie-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * Crie o utilizador de **[teste OpsGenie](#create-opsgenie-test-user)** - para ter uma contrapartida de B.Simon em OpsGenie que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **OpsGenie,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://app.opsginie.com/auth/saml/<UNIQUEID>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://app.opsginie.com/auth/saml?id=<UNIQUEID>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Identificação e Resposta real, o que é explicado mais tarde neste tutorial.

5. Na configuração do Single Sign-On com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

1. Na secção **set Up OpsGenie,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso à OpsGenie.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **OpsGenie**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-opsgenie-sso"></a>Configure OpsGenie SSO

1. Abra outra instância do navegador e, em seguida, inscreva-se na OpsGenie como administrador.

2. Clique em **Definições**e, em seguida, clique no separador **'Sinal Único'.**
   
    ![OpsGenie Single Sign-On](./media/opsgenie-tutorial/tutorial-opsgenie-06.png)

3. Para ativar o SSO, selecione **Ativado**.
   
    ![Definições opsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-07.png) 

4. Na secção **Fornecedor,** clique no separador **Diretório Ativo Azure.**
   
    ![Definições opsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-08.png) 

5. Na página de diálogo azure Ative Directory, execute os seguintes passos:
   
    ![Definições opsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-09.png)
    
    a. Copie o valor **DO ID da aplicação** e cole-o na caixa de texto **Identifier (Id entidade)** na secção **basic SAML Configuração** no portal Azure.

    a. Copie o valor URL de **resposta** e cole-o na caixa de texto URL de **resposta** na secção **de configuração Básica SAML** no portal Azure.

    a. Na caixa de texto **SAML 2.0 Endpoint,** cola o valor URL de **Login**que copiou do portal Azure.
    
    b. No Url de **Metadados:** caixa de texto, pasta app Palavras de **metadados Valor url** que copiou do portal Azure.
    
    c. Para ativar o SSO, ligue o alternância de **inscrição simples ativa.**

    d. Clique **em aplicar as definições de SSO**.

### <a name="create-opsgenie-test-user"></a>Criar o utilizador de teste OpsGenie

O objetivo desta secção é criar um utilizador chamado B.Simon em OpsGenie. 

1. Numa janela do navegador web, inscreva-se no seu inquilino OpsGenie como administrador.

2. Navegue na lista de Utilizadores clicando em **Utilizadores** no painel esquerdo.
   
    ![Definições opsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-10.png) 

3. Clique em **Adicionar Utilizador**.

4. No diálogo **Adicionar utilizador,** execute os seguintes passos:
   
    ![Definições opsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-11.png)
   
    a. Na caixa de texto **por e-mail,** digite o endereço de e-mail de B.Simon endereçado no Diretório Ativo Azure.
   
    b. Na caixa de texto **Nome Completo,** tipo **B.Simon**.
   
    c. Clique em **Guardar**. 

> [!NOTE]
> B.Simon recebe um e-mail com instruções para configurar o seu perfil.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo OpsGenie no Painel de Acesso, deve ser automaticamente inscrito no OpsGenie para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente opsGenie com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)