---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Gestor de Certificados sectigo | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Sectigo Certificate Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: c589db84b6221aa23868b3b49aea84f33623619f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92673872"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Tutorial: Integração do Diretório Ativo Azure com o Gestor de Certificados sectigo

Neste tutorial, aprende-se a integrar o Sectigo Certificate Manager (também chamado SCM) com o Azure Ative Directory (Azure AD).

Integrar o Sectigo Certificate Manager com a Azure AD dá-lhe os seguintes benefícios:

* Você pode usar Azure AD para controlar quem tem acesso ao Sectigo Certificate Manager.
* Os utilizadores podem ser automaticamente inscritos no Sectigo Certificate Manager com as suas contas AD Azure (súb0 único).
* Pode gerir as suas contas num local central, o portal Azure.

Para obter mais informações sobre software como integração de aplicações de serviço (SaaS) com Azure AD, consulte [um único sinal de acesso às aplicações no Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o Sectigo Certificate Manager, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição AD Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Conta de Sectigo Certificate Manager.

> [!NOTE]
> Sectigo executa várias instâncias de Sectigo Certificate Manager. O principal exemplo de Sectigo Certificate Manager é  **https: \/ /cert-manager.com**, e este URL é usado neste tutorial.  Se a sua conta estiver num caso diferente, deve ajustar os URLs em conformidade.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa a Azure AD um único sign-on em um ambiente de teste e integrar Sectigo Certificate Manager com Azure AD.

Sectigo Certificate Manager suporta as seguintes funcionalidades:

* **Sinal único iniciado pela SP**
* **Sinal único iniciado pelo IDP**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Adicionar Sectigo Certificate Manager no portal Azure

Para integrar o Sectigo Certificate Manager com a Azure AD, tem de adicionar o Sectigo Certificate Manager à sua lista de aplicações geridas pelo SaaS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu esquerdo, selecione **Azure Ative Directory**.

    ![A opção Azure Ative Directory](common/select-azuread.png)

1. Selecione **aplicações da Empresa**  >  **Todas as aplicações**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

1. Para adicionar uma aplicação, selecione **Nova aplicação**.

    ![A nova opção de aplicação](common/add-new-app.png)

1. Na caixa de pesquisa, **insira o Gestor de Certificados de Sectigo.** Nos resultados da pesquisa, selecione **Sectigo Certificate Manager** e, em seguida, selecione **Add**.

    ![Sectigo Certificate Manager na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD um único sign-on com Sectigo Certificate Manager com base em um utilizador de teste chamado **Britta Simon**. Para um único sinal de sação para o trabalho, deve estabelecer uma relação ligada entre um utilizador AZure AD e o utilizador relacionado no Sectigo Certificate Manager.

Para configurar e testar o Azure AD com o Sectigo Certificate Manager, deve completar os seguintes blocos de construção:

| Tarefa | Descrição |
| --- | --- |
| **[Configurar Azure AD único sinal de inscrição](#configure-azure-ad-single-sign-on)** | Permite que os seus utilizadores utilizem esta funcionalidade. |
| **[Configure Sectigo Certificate Manager único sign-on](#configure-sectigo-certificate-manager-single-sign-on)** | Configura as definições de inscrição única na aplicação. |
| **[Criar um utilizador de teste AZure AD](#create-an-azure-ad-test-user)** | Testa o Azure AD um único sinal para um utilizador chamado Britta Simon. |
| **[Atribuir o utilizador de teste AZure AD](#assign-the-azure-ad-test-user)** | Permite que Britta Simon utilize um único sinal de Azure. |
| **[Criar um utilizador de teste Sectigo Certificate Manager](#create-a-sectigo-certificate-manager-test-user)** | Cria uma contrapartida de Britta Simon em Sectigo Certificate Manager que está ligada à representação AZure AD do utilizador. |
| **[Testar o início de sessão único](#test-single-sign-on)** | Verifica se a configuração funciona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, configurar o Azure AD com o Sectigo Certificate Manager no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)no painel de integração de aplicações **Sectigo Certificate Manager,** selecione **Single sign-on**.

    ![Configurar a opção de inscrição única](common/select-sso.png)

1. No painel **de método de inscrição,** selecione o modo **SAML** ou **SAML/WS-Fed** para ativar um único sinal de súmido.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

1. Na **configuração single Sign-On com painel SAML,** selecione **Editar** (o ícone do lápis) para abrir o painel **de configuração DE SAML básico.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML** completam os seguintes passos:

    1. Na caixa **identifier (Entity ID),** para a instância principal do Gestor de Certificados de Sectigo, **insira https: \/ /cert-manager.com/shibboleth**.

    1. Na caixa **URL de resposta,** para a instância principal do Gestor de Certificados de Sectigo, **insira https: \/ /cert-manager.com/Shibboleth.sso/SAML2/POST**.
        
    > [!NOTE]
    > Embora, em geral, o **URL de inscrição** seja obrigatório para *o modo iniciado pelo SP,* não é necessário fazer login no Sectigo Certificate Manager.        

1. Opcionalmente, na secção **de Configuração Básica SAML,** para configurar o *modo iniciado pelo IDP* e permitir o trabalho do **Teste,** completar os seguintes passos:

    1. Selecione **Definir URLs adicionais**.

    1. Na caixa **'Retransmissão State',** insira o URL específico do cliente do Sectigo Certificate Manager. Para a instância principal do Gestor de Certificados de Sectigo, **insira https: \/ /cert-manager.com/customer/ \<customerURI\> /idp**.

    ![Sectigo Certificate Manager domínio e URLs informações únicas de inscrição](common/idp-relay.png)

1. Na secção **Atributos & Reclamações** do Utilizador, complete os seguintes passos:

    1. Eliminar todas as **reclamações adicionais.**
    
    1. **Selecione Adicionar nova reclamação** e adicionar as seguintes quatro reclamações:
    
        | Name | Espaço de Nomes | Origem | Atributo de origem | Description |
        | --- | --- | --- | --- | --- |
        | nome eduPersonPrincipal | vazio | Atributo | user.userprincipalname | Deve coincidir com o campo **IdP Person ID** em Sectigo Certificate Manager para Administrações. |
        | correio | vazio | Atributo | user.mail | Necessário |
        | nomeDado | vazio | Atributo | user.givenname | Opcional |
        | sn | vazio | Atributo | utilizador.sobrenome | Opcional |

       ![Sectigo Certificate Manager - Adicione quatro novas reclamações](media/sectigo-certificate-manager-tutorial/additional-claims.png)

1. Na secção **certificado de assinatura SAML,** selecione **Descarregue** ao lado **do Metadados da Federação XML**. Guarde o ficheiro XML no seu computador.

    ![A opção de descarregamento de metadados XML da Federação](common/metadataxml.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Configure Sectigo Certificate Manager único sign-on

Para configurar um único sinal no lado do Gestor de Certificados de Sectigo, envie o ficheiro XML da Federação descarregada para a equipa de suporte do [Sectigo Certificate Manager](https://sectigo.com/support). A equipa de apoio do Sectigo Certificate Manager utiliza as informações que lhes envia para garantir que a ligação de sinalização única SAML seja corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

Nesta secção, cria-se um utilizador de teste chamado Britta Simon no portal Azure.

1. No portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >    >  **Todos os utilizadores**.

    ![As opções dos Utilizadores e de Todos os utilizadores](common/users.png)

1. Selecione **Novo utilizador**.

    ![A nova opção de utilizador](common/new-user.png)

1. No painel de **utilização,** complete os seguintes passos:

    1. Na caixa **Nome,** **insira BrittaSimon**.
  
    1. Na caixa **do nome do utilizador,** **introduza brittasimon \@ \<your-company-domain> . \<extension\>** Por exemplo, **\@ brittasimon contoso.com**.

    1. Selecione a caixa **de verificação de senha show.** Grave o valor que é apresentado na **caixa de palavra-passe.**

    1. Selecione **Criar**.

    ![O painel de utilizador](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você concede a Britta Simon acesso ao Sectigo Certificate Manager para que o utilizador possa usar a Azure um único sinal de inscrição.

1. No portal Azure, selecione **aplicações Enterprise**  >  **Todas as aplicações**  >  **Sectigo Certificate Manager**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

1. Na lista de candidaturas, selecione **Sectigo Certificate Manager**.

    ![Sectigo Certificate Manager na lista de candidaturas](common/all-applications.png)

1. No menu, selecione **Utilizadores e grupos.**

    ![A opção Utilizadores e grupos](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Em seguida, no painel de **atribuição Adicionar,** selecione **Utilizadores e grupos**.

    ![O painel de atribuição adicionar](common/add-assign-user.png)

1. No painel **de Utilizadores e grupos,** selecione **Britta Simon** na lista de utilizadores. Escolha **Selecionar**.

1. Se estiver à espera de um valor de função na afirmação SAML, no painel **de funções Select,** selecione a função relevante para o utilizador da lista. Escolha **Selecionar**.

1. No painel **de atribuição de adição,** selecione **Atribuir**.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Criar um utilizador de teste Sectigo Certificate Manager

Nesta secção, cria-se um utilizador chamado Britta Simon em Sectigo Certificate Manager. Trabalhe com a equipa de apoio do [Sectigo Certificate Manager](https://sectigo.com/support) para adicionar o utilizador na plataforma Sectigo Certificate Manager. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal de inscrição Azure.

#### <a name="test-from-sectigo-certificate-manager-sp-initiated-single-sign-on"></a>Teste do Gestor de Certificados de Sectigo (santil-on único iniciado pelo SP)

Navegue pelo URL específico do cliente (para a instância principal do Gestor de Certificados de Sectigo, https: \/ /cert-manager.com/customer/ \<customerURI\> /, e selecione o botão abaixo **ou inscreva-se com**.  Se configurar corretamente, será automaticamente inscrito no Sectigo Certificate Manager.

#### <a name="test-from-azure-single-sign-on-configuration-idp-initiated-single-sign-on"></a>Teste a partir da configuração de um único sinal de Azure (sinal único iniciado pelo IDP)

No painel de integração da aplicação **Sectigo Certificate Manager,** selecione **single sign-on** e selecione o botão **De teste.**  Se configurar corretamente, será automaticamente inscrito no Sectigo Certificate Manager.

#### <a name="test-by-using-the-my-apps-portal-idp-initiated-single-sign-on"></a>Teste utilizando o portal My Apps (iniciação única iniciada pelo IDP)

Selecione **Sectigo Certificate Manager** no portal My Apps.  Se configurar corretamente, será automaticamente inscrito no Sectigo Certificate Manager. Para obter mais informações sobre o portal My Apps, consulte [o Access e utilize aplicações no portal My Apps.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Para saber mais, reveja estes artigos:

- [Lista de tutoriais para integrar apps saaS com diretório ativo Azure](./tutorial-list.md)
- [Inscrição única para aplicações no Azure Ative Directory](../manage-apps/what-is-single-sign-on.md)
- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)