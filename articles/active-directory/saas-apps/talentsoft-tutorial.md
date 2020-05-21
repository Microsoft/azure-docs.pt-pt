---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com a Talentsoft [ Integração de Um Único Sign-On) com a Talentsoft [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Talentsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9807bf77-fd2a-41fe-86d0-d746a8736b2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f1384d23f77407d2d20066cc2fa086037d546f6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648467"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-talentsoft"></a>Tutorial: Azure Ative Directory integração individual (SSO) com a Talentsoft

Neste tutorial, aprenderá a integrar a Talentsoft com o Azure Ative Directory (Azure AD). Quando integrar a Talentsoft com o Azure AD, pode:

* Controlo em Azure AD que tem acesso à Talentsoft.
* Permita que os seus utilizadores sejam automaticamente inscritos na Talentsoft com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada pela Talentsoft single sign-on (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Talentsoft suporta **SP e IDP** iniciadoS SSO

## <a name="adding-talentsoft-from-the-gallery"></a>Adicionar Talentsoft da galeria

Para configurar a integração da Talentsoft em Azure AD, precisa de adicionar a Talentsoft da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Talentsoft** na caixa de pesquisa.
1. Selecione **Talentsoft** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-talentsoft"></a>Configure e teste Azure AD single sign-on para Talentsoft

Configure e teste Azure AD SSO com Talentsoft usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Talentsoft.

Para configurar e testar o Azure AD SSO com a Talentsoft, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Talentsoft SSO](#configure-talentsoft-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Create Talentsoft test user](#create-talentsoft-test-user)** - para ter uma contrapartida de B.Simon na Talentsoft que está ligada à representação azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Talentsoft,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<fedserver>/<tenant>/trust`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<fedserver>/<tenant>/saml20`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<tenant>.talentsoft.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta real e URL de sinalização. Contacte a equipa de suporte do [Talentsoft Client](mailto:advancedservices@talentsoft.com) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **Configurar talentsoft,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de **nome do Utilizador,** introduza o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso à Talentsoft.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Talentsoft**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-talentsoft-sso"></a>Configure Talentsoft SSO

Para configurar um único sign-on no lado **talentsoft,** você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipe de [suporte Talentsoft](mailto:advancedservices@talentsoft.com). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-talentsoft-test-user"></a>Criar o utilizador de teste Talentsoft

Nesta secção, cria-se um utilizador chamado B.Simon na Talentsoft. Trabalhe com a equipa de [suporte talentsoft](mailto:advancedservices@talentsoft.com) para adicionar os utilizadores na plataforma Talentsoft. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Talentsoft no Painel de Acesso, deve ser automaticamente inscrito no Talentsoft para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente talentsoft com Azure AD](https://aad.portal.azure.com/)