---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com o Jamf Pro | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Jamf Pro.
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
ms.openlocfilehash: 7aaaa854ef7d0fd44bdccbe07cb38112475be367
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98727633"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-jamf-pro"></a>Tutorial: Azure Ative Directory SSO integração com Jamf Pro

Neste tutorial, você vai aprender a integrar Jamf Pro com Azure Ative Directory (Azure AD). Quando integrar o Jamf Pro com Azure AD, pode:

* Use a Azure AD para controlar quem tem acesso ao Jamf Pro.
* Assine automaticamente os seus utilizadores no Jamf Pro com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição jamf Pro que é um único sinal (SSO) ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. 

* Jamf Pro suporta **SSO iniciado pelo SP** e iniciado pelo **IdP.**

## <a name="add-jamf-pro-from-the-gallery"></a>Adicione Jamf Pro da galeria

Para configurar a integração do Jamf Pro no Azure AD, precisa de adicionar o Jamf Pro da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure utilizando uma conta de trabalho ou escola ou a sua conta pessoal da Microsoft.
1. No painel esquerdo, selecione o serviço **Azure Ative Directory.**
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **da galeria,** insira o *Jamf Pro* na caixa de pesquisa.
1. Selecione **Jamf Pro** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-sso-in-azure-ad-for-jamf-pro"></a>Configurar e testar SSO em AD Azure para Jamf Pro

Configure e teste Azure AD SSO com Jamf Pro usando um utilizador de teste chamado B.Simon. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Jamf Pro.

Nesta secção, configura e testa O SSO Azure AD com o Jamf Pro.

