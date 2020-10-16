---
title: 'Tutorial: Azure Ative Directy integração única de sign-on (SSO) com NS1 SSO para Azure / Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o NS1 SSO para o Azure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.openlocfilehash: d0fee044506a9a19e09478ef8d70b3719ecc167a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88554265"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ns1-sso-for-azure"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com NS1 SSO para Azure

Neste tutorial, você vai aprender a integrar NS1 SSO para Azure com Azure Ative Directory (Azure AD). Quando integrar o SSO NS1 para Azure com Azure AD, pode:

* Controle em Azure AD que tem acesso a NS1 SSO para Azure.
* Permita que os seus utilizadores sejam automaticamente inscritos no SSO NS1 para a Azure com as suas contas AD Azure.
* Gerencie as suas contas numa localização central, o portal Azure.

Para saber mais sobre software como integração de aplicações de serviço (SaaS) com Azure AD, consulte O que é o acesso à [aplicação e um único acesso com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* NS1 SSO para a assinatura única ativada (SSO) do Azure.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* O SSO NS1 para a Azure suporta o SP e o IDP iniciado SSO.
* Depois de configurar o NS1 SSO para o Azure, pode impor o controlo da sessão. Isto protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o acesso condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="add-ns1-sso-for-azure-from-the-gallery"></a>Adicione SSO NS1 para Azure da galeria

Para configurar a integração do NS1 SSO para Azure em Ad AZure, precisa adicionar NS1 SSO para Azure da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Vá a **Aplicações Empresariais**e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite NS1 SSO para Azure** na caixa de pesquisa.
1. Selecione **NS1 SSO para Azure** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ns1-sso-for-azure"></a>Configurar e testar a Azure AD um único sinal para NS1 SSO para Azure

Configure e teste Azure AD SSO com NS1 SSO para Azure utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, estabeleça uma relação ligada entre um utilizador Azure AD e o utilizador relacionado no SSO NS1 para a Azure.

Aqui estão os passos gerais para configurar e testar Azure AD SSO com NS1 SSO para Azure:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.

    a. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com B.Simon.

    b. **[Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que a B.Simon utilize um único sinal de Ad AD.
1. **[Configure o SSO NS1 para o Azure SSO](#configure-ns1-sso-for-azure-sso)** configurar as definições de inscrição única no lado da aplicação.

    a. **[Crie um SSO NS1 para o utilizador de teste Azure](#create-an-ns1-sso-for-azure-test-user)** para ter uma contraparte de B.Simon em NS1 SSO para a Azure. Esta contrapartida está ligada à representação Azure AD do utilizador.
1. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página **NS1 SSO for Azure** application integration, encontre a secção **Gerir.** Selecione **um único sinal de inscrição**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, selecione o ícone de lápis para **Configuração SAML Básica** para editar as definições.

   ![Screenshot de Configurar um único sinal com página SAML, com ícone de lápis em destaque](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier,** digite o seguinte URL: `https://api.nsone.net/saml/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL que utiliza o seguinte padrão: `https://api.nsone.net/saml/sso/<ssoid>`

1. Selecione **Definir URLs adicionais**e executar o seguinte passo se quiser configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite o seguinte URL:  `https://my.nsone.net/#/login/sso`

    > [!NOTE]
    > O valor URL de resposta não é real. Atualizar valor URL de resposta com o URL de resposta real. Contacte o [SSO NS1 para obter](mailto:techops@nsone.net) o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação NS1 SSO for Azure espera as afirmações do SAML num formato específico. Configure os seguintes pedidos para este pedido. Pode gerir os valores destes atributos a partir da secção **Atributos & Reclamações** do Utilizador na página de integração da aplicação. Na **configuração single Sign-On com** a página SAML, selecione o ícone de lápis para abrir a caixa de diálogo atributos do **utilizador.**

    ![Screenshot de atributos de utilizador & Seção reivindicações, com ícone de lápis em destaque](./media/ns1-sso-for-azure-tutorial/attribute-edit-option.png)

1. Selecione o nome do atributo para editar a reclamação.

    ![Screenshot da secção Atributos de Utilizador & Reclamações, com nome de atributo em destaque](./media/ns1-sso-for-azure-tutorial/attribute-claim-edit.png)

1. Selecione **Transformação**.

    ![Screenshot da secção de reclamação de Gestão, com a Transformação em destaque](./media/ns1-sso-for-azure-tutorial/prefix-edit.png)

1. Na secção **De Transformação** Gerir, execute os seguintes passos:

    ![Screenshot da secção de transformação Manage, com vários campos em destaque](./media/ns1-sso-for-azure-tutorial/prefix-added.png)

    1. Selecione **ExactMailPrefix()** como **Transformação**.

    1. Selecione **user.userprincipalname** como **Parâmetro 1**.

    1. Selecione **Adicionar**.

    1. Selecione **Guardar**.

1. Na **configuração de um único sinal de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** selecione o botão de cópia. Isto copia o **url de metadados da Federação de Aplicações** e guarda-o no seu computador.

    ![Screenshot do Certificado de Assinatura SAML, com o botão de cópia realçado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, cria-se um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >  **Users**  >  **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:

   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavras-passe Show** e, em seguida, anote o valor mostrado no campo **Palavra-passe.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que B.Simon utilize o Azure single sign-on, concedendo acesso a NS1 SSO para Azure.

1. No portal Azure, selecione **Aplicações empresariais**  >  **Todas as aplicações**.
1. Na lista de candidaturas, selecione **NS1 SSO para Azure**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![Screenshot da secção Gerir, com utilizadores e grupos em destaque](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Na caixa de diálogo **de atribuição de** adicionar, selecione **Utilizadores e grupos**.

    ![Screenshot da página de Utilizadores e grupos, com o utilizador Adicionar em destaque](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Em seguida, escolha o botão **Selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, escolha o botão **Selecione** na parte inferior do ecrã.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

## <a name="configure-ns1-sso-for-azure-sso"></a>Configure NS1 SSO para Azure SSO

Para configurar um único sinal no lado NS1 SSO para o lado Azure, é necessário enviar o URL de Metadados da Federação de Aplicações para a [equipa de suporte NS1 SSO para a equipa de suporte Azure](mailto:techops@nsone.net). Configuram esta definição para que a ligação SSO SAML seja corretamente definida em ambos os lados.

### <a name="create-an-ns1-sso-for-azure-test-user"></a>Crie um SSO NS1 para utilizador de teste Azure

Nesta secção, cria-se um utilizador chamado B.Simon in NS1 SSO for Azure. Trabalhe com a equipa de suporte NS1 SSO para a Azure para adicionar os utilizadores na plataforma NS1 SSO para a azure. Não pode utilizar uma única sessão de inscrição até criar e ativar os utilizadores.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de acesso Azure AD utilizando o Painel de Acesso.

Quando selecionar o SSO NS1 para azulejo Azure no Painel de Acesso, deverá ser automaticamente inscrito no SSO NS1 para azure para o qual configura sSO. Para mais informações, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente O SSO NS1 para Azure com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)