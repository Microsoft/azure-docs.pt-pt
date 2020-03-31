---
title: 'Tutorial: Integração do Diretório Ativo Azure com CPQSync por Cincom Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o CPQSync por Cincom.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d9411095-859c-4223-8d0b-36d1f89b04d5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fc9cf236beab78d666f634489a205828050fae4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "68879563"
---
# <a name="tutorial-integrate-cpqsync-by-cincom-with-azure-active-directory"></a>Tutorial: Integrar cpQSync por Cincom com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar cpQSync por Cincom com Azure Ative Directory (Azure AD). Quando integrar o CPQSync por Cincom com a Azure AD, pode:

* Controlo em Azure AD que tem acesso ao CPQSync por Cincom.
* Ative que os seus utilizadores sejam automaticamente inscritos no CPQSync pela Cincom com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* CpQSync by Cincom single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* CPQSync by Cincom suporta **SP e IDP** iniciadoS SSO

## <a name="adding-cpqsync-by-cincom-from-the-gallery"></a>Adicionar CPQSync por Cincom da galeria

Para configurar a integração do CPQSync por Cincom no Azure AD, é necessário adicionar CPQSync by Cincom da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **CPQSync por Cincom** na caixa de pesquisa.
1. Selecione **CPQSync por Cincom** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cpqsync-by-cincom"></a>Configure e teste Azure AD single sign-on para CPQSync by Cincom

Configure e teste Azure AD SSO com CPQSync by Cincom utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no CPQSync pelo Cincom.

Para configurar e testar o Azure AD SSO com CPQSync by Cincom, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
2. **[Configure o CPQSync por Cincom SSO](#configure-cpqsync-by-cincom-sso)** - para configurar as definições de Início de Início único no lado da aplicação.
    1. **[Crie cpQSync por utilizador](#create-cpqsync-by-cincom-test-user)** de teste da Cincom - para ter uma contraparte de B.Simon no CPQSync por Cincom que esteja ligada à representação do utilizador da AD Azure.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **CPQSync by Cincom,** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://cincom.oktapreview.com/sso/saml2/<CUSTOMURL>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://cincom.okta.com/sso/saml2/<CUSTOMDOMAIN>`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL:`https://cincom.okta.com/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte o [CPQSync pela equipa](https://cpqsupport.cincomcpq.com) de suporte ao Cliente da Cincom para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

4. Na configuração de um único sign-on com a página **SAML,** na secção certificado de **assinatura SAML,** encontre **certificado (Cru)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificateraw.png)

6. Na secção **Configurar cpQSync por Cincom,** copie os URL(s) adequados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
    1. No campo **Nome**, introduza `B.Simon`.  
    1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
    1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
    1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso ao CPQSync pelo Cincom.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **CPQSync por Cincom**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-cpqsync-by-cincom-sso"></a>Configure CPQSync por Cincom SSO

Para configurar um único sinal no **CPQSync por** lado do Cincom, é necessário enviar o Certificado descarregado **(Raw)** e URLs copiados apropriados do portal Azure para [CPQSync pela equipa de suporte do Cincom.](https://cpqsupport.cincomcpq.com) Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-cpqsync-by-cincom-test-user"></a>Criar CPQSync por utilizador de teste cincom

Nesta secção, cria-se um utilizador chamado B.Simon no CPQSync pelo Cincom. Trabalhe com [cpQSync por equipa](https://cpqsupport.cincomcpq.com) de suporte do Cincom para adicionar os utilizadores na plataforma CPQSync by Cincom. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo CPQSync por Cincom no Painel de Acesso, deve ser automaticamente inscrito no CPQSync by Cincom para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

