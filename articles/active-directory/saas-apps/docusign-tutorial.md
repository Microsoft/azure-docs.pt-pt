---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com docuSign | Microsoft Docs'
description: Saiba como configurar um único sign-on (SSO) entre o Azure Ative Directory e o DocuSign.
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
ms.openlocfilehash: 00d4381c7af7fdf82ee1e895072d92d1e641f8c4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454699"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Tutorial: Azure Ative Directory integração única (SSO) com DocuSign

Neste tutorial, você vai aprender a integrar DocuSign com Microsoft Azure Ative Directory (Azure AD). Quando integrar o DocuSign com a Ad Azure, pode:

* Use a Azure AD para controlar quem tem acesso ao DocuSign.
* Habilita o registo automático do DocuSign para os seus utilizadores através das suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição do DocuSign que está ativada por um único sinal (SSO).

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar Azure AD SSO em um ambiente de teste para verificar que:

* A DocuSign suporta o prestador de serviços **SP** iniciou a SSO.

* O DocuSign suporta o fornecimento de utilizadores **just-in-time.**

* O DocuSign suporta [o fornecimento automático do utilizador.](./docusign-provisioning-tutorial.md)

* Assim que configurar o DocuSign, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-docusign-from-the-gallery"></a>Adicionar DocuSign da galeria

Para configurar a integração do DocuSign no AD Azure, deve adicionar DocuSign da galeria à sua lista de aplicações geridas para o SaaS:

1. Inscreva-se no portal Azure utilizando uma conta de trabalho ou escola, ou utilizando uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite DocuSign** na caixa de pesquisa.
1. Selecione **DocuSign** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-docusign"></a>Configurar e testar Azure AD SSO para docuSign

Configure e teste Azure AD SSO com DocuSign utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, deve estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador correspondente no DocuSign.

Para configurar e testar a Azure AD SSO com o DocuSign, execute os seguintes passos:

