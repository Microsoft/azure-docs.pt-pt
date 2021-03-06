---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com ekarda | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Ekarda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: b057d07e10676291f42a9a070e32cb17df672651
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735163"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ekarda"></a>Tutorial: Azure Ative Directory integração única (SSO) com ekarda

Neste tutorial, você vai aprender a integrar ekarda com Azure Ative Directory (Azure AD). Quando integrar ekarda com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Ekarda.
* Permita que os seus utilizadores sejam automaticamente inscritos na ekarda utilizando as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição ekarda ativada para um único sign-on (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Ekarda apoia SSO iniciado pelo SP e iniciado pelo IDP.
* A ekarda suporta o fornecimento de utilizadores just-in-time.

## <a name="add-ekarda-from-the-gallery"></a>Adicione ekarda da galeria

Para configurar a integração da Ekarda no Azure AD, adicione ekarda da galeria à sua lista de aplicações geridas pela SaaS:

1. Inscreva-se no portal Azure utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.

1. No painel esquerdo, selecione o serviço **Azure Ative Directory.**
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar a nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **escreva ekarda** na caixa de pesquisa.
1. Selecione **ekarda** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-ekarda"></a>Configurar e testar Azure AD SSO para ekarda

Configure e teste Azure AD SSO com ekarda utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação ligada entre um utilizador Azure AD e o utilizador relacionado em ekarda.

Para configurar e testar a Azure AD SSO com ekarda, execute os seguintes passos:

1. [Configure Azure AD SSO](#configure-azure-ad-sso) para permitir que os seus utilizadores utilizem esta funcionalidade.

    1. [Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user) para testar o Azure AD com B.Simon.
    1. [Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que a B.Simon utilize um único sinal de Ad AD.
1. [Configure a Ekarda SSO](#configure-ekarda-sso) para configurar as definições de inscrição única no lado da aplicação.
    * [Crie um utilizador de teste ekarda](#create-an-ekarda-test-user) para ter uma contraparte de B.Simon em ekarda que está ligada à representação AZure AD do utilizador.
1. [Teste SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos no portal Azure para ativar o Azure AD SSO:

1. Inicie sessão no portal do Azure.
1. Na página de integração da aplicação **ekarda,** encontre a secção **Gerir** e selecione **um único sinal de s-on**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. Na **configuração single Sign-On com página SAML,** selecione o ícone de lápis para editar as definições básicas de **configuração SAML.**

   ![Screenshot de Configurar Single Sign-On com página SAML, com o ícone do lápis em destaque.](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** se vir **o ficheiro de metadados do Fornecedor de Serviços,** siga estes passos:
    1. Selecione **carregar o ficheiro de metadados**.
    1. Selecione o ícone de pasta para selecionar o ficheiro de metadados e, em seguida, **selecione Upload**.
    1. Quando o ficheiro de metadados é carregado com sucesso, os valores de **Identifier** e **URL de resposta** aparecem automaticamente na caixa de texto da secção ekarda.

    > [!Note]
    > Se os valores de URL **de identificação** e **resposta** não aparecerem automaticamente, preencha os valores manualmente de acordo com os seus requisitos.

1. Se não vir **o ficheiro de metadados do Fornecedor de Serviços** na secção **de Configuração Básica SAML** e pretender configurar a aplicação no modo iniciado pelo IDP, introduza valores para os seguintes campos:

    1. Na caixa de texto **identifier,** digite um URL que segue este padrão: `https://my.ekarda.com/users/saml_metadata/<COMPANY_ID>`
    1. Na caixa de texto **URL de resposta,** digite um URL que segue este padrão: `https://my.ekarda.com/users/saml_acs/<COMPANY_ID>`

1. Se pretender configurar a aplicação no modo iniciado pelo SP, selecione **Definir URLs adicionais** e fazê-lo:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL que segue este padrão:  `https://my.ekarda.com/users/saml_sso/<COMPANY_ID>`

    > [!NOTE]
    > Os valores nos dois passos anteriores não são reais. Atualize-os com o identificador real, responda URL e valores de URL de assinatura. Contacte a [equipa de suporte do Cliente Ekarda](mailto:contact@ekarda.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** selecione **Descarregamento** para guardar **Certificado (Base64)** no seu computador.

    ![Screenshot da secção de Certificado de Assinatura SAML da Configuração Single Sign-On com página SAML, com link de descarregamento destacado para o certificado Base64.](common/certificatebase64.png)

1. Na secção **Configuração ekarda,** copie os URLs apropriados com base nos seus requisitos.

    ![Screenshot da secção Configuração ekarda da Configuração Single Sign-On com página SAML, com as ligações de cópia URL realçadas.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, utilizará o portal Azure para criar um utilizador de teste chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >    >  **Todos os utilizadores**.

1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, introduza `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e escreva o valor que aparece na caixa **palavra-passe.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à ekarda.

1. No portal Azure, selecione **Aplicações empresariais**  >  **Todas as aplicações**.
1. Na lista de candidaturas, selecione **ekarda**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

1. **Selecione Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Adicionar Atribuição'.**

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Em seguida, escolha **Selecione** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

## <a name="configure-ekarda-sso"></a>Configure ekarda SSO

1. Para automatizar a configuração dentro do Ekarda, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar ekarda** irá direcioná-lo para a aplicação Ekarda. A partir daí, forneça as credenciais de administração para assinar em Ekarda. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração](common/setup-sso.png)

3. Se quiser configurar o ekarda manualmente, numa janela diferente do navegador web, inscreva-se no site da empresa Ekarda como administrador.

1. **Selecione Admin**  >  **A minha conta**.

    ![Screenshot do site Ekarda UI com a minha conta em destaque no menu Admin.](./media/ekarda-tutorial/ekarda.png)

1. Na parte inferior da página, localizar a secção **DEFINIÇÕES SAML.** Esta secção é onde você configura a integração SAML.
1. Na secção **DEFINIÇÕES SAML,** siga estes passos:

    ![Screenshot da página Ekarda SAML SETTINGS com campos de configuração SAML destacados.](./media/ekarda-tutorial/ekarda1.png)

    1. Selecione a ligação **de metadados do Fornecedor de Serviços** e guarde-a como ficheiro no seu computador.
    1. Selecione a caixa de verificação **Ativar SAML.**
    1. Na caixa de texto **IDP Entity ID,** cole o valor do **identificador Azure AD** que copiou anteriormente do portal Azure.
    1. Na caixa de texto **URL do IDP Login,** cole o valor URL de **login** que copiou anteriormente do portal Azure.
    1. Na caixa de texto **URL do IDP Logout,** cole o valor **URL logout** que copiou anteriormente do portal Azure.
    1. Utilize o Bloco de Notas para abrir o ficheiro **Certificado (Base64)** que descarregou a partir do portal Azure. Cole esse conteúdo na caixa de texto do **Certificado IDP x509.**
    1. Selecione a caixa de verificação **Enable SLO** na secção **OPÇÕES.**
    1. Selecione **Atualizar**.

### <a name="create-an-ekarda-test-user"></a>Criar um utilizador de teste ekarda

Nesta secção, um utilizador chamado B.Simon é criado em ekarda. A ekarda suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não tem nada a tomar nesta secção. Se um utilizador chamado B.Simon já não existir na Ekarda, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para ekarda Assinar no URL onde pode iniciar o fluxo de login.

* Vá diretamente para o URL de inscrição de Ekarda e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no ekarda para o qual configura o SSO

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo ekarda nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no ekarda para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Depois de configurar ekarda, pode impor o controlo da sessão. Esta precaução protege contra a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Controlo de Aplicações de Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).