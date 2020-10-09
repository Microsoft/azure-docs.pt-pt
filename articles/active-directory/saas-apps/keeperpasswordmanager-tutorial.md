---
title: 'Tutorial: Integração do Azure Ative Directory com Keeper Password Manager & Digital Vault [ Microsoft Docs'
description: Saiba como configurar um único sinal de entrada entre o Azure Ative Directory e o Keeper Password Manager & Digital Vault.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2020
ms.author: jeedes
ms.openlocfilehash: c6bd0c130e860a5700256a54c081cc046219b41a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88546751"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Tutorial: Integração do Diretório Ativo Azure com Keeper Password Manager & Digital Vault

Neste tutorial, aprende-se a integrar o Keeper Password Manager & Digital Vault com o Azure Ative Directory (Azure AD).
Integrar o Keeper Password Manager & Digital Vault com Azure AD fornece-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Keeper Password Manager & Digital Vault.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Keeper Password Manager & Digital Vault (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o Keeper Password Manager & Digital Vault, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Keeper Password Manager & assinatura ativada do Cofre Digital

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Keeper Password Manager & Digital Vault suporta **SSO** iniciado SP

* Keeper Password Manager & Digital Vault suporta o fornecimento do utilizador **just in time**

* Assim que configurar o Keeper Password Manager & Digital Vault, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>Adicionar Keeper Password Manager & Cofre Digital da galeria

Para configurar a integração do Keeper Password Manager & Digital Vault em AZure AD, é necessário adicionar o Keeper Password Manager & Digital Vault da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Keeper Password Manager & Digital Vault** na caixa de pesquisa.
1. Selecione **Keeper Password Manager & Digital Vault** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager--digital-vault"></a>Configure e teste Azure AD SSO para Keeper Password Manager & Cofre Digital

Configure e teste Azure AD SSO com Keeper Password Manager & Digital Vault usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Keeper Password Manager & Digital Vault.

Para configurar e testar o Azure AD SSO com o Keeper Password Manager & Digital Vault, complete os seguintes blocos de construção::

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.

    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.

1. **[Configure o Gestor de Passwords do Guardião & O Cofre Digital SSO](#configure-keeper-password-manager--digital-vault-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    * **[Create Keeper Password Manager & utilizador](#create-keeper-password-manager--digital-vault-test-user)** de teste digital Vault - para ter uma contrapartida de Britta Simon no Keeper Password Manager & Digital Vault que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), no Keeper Password Manager & página de integração de aplicações **digital Vault,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão:
    * Para **cloud SSO** : `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Para **sSO on-prem:**`https://<KEEPER_FQDN>/sso-connect/saml/login`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão:
    * Para **cloud SSO** : `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * Para **sSO on-prem:**`https://<KEEPER_FQDN>/sso-connect`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:
    * Para **cloud SSO** : `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Para **sSO on-prem:**`https://<KEEPER_FQDN>/sso-connect/saml/sso`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real em URL, Identifier e URL de resposta. Contacte [o Keeper Password Manager & equipa de suporte](https://keepersecurity.com/contact.html) ao Cliente Do Cofre Digital para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção set up Keeper Password Manager & Secção **De Cofre Digital,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use o Azure single sign-on, concedendo acesso ao Keeper Password Manager & Digital Vault.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Keeper Password Manager & Digital Vault**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>Configure Keeper Password Manager & Digital Vault SSO

Para configurar um único sign-on no Keeper Password Manager & lado **da configuração do cofre digital,** siga as diretrizes indicativas no [Guia de Suporte do Guardião](https://docs.keeper.io/sso-connect-guide/).

### <a name="create-keeper-password-manager--digital-vault-test-user"></a>Criar Gestor de Passwords do Guardião & utilizador de teste de cofre digital

Para permitir que os utilizadores de Azure AD iniciem sessão no Keeper Password Manager & Digital Vault, devem ser abastados no Keeper Password Manager & Digital Vault. A aplicação suporta o fornecimento do utilizador a tempo e após a autenticação os utilizadores serão criados automaticamente na aplicação. Pode contactar [o Suporte do Guardião,](https://keepersecurity.com/contact.html)se pretender configurar os utilizadores manualmente.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no Gestor de Passwords do Guardião & o azulejo digital do Cofre no Painel de Acesso, deverá ser automaticamente inscrito no Gestor de Passwords do Guardião & Cofre Digital para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Gestor de Passwords do Guardião & Cofre Digital com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