1. [Configure Azure AD SSO](#configure-azure-ad-sso) para que os seus utilizadores possam utilizar esta funcionalidade.
    1. [Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user) para testar o Azure AD com B.Simon.
    1. [Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que a B.Simon utilize um único sinal de Ad AD.
1. [Configurar o DocuSign SSO](#configure-docusign-sso) para configurar as definições de inscrição única no lado da aplicação.
    1. [Crie um utilizador de teste DocuSign](#create-docusign-test-user) para gerar uma contraparte de B.Simon em DocuSign que está ligada à representação AD AZure do utilizador.
1. [Teste SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Para ativar o Azure AD SSO no portal Azure, siga estes passos:

1. No portal Azure, na página de integração da aplicação **DocuSign,** encontre a secção **Gerir** e, em seguida, selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, selecione o ícone de caneta para **configuração SAML básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** siga estes passos:

    a. Na placa de texto **URL,** introduza um URL utilizando o seguinte padrão:

    `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Na caixa de texto **identifier (Entity ID),** introduza um URL utilizando o seguinte padrão:

    `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    c. Na caixa de texto **URL de resposta,** insira qualquer um dos seguintes padrões de URL:
    
    | URL de Resposta |
    |-------------|
    |`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/<IDPID>`|
    |`https://<subdomain>.docusign.net/SAML/`|

    > [!NOTE]
    > Estes valores divididos são espaços reservados. Substitua-os pelos valores no URL de inscrição real, identifier e URL de resposta. Estes detalhes são explicados na secção "Ver PONTOS finais SAML 2.0" mais tarde neste tutorial.

1. Na **configuração de um único sinal com** a página SAML, na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)**. Selecione **Baixar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **DocuSign configurar,** copie o URL (ou URLs) apropriado com base nos seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste chamado B.Simon no portal Azure.

1. No painel esquerdo do portal Azure, selecione **O Diretório Ativo Azure,** selecione **Utilizadores,** e, em seguida, selecione **Todos os utilizadores**.
1. Na parte superior do ecrã, selecione **Novo utilizador**.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome,** insira **B.Simon**.  
   1. No campo **nome do utilizador,** insira `<username>@<companydomain>.<extension>` . Por exemplo: `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe Show** e, em seguida, tome nota do valor que é apresentado na caixa **palavra-passe.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você concederá acesso a B.Simon ao DocuSign para que este utilizador possa usar a assinatura única do Azure.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **DocuSign**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador** e, em seguida, na caixa de diálogo de atribuição de **adicionar,** selecione **Utilizadores e grupos**.
1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista **de Utilizadores** e, em seguida, prima o botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione o botão **'Atribuir'.**

## <a name="configure-docusign-sso"></a>Configurar docusign SSO

1. Para automatizar a configuração no DocuSign, tem de instalar a extensão do navegador 'As minhas aplicações' segura, selecionando **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, selecione **Setup DocuSign**. Está direcionado para a aplicação do DocuSign. A partir daí, forneça as credenciais de administração para se inscrever no DocuSign. A extensão do navegador configura automaticamente a aplicação e automatiza os passos 3 a 5.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o DocuSign manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa DocuSign como administrador.

4. No canto superior direito da página, selecione o logótipo do perfil e, em seguida, selecione **Ir para a Administração**.
  
    ![Ir para Administração sob Perfil][51]

5. Na página de soluções de domínio, selecione **Domínios**.

    ![Soluções/Domínios de Domínio][50]

6. Na secção **Domínios,** selecione **CLAIM DOMAIN**.

    ![Opção de Domínio de Reclamação][52]

7. Na caixa de diálogo **'Claim's a Domain,** na caixa **'Nome de Domínio',** digite o domínio da empresa e, em seguida, selecione **CLAIM**. Certifique-se de verificar o domínio e de que o seu estado está ativo.

    ![Reclamar um diálogo de nome de domínio/domínio][53]

8. Na página de soluções de domínio, selecione **Fornecedores de Identidade**.
  
    ![Opção fornecedores de identidade][54]

9. Na secção **Fornecedores de Identidade,** selecione **ADD IDENTITY PROVIDER**.

    ![Adicionar opção fornecedor de identidade][55]

10. Na página **Definições do Fornecedor de Identidade,** siga estes passos:

    ![Campos de Definições de Fornecedor de Identidade][56]

    a. Na caixa **Nome,** digite um nome único para a sua configuração. Não use espaços.

    b. Na caixa de **emitente do Fornecedor de Identidade,** cole o valor do **identificador Azure AD,** que copiou do portal Azure.

    c. Na caixa **URL do Fornecedor de Identidade,** cole o valor URL de **login,** que copiou do portal Azure.

    d. Na caixa **URL do Fornecedor de Identidade,** cole o valor do URL **logout,** que copiou do portal Azure.

    e. **Selecione Sign AuthN request**.

    f. Para **enviar pedido AuthN por,** selecione **POST**.

    exemplo, Para **enviar pedido de logout por,** selecione **GET**.

    h. Na secção **de mapeamento de atributos personalizados,** selecione **ADD NEW MAPPING**.

       ![Atributo personalizado Mapeamento UI][62]

    i. Escolha o campo que pretende mapear para a reivindicação AZure AD. Neste exemplo, a **reclamação de endereço de e-mail** é mapeada com o valor de `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` . É o nome de reclamação padrão da Azure AD para a reclamação de e-mail. Selecione **SAVE**.

       ![Campos de mapeamento de atributos personalizados][57]

       > [!NOTE]
       > Utilize o **identificador de utilizador** apropriado para mapear o utilizador de Azure AD para docuSign. Selecione o campo adequado e introduza o valor apropriado com base nas definições da sua organização.

    j. Na secção **Certificados de Fornecedor de Identidade,** selecione **ADD CERTIFICATE**, faça upload do certificado que descarregou do portal AD Azure e selecione **SAVE**.

       ![Certificados de Fornecedor de Identidade/Certificado de Adição][58]

    k. Na secção **Fornecedores de Identidade,** selecione **AÇÕES** e, em seguida, selecione **Pontos de Final**.

       ![Fornecedores de Identidade/Pontos Finais][59]

    l. Na secção **Ver SAML 2.0 Endpoints** do portal de administração DocuSign, siga estes passos:

       ![Ver PONTOS finais SAML 2.0][60]
       
       1. Copie o **URL do emitente do fornecedor de serviços** e, em seguida, cole-o na caixa **de identificador** na secção **de configuração SAML Básica** no portal Azure.
       
       1. Copie o **URL do Serviço de Apoio ao Consumidor de Afirmação do Fornecedor** de Serviços de Serviço , e, em seguida, cole-o na caixa **URL de resposta** na secção **de configuração SAML Básica** no portal Azure.
       
       1. Copie o **URL de login do fornecedor de serviços** e, em seguida, cole-o na caixa de URL sign **on** na secção **de configuração SAML básica** no portal Azure. No final do URL de Login do Fornecedor de **Serviços** obterá o valor IDPID.

       1. Selecione **Fechar**.

### <a name="create-docusign-test-user"></a>Criar utilizador de teste DocuSign

Nesta secção, um utilizador chamado B.Simon é criado em DocuSign. O DocuSign suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no DocuSign, um novo é criado após a autenticação.

> [!Note]
> Se precisar de criar um utilizador manualmente, contacte a equipa de suporte do [DocuSign](https://support.docusign.com/).

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

1. Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de assinatura do DocuSign, onde pode iniciar o fluxo de login. 

2. Vá diretamente ao URL de inscrição do DocuSign e inicie o fluxo de login a partir daí.

3. Pode utilizar o Microsoft Access Panel. Quando clicar no azulejo DocuSign no Painel de Acesso, deverá ser automaticamente inscrito no DocuSign para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos Seguintes

Assim que configurar o DocuSign, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

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