---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com plataforma de patentes IamIP / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a IamIP Patent Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: jeedes
ms.openlocfilehash: dc9be41cd7975610f2779da09b94066c70a91652
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460534"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iamip-patent-platform"></a>Tutorial: Azure Ative Directory integração única (SSO) com plataforma de patentes IamIP

Neste tutorial, você vai aprender a integrar a Plataforma de Patentes IamIP com O Diretório Ativo Azure (Azure AD). Quando integrar a Plataforma de Patentes IamIP com AZure AD, pode:

* Use a Azure AD para controlar quem pode aceder à Plataforma de Patentes IamIP.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Plataforma de Patentes IamIP com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte [o Single sign-on para aplicações no Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição da Plataforma de Patentes IamIP com um único sinal de sso ativado.

## <a name="tutorial-description"></a>Descrição tutorial

Neste tutorial, você vai configurar e testar Azure AD SSO em um ambiente de teste.

A Plataforma de Patentes IamIP suporta SSO iniciado pelo SP e iniciado pelo IDP.

Depois de configurar a Plataforma de Patentes IamIP, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).


## <a name="add-iamip-patent-platform-from-the-gallery"></a>Adicione plataforma de patentes IamIP da galeria

Para configurar a integração da Plataforma de Patentes IamIP no AD Azure, é necessário adicionar a Plataforma de Patentes IamIP da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta de trabalho ou escola ou com uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione **Azure Active Directory**.
1. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** digite **plataforma de patentes IamIP** na caixa de pesquisa.
1. Selecione plataforma de **patentes IamIP** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-iamip-patent-platform"></a>Configurar e testar Azure AD SSO para plataforma de patentes IamIP

Irá configurar e testar O SSO Azure AD com plataforma de patente IamIP utilizando um utilizador de teste chamado B.Simon. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador correspondente na Plataforma de Patentes IamIP.

Para configurar e testar o Azure AD SSO com a Plataforma de Patentes IamIP, você vai dar estes passos de alto nível:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem a funcionalidade.
    * **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com um único sinal de sessão.
    * **[Conceder acesso ao utilizador de teste](#grant-access-to-the-test-user)** para permitir ao utilizador utilizar um único sinal de Azure.

1. **[Configurar a Plataforma de Patentes IamIP SSO](#configure-iamip-patent-platform-sso)** do lado da aplicação.
    * Crie um utilizador de **[teste da Plataforma de PatenteS IamIP](#create-iamip-patent-platform-test-user)** como contrapartida da representação AD AD do utilizador.

1. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir que o Azure AD SSO no portal Azure:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações da **Plataforma de Patentes IamIP,** na secção **Gerir,** selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. Na **configuração de Sign-On única com** a página SAML, selecione o botão de lápis para **configuração SAML Básica** para editar as definições:

   ![Botão de lápis para configuração básica de SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** se tiver um ficheiro de metadados do Fornecedor de Serviços e pretender configurar sSO no modo iniciado pelo IDP, tome estas medidas:

    a. Selecione **carregar o ficheiro de metadados:**

    ![Carregar ficheiro de metadados](common/upload-metadata.png)

    b. Selecione o botão de pasta, selecione o ficheiro de metadados e, em seguida, **selecione Upload**:

    ![Botões de pasta e upload](common/browse-upload-metadata.png)

    c. Após os uploads do ficheiro de metadados, os valores de URL **de identificação** e **resposta** povoam-se automaticamente na secção **de Configuração Básica SAML:**

    ![Valores de URL de identificação e resposta](common/idp-intiated.png)

    > [!Note]
    > Se os valores de URL **de identificação** e **resposta** não forem preenchidos automaticamente, forneça os valores manualmente de acordo com os seus requisitos.

1. Selecione **Definir URLs adicionais** e completar o seguinte passo se quiser configurar a aplicação no modo iniciado pelo SP:

    Na caixa **URL de inscrição,**  **insira https: \/ /patents.iamip.com/login-user**.

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** selecione o link **de descarregamento** para **Certificado (Raw)** para descarregar o certificado e guardá-lo no seu computador:

    ![Link de descarregamento de certificado](common/certificateraw.png)

1. Na secção Plataforma de **Patentes IamIP,** copie os URL ou URLs apropriados, com base nos seus requisitos:

    ![URLs de configuração de cópia](common/idp-intiated.png))

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste chamado B.Simon no portal Azure.

1. No painel esquerdo do portal Azure, selecione **Azure Ative Directory**. Selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** complete estes passos:
   1. Na caixa **Nome,** insira **B.Simon**.  
   1. Na caixa **do nome do utilizador,** \<username> @ \<companydomain> introduza \<extension> . . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione **Mostrar a palavra-passe**e, em seguida, anotar o valor que é apresentado na caixa **palavra-passe.**
   1. Selecione **Criar**.

### <a name="grant-access-to-the-test-user"></a>Conceder acesso ao utilizador de teste

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo a esse utilizador acesso à Plataforma de Patentes IamIP.

1. No portal Azure, selecione **aplicações Enterprise**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **plataforma de patentes IamIP**.
1. Na página geral da aplicação, na secção **Gerir,** selecione **Utilizadores e grupos**:

   ![Selecionar Utilizadores e grupos](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **de atribuição de adicionar:**

    ![Selecione Adicionar utilizador](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** na lista **de Utilizadores** e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione o botão **'Atribuir'.**

## <a name="configure-iamip-patent-platform-sso"></a>Configurar plataforma de patentes IamIP SSO

Para configurar um único sinal no lado da Plataforma de Patentes IamIP, você precisa enviar o certificado em bruto descarregado e os URLs apropriados que copiou do portal Azure para a equipa de suporte da [Plataforma de Patentes IamIP.](mailto:info@iamip.com) Configuram a ligação SSO SAML para ser correta em ambos os lados.

### <a name="create-iamip-patent-platform-test-user"></a>Criar utilizador de teste da Plataforma de Patentes IamIP

Trabalhe com a equipa de suporte da [Plataforma de Patentes IamIP](mailto:info@iamip.com) para adicionar um utilizador chamado B.Simon na Plataforma de Patentes IamIP. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, irá testar a sua configuração Azure AD SSO utilizando o Painel de Acesso.

Quando selecionar o azulejo da Plataforma de Patentes IamIP no Painel de Acesso, deverá ser automaticamente inscrito na instância da Plataforma de Patentes IamIP para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [o Painel de Introdução ao Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais sobre como integrar apps SaaS com Diretório Ativo Azure](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)

- [Experimente plataforma de patentes IamIP com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)