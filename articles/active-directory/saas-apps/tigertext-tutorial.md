---
title: 'Tutorial: Integração do Diretório Ativo Azure com tigerConnect Secure Messenger | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o TigerConnect Secure Messenger.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/21/2020
ms.author: jeedes
ms.openlocfilehash: 6831e96ebd8fd2db51d640ff3d93955b1a9f1477
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182160"
---
# <a name="tutorial-azure-active-directory-integration-with-tigerconnect-secure-messenger"></a>Tutorial: Integração do Diretório Ativo Azure com TigerConnect Secure Messenger

Neste tutorial, você aprende a integrar TigerConnect Secure Messenger com Azure Ative Directory (Azure AD).

A integração do TigerConnect Secure Messenger com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso ao TigerConnect Secure Messenger.
* Pode permitir que os seus utilizadores se inscrevam automaticamente no TigerConnect Secure Messenger (único sún) com as suas contas AD Azure.
* Pode gerir as suas contas num local central: o portal Azure.

Para mais detalhes sobre o software como uma integração de aplicações de serviço (SaaS) com a Azure AD, consulte [o que é o acesso à aplicação e um único acesso com o Azure Ative Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o TigerConnect Secure Messenger, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma subscrição tigerConnect Secure Messenger com um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa a Azure AD um único sinal de acesso em um ambiente de teste e integrar TigerConnect Secure Messenger com Azure AD.

* TigerConnect Secure Messenger suporta **SSO** iniciado SP
* Assim que configurar o TigerConnect Secure Messenger, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-tigerconnect-secure-messenger-from-the-gallery"></a>Adicionando TigerConnect Secure Messenger da galeria

Para configurar a integração do TigerConnect Secure Messenger em Azure AD, precisa adicionar TigerConnect Secure Messenger da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite TigerConnect Secure Messenger** na caixa de pesquisa.
1. Selecione **TigerConnect Secure Messenger** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar Azure AD SSO

Nesta secção, configura e testa o Azure AD com o TigerConnect Secure Messenger com base num utilizador de teste chamado **Britta Simon**. Para um único sinal de sação a funcionar, deve estabelecer uma ligação entre um utilizador Azure AD e o utilizador relacionado no TigerConnect Secure Messenger.

Para configurar e testar um único sinal de Azure AD com o TigerConnect Secure Messenger, tem de completar os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste AZure AD](#create-an-azure-ad-test-user)** para testar o Azure AD com Britta Simon.
    * **[Atribua ao utilizador de teste AZure AD](#assign-the-azure-ad-test-user)** para permitir que a Britta Simon utilize um único sinal de Ad AD.
1. **[Configure o TigerConnect Secure Messenger SSO](#configure-tigerconnect-secure-messenger-sso)** para configurar as definições de inscrição única no lado da aplicação.
    * **[Crie um utilizador de teste TigerConnect Secure Messenger](#create-a-tigerconnect-secure-messenger-test-user)** para que haja um utilizador chamado Britta Simon em TigerConnect Secure Messenger que está ligado ao utilizador Azure AD chamado Britta Simon.
1. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com TigerConnect Secure Messenger, tome os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **TigerConnect Secure Messenger,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    1. Na **caixa de sinalização na** url, introduza um URL:

       `https://home.tigertext.com`

    1. Na caixa **identifier (Entity ID),** digite um URL utilizando o seguinte padrão:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > O valor **do Identificador (ID da Entidade)** não é real. Atualize este valor com o identificador real. Para obter o valor, contacte a equipa de [suporte tigerConnect Secure Messenger](mailto:prosupport@tigertext.com). Também pode consultar os padrões indicados no painel **de configuração SAML básico** no portal Azure.

1. Na **configuração single Sign-On com painel SAML,** na secção Certificado de Assinatura **SAML,** selecione **Descarregamento** para descarregar o **Metdata XML** da Federação das opções dadas e guarde-o no seu computador.

    ![A opção de descarregamento de metadados XML da Federação](common/metadataxml.png)

1. Na secção **Configurar TigerConnect Secure Messenger,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao TigerConnect Secure Messenger.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, **selecione TigerConnect Secure Messenger**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-tigerconnect-secure-messenger-sso"></a>Configurar TigerConnect Secure Messenger SSO

Para configurar um único sinal no lado tigerConnect Secure Messenger, é necessário enviar os Metadados XML da Federação descarregados e os URLs copiados apropriados do portal Azure para a equipa de [suporte TigerConnect Secure Messenger](mailto:prosupport@tigertext.com). A equipa TigerConnect Secure Messenger certificar-se-á de que a ligação SSO SAML está corretamente definida em ambos os lados.

## <a name="create-a-tigerconnect-secure-messenger-test-user"></a>Criar um utilizador de teste TigerConnect Secure Messenger

Nesta secção, cria-se um utilizador chamado Britta Simon em TigerConnect Secure Messenger. Trabalhe com a [equipa de suporte TigerConnect Secure Messenger](mailto:prosupport@tigertext.com) para adicionar Britta Simon como utilizador no TigerConnect Secure Messenger. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de Azure AD utilizando o portal My Apps.

Quando selecionar **TigerConnect Secure Messenger** no portal My Apps, deverá ser automaticamente inscrito na subscrição TigerConnect Secure Messenger para a qual configura um único s-on. Para obter mais informações sobre o portal My Apps, consulte [o Access e utilize aplicações no portal My Apps.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais para integrar apps saaS com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)

- [Experimente TigerConnect Secure Messenger com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)