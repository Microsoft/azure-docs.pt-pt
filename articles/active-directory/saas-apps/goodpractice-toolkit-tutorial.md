---
title: 'Tutorial: Integração do Azure Ative Directory com o Mind Tools Toolkit Microsoft Docs'
description: Saiba como configurar um único sinal de entrada entre o Azure Ative Directory e o Mind Tools Toolkit.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.openlocfilehash: 10773a2a379291fa861dbb2adfdda2ba3f63117f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88550644"
---
# <a name="tutorial-azure-active-directory-integration-with-mind-tools-toolkit"></a>Tutorial: Integração do Diretório Ativo Azure com o Mind Tools Toolkit

Neste tutorial, aprende-se a integrar o Mind Tools Toolkit com o Azure Ative Directory (Azure AD).

Com esta integração, pode:

* Controle em Azure AD que tem acesso ao Kit de Ferramentas de Ferramentas Mind.
* Ativar os seus utilizadores para serem automaticamente inscritos no Mind Tools Toolkit (súmido único) com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre software como integração de aplicações de serviço (SaaS) com Azure AD, consulte O que é o acesso à [aplicação e um único acesso com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o Mind Tools Toolkit, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição do Conjunto de Ferramentas mind tools com um único sinal de sso ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* O Mind Tools Toolkit suporta sSO iniciado pelo SP.
* O Mind Tools Toolkit suporta o fornecimento de utilizadores just-in-time.
* Depois de configurar o Conjunto de Ferramentas de Ferramentas mentais, pode impor o controlo da sessão. Este controlo protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o acesso condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mind-tools-toolkit-from-the-gallery"></a>Adicione o Kit de Ferramentas Mind Da galeria

Para configurar a integração do Mind Tools Toolkit no AD Azure, é necessário adicionar o Mind Tools Toolkit da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. No painel de navegação mais à esquerda, selecione o serviço **Azure Ative Directory.**
1. Vá a **Aplicações Empresariais**e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** insira o **Conjunto de Ferramentas Mind Tools** na caixa de pesquisa.
1. Selecione o Conjunto de **Ferramentas de Mente** a partir dos resultados da pesquisa e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o Mind Tools Toolkit utilizando um utilizador de teste chamado **B.Simon**. Para um único s-on para o trabalho, deve estabelecer uma relação ligada entre um utilizador AZure AD e o utilizador relacionado no Mind Tools Toolkit.

Para configurar e testar o Azure AD com o Mind Tools Toolkit, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com B.Simon.
    1. **[Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que a B.Simon utilize um único sinal de Ad AD.
1. **[Configure o Conjunto de Ferramentas mentais SSO](#configure-mind-tools-toolkit-sso)** para configurar as definições de inscrição única no lado da aplicação.
    1. **[Crie um utilizador de teste de ferramentas mentais](#create-a-mind-tools-toolkit-test-user)** para ter uma contrapartida de B.Simon in Mind Tools Toolkit. Esta contrapartida está ligada à representação Azure AD do utilizador.
1. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, configurar o Azure AD com o Mind Tools Toolkit seguindo estes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Mind Tools Toolkit,** selecione **Single sign-on**.

    ![A secção Gestão, com único sinal de inscrição em destaque](common/select-sso.png)

1. Na caixa de diálogo **de método de inscrição única,** selecione **SAML/WS-Fed** para ativar um único sinal de entrada.

    ![Selecione uma única caixa de diálogo de método de inscrição, com SAML realçado](common/select-saml-option.png)

1. Na **configuração single Sign-On com** a página SAML, selecione o ícone de lápis para **configuração SAML básica** para editar as definições.

    ![O set up Single Sign-On com página SAML, com o ícone de lápis para configuração básica SAML em destaque](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** na caixa **URL sign-on,** introduza um URL com o padrão `https://app.goodpractice.net/#/<subscriptionUrl>/s/<locationId>` .

    > [!NOTE]
    > O valor **do URL de inscrição** não é real. Atualize o valor com o URL de inscrição real. Contacte a equipa de suporte do [Cliente do Mind Tools Toolkit](mailto:support@goodpractice.com) para obter o valor.

1. Na **configuração Single Sign-On com** a página SAML, aceda à secção **de Certificado de Assinatura SAML.** À direita do **Metadados da Federação XML,** selecione **Descarregue** para descarregar o texto XML e guarde-o no seu computador. O conteúdo do XML depende das opções selecionadas.

    ![A secção de Certificado de Assinatura SAML, com Download em destaque ao lado do Metadadata XML da Federação](common/metadataxml.png)

1. Na secção **Configurar Conjunto de Ferramentas de Ferramentas mentais,** copie qualquer um dos URLs que necessitar.

    * **Login URL**

    * **Identificador de Azure Ad**

    * **Logout URL**

    ![A secção Configurar ferramentas mentais, com os URLs de configuração em destaque](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, cria-se um utilizador de teste chamado B.Simon no portal Azure:

1. No lado mais à esquerda do portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >  **Users**  >  **Todos os utilizadores**.
1. Na parte superior do ecrã, selecione **Novo utilizador**.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome,** insira **B.Simon**.  
   1. No campo **nome do utilizador,** introduza **B.Simon@**_de empresa_**.** _extensão_. Por exemplo, B.Simon@contoso.com.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor mostrado na caixa **palavra-passe.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que B.Simon utilize o Azure single sign-on, concedendo acesso ao Mind Tools Toolkit.

1. No portal Azure, selecione **Aplicações empresariais**  >  **Todas as aplicações**.
1. Na lista de aplicações, selecione **Mind Tools Toolkit**.
1. Na página geral da aplicação, vá à secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A secção Gerir, com Utilizadores e grupos em destaque](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Na caixa de diálogo **de atribuição de** adicionar, selecione **Utilizadores e grupos**.

   ![A janela dos Utilizadores e grupos, com o Utilizador Adicionar em destaque](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Em seguida, escolha o botão **Selecione** na parte inferior do ecrã.
1. Se espera algum valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, escolha o botão **Selecione** na parte inferior do ecrã.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

## <a name="configure-mind-tools-toolkit-sso"></a>Configurar ferramentas mentais kit de ferramentas SSO

Para configurar um único sinal no lado do **Conjunto de Ferramentas Mind Tools,** envie o texto **XML da Federação** descarregada e os URLs previamente copiados para a equipa de suporte do [Mind Tools Toolkit](mailto:support@goodpractice.com). Configuram esta definição para que a ligação SSO SAML seja corretamente definida em ambos os lados.

### <a name="create-a-mind-tools-toolkit-test-user"></a>Criar um utilizador de teste de ferramentas mentais

Nesta secção, cria-se um utilizador chamado B.Simon in Mind Tools Toolkit.

O Mind Tools Toolkit suporta o provisionamento just-in-time, que é ativado por padrão. Não há ação para tomares nesta secção. Se um utilizador já não existir no Mind Tools Toolkit, é criado um novo quando tenta aceder ao Mind Tools Toolkit.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de Azure AD utilizando o portal My Apps.

Quando selecionar o azulejo do Conjunto de Ferramentas Mind Tools no portal My Apps, é automaticamente inscrito no Conjunto de Ferramentas Mind Tools para o qual configura sSO. Para obter mais informações sobre o portal My Apps, consulte [Introdução ao portal My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para integrar apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o kit de ferramentas de ferramentas mentais com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o Conjunto de Ferramentas de Ferramentas mentais com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
