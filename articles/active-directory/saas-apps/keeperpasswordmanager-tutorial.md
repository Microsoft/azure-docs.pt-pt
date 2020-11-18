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
ms.date: 11/13/2020
ms.author: jeedes
ms.openlocfilehash: 88f84fba43959ee5e5b8d93446e4985a75697813
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685873"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Tutorial: Integração do Diretório Ativo Azure com Keeper Password Manager & Digital Vault

Neste tutorial, aprende-se a integrar o Keeper Password Manager & Digital Vault com o Azure Ative Directory (Azure AD).
Integrar o Keeper Password Manager & Digital Vault com Azure AD fornece-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Keeper Password Manager & Digital Vault.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Keeper Password Manager & Digital Vault (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o Keeper Password Manager & Digital Vault, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Keeper Password Manager & assinatura ativada do Cofre Digital

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Keeper Password Manager & Digital Vault suporta **SSO** iniciado SP

* Keeper Password Manager & Digital Vault suporta o fornecimento do utilizador **just in time**

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>Adicionar Keeper Password Manager & Cofre Digital da galeria

Para configurar a integração do Keeper Password Manager & Digital Vault em AZure AD, é necessário adicionar o Keeper Password Manager & Digital Vault da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
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

1. No portal Azure, no Keeper Password Manager & página de integração de aplicações **digital Vault,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
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

1. Keeper Password Manager & aplicação Digital Vault espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de ficha SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, Keeper Password Manager & aplicação Digital Vault espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name | Atributo de origem|
    | ------------| --------- |
    | Primeiro | user.givenname |
    | Último | utilizador.sobrenome |
    | E-mail | user.mail |

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

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Keeper Password Manager & Digital Vault**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>Configure Keeper Password Manager & Digital Vault SSO

Para configurar um único sign-on no Keeper Password Manager & lado **da configuração do cofre digital,** siga as diretrizes indicativas no [Guia de Suporte do Guardião](https://docs.keeper.io/sso-connect-guide/).

### <a name="create-keeper-password-manager--digital-vault-test-user"></a>Criar Gestor de Passwords do Guardião & utilizador de teste de cofre digital

Para permitir que os utilizadores de Azure AD iniciem sessão no Keeper Password Manager & Digital Vault, devem ser abastados no Keeper Password Manager & Digital Vault. A aplicação suporta o fornecimento do utilizador a tempo e após a autenticação os utilizadores serão criados automaticamente na aplicação. Pode contactar [o Suporte do Guardião,](https://keepersecurity.com/contact.html)se pretender configurar os utilizadores manualmente.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Keeper Password Manager & URL de sinal de assinatura digital do cofre onde pode iniciar o fluxo de login. 

* Vá diretamente ao Keeper Password Manager & URL de assinatura de cofre digital e inicie o fluxo de login a partir daí.

* Pode utilizar o Microsoft Access Panel. Quando clicar no Gestor de Passwords do Guardião & o azulejo digital do cofre no Painel de Acesso, este irá redirecionar para Keeper Password Manager & URL de assinatura de cofre digital. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Próximos passos

Assim que configurar o Keeper Password Manager & Digital Vault, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)