---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Blackboard Learn - Shibboleth | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Blackboard Learn - Shibboleth.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: 0355c8dc682ed45865a65d59b8b4810a85d588fa
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621202"
---
# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn---shibboleth"></a>Tutorial: Integração do Diretório Ativo Azure com o Blackboard Learn - Shibboleth

Neste tutorial, você vai aprender a integrar Blackboard Learn - Shibboleth com Azure Ative Directory (Azure AD). Quando integrar o Blackboard Learn - Shibboleth com Azure AD, pode:

* Control em Azure AD que tem acesso a Blackboard Learn - Shibboleth.
* Ative os seus utilizadores a serem automaticamente inscritos no Blackboard Learn - Shibboleth com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:
 
* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura ativada por Blackboard Learn - Shibboleth (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Blackboard Learn - Shibboleth suporta **SSO** iniciado SP

## <a name="add-blackboard-learn---shibboleth-from-the-gallery"></a>Add Blackboard Learn - Shibboleth da galeria

Para configurar a integração do Blackboard Learn - Shibboleth em Azure AD, precisa adicionar Blackboard Learn - Shibboleth da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **da galeria,** **escreva Blackboard Learn - Shibboleth** na caixa de pesquisa.
1. Selecione **Blackboard Learn - Shibboleth** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-blackboard-learn---shibboleth"></a>Configure e teste Azure AD SSO para Aprendizagem de Quadro-Negro - Shibboleth

Configure e teste Azure AD SSO com Blackboard Learn - Shibboleth usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Blackboard Learn - Shibboleth.

Para configurar e testar Azure AD SSO com Blackboard Learn - Shibboleth, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Blackboard Learn - Shibboleth SSO](#configure-blackboard-learn---shibboleth-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Blackboard Learn - utilizador de teste Shibboleth](#create-blackboard-learn---shibboleth-test-user)** - para ter uma contrapartida de B.Simon in Blackboard Learn - Shibboleth que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com Blackboard Learn - Shibboleth, execute os seguintes passos:

1. No portal Azure, na página de integração da aplicação **Blackboard Learn - Shibboleth,** selecione **Single sign-on**.

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

3. Na **configuração single Sign-On com página SAML,** clique no ícone de lápis para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/Login`

    b. Na caixa **identifier,** digite um URL utilizando o seguinte padrão: `https://<yourblackoardlearnserver>.blackboardlearn.com/shibboleth-sp`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/SAML2/POST`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Sign-On real, identifier e responder URL. Contacte a Equipa de Suporte do [Cliente Shibboleth](https://www.blackboard.com/forms/contact-us_form.aspx) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configurar Blackboard Learn - Shibboleth,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Blackboard Learn - Shibboleth.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Blackboard Learn - Shibboleth**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="configure-blackboard-learn---shibboleth-sso"></a>Configure Blackboard Learn - Shibboleth SSO

Para configurar um único sign-on no **lado do Blackboard Learn - Shibboleth,** você precisa enviar o **metudata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipa de [suporte do Blackboard Learn - Shibboleth.](https://www.blackboard.com/forms/contact-us_form.aspx) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-blackboard-learn---shibboleth-test-user"></a>Create Blackboard Learn - Utilizador de teste Shibboleth

Nesta secção, você cria um utilizador chamado Britta Simon in Blackboard Learn - Shibboleth. Trabalhe com [a equipa de suporte blackboard Learn - Shibboleth](https://www.blackboard.com/forms/contact-us_form.aspx) para adicionar os utilizadores na plataforma Blackboard Learn - Shibboleth. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Blackboard Learn - SHIbboleth Sign-on URL onde pode iniciar o fluxo de login. 

* Vá para Blackboard Learn - Shibboleth Sign-on URL diretamente e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Blackboard Learn - Shibboleth nas Minhas Apps, deverá ser automaticamente inscrito no Blackboard Learn - Shibboleth para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Blackboard Learn - Shibboleth, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
