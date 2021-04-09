---
title: 'Tutorial: Integração do Azure Ative Directory com a Aha! | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Aha!.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: a39371e7a22334b11be1d1a0a9d28557efe177c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101654447"
---
# <a name="tutorial-integrate-aha-with-azure-active-directory"></a>Tutorial: Integrar Aha! com O Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar Aha! com o Azure Ative Directory (Azure AD). Quando integrar a Aha! com Azure AD, você pode:

* Controle em Azure AD que tem acesso a Aha!.
* Permita que os seus utilizadores sejam automaticamente inscritos na Aha! com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Aha! assinatura ativada por um único sinal (SSO).

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Aha! suporta **SP** iniciado SSO
* Aha! suporta **provisão do** utilizador Just In Time

## <a name="add-aha-from-the-gallery"></a>Adicione Aha! da galeria

Para configurar a integração de Aha! em Azure AD, você precisa adicionar Aha! da galeria para a sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **da galeria,** **escreva Aha!** na caixa de pesquisa.
1. **Selecione Aha!** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-aha"></a>Configure e teste Azure AD SSO para Aha!

Configure e teste Azure AD SSO com Aha! usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Aha!.

Para configurar e testar a Azure AD SSO com Aha!, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
2. **[Configure Aha! SSO](#configure-aha-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create Aha! test user](#create-aha-test-user)** - para ter uma contraparte de B.Simon em Aha! que está ligada à representação Azure AD do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na **Aha!** página de integração de aplicações, encontre a secção **Gerir** e selecione **'Único sinal de s-on'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com página SAML,** clique no ícone de lápis para **configuração SAML básica** para editar as definições.

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<companyname>.aha.io/session/new`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<companyname>.aha.io`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contate [Aha! Equipa de apoio](https://www.aha.io/company/contact) ao cliente para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. No **set Up Aha!** secção, copiar os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Aha!.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Aha!**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-aha-sso"></a>Configure Aha! SSO

1. Para automatizar a configuração dentro do Aha!, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Setup Aha!** vai direcioná-lo para o Aha! aplicação. A partir daí, forneça as credenciais de administrador para assinar em Aha!. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-8.

    ![Configuração de configuração](common/setup-sso.png)

3. Se quiser tramar a Aha! manualmente, abra uma nova janela do navegador web e assine no seu Aha! Site da empresa como administrador e executar os seguintes passos:

4. No menu em cima, clique em **Definições**.

    ![Definições](./media/aha-tutorial/setting.png "Definições")

5. Clique **em Conta**.

    ![Perfil](./media/aha-tutorial/account.png "Perfil")

6. Clique **em Segurança e numa única sincção.**

    ![Screenshot que realça a opção de menu de segurança e de inscrição única.](./media/aha-tutorial/security.png "Segurança e único sinal")

7. Na secção **'Sign-On único',** como **Fornecedor de Identidade**, selecione **SAML2.0**.

    ![Segurança e único sinal](./media/aha-tutorial/saml.png "Segurança e único sinal")

8. Na página de configuração **'Sign-On' único,** execute os seguintes passos:

    ![Único sign-on](./media/aha-tutorial/sso.png "Início de Sessão Único")

    a. Na caixa de texto **'Nome',** digite um nome para a sua configuração.

    b. Para **configurar a utilização**, selecione **Metadadata File**.

    c. Para fazer o upload do seu ficheiro de metadados descarregado, clique em **Procurar**.

    d. Clique em **Atualizar**.

### <a name="create-aha-test-user"></a>Cria aha! utilizador de teste

Nesta secção, um utilizador chamado B.Simon é criado em Aha!. Aha! suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Aha!, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto vai redirecionar para Aha! URL de entrada de entrada onde pode iniciar o fluxo de login. 

* Vai para Aha! Iniciar o URL de entrada diretamente e iniciar o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no Aha! azulejo nas My Apps, isto vai redirecionar para Aha! URL de início de sessão. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurar Aha! pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).