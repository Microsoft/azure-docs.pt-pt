---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Proto.io | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Proto.io.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/12/2020
ms.author: jeedes
ms.openlocfilehash: cdd712495d09f45054d046693833a7f6efee2c6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516567"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-protoio"></a>Tutorial: Azure Ative Directory integração única (SSO) com Proto.io

Neste tutorial, você vai aprender a integrar Proto.io com Azure Ative Direy (Azure AD). Quando integra Proto.io com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Proto.io.
* Ative os seus utilizadores a serem automaticamente inscritos para Proto.io com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Proto.io subscrição ativada por um único sso (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Proto.io apoia SP **e IDP** inicia SSO

* Uma vez configurados Proto.io pode impor o controlo da sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-protoio-from-the-gallery"></a>Adicionar Proto.io da galeria

Para configurar a integração de Proto.io no AD Azure, é necessário adicionar Proto.io da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva **Proto.io** na caixa de pesquisa.
1. Selecione **Proto.io** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-protoio"></a>Configurar e testar a Azure AD um único sinal de Proto.io

Configure e teste Azure AD SSO com Proto.io usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Proto.io.

Para configurar e testar o Azure AD SSO com Proto.io, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Proto.io SSO](#configure-protoio-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Crie Proto.io utilizador de teste](#create-protoio-test-user)** - para ter uma contraparte de B.Simon em Proto.io que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Proto.io,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://proto.io/saml/sp/<PROTO-SAML-ACCOUNT-ID>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://saml.proto.io/login/callback?code=<PROTO-SAML-ACCOUNT-ID>`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<PROTO-SAML-ACCOUNT-ID>.proto.io`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [Proto.io equipa de apoio ao Cliente](mailto:support@proto.io) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Proto.io aplicação espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, Proto.io aplicação espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Name | Atributo de origem|
    | --------------- | --------- |
    | e-mail | user.mail |
    | fname | user.givenname |
    | Nome | utilizador.sobrenome |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configuração Proto.io,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, permitindo o acesso a Proto.io.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Proto.io**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-protoio-sso"></a>Configurar Proto.io SSO

Para configurar um único sign-on **Proto.io** lado, você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para [Proto.io equipa de suporte](mailto:support@proto.io). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-protoio-test-user"></a>Criar Proto.io utilizador de teste

Nesta secção, cria-se um utilizador chamado Britta Simon em Proto.io. Trabalhe com [Proto.io equipa de suporte](mailto:support@proto.io) para adicionar os utilizadores na plataforma Proto.io. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Proto.io no Painel de Acesso, deverá ser automaticamente inscrito no Proto.io para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente Proto.io com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger Proto.io com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)