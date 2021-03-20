---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com Elium | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Elium.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: 7f0e9d0c97b9325a30de3cb8c6ce10a3ba8489f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454118"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-elium"></a>Tutorial: Azure Ative Directory integração única (SSO) com Elium

Neste tutorial, você vai aprender a integrar Elium com Azure Ative Directory (Azure AD). Quando integrar Elium com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Elium.
* Ative os seus utilizadores a serem automaticamente inscritos no Elium com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por Elium single sign-on (SSO).

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Elium apoia **SP e IDP** iniciado SSO
* Elium suporta o fornecimento do utilizador **Just In Time**

## <a name="adding-elium-from-the-gallery"></a>Adicionando Elium da galeria

Para configurar a integração do Elium no AD Azure, precisa adicionar Elium da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **digite Elium** na caixa de pesquisa.
1. Selecione **Elium** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-elium"></a>Configurar e testar Azure AD SSO para elium

Configure e teste Azure AD SSO com Elium usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Elium.

Para configurar e testar a Azure AD SSO com Elium, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Elium SSO](#configure-elium-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Elium test user](#create-elium-test-user)** - para ter uma contraparte de B.Simon em Elium que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Elium,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<platform-domain>.elium.com/login/saml2/acs`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE]
    > Estes valores não são reais. Você receberá estes valores do **ficheiro de metadados SP** transferível em , o que é explicado mais tarde neste `https://<platform-domain>.elium.com/login/saml2/metadata` tutorial.

1. A aplicação Elium espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Elium espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name | Atributo de origem|
    | ---------------| ----------------|
    | e-mail   |user.mail |
    | first_name| user.givenname |
    | last_name| utilizador.sobrenome|
    | job_title| user.jobtitle|
    | empresa| user.companyname|

    > [!NOTE]
    > Estas são as alegações por defeito. **Apenas é necessário reclamar por e-mail.** Para o fornecimento de JIT também é obrigatório apenas e-mail reclamação. Outras reclamações personalizadas podem variar de uma plataforma de cliente para outra plataforma de clientes.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar Elium,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Elium.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Elium**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-elium-sso"></a>Configurar Elium SsO

1. Para automatizar a configuração dentro do Elium, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar Elium** irá direcioná-lo para a aplicação Elium. A partir daí, forneça as credenciais de administração para assinar em Elium. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar o Elium manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Elium como administrador e execute os seguintes passos:

1. Clique no perfil do **Utilizador** a partir do canto superior direito e, em seguida, selecione **Definições**.

    ![Configurar Sign-On Elium 01](./media/elium-tutorial/elium-01.png)

1. Selecione **Segurança** em **Avançado**.

    ![Configurar Sign-On Elium 02](./media/elium-tutorial/elium-02.png)

1. Desloque-se até à secção **de inscrição única (SSO)** e execute os seguintes passos:

    ![Configurar Sign-On Elium 03](./media/elium-tutorial/elium-03.png)

    a. Copie o valor de **Verificar se a autenticação SAML2 funciona para a sua conta** e colá-la na caixa de texto URL de **inscrição na** secção **Configuração Básica saml** no portal Azure.

    > [!NOTE]
    > Depois de configurar o SSO, pode sempre aceder à página de login remoto predefinido no seguinte URL: `https://<platform_domain>/login/regular/login` 

    b. Selecione Ative A caixa **de verificação da federação SAML2.**

    c. Selecione **JIT Provisioning** checkbox.

    d. Abra os **metadados SP** clicando no botão **Descarregar.**

    e. Procure a **entidadeID** no ficheiro **de metadados SP,** copie o valor **de ÍV** da entidade e cole-a na caixa de texto **identifier** na secção **configuração de SAML Básico** no portal Azure. 

    ![Configurar Sign-On Elium 04](./media/elium-tutorial/elium-04.png)

    f. Procure o **Serviço de Afirmaçãoconsumer no** ficheiro de **metadados SP,** copie o valor **de Localização** e cole-o na caixa de texto **URL de resposta** na secção **Configuração Básica SAML** no portal Azure.

    ![Configurar Sign-On Elium 05](./media/elium-tutorial/elium-05.png)

    exemplo, Abra o ficheiro de metadados descarregado do portal Azure para o bloco de notas, copie o conteúdo e cole-o na caixa de texto de **metadados IdP.**

    h. Clique em **Guardar**.

### <a name="create-elium-test-user"></a>Criar utilizador de teste de Elium

Nesta secção, um utilizador chamado B.Simon é criado em Elium. O Elium suporta o **provisionamento just-in-time**, que é ativado por defeito. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existe em Elium, um novo é criado quando tenta aceder a Elium.

> [!Note]
> Se precisar de criar um utilizador manualmente, contacte a [equipa de suporte do Elium](mailto:support@elium.com).

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 
 
#### <a name="sp-initiated"></a>SP iniciado:
 
* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Elium Sign on URL onde pode iniciar o fluxo de login.  
 
* Vá diretamente ao URL de inscrição de Elium e inicie o fluxo de login a partir daí.
 
#### <a name="idp-initiated"></a>IDP iniciado:
 
* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Elium para o qual configura o SSO 
 
Também pode utilizar o Microsoft Access Panel para testar a aplicação em qualquer modo. Quando clicar no azulejo de Elium no Painel de Acesso, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Elium para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos Seguintes

Uma vez configurado Elium, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).