---
title: 'Tutorial: Azure Ative Diretório integração de inscrição única (SSO) com docuSign [ Integração de assinaturas únicas do Azure Ative Diretório) com docuSign [ DocuSign ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b827c2e949502ad8bd19378a84ea89947929459d
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509368"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Tutorial: Azure Ative Diretório integração de inscrição única (SSO) com docuSign

Neste tutorial, aprenderá a integrar o DocuSign com o Microsoft Azure Ative Directory (Azure AD). Quando integrar o DocuSign com o Azure AD, pode:

* Utilize a AD Azure para controlar quem tem acesso ao DocuSign.
* Ative o sessão automático no DocuSign para os seus utilizadores através das suas contas Azure AD.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre software como uma integração de aplicações de serviço (SaaS) com a Azure AD, consulte [o single sign-on para aplicações em Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição DocuSign ativada por um único sinal (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, irá configurar e testar o Azure AD SSO num ambiente de teste para verificar se:

* O DocuSign suporta o prestador de serviços (SP)iniciado por SSO.

* O DocuSign suporta o fornecimento de utilizadores **just-in-time.**

* O DocuSign suporta [o fornecimento automático de utilizadores](https://docs.microsoft.com/azure/active-directory/saas-apps/docusign-provisioning-tutorial).
* Assim que configurar o DocuSign, pode impor o controlo da Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-docusign-from-the-gallery"></a>Adicionar DocuSign da galeria

Para configurar a integração do DocuSign em Azure AD, deve adicionar docuSign da galeria à sua lista de aplicações saaS geridas:

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou escola, ou utilizando uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **DocuSign** na caixa de pesquisa.
1. Selecione **DocuSign** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-docusign"></a>Configure e teste azure AD único sinal para DocuSign

Configure e teste Azure AD SSO com DocuSign utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, deve estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador correspondente no DocuSign.

Para configurar e testar o Azure AD SSO com o DocuSign, complete os seguintes blocos de construção:

1. [Configure O SSO AD Azure](#configure-azure-ad-sso) para que os seus utilizadores possam utilizar esta funcionalidade.
    1. [Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user) para testar o único sinal de Azure AD com B.Simon.
    1. [Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que b.Simon utilize um único sinal de AD Azure.
1. [Configure docuSign SSO](#configure-docusign-sso) para configurar as definições de sinal única no lado da aplicação.
    1. [Crie um utilizador de teste DocuSign](#create-docusign-test-user) para gerar uma contrapartida de B.Simon no DocuSign que esteja ligada à representação da AD Azure do utilizador.
1. [Teste sSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Para permitir o Azure AD SSO no portal Azure, siga estes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **DocuSign,** encontre a secção **Gerir** e, em seguida, selecione **um único sinal .**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set set single sign-on com** a página SAML, selecione o ícone da caneta para **configuração SAML básica** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** siga estes passos:

    a. No **Sign on URL** textbox, introduza um URL utilizando o seguinte padrão:

    `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Na caixa de texto **identificador (Id** da entidade), introduza um URL utilizando o seguinte padrão:

    `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    c. Na caixa de texto url de **resposta,** introduza um URL utilizando o seguinte padrão:
    
    `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login`

    > [!NOTE]
    > Estes valores são espaços reservados. Substitua-os com os valores no URL de inscrição real, identificador e URL de resposta. Estes detalhes são explicados na secção "Ver SAML 2.0 Endpoints" mais tarde neste tutorial.

1. No **set set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** encontre **certificado (Base64)**. Selecione **Baixar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção Configurar do **CuSign,** copie o URL (ou URLs) apropriado com base nos seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste chamado B.Simon no portal Azure.

1. No painel esquerdo do portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Na parte superior do ecrã, selecione **Novo utilizador**.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome,** insira **B.Simon.**  
   1. No campo de nome `<username>@<companydomain>.<extension>`do **Utilizador,** introduza . Por exemplo: `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, tome nota do valor que está apresentado na caixa **password.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, você concederá acesso b.Simon ao DocuSign para que este utilizador possa usar o único sinal de Azure.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **DocuSign**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**, e, em seguida, na caixa de diálogo **Adicionar Assignment,** selecione Utilizadores e **grupos**.

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. Na caixa de diálogo **Utilizadores e grupos,** selecione **B.Simon** da lista **de Utilizadores** e, em seguida, pressione o botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, prima o botão **Select** na parte inferior do ecrã.
1. Na caixa de diálogo **Adicionar Atribuição,** selecione o botão **Atribuir.**

## <a name="configure-docusign-sso"></a>Configure DocuSign SSO

1. Para automatizar a configuração no DocuSign, tem de instalar a extensão de navegador 'Secure Sign-in' das Minhas Aplicações selecionando **a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, **selecione Configurar docuSign**. Está direcionado para a aplicação DocuSign. A partir daí, forneça as credenciais de administração para iniciar sessão no DocuSign. A extensão do navegador configura automaticamente a aplicação e automatiza os passos 3 a 5.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o DocuSign manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa DocuSign como administrador.

4. No canto superior direito da página, selecione o logótipo do perfil e, em seguida, selecione **Ir para Administrador**.
  
    ![Ir ao Administrador sob perfil][51]

5. Na página de soluções de domínio, selecione **Domínios**.

    ![Soluções de Domínio/Domínios][50]

6. Na secção **Domínios,** selecione **DOMÍNIO DE REIVINDICAÇÃO**.

    ![Opção domínio de reclamação][52]

7. Na caixa de diálogo **'Claim',** na caixa **'Nome de Domínio',** digite o domínio da empresa e, em seguida, selecione **CLAIM**. Certifique-se de que verifica o domínio e que o seu estado está ativo.

    ![Reclamar um diálogo de nome de domínio/domínio][53]

8. Na página de soluções de domínio, selecione **Fornecedores de Identidade**.
  
    ![Opção Fornecedores de Identidade][54]

9. Na secção Fornecedores de **Identidade,** selecione **ADD IDENTITY PROVIDER**.

    ![Adicionar opção fornecedor de identidade][55]

10. Na página Definições do Fornecedor de **Identidade,** siga estes passos:

    ![Campos de definição de fornecedor de identidade][56]

    a. Na caixa **Nome,** escreva um nome único para a sua configuração. Não use espaços.

    b. Na caixa de **Emitente**de Fornecedor de Identidade, cola o valor do **Identificador AD Azure,** que copiou do portal Azure.

    c. Na caixa URL do Fornecedor de **Identidade,** colá o valor URL do **Login,** que copiou do portal Azure.

    d. Na caixa URL de Logout do Fornecedor de **Identidade,** colhe o valor do URL de **Logout,** que copiou do portal Azure.

    e. Selecione **pedido Sign AuthN**.

    f. Para **enviar pedido AuthN por**, selecione **POST**.

    g. Para **Enviar pedido de logout por**, selecione **GET**.

    h. Na secção de Mapeamento de **Atributos Personalizados,** selecione **ADICIONAR NOVO MAPEAMENTO**.

       ![Atribuição personalizada Mapeamento UI][62]

    i. Escolha o campo que pretende mapear para a reivindicação da AD Azure. Neste exemplo, a reclamação de endereço de `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` **e-mail** é mapeada com o valor de . Este é o nome de reclamação padrão da AD Azure para a reclamação de e-mail. Selecione **SAVE**.

       ![Campos de mapeamento de atributos personalizados][57]

       > [!NOTE]
       > Utilize o **identificador** utilizador apropriado para mapear o utilizador de Azure AD para o mapeamento do utilizador DocuSign. Selecione o campo adequado e introduza o valor adequado com base nas definições da sua organização.

    j. Na secção certificados de **fornecedor de identidade,** selecione **ADD CERTIFICATE,** faça upload do certificado que descarregou do portal Azure AD e selecione **SAVE**.

       ![Certificados/Certificado de Adição de Fornecedor de Identidade][58]

    k. Na secção Fornecedores de **Identidade,** selecione **AÇÕES**, e, em seguida, selecione **Pontos Finais**.

       ![Fornecedores de Identidade/Pontos Finais][59]

    l. Na secção **Ver SAML 2.0 Pontos finais** do portal de administração DocuSign, siga estes passos:

       ![Ver Pontos Finais SAML 2.0][60]
       
       1. Copie o URL do **Emitente**do Prestador de Serviços e cole-o na caixa **de identificação** na secção **de configuração SAML básica** no portal Azure.
       
       1. Copie o URL do **Serviço Afirmação**do Serviço ao Consumidor e cole-o na caixa URL de **resposta** na secção **de configuração SAML Básica** no portal Azure.
       
       1. Copie o URL de login do prestador de **serviços**e, em seguida, cole-o na caixa **de URL Sign On** na secção basic **SAML Configuração** no portal Azure. No final do URL de **Login** do Fornecedor de Serviços, obterá o valor IDPID.

       1. Selecione **Fechar**.

### <a name="create-docusign-test-user"></a>Criar o utilizador de teste DocuSign

Nesta secção, um utilizador chamado B.Simon é criado no DocuSign. O DocuSign suporta o fornecimento de utilizadores just-in-time, que é ativado por predefinição. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no DocuSign, um novo é criado após a autenticação.

> [!Note]
> Se precisar de criar um utilizador manualmente, contacte a equipa de [suporte do DocuSign](https://support.docusign.com/).

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de acesso do Azure AD utilizando o Painel de Acesso.

Quando selecionar o azulejo DocuSign no Painel de Acesso, deve ser automaticamente inscrito na instância DocuSign para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais sobre como integrar aplicações saaS com AD Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única em Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é acesso condicional em Azure AD?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente docuSign com AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
