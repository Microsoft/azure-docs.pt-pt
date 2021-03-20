---
title: 'Tutorial: Integração do Diretório Ativo Azure com a configuração SSO AD SSO da MVISION Cloud Ad | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e a Configuração SSO AD SSO da MVISION Cloud Azure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: jeedes
ms.openlocfilehash: 456348f5492af44274a37a6400fb8d95e9bd55a4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015150"
---
# <a name="tutorial-integrate-mvision-cloud-azure-ad-sso-configuration-with-azure-active-directory"></a>Tutorial: Integrar a configuração SSO AD AD AD da MVISION com diretório ativo Azure

Neste tutorial, você vai aprender a integrar a configuração SSO AD AD da MVISION Cloud com O Diretório Ativo Azure (Azure AD). Quando integrar a configuração SSO AD AD AD da Cloud Cloud com AD Azure, pode:

* Control em Azure AD que tem acesso à Configuração SSO AD AD Azure da Cloud Cloud.
* Ativar os seus utilizadores a serem automaticamente inscritos na Configuração SSO AD AD AZure da Cloud Cloud com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode ter um mês de teste gratuito [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* MVISION Cloud Azure AD SSO Configuração única de sso ativada subscrição .


## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* MVISION Cloud Ad SSO Configuração suporta SSO iniciado **SP e IDP**
* Assim que configurar o Dropbox, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O Controlo de Sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-mvision-cloud-azure-ad-sso-configuration-from-the-gallery"></a>Adicionar configuração SSO AD da MVISION Cloud Ad da galeria

Para configurar a integração da Configuração SSO AD AD AD da MVISION Cloud Azure em Ad AD, é necessário adicionar a configuração SSO AD AD AD da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite MVISION Cloud Azure AD SSO Configuração** na caixa de pesquisa.
1. Selecione **MVISION Cloud Ad SSO Configuração do** painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com AXURE AD SSO Configuração MVISION Cloud AD usando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Configuração SSO AD AD Azure da MVISION Cloud.

Para configurar e testar O Azure AD SSO com a configuração SSO AD AD AdSO da MVISION Cloud, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
1. **[Configuração SSO de configuração MVISION Cloud Azure AD SSO](#configure-mvision-cloud-azure-ad-sso-configuration-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. Crie o utilizador de **[teste de configuração SSO MVISION Cloud Azure AD](#create-mvision-cloud-azure-ad-sso-configuration-test-user)** - para ter uma contraparte de Britta Simon na Configuração SSO AD AD Azure da MVISION Que esteja ligada à representação AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Datadog,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)


4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<ENV>.myshn.net/shndash/response/saml-postlogin`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![MVISION Cloud Azure AD SSO Configuration Domain e URLs informações únicas de sing-on](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte a equipa de [suporte do cliente de configuração MVISION Cloud Azure AD SSO](mailto:support@skyhighnetworks.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

7. Na secção **de Configuração MVISION Cloud Azure AD SSO,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador****brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, permite à Britta Simon utilizar o Azure single sign-on, permitindo o acesso à Configuração SSO AD da MVISION Cloud.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações**, em seguida, selecione **MVISION Cloud AD SSO Configuração**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **MVISION Cloud Azure AD SSO Configuração**.

    ![O link de configuração SSO AD da MVISION na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**


## <a name="configure-mvision-cloud-azure-ad-sso-configuration-sso"></a>Configuração SSO de configuração de SSO de nuvem MVISION

Para configurar um único sign-on no lado de **configuração MVISION Cloud Azure AD SSO,** é necessário enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de suporte de [configuração MVISION Cloud Azure AD SSO](mailto:support@skyhighnetworks.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.


### <a name="create-mvision-cloud-azure-ad-sso-configuration-test-user"></a>Criar utilizador de teste de configuração SSO AD SSO do MVISION Cloud Azure

Nesta secção, cria um utilizador chamado B.Simon na Configuração SSO AD AD Ad da MVISION Cloud. Trabalhe com a [equipa de suporte de configuração MVISION Cloud Ad SSO](mailto:support@skyhighnetworks.com) para adicionar os utilizadores na plataforma de configuração SSO AD AD Adure da MVISION Cloud. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo de configuração SSO MVISION Cloud Azure AD no Painel de Acesso, deverá ser automaticamente inscrito na Configuração SSO AD AD Adure MVISION cloud para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)

- [Experimente a configuração SSO AD da MVISION cloud com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)