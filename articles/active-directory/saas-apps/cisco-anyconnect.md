---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a Cisco AnyConnect | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Cisco AnyConnect.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: a89ab7f2304fa51d3e8c7a968d445c9b40a457a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92456093"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-anyconnect"></a>Tutorial: Azure Ative Directory integração única (SSO) com a Cisco AnyConnect

Neste tutorial, você vai aprender a integrar Cisco AnyConnect com Azure Ative Directory (Azure AD). Quando integrar a Cisco AnyConnect com AZure AD, pode:

* Control em Azure AD que tem acesso à Cisco AnyConnect.
* Permita que os seus utilizadores sejam automaticamente inscritos na Cisco AnyConnect com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Cisco AnyConnect única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Cisco AnyConnect suporta **IDP** iniciado SSO

## <a name="adding-cisco-anyconnect-from-the-gallery"></a>Adicionar Cisco AnyConnect da galeria

Para configurar a integração da Cisco AnyConnect no AD Azure, é necessário adicionar o Cisco AnyConnect da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Cisco AnyConnect** na caixa de pesquisa.
1. Selecione **Cisco AnyConnect** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-cisco-anyconnect"></a>Configure e teste Azure AD SSO para Cisco AnyConnect

Configure e teste Azure AD SSO com a Cisco AnyConnect utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Cisco AnyConnect.

Para configurar e testar o Azure AD SSO com a Cisco AnyConnect, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure cisco AnyConnect SSO](#configure-cisco-anyconnect-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Cisco AnyConnect test user](#create-cisco-anyconnect-test-user)** - para ter uma contraparte de B.Simon em Cisco AnyConnect que está ligada à representação AD Ad Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Cisco AnyConnect,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. No **set-on único com** a página SAML, insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `< YOUR CISCO ANYCONNECT VPN VALUE >`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `< YOUR CISCO ANYCONNECT VPN VALUE >`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte [a equipa de suporte do Cliente Cisco AnyConnect](https://www.cisco.com/c/en/us/support/index.html) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o ficheiro de certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Cisco AnyConnect,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

> [!NOTE]
> Se quiser embarcar vários TGTs do servidor, então precisa adicionar várias instâncias da aplicação Cisco AnyConnect da galeria. Também pode optar por fazer o upload do seu próprio certificado em Azure AD para todas estas instâncias de aplicação. Desta forma pode ter o mesmo certificado para as aplicações, mas pode configurar diferentes URL de Identificação e Resposta para cada aplicação.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Cisco AnyConnect.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Cisco AnyConnect**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-cisco-anyconnect-sso"></a>Configurar Cisco AnyConnect SSO

1. Vais fazer isto no CLI primeiro, podes voltar e fazer uma passagem asdm numa outra altura.

1. Ligue-se ao seu Aparelho VPN, estará a utilizar um comboio de código ASA com 9.8, e os seus clientes VPN serão 4.6+.

1. Primeiro, criará um Trustpoint e importará o nosso cert SAML.

   ```
    config t

    crypto ca trustpoint AzureAD-AC-SAML
      revocation-check none
      no id-usage
      enrollment terminal
      no ca-check
    crypto ca authenticate AzureAD-AC-SAML
    -----BEGIN CERTIFICATE-----
    …
    PEM Certificate Text from download goes here
    …
    -----END CERTIFICATE-----
    quit
   ```

1. Os seguintes comandos irão providenciar o seu IdP SAML.

   ```
    webvpn
    saml idp https://sts.windows.net/xxxxxxxxxxxxx/ (This is your Azure AD Identifier from the Set up Cisco AnyConnect section in the Azure portal)
    url sign-in https://login.microsoftonline.com/xxxxxxxxxxxxxxxxxxxxxx/saml2 (This is your Login URL from the Set up Cisco AnyConnect section in the Azure portal)
    url sign-out https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0 (This is Logout URL from the Set up Cisco AnyConnect section in the Azure portal)
    trustpoint idp AzureAD-AC-SAML
    trustpoint sp (Trustpoint for SAML Requests - you can use your existing external cert here)
    no force re-authentication
    no signature
    base-url https://my.asa.com
    ```

1. Agora pode aplicar a autenticação SAML numa Configuração do Túnel VPN.

    ```
    tunnel-group AC-SAML webvpn-attributes
      saml identity-provider https://sts.windows.net/xxxxxxxxxxxxx/
      authentication saml
    end

    write mem
    ```

    > [!NOTE]
    > Existe uma funcionalidade com a configuração DO IdP SAML - Se fizer alterações no config IdP, tem de remover o config do fornecedor de identidade saml do seu Grupo de Túneis e reencamê-lo para que as alterações se tornem eficazes.

### <a name="create-cisco-anyconnect-test-user"></a>Criar cisco AnyConnect utilizador de teste

Nesta secção, cria-se um utilizador chamado Britta Simon em Cisco AnyConnect. Trabalhe com a [equipa de suporte da Cisco AnyConnect](https://www.cisco.com/c/en/us/support/index.html) para adicionar os utilizadores na plataforma Cisco AnyConnect. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no Cisco AnyConnect para o qual configura o SSO
* Pode utilizar o Microsoft Access Panel. Quando clicar no azulejo Cisco AnyConnect no Painel de Acesso, deverá ser automaticamente inscrito no Cisco AnyConnect para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos Seguintes

Uma vez configurado Cisco AnyConnect pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).