1. [Configure o SSO em Azure AD](#configure-sso-in-azure-ad) para que os seus utilizadores possam utilizar esta funcionalidade.
    1. [Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user) para testar O Azure AD SSO com a conta B.Simon.
    1. [Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para que a B.Simon possa utilizar sSO em Azure AD.
1. [Configure o SSO no Jamf Pro](#configure-sso-in-jamf-pro) para configurar as definições SSO no lado da aplicação.
    1. [Crie um utilizador de teste Jamf Pro](#create-a-jamf-pro-test-user) para ter uma contraparte de B.Simon no Jamf Pro que esteja ligada à representação AD AZure do utilizador.
1. [Teste a configuração SSO](#test-the-sso-configuration) para verificar se a configuração funciona.

## <a name="configure-sso-in-azure-ad"></a>Configurar SSO em Azure AD

Nesta secção, você ativa Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Jamf Pro,** encontre a secção **Gerir** e selecione **Single Sign-On**.
1. Na página **'Selecione' Um Método de Sign-On Único,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, selecione o ícone de lápis para **configuração SAML básica** para editar as definições.

   ![Editar a página básica de configuração SAML.](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo **iniciado pelo IdP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier,** introduza um URL que utiliza a seguinte fórmula: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. Na caixa de texto **URL de resposta,** introduza um URL que utiliza a seguinte fórmula: `https://<subdomain>.jamfcloud.com/saml/SSO`

1. Selecione **Definir URLs adicionais**. Se pretender configurar a aplicação no modo **iniciado pelo SP,** na caixa de texto **URL sign-on,** introduza um URL que utiliza a seguinte fórmula: `https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Você receberá o valor do identificador real a partir da secção **Sign-On única** no portal Jamf Pro, que é explicado mais tarde no tutorial. Pode extrair o valor real do subdomínio do valor do identificador e utilizar essa informação de subdomínio como URL de inscrição e URL de resposta. Também pode consultar as fórmulas apresentadas na secção **Configuração Básica SAML** no portal Azure.

1. Na **configuração single Sign-On com** a página SAML, vá à secção **de Certificado de Assinatura SAML,** selecione o botão **de cópia** para copiar o URL **de metadados da Federação de Aplicações** e guarde-o para o seu computador.

    ![O link de descarregamento do Certificado de Assinatura SAML](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, cria-se um utilizador de teste no portal Azure chamado B.Simon.

1. No painel esquerdo do portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.
   1. No campo **nome do utilizador,** insira [nome]@[companydomain]. [extensão]. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, concedes acesso à B.Simon ao Jamf Pro.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Jamf Pro**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Adicionar Atribuição'.**
1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, selecione o botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione o botão **'Atribuir'.**

## <a name="configure-sso-in-jamf-pro"></a>Configurar SSO no Jamf Pro

1. Para automatizar a configuração dentro do Jamf Pro, instale a extensão do **navegador 'As aplicações' Secure's,** selecionando **instalar a extensão**.

    ![As minhas apps secure sign-in browser extension page](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, **selecione Configurar o Jamf Pro**. Quando a aplicação Jamf Pro abrir, forneça as credenciais do administrador para iniciar sinsumento. A extensão do navegador configurará automaticamente a aplicação e automatizará os passos 3 a 7.

    ![Página de configuração de configuração no Jamf Pro](common/setup-sso.png)

3. Para configurar o Jamf Pro manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Jamf Pro como administrador. Então, dê os seguintes passos.

4. Selecione o **ícone Definições** a partir do canto superior direito da página.

    ![Selecione o ícone de definições no Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Selecione **um único sinal de inscrição**.

    ![Selecione single Sign-On no Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Na página **'Sign-On' único,** tome os seguintes passos.

    ![A página single Sign-On no Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Selecione **Editar**.

    b. Selecione a caixa **de verificação de autenticação individual Sign-On** ativada.

    c. Selecione **Azure** como opção do menu drop-down do Fornecedor de **Identidade.**

    d. Copie o valor **de ID** da ENTIDADE e cole-o no campo **Identificador (ID da Entidade)** na secção **Configuração DE SAML Básico** no portal Azure.

    > [!NOTE]
    > Utilize o valor no `<SUBDOMAIN>` campo para completar o URL de inscrição e URL de resposta na secção **configuração básica do SAML** no portal Azure.

    e. Selecione **o URL dos metadados** do menu de **metadados** de identidade Source. No campo que aparece, cole o valor url de **metadados da Federação de Aplicações** que copiou do portal Azure.

    f. (Opcional) Edite o valor de expiração do token ou selecione "Desativar a expiração do token SAML".

7. Na mesma página, desloque-se até à secção **de Mapeamento** do Utilizador. Então, dê os seguintes passos.

    ![A secção de mapeamento do utilizador da página single Sign-On no Jamf Pro.](./media/jamfprosamlconnector-tutorial/tutorial-jamfprosamlconnector-single.png)

    a. Selecione a opção **NameID** para **o mapeamento do utilizador do fornecedor de identidade.** Por predefinição, esta opção está definida como **NameID,** mas pode definir um atributo personalizado.

    b. Selecione **e-mail** para **jamf Pro user mapping**. Jamf Pro mapeia atributos SAML enviados pelo IdP primeiro pelos utilizadores e depois por grupos. Quando um utilizador tenta aceder ao Jamf Pro, o Jamf Pro obtém informações sobre o utilizador do Fornecedor de Identidade e corresponde-as a todas as contas de utilizador do Jamf Pro. Se a conta de utilizador recebida não for encontrada, o Jamf Pro tenta compará-la pelo nome de grupo.

    c. Cole o valor `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` no campo DE NOME DE ATRIBUTO DE GRUPO DE FORNECEDOR DE **IDENTIDADE.**

    d. Na mesma página, desloque-se até à secção **de Segurança** e selecione **Deixe os utilizadores contornarem a autenticação Sign-On única**. Como resultado, os utilizadores não serão redirecionados para a página de entrada do Fornecedor de Identidade para autenticação e podem iniciar sação diretamente no Jamf Pro. Quando um utilizador tenta aceder ao Jamf Pro através do Fornecedor de Identidade, ocorre a autenticação e autorização SSO iniciada pelo IdP.

    e. Selecione **Guardar**.

### <a name="create-a-jamf-pro-test-user"></a>Criar um utilizador de teste Jamf Pro

Para que os utilizadores da Azure AD entrem no Jamf Pro, devem ser adcedidos ao Jamf Pro. O provisionamento no Jamf Pro é uma tarefa manual.

Para obter uma conta de utilizador, tome as seguintes medidas:

1. Inscreva-se no site da empresa Jamf Pro como administrador.

2. Selecione o ícone **Definições** no canto superior direito da página.

    ![O ícone de definições no Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Selecione **As contas de utilizador Jamf Pro & Grupos**.

    ![As contas de utilizador Jamf Pro & ícones nas definições do Jamf Pro](./media/jamfprosamlconnector-tutorial/user1.png)

4. Selecione **Nova**.

    ![Página de configurações do sistema Jamf Pro & De Grupos](./media/jamfprosamlconnector-tutorial/user2.png)

5. Selecione **Criar Conta Padrão**.

    ![A opção 'Criar Conta Padrão' na página Grupos de & De Grupos de Utilizadores Jamf Pro](./media/jamfprosamlconnector-tutorial/user3.png)

6. Na caixa de diálogo **New Account,** execute os seguintes passos:

    ![Novas opções de configuração de conta nas definições do sistema Jamf Pro](./media/jamfprosamlconnector-tutorial/user4.png)

    a. No campo **USERNAME, introduza** `Britta Simon` o nome completo do utilizador de teste.

    b. Selecione as opções para **ACCESS LEVEL,** **PRIVILEGE SET** e **ACCESS STATUS** que estão de acordo com a sua organização.

    c. No campo **NOME COMPLETO,** insira `Britta Simon` .

    d. No campo **EMAIL ADDRESS,** insira o endereço de e-mail da conta de Britta Simon.

    e. No campo **PASSWORD,** introduza a palavra-passe do utilizador.

    f. No campo **VERIFICAR PASSWORD,** introduza novamente a palavra-passe do utilizador.

    exemplo, Selecione **Guardar**.

## <a name="test-the-sso-configuration"></a>Teste a configuração SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o Sinal jamf Pro no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de login jamf Pro e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Jamf Pro para o qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo jamf Pro nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Jamf Pro para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Assim que configurar o Jamf Pro, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O Controlo de Sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).