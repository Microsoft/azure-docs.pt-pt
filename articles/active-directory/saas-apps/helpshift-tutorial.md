---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Helpshift Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Helpshift.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.openlocfilehash: 41dd61182674d63e7c8b83232c53b9e7054de04d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91816850"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-helpshift"></a>Tutorial: Azure Ative Directory integração única (SSO) com Helpshift

Neste tutorial, você vai aprender a integrar Helpshift com Azure Ative Directory (Azure AD). Quando integrar o Helpshift com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Helpshift.
* Ative os seus utilizadores a serem automaticamente inscritos para ajudar a fazer o helpshift com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Inscrição única de ajuda (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Ajuda a apoiar **SP e IDP** iniciado SSO

## <a name="adding-helpshift-from-the-gallery"></a>Adicionar Helpshift da galeria

Para configurar a integração do Helpshift no AZure AD, é necessário adicionar Helpshift da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **escreva Helpshift** na caixa de pesquisa.
1. Selecione **Helpshift** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-helpshift"></a>Configurar e testar Azure AD único sinal de inscrição para helpshift

Configure e teste Azure AD SSO com Helpshift usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Helpshift.

Para configurar e testar o Azure AD SSO com ajuda, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Helpshift SSO](#configure-helpshift-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Helpshift test user](#create-helpshift-test-user)** - para ter uma contraparte de B.Simon em Helpshift que está ligada à representação AD AZure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Helpshift,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<YourDOMAIN>.helpshift.com/`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<YourDOMAIN>.helpshift.com/login/saml/acs/`

1. Clique **em Definir URLs adicionais** e execute os seguintes passos se desejar configurar a aplicação no modo iniciado **sp:**

    d. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<YourDOMAIN>.helpshift.com/login/saml/idp-login/`

    e. Na caixa de texto **do Estado de retransmissão,** digite um URL utilizando o seguinte padrão: `https://<YourDOMAIN>.helpshift.com/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta, URL de inscrição e Estado de retransmissão. Contacte [a equipa de suporte do Cliente Helpshift](mailto:support@helpshift.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **'Configurar' Ajuda,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Helpshift.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Helpshift**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-helpshift-sso"></a>Configurar ajudashift SSO

1. Num navegador web diferente, inscreva-se na sua aplicação Helpshift como administrador.

1. Abra o painel **de instrumentos de ajuda** e clique no ícone **Definições**.

    ![A screenshot mostra o ícone De Definições De Ajuda.](./media/helpshift-tutorial/configuration01.png)

1. Clique no separador **Integrações** e execute os seguintes passos:

    ![A screenshot mostra o separador Integração onde pode executar os passos descritos.](./media/helpshift-tutorial/configuration02.png)

    a. Ligue o **Sign-On Único (SAML – SSO)**.

    b. Selecione **Fornecedor de Identidade (IDP)** como **Diretório Ativo Azure**.

    c. Na caixa de texto **URL DE PONTO FINAL SAML 2.0,** cole o valor URL de **login,** que copiou a partir do portal Azure.

    d. Abrir o ficheiro **certificado (Base64)** no Bloco de Notas, copiar o conteúdo do ficheiro (sem utilizar as linhas '- ---BEGIN CERTIFICATE --- - -- -- -- '- '- '- '- '' e colar-o na caixa de texto **do Certificado X.509.**

    e. Na caixa de texto URL do **emitente,** cole o valor do **identificador Azure AD,** que copiou a partir do portal Azure.

    f. Clique em **APPLY ALTERAÇÕES**.

### <a name="create-helpshift-test-user"></a>Criar utilizador de teste de ajuda

Nesta secção, cria-se um utilizador chamado B.Simon in Helpshift. Trabalhe com [a equipa de suporte do Cliente Helpshift](mailto:support@helpshift.com) para adicionar os utilizadores na plataforma Helpshift. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo helpshift no Painel de Acesso, deverá ser automaticamente inscrito no Helpshift para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Helpshift com a Azure AD](https://aad.portal.azure.com/)