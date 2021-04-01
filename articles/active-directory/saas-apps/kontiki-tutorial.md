---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Kontiki | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Kontiki.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 9e0b438e0cfa729300099463086248780c69484c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95019845"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Tutorial: Integração do Diretório Ativo Azure com a Kontiki

Neste tutorial, você aprende a integrar Kontiki com Azure Ative Directory (Azure AD).

Integrar a Kontiki com a Azure AD dá-lhe os seguintes benefícios:

* Podes usar a Azure AD para controlar quem tem acesso ao Kontiki.
* Os utilizadores podem ser automaticamente inscritos na Kontiki com as suas contas AD Azure (súm numa única sação).
* Pode gerir as suas contas num local central, o portal Azure.

Para obter mais informações sobre software como integração de aplicações de serviço (SaaS) com Azure AD, consulte [um único sinal de acesso às aplicações no Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração azure AD com a Kontiki, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição AD Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma assinatura Kontiki com um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa a Azure AD um único sign-on em um ambiente de teste e integrar Kontiki com Azure AD.

Kontiki suporta as seguintes características:

* **Sinal único iniciado pela SP**
* **Provisão de utilizadores just-in-time**

## <a name="add-kontiki-in-the-azure-portal"></a>Adicione Kontiki no portal Azure

Para integrar a Kontiki com o Azure AD, tem de adicionar a Kontiki à sua lista de aplicações geridas pelo SaaS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu esquerdo, selecione **Azure Ative Directory**.

    ![A opção Azure Ative Directory](common/select-azuread.png)

1. Selecione **aplicações da Empresa**  >  **Todas as aplicações**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

1. Para adicionar uma aplicação, selecione **Nova aplicação**.

    ![A nova opção de aplicação](common/add-new-app.png)

1. Na caixa de pesquisa, insira **Kontiki.** Nos resultados da pesquisa, selecione **Kontiki** e, em seguida, **selecione Add**.

    ![Kontiki na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Kontiki com base em um utilizador de teste chamado **Britta Simon**. Para um único sinal de sação a funcionar, deve estabelecer uma relação ligada entre um utilizador AZure AD e o utilizador relacionado em Kontiki.

Para configurar e testar o Azure AD com kontiki, deve completar os seguintes blocos de construção:

| Tarefa | Descrição |
| --- | --- |
| **[Configurar Azure AD único sinal de inscrição](#configure-azure-ad-single-sign-on)** | Permite que os seus utilizadores utilizem esta funcionalidade. |
| **[Configurar kontiki único sign-on](#configure-kontiki-single-sign-on)** | Configura as definições de inscrição única na aplicação. |
| **[Criar um utilizador de teste AZure AD](#create-an-azure-ad-test-user)** | Testa o Azure AD um único sinal para um utilizador chamado Britta Simon. |
| **[Atribuir o utilizador de teste AZure AD](#assign-the-azure-ad-test-user)** | Permite que Britta Simon utilize um único sinal de Azure. |
| **[Criar um utilizador de teste Kontiki](#create-a-kontiki-test-user)** | Cria uma contrapartida de Britta Simon em Kontiki que está ligada à representação AZure AD do utilizador. |
| **[Testar o início de sessão único](#test-single-sign-on)** | Verifica se a configuração funciona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você configura Azure AD único sign-on com Kontiki no portal Azure.

1. No [portal Azure](https://portal.azure.com/), no painel de integração de aplicações **Kontiki,** selecione **Single sign-on**.

    ![Configurar a opção de inscrição única](common/select-sso.png)

1. No painel **de método de inscrição,** selecione o modo **SAML** ou **SAML/WS-Fed** para ativar um único sinal de súmido.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

1. Na **configuração single Sign-On com painel SAML,** selecione **Editar** (o ícone do lápis) para abrir o painel **de configuração DE SAML básico.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. No painel **de configuração básico do SAML,** na caixa de texto **URL,** introduza um URL com o seguinte padrão: `https://<companyname>.mc.eval.kontiki.com`

    ![Kontiki Domain e URLs informações únicas de assinatura](common/sp-signonurl.png)

    > [!NOTE]
    > Contacte a [equipa de suporte do Cliente Kontiki](https://kollective.com/support/) para obter o valor correto de usar. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração single Sign-On com painel SAML,** na secção certificado de assinatura **SAML,** selecione **Descarregue** ao lado **do Metadadata da Federação XML**. Selecione uma opção de descarregamento com base nos seus requisitos. Guarde o certificado no seu computador.

    ![Opção de descarregamento de certificado de metadados XML da Federação](common/metadataxml.png)

1. Na secção **Configurar Kontiki,** copie os seguintes URLs com base nos seus requisitos:

    * URL de Inicio de Sessão
    * Identificador de Azure Ad
    * Logout URL

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Configurar kontiki único sign-on

Para configurar um único sign-on no lado Kontiki, envie o ficheiro XML da Federação descarregada e os URLs relevantes que copiou do portal Azure para a equipa de [suporte kontiki.](https://kollective.com/support/) A equipa de apoio kontiki utiliza as informações que envia para garantir que a ligação de sinalização única SAML seja corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

Nesta secção, cria-se um utilizador de teste chamado Britta Simon no portal Azure.

1. No portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >    >  **Todos os utilizadores**.

    ![As opções dos Utilizadores e de Todos os utilizadores](common/users.png)

1. Selecione **Novo utilizador**.

    ![A nova opção de utilizador](common/new-user.png)

1. No painel de **utilização,** complete os seguintes passos:

    1. Na caixa **Nome,** **insira BrittaSimon**.
  
    1. Na caixa **do nome do utilizador,** **introduza brittasimon \@ \<your-company-domain> . \<extension>** Por exemplo, **\@ brittasimon contoso.com**.

    1. Selecione a caixa **de verificação de senha show.** Anota o valor que é apresentado na **caixa de palavra-passe.**

    1. Selecione **Criar**.

    ![O painel de utilizador](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, concedes à Britta Simon acesso a Kontiki para que ela possa usar o Azure single sign-on.

1. No portal Azure, selecione **aplicações Enterprise**  >  **Todas as aplicações**  >  **Kontiki**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

1. Na lista de candidaturas, selecione **Kontiki**.

    ![Kontiki na lista de candidaturas](common/all-applications.png)

1. No menu, selecione **Utilizadores e grupos.**

    ![A opção Utilizadores e grupos](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Em seguida, no painel de **atribuição Adicionar,** selecione **Utilizadores e grupos**.

    ![O painel de atribuição adicionar](common/add-assign-user.png)

1. No painel **de Utilizadores e grupos,** selecione **Britta Simon** na lista de utilizadores. Escolha **Selecionar**.

1. Se estiver à espera de um valor de função na afirmação SAML, no painel **de funções Select,** selecione a função relevante para o utilizador da lista. Escolha **Selecionar**.

1. No painel **de atribuição de adição,** selecione **Atribuir**.

### <a name="create-a-kontiki-test-user"></a>Criar um utilizador de teste Kontiki

Não há nenhum item de ação para configurar o fornecimento de utilizadores em Kontiki. Quando um utilizador designado tenta iniciar sessão no Kontiki utilizando o portal My Apps, a Kontiki verifica se o utilizador existe. Se não for encontrada nenhuma conta de utilizador, a Kontiki cria automaticamente a conta de utilizador.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal de Azure AD utilizando o portal My Apps.

Depois de configurar um único sôs-on, quando selecionar **Kontiki** no portal My Apps, você é automaticamente inscrito na Kontiki. Para obter mais informações sobre o portal My Apps, consulte [o Access e utilize aplicações no portal My Apps.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Para saber mais, reveja estes artigos:

- [Lista de tutoriais para integrar apps saaS com diretório ativo Azure](./tutorial-list.md)
- [Inscrição única para aplicações no Azure Ative Directory](../manage-apps/what-is-single-sign-on.md)
- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)