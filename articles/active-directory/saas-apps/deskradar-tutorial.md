---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Deskradar Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Deskradar.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: d58bb5ddf78a23463e908977c41501078cd84b56
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92454801"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-deskradar"></a>Tutorial: Azure Ative Directory integração única (SSO) com Deskradar

Neste tutorial, você vai aprender a integrar Deskradar com Azure Ative Directory (Azure AD). Quando integrar deskradar com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Deskradar.
* Ative os seus utilizadores a serem automaticamente inscritos no Deskradar com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura única ativada do Deskradar (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.



* Deskradar apoia **SP e IDP** iniciado SSO



## <a name="adding-deskradar-from-the-gallery"></a>Adicionar Deskradar da galeria

Para configurar a integração do Deskradar no AD Azure, é necessário adicionar o Deskradar da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** escreva **Deskradar** na caixa de pesquisa.
1. Selecione **Deskradar** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-deskradar"></a>Configurar e testar Azure AD único sign-on para Deskradar

Configure e teste Azure AD SSO com Deskradar usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Deskradar.

Para configurar e testar o Azure AD SSO com o Deskradar, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure deskradar SSO](#configure-deskradar-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Deskradar test user](#create-deskradar-test-user)** - para ter uma contraparte de B.Simon em Deskradar que está ligada à representação AD AZure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Deskradar,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<YOURDOMAIN>.deskradar.cloud`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<YOURDOMAIN>.deskradar.cloud/auth/sso/saml/consume`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<YOURDOMAIN>.deskradar.cloud/auth/sso/saml/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Substitua **o YOURDOMAIN** pelo domínio do seu deskradar. Contacte [a equipa de suporte do Cliente Deskradar](mailto:support@deskradar.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação Deskradar espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação Deskradar espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com o seu requisito.

    | Name | Atributo de origem|
    | ---------------| --------- |
    | FirstName | user.givenname |
    | LastName | utilizador.sobrenome |
    | E-mail | user.userprincipalname |
    | | |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Deskradar,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Deskradar.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Deskradar**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="configure-deskradar-sso"></a>Configure Deskradar SSO

1. Para automatizar a configuração dentro do Deskradar, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique no **Deskradar de configuração** irá direcioná-lo para a aplicação Deskradar. A partir daí, forneça as credenciais de administrador para assinar no Deskradar. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar o Deskradar manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Deskradar como administrador e execute os seguintes passos:

1. Abra o painel **da equipa** clicando no ícone na barra lateral.

1. Mude para **separador autenticação.**

1. No separador **SAML 2.0,** execute os seguintes passos:

    ![Screenshot que mostra o separador "S A M L 2.0" com setas apontando para os campos "S A M L S S D U R L" e "Identifier Provider Issuer".](./media/deskradar-tutorial/14-paste-urls.jpg)

    a. Ativar o método de autenticação **SAML.**

    b. Na caixa de texto **URL SSO SAML,** insira o valor **URL de login,** que copiou a partir do portal Azure.

    c. Na caixa de texto do **Fornecedor de Identidade,** insira o valor **do identificador Azure AD,** que copiou a partir do portal Azure.

1. Abra o ficheiro Certificado descarregado **(Base64)** com um editor de texto e copie e cole o seu conteúdo no campo **de Certificado Público** em Deskradar.

    ![Configuração deskradar](./media/deskradar-tutorial/15-paste-certificate.jpg)

### <a name="create-deskradar-test-user"></a>Criar utilizador de teste Deskradar

Nesta secção, cria-se um utilizador chamado B.Simon in Deskradar. Trabalhe com a [equipa de suporte do Cliente Deskradar](mailto:support@deskradar.com) para adicionar os utilizadores na plataforma Deskradar. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo deskradar no Painel de Acesso, deverá ser automaticamente inscrito no Deskradar para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente deskradar com Azure AD](https://aad.portal.azure.com/)