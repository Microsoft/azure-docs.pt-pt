---
title: 'Tutorial: Integração de Diretório Ativo Azure com Kit de Ferramentas De Ferramentas Mentais [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Mind Tools Toolkit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 65b2979d-9e2f-4530-bc08-546975269ebc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c0c1cab94a6b83ca429fd640759bed8af0ae881
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124866"
---
# <a name="tutorial-azure-active-directory-integration-with-mind-tools-toolkit"></a>Tutorial: Integração de Diretório Ativo Azure com Kit de Ferramentas De Ferramentas Mentais

Neste tutorial, aprende-se a integrar o Kit de Ferramentas De Ferramentas Mentais com o Azure Ative Directory (Azure AD).

Com esta integração, pode:

* Controle em Azure AD que tem acesso a Mind Tools Toolkit.
* Ative que os seus utilizadores sejam automaticamente inscritos no Mind Tools Toolkit (único sinal) com as suas contas Azure AD.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre software como uma integração de aplicações de serviço (SaaS) com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Anúncio Azure com o Mind Tools Toolkit, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição de Toolkit De Ferramentas Mentais com um único sinal (SSO) ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* O Kit de Ferramentas Mentais suporta o SSO iniciado por SP.
* O Mind Tools Toolkit suporta o fornecimento de utilizadores just-in-time.
* Depois de configurar o Kit de Ferramentas de Ferramentas Mentais, pode impor o controlo da sessão. Este controlo protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o acesso condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="add-mind-tools-toolkit-from-the-gallery"></a>Adicione toolkit de ferramentas mentais da galeria

Para configurar a integração do Mind Tools Toolkit no Azure AD, é necessário adicionar o Kit de Ferramentas Mind Tools da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou de escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação mais à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Vá a **Aplicações Empresariais**e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** introduza o Kit de **Ferramentas De Ferramentas Mentais** na caixa de pesquisa.
1. Selecione O Kit de **Ferramentas de Ferramentas mentais** a partir dos resultados da pesquisa e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com o Mind Tools Toolkit utilizando um utilizador de teste chamado **B.Simon**. Para que o início de sessão seja introduzido, deve estabelecer uma relação ligada entre um utilizador da AD Azure e o utilizador relacionado no Mind Tools Toolkit.

Para configurar e testar o único sign-on azure ad com o Mind Tools Toolkit, complete os seguintes blocos de construção:

1. **[Configure O SSO AD Azure](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de Azure AD com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure ferramentas mentais Toolkit SSO](#configure-mind-tools-toolkit-sso)** para configurar as definições de sinal única no lado da aplicação.
    1. Crie um utilizador de teste de Toolkit de **[Ferramentas Mentais](#create-a-mind-tools-toolkit-test-user)** para ter uma contrapartida de B.Simon no Kit de Ferramentas De Ferramentas Mentais. Esta contrapartida está ligada à representação da AD Azure do utilizador.
1. **[Teste sSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, configura o único signo do Azure AD com o Mind Tools Toolkit seguindo estes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **da Mind Tools Toolkit,** selecione **Single sign-on**.

    ![A secção Gerir, com um único sinal em destaque](common/select-sso.png)

1. Na **seleta de uma** única caixa de diálogo de método de início de sinal, selecione **SAML/WS-Fed** para ativar um único sinal.

    ![A Select uma única caixa de diálogo do método de sinalização, com a SAML em destaque](common/select-saml-option.png)

1. Na configuração do single sign-on com a página **SAML,** selecione o ícone do lápis para **configuração Básica sAML** para editar as definições.

    ![Configurar um único sign-on com a página SAML, com o ícone do lápis para configuração Básica sAML em destaque](common/edit-urls.png)

1. Na secção **de Configuração SAML Básica,** na caixa **URL sign-on,** introduza um URL com o padrão `https://app.goodpractice.net/#/<subscriptionUrl>/s/<locationId>` .

    > [!NOTE]
    > O valor do **URL de inscrição** não é real. Atualize o valor com o URL de inscrição real. Contacte a equipa de suporte do [Cliente Mind Tools Toolkit](mailto:support@goodpractice.com) para obter o valor.

1. No **set-up Single Sign-On com** a página SAML, vá à secção de Certificado de **Assinatura SAML.** À direita dos **Metadados da Federação XML,** selecione **Descarregue** o texto XML e guarde-o no seu computador. O conteúdo do XML depende das opções que selecionar.

    ![A secção de Certificado de Assinatura SAML, com Download destacado ao lado de Metadados da Federação XML](common/metadataxml.png)

1. Na secção **'Ferramentas', faça** cópia do que precisar.

    * **Login URL**

    * **Identificador Azure AD**

    * **Logout URL**

    ![A secção 'Configurar ferramentas de ferramentas mentais', com os URLs de configuração em destaque](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, cria-se um utilizador de teste chamado B.Simon no portal Azure:

1. No lado esquerdo do portal Azure, selecione Utilizadores de **Diretório Ativo Azure**  >  **Users**  >  **Todos os utilizadores**.
1. Na parte superior do ecrã, selecione **Novo utilizador**.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome,** insira **B.Simon.**  
   1. No campo de **nome do Utilizador,** introduza o domínio da_empresa_ **B.Simon@****.** _extensão_. Por exemplo, B.Simon@contoso.com.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está mostrado na caixa **password.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que a B.Simon utilize um único sign-on Azure, concedendo acesso ao Mind Tools Toolkit.

1. No portal Azure, selecione **Aplicações Empresariais**  >  **Todas as aplicações**.
1. Na lista de aplicações, selecione **Mind Tools Toolkit**.
1. Na página geral da aplicação, vá à secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A secção Gerir, com Utilizadores e grupos em destaque](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Na caixa de diálogo **Adicionar Atribuição,** selecione **Utilizadores e grupos**.

   ![A janela utilizadores e grupos, com adicionar utilizador em destaque](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Em seguida, escolha o botão **Select** na parte inferior do ecrã.
1. Se esperar algum valor de papel na afirmação do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, escolha o botão **Select** na parte inferior do ecrã.
1. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

## <a name="configure-mind-tools-toolkit-sso"></a>Configure ferramentas mentais Toolkit SSO

Para configurar um único sinal no lado do Kit de Ferramentas De **Ferramentas Mentais,** envie o texto XML de **Metadados** da Federação descarregado e os URLs previamente copiados para a equipa de suporte do [Mind Tools Toolkit](mailto:support@goodpractice.com). Configuram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-a-mind-tools-toolkit-test-user"></a>Criar um utilizador de teste de Toolkit de Ferramentas Mentais

Nesta secção, cria-se um utilizador chamado B.Simon no Mind Tools Toolkit.

O Mind Tools Toolkit suporta o fornecimento just-in-time, que é ativado por padrão. Não há ação para tomar nesta secção. Se um utilizador já não existir no Mind Tools Toolkit, um novo é criado quando tenta aceder ao Kit de Ferramentas Mind Tools.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de Acesso AD Azure utilizando o portal My Apps.

Ao selecionar o azulejo Mind Tools Toolkit no portal My Apps, é automaticamente inscrito no Kit de Ferramentas De Ferramentas Mentais para o qual configura o SSO. Para mais informações sobre o portal My Apps, consulte [introdução ao portal My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para integrar aplicações SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente toolkit de ferramentas mentais com anúncio azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o Kit de Ferramentas de Ferramentas Mentais com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
