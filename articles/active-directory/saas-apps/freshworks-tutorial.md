---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Freshworks | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Freshworks.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 64a8907ec359235c2856b1f92388d9daa38cd190
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651727"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshworks"></a>Tutorial: Azure Ative Directory integração única (SSO) com Freshworks

Neste tutorial, você vai aprender a integrar Freshworks com Azure Ative Directory (Azure AD). Quando integra Freshworks com AZure AD, pode:

* Controlo em Azure AD que tem acesso a Freshworks.
* Permita que os seus utilizadores sejam automaticamente inscritos na Freshworks com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por assinatura única de freshworks (SSO).

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Freshworks suporta **SP e IDP** iniciado SSO

## <a name="add-freshworks-from-the-gallery"></a>Adicione obras frescas da galeria

Para configurar a integração da Freshworks no Azure AD, é necessário adicionar Freshworks da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **de galeria,** escreva **Freshworks** na caixa de pesquisa.
1. Selecione **Freshworks** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-freshworks"></a>Configurar e testar Azure AD SSO para obras frescas

Configure e teste Azure AD SSO com Freshworks usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Freshworks.

Para configurar e testar a Azure AD SSO com freshworks, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Freshworks SSO](#configure-freshworks-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Freshworks test user](#create-freshworks-test-user)** - para ter uma contraparte de B.Simon em Freshworks que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **Freshworks,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.freshworks.com/sp/SAML/<MODULE_ID>/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.freshworks.com/sp/SAML/CUSTOM_URL`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<SUBDOMAIN>.freshworks.com/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e sinal no URL. Contacte [a equipa de suporte do Cliente Freshworks](mailto:support@freshworks.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Para modificar as opções **de Assinatura** de acordo com o seu requisito, clique em Editar o botão **Editar** para abrir o diálogo **do Certificado de Assinatura SAML.**

     ![image](common/edit-certificate.png)

     ![Screenshot que mostra o diálogo "S A M L Signing Certificate" com o botão "Editar" selecionado.](./media/freshworks-tutorial/response.png)

    a. Selecione **Sign SAML response** como **Opção de Assinatura**.

    b. Clique em **Guardar**.

1. Na secção **Configurar Freshworks,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Freshworks.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Freshworks**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-freshworks-sso"></a>Configure a SSO de Obras Frescas

1. Abra uma nova janela do navegador web e inscreva-se no site da empresa Freshworks como administrador e execute os seguintes passos:

2. A partir do lado esquerdo do menu, clique no ícone **de Segurança,** em seguida, verifique a opção **de inscrição única** e selecione **SSO SAML** em **métodos de autenticação**.

    ![Screenshot que mostra a secção "Métodos de Segurança - Autenticação" com a opção "Single sign-on" ligada e selecionada "S A M L S S O".](./media/freshworks-tutorial/configure01.png)

3. Na secção **de inscrição única,** execute os seguintes passos:

    ![Configuração de obras frescas](./media/freshworks-tutorial/configure02.png)

    a. Clique em **Copiar** para copiar o **ID da Entidade fornecedora de serviços (SP)** para o seu exemplo e cole-o na caixa de texto **Identifier (Entity ID)** na secção **de Configuração SAML Básica** no portal Azure.

    b. No ID de Entidade fornecido pela caixa de texto **IdP,** cole o valor **identificador Azure AD,** que copiou a partir do portal Azure.

    c. Na caixa de texto **URL SSO SAML,** cole o valor **URL de login,** que copiou a partir do portal Azure.

    d. Abra o certificado codificado Base64 no bloco de notas, copie o seu conteúdo e cole-o na caixa de texto do **certificado de Segurança.**

    e. Clique em **Guardar**.

### <a name="create-freshworks-test-user"></a>Criar utilizador de testes freshworks

Nesta secção, cria-se um utilizador chamado B.Simon in Freshworks. Trabalhe com [a equipa de suporte do Cliente Freshworks](mailto:support@freshworks.com) para adicionar os utilizadores na plataforma Freshworks. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação. 

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Freshworks Sign on URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de login da Freshworks e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Freshworks para o qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo Freshworks nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Freshworks para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado a Freshworks, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).