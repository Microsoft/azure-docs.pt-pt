---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com NS1 SSO para Azure [ Azure] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o NS1 SSO para o Azure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 24a1afb6-b8b6-4787-bd4b-8fe3a32f8def
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e917265f4bf6f857a0eada2433f0a0e4e24d7c5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77565592"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ns1-sso-for-azure"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com NS1 SSO para Azure

Neste tutorial, você vai aprender a integrar NS1 SSO para Azure com Azure Ative Directory (Azure AD). Quando integrar ns1 SSO para Azure com Azure AD, pode:

* Controlo em Azure AD que tem acesso a NS1 SSO para Azure.
* Permita que os seus utilizadores sejam automaticamente inscritos no NS1 SSO para o Azure com as suas contas Azure AD.
* Gerencie as suas contas num local central, o portal Azure.

Para saber mais sobre software como uma integração de aplicações de serviço (SaaS) com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* NS1 SSO para assinatura única (SSO) ativada por NS1 SSO.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* NS1 SSO para Azure suporta SP e IDP iniciado SSO.
* Depois de configurar ns1 SSO para Azure, pode impor o controlo da sessão. Isto protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o acesso condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .


## <a name="add-ns1-sso-for-azure-from-the-gallery"></a>Adicione NS1 SSO para Azure da galeria

Para configurar a integração do NS1 SSO para azure em Azure AD, você precisa adicionar NS1 SSO para Azure da galeria para a sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou de escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Vá a **Aplicações Empresariais**e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **NS1 SSO para Azure** na caixa de pesquisa.
1. Selecione **NS1 SSO para Azure** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ns1-sso-for-azure"></a>Configure e teste Azure AD único sign-on para NS1 SSO para Azure

Configure e teste Azure AD SSO com NS1 SSO para Azure utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, estabeleça uma relação ligada entre um utilizador da AD Azure e o utilizador relacionado no NS1 SSO para o Azure.

Aqui estão os passos gerais para configurar e testar Azure AD SSO com NS1 SSO para Azure:

1. **[Configure O SSO AD Azure](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.

    a. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de Azure AD com B.Simon.

    b. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure ns1 SSO para Azure SSO](#configure-ns1-sso-for-azure-sso)** para configurar as definições de inscrição únicas no lado da aplicação.

    a. **[Crie um NS1 SSO para](#create-an-ns1-sso-for-azure-test-user)** que o utilizador do teste Azure tenha uma contrapartida de B.Simon no NS1 SSO para o Azure. Esta contrapartida está ligada à representação da AD Azure do utilizador.
1. **[Teste sSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **da NS1 SSO para Azure,** encontre a secção **Gerir.** Selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set set single sign-on com** a página SAML, selecione o ícone do lápis para **configuração SAML básica** para editar as definições.

   ![Screenshot de Configurar um único sign-on com página SAML, com ícone de lápis realçado](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado **idp,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **identificador,** escreva o seguinte URL:`https://api.nsone.net/saml/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL que utilize o seguinte padrão:`https://api.nsone.net/saml/sso/<ssoid>`

1. Selecione **Definir URLs adicionais**e executar o seguinte passo se pretender configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **"Sign-on URL",** escreva o seguinte URL:`https://my.nsone.net/#/login/sso`

    > [!NOTE]
    > O valor url de resposta não é real. Atualizar valor URL resposta com o URL de resposta real. Contacte a equipa de suporte ao [Cliente NS1 para](mailto:techops@nsone.net) obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. A aplicação NS1 SSO para Azure espera as afirmações do SAML num formato específico. Configure as seguintes reclamações para esta aplicação. Pode gerir os valores destes atributos a partir da secção **Deatributos do Utilizador & Reclamações** na página de integração da aplicação. Na configuração do single sign-on com a página **SAML,** selecione o ícone do lápis para abrir a caixa de diálogo **user Atributos.**

    ![Screenshot dos atributos do utilizador & secção de reclamações, com ícone de lápis em destaque](./media/ns1-sso-for-azure-tutorial/attribute-edit-option.png)

1. Selecione o nome do atributo para editar a reclamação.

    ![Screenshot dos atributos do utilizador & secção de reclamações, com o nome do atributo destacado](./media/ns1-sso-for-azure-tutorial/attribute-claim-edit.png)

1. Selecione **A Transformação**.

    ![Screenshot da secção de reivindicação de gestão, com Transformação em destaque](./media/ns1-sso-for-azure-tutorial/prefix-edit.png)

1. Na secção **de transformação Gerir,** execute os seguintes passos:

    ![Screenshot da secção de transformação de Gerir, com vários campos em destaque](./media/ns1-sso-for-azure-tutorial/prefix-added.png)

    1. Selecione **ExactMailPrefix()** como **Transformação**.

    1. Selecione **user.userprincipalname** as **Parameter 1**.

    1. Selecione **Adicionar**.

    1. Selecione **Guardar**.

1. No **set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** selecione o botão de cópia. Isto copia o Url de **Metadados da Federação** de Aplicações e guarda-o no seu computador.

    ![Screenshot do Certificado de Assinatura SAML, com o botão de cópia realçado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, cria-se um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure,**Users** > selecione **Utilizadores de Diretório** > Ativo Azure**Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:

   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está mostrado no campo **Password.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que a B.Simon utilize um único sign-on Azure, concedendo acesso ao NS1 SSO para o Azure.

1. No portal Azure, selecione **Aplicações Empresariais** > **Todas as aplicações**.
1. Na lista de aplicações, selecione **NS1 SSO para Azure**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![Screenshot da secção Gerir, com Utilizadores e grupos em destaque](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Na caixa de diálogo **Adicionar Atribuição,** selecione **Utilizadores e grupos**.

    ![Screenshot de Utilizadores e página de grupos, com adicionar utilizador destacado](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Em seguida, escolha o botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de papel na afirmação do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, escolha o botão **Select** na parte inferior do ecrã.
1. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

## <a name="configure-ns1-sso-for-azure-sso"></a>Configure NS1 SSO para SSO Azure

Para configurar um único sinal no NS1 SSO para o lado Azure, você precisa enviar o URL de Metadados da Federação de Aplicações para a equipa de [suporte NS1 SSO para azure](mailto:techops@nsone.net). Configuram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-an-ns1-sso-for-azure-test-user"></a>Criar um NS1 SSO para o utilizador de teste Azure

Nesta secção, cria-se um utilizador chamado B.Simon no NS1 SSO para o Azure. Trabalhe com a equipa de suporte NS1 SSO para azure para adicionar os utilizadores na plataforma NS1 SSO para azure. Não pode utilizar um único sinal até criar e ativar os utilizadores.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de acesso do Azure AD utilizando o Painel de Acesso.

Quando selecionar o NS1 SSO para azulejos Azure no Painel de Acesso, deve ser automaticamente inscrito no NS1 SSO para Azure para o qual configura o SSO. Para mais informações, consulte [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente NS1 SSO para Azure com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)