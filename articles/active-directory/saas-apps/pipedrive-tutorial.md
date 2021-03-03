---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Pipedrive | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Pipedrive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 96abe2209d4298f23c47c62fec970430f69cbaf6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101654430"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pipedrive"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Pipedrive

Neste tutorial, você vai aprender a integrar Pipedrive com Azure Ative Directory (Azure AD). Quando integrar pipedrive com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Pipedrive.
* Ativar os seus utilizadores a serem automaticamente inscritos na Pipedrive com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Pipedrive assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Pipedrive suporta SSO iniciado **SP e IDP**

## <a name="add-pipedrive-from-the-gallery"></a>Adicione Pipedrive da galeria

Para configurar a integração da Pipedrive no Azure AD, é necessário adicionar pipedrive da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Pipedrive** na caixa de pesquisa.
1. Selecione **Pipedrive** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-pipedrive"></a>Configure e teste Azure AD SSO para pipedrive

Configure e teste Azure AD SSO com Pipedrive utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Pipedrive.

Para configurar e testar o Azure AD SSO com pipedrive, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure pipedrive SSO](#configure-pipedrive-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Pipedrive test user](#create-pipedrive-test-user)** - para ter uma contraparte de B.Simon em Pipedrive que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Pipedrive,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp/metadata.xml`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<COMPANY-NAME>.pipedrive.com/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de suporte do Cliente Pipedrive](mailto:support@pipedrive.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação Pipedrive espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Pipedrive espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name | Atributo de origem|
    | ------------ | --------- |
    | e-mail | user.mail |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador e também copiar o Url de **Metadados da Federação de Aplicações** e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](./media/pipedrive-tutorial/certificate-data.png)

1. Na secção **Configurar Pipedrive,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Pipedrive.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Pipedrive**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-pipedrive-sso"></a>Configurar pipedrive SSO

1. Numa janela de navegador diferente, inscreva-se no website da Pipedrive como administrador.

1. Clique no **perfil do utilizador** e selecione **Definições**.

    ![Screenshot que mostra "Definições" selecionadas no menu "Perfil do Utilizador".](./media/pipedrive-tutorial/configure-1.png)

1. Desloque-se até ao centro de segurança e selecione **single sign-on**.

    ![Screenshot que mostra "Single sign-on" selecionado no "Security Center".](./media/pipedrive-tutorial/configure-2.png)

1. Na **configuração SAML para** a secção pipedrive, execute os seguintes passos:

    ![Screenshot que mostra a secção "S A M L para Pipedrive" com todas as caixas de texto em destaque.](./media/pipedrive-tutorial/configure-3.png)

    a. Na caixa de texto **emitente,** cole o valor do **url de metadados da Federação de Aplicações,** que copiou a partir do portal Azure.

    b. Na caixa de texto **url (SSO) de signing único,** cole o valor URL de **login,** que copiou a partir do portal Azure.

    c. Na caixa de texto **url de Log out (SLO) única,** cole o valor URL **logout,** que copiou a partir do portal Azure.

    d. Na caixa de texto do **certificado x.509,** abra o ficheiro **Certificado (Base64)** descarregado do portal Azure para o Bloco de Notas e copie o conteúdo do mesmo e cole-o na caixa de texto **do certificado x.509** e guarde alterações.

### <a name="create-pipedrive-test-user"></a>Criar utilizador de teste Pipedrive

1. Numa janela de navegador diferente, inscreva-se no website da Pipedrive como administrador.

1. Percorra para baixo para a empresa e selecione **gerir os utilizadores**.

    ![Screenshot que mostra "Gerir utilizadores" selecionados a partir do menu "Empresa".](./media/pipedrive-tutorial/user-1.png)

1. Clique em **Adicionar utilizadores**.
    
    ![Screenshot que mostra a página "Gerir os utilizadores" com o botão "Adicionar utilizadores" selecionado no lado direito.](./media/pipedrive-tutorial/user-2.png)

1. Na secção Gerir os **utilizadores,** execute os seguintes passos:

    ![Configuração pipedrive](./media/pipedrive-tutorial/user-3.png)

    a. Na caixa de sms **do Email,** insira o endereço de e-mail do utilizador como `B.Simon@contoso.com` .

    b. Na caixa de texto do **primeiro nome,** insira o primeiro nome do utilizador.

    c. Na caixa de texto **do último nome,** insira o último nome do utilizador.

    d. Clique **em Confirmar e convidar os utilizadores.**

## <a name="test-sso"></a>Teste SSO 


Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Pipedrive Sign no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de inscrição da Pipedrive e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Pipedrive para o qual configura o SSO. 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo Pipedrive nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Pipedrive para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Pipedrive, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).