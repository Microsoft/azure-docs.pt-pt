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
ms.openlocfilehash: ef49a8a3ac1779071a4d4906bfd053530063102d
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94984469"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Tutorial: Integração do Diretório Ativo Azure com Keeper Password Manager & Digital Vault

Neste tutorial, aprende-se a integrar o Keeper Password Manager & Digital Vault com o Azure Ative Directory (Azure AD).
Esta integração proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Keeper Password Manager & Digital Vault.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Keeper Password Manager & Digital Vault (entrada única) com as suas contas AD Azure.
* Pode gerir as suas contas num local central: o portal Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com keeper password Manager & Digital Vault, você precisa:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um [julgamento de um mês.](https://azure.microsoft.com/pricing/free-trial/)
* Keeper Password Manager & subscrição digital Vault, ativada para uma única sins de sso ( SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Keeper Password Manager & Digital Vault suporta SSO iniciado com SP.

* Keeper Password Manager & Digital Vault suporta o fornecimento de utilizadores just-in-time.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Adicione o Gestor de Passwords do Guardião & Cofre Digital da galeria

Para configurar a integração do Keeper Password Manager & Digital Vault em AZure AD, adicione a aplicação da galeria à sua lista de aplicações geridas como um serviço (SaaS).

1. Inscreva-se no portal Azure utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione o serviço **Azure Ative Directory.**
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. In **Add from the gallery**, type Keeper Password Manager & Digital **Vault** in the search box.
1. Selecione **Keeper Password Manager & Digital Vault** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager--digital-vault"></a>Configure e teste Azure AD SSO para Keeper Password Manager & Cofre Digital

Configure e teste Azure AD SSO com Keeper Password Manager & Digital Vault usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação ligada entre um utilizador Azure AD e o utilizador relacionado no Keeper Password Manager & Digital Vault.

Para configurar e testar O SSO Azure AD com Keeper Password Manager & Digital Vault:

1. [Configure Azure AD SSO](#configure-azure-ad-sso) para permitir que os seus utilizadores utilizem esta funcionalidade.

    * [Crie um utilizador de teste AZure AD](#create-an-azure-ad-test-user) para testar o Azure AD com Britta Simon.
    * [Atribua ao utilizador de teste AZure AD](#assign-the-azure-ad-test-user) para permitir que a Britta Simon utilize um único sinal de Ad AD.

1. [Configure o Gestor de Passwords do Guardião & O Cofre Digital SSO](#configure-keeper-password-manager--digital-vault-sso) para configurar as definições SSO no lado da aplicação.
    * [Crie um Keeper Password Manager & utilizador](#create-a-keeper-password-manager--digital-vault-test-user) de teste de Cofre Digital para ter uma contraparte de Britta Simon no Keeper Password Manager & Digital Vault ligado à representação AD AZure do utilizador.
1. [Teste SSO](#test-sso) para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, no Keeper Password Manager & página de integração de aplicações **digital Vault,** encontre a secção **Gerir.** Selecione **um único sinal de inscrição**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, selecione o ícone de lápis para **Configuração SAML Básica** para editar as definições.

   ![Screenshot de Configurar Single Sign-On com SAML, com ícone de lápis em destaque.](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** execute os seguintes passos:

    a. Para **iniciar s placar em URL,** digite um URL que utiliza o seguinte padrão:
    * Para sSO em nuvem: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Para sSO no local: `https://<KEEPER_FQDN>/sso-connect/saml/login`

    b. Para **identificador (ID de entidade)**, digite um URL que utiliza o seguinte padrão:
    * Para sSO em nuvem: `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * Para sSO no local: `https://<KEEPER_FQDN>/sso-connect`

    c. Para **URL de resposta,** escreva um URL que utiliza o seguinte padrão:
    * Para sSO em nuvem: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Para sSO no local: `https://<KEEPER_FQDN>/sso-connect/saml/sso`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de inscrição real, identificador e URL de resposta. Para obter estes valores, contacte o [Keeper Password Manager & equipa de suporte ao Cliente do Cofre Digital](https://keepersecurity.com/contact.html). Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. O Keeper Password Manager & aplicação Digital Vault espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de ficha SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![Screenshot de Atributos de Utilizador & Reclamações.](common/default-attributes.png)

1. Além disso, o Keeper Password Manager & aplicação Digital Vault espera que mais alguns atributos sejam repercutidos na resposta SAML. Estes são mostrados na tabela seguinte. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome | Atributo de origem|
    | ------------| --------- |
    | Primeiro | user.givenname |
    | Último | utilizador.sobrenome |
    | E-mail | user.mail |

5. Na **Configuração single Sign-On com SAML,** na secção **certificado de assinatura SAML,** selecione **Download**. Isto descarrega o **MÍslido XML da Federação metadados** das opções que o seu requisito exige e guarda-o no seu computador.

    ![Screenshot do Certificado de Assinatura SAML, com Download em destaque.](common/metadataxml.png)

6. No **set up Keeper Password Manager & Digital Vault,** copie os URLs apropriados, conforme o seu requisito.

    ![Screenshot de Configurar Keeper Password Manager & Digital Vault, com URLs em destaque.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

Nesta secção, cria-se um utilizador de teste no portal Azure chamado `B.Simon` .

1. A partir do painel esquerdo no portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >  **Users**  >  **Todos os utilizadores**.
1. Na parte superior do ecrã, selecione **Novo utilizador**.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. Para **nome,** insira `B.Simon` .  
   1. Para **o nome de utilizador,** insira o `username@companydomain.extension` . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione **Mostrar a palavra-passe** e, em seguida, anotar o valor mostrado.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que B.Simon utilize o Azure single sign-on, concedendo acesso ao Keeper Password Manager & Digital Vault.

1. No portal Azure, selecione **Aplicações empresariais**  >  **Todas as aplicações**.
1. Na lista de aplicações, selecione **Keeper Password Manager & Digital Vault**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. Selecione **Adicionar utilizador**. In **Add Assignment**, selecione **Utilizadores e grupos**.
1. Em **Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Em seguida, escolha **Selecione** na parte inferior do ecrã.
1. Se está à espera que uma função seja atribuída aos utilizadores, pode selecioná-la na lista **de funções Select.** Se não tiver sido criada qualquer função para esta aplicação, a função **De Acesso Predefinido** é selecionada.
1. In **Add Assignment**, selecione **Atribua**.


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>Configure Keeper Password Manager & Digital Vault SSO

Para configurar o SSO para a aplicação, consulte as diretrizes no [guia de suporte do Keeper](https://docs.keeper.io/sso-connect-guide/).

### <a name="create-a-keeper-password-manager--digital-vault-test-user"></a>Criar um gestor de passwords do Guardião & utilizador de teste de cofre digital

Para permitir que os utilizadores de Azure AD inscrevam-se no Keeper Password Manager & Digital Vault, tem de os providenciar. A aplicação suporta o fornecimento de utilizadores just-in-time, e após a autenticação os utilizadores são criados automaticamente na aplicação. Se pretender configurar os utilizadores manualmente, contacte [o suporte do Keeper](https://keepersecurity.com/contact.html).

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* No portal Azure, selecione **Testar esta aplicação**. Isto redireciona para o URL de inscrição para Keeper Password Manager & Digital Vault, onde pode iniciar o início de sposição. 

* Pode ir diretamente ao URL de inscrição para a aplicação e iniciar a entrada a partir daí.

* Pode utilizar o Microsoft Access Panel. Quando seleciona o **Gestor de Passwords do Guardião &** o azulejo digital do cofre no Painel de Acesso, este redireciona-o para o URL de inscrição para a aplicação. Para obter mais informações sobre o Painel de Acesso, consulte [iniciar sôr-se e iniciar aplicações a partir do portal My Apps.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Próximos passos

Depois de configurar o Keeper Password Manager & Digital Vault, pode impor o controlo da sessão. Isto protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. Para obter mais informações, consulte [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security.](/cloud-app-security/proxy-deployment-aad)