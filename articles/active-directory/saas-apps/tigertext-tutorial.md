---
title: 'Tutorial: Integração do Active Directory do Azure com o Messenger de proteger TigerText | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e TigerText Messenger de proteger.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03f1e128-5bcb-4e49-b6a3-fe22eedc6d5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 7507e5d23898fd069797c14d0fa18419b8345d78
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65888258"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Tutorial: Integração do Active Directory do Azure com o Messenger de proteger TigerText

Neste tutorial, saiba como integrar TigerText Messenger de proteger com o Azure Active Directory (Azure AD).

Integrar TigerText Messenger de seguro no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao TigerText Messenger de proteger.
* Pode permitir que os utilizadores iniciar sessão automaticamente TigerText Secure Messenger (início de sessão único) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central: portal do Azure.

Para obter detalhes sobre o software como uma integração de aplicação de serviço (SaaS) com o Azure AD, consulte [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Messenger de proteger TigerText, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma subscrição do Messenger de proteger TigerText com início de sessão único ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste e integrar TigerText Messenger de proteger com o Azure AD.

Messenger de proteger TigerText suporta iniciado por SP início de sessão único (SSO).

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>Adicionar TigerText Messenger de seguro no Azure Marketplace

Para configurar a integração do Messenger de proteger TigerText com o Azure AD, terá de adicionar TigerText Messenger de proteger do Azure Marketplace à sua lista de aplicações de SaaS geridas:

1. Inicie sessão no [portal do Azure](https://portal.azure.com?azure-portal=true).
1. No painel esquerdo, selecione **Azure Active Directory**.

    ![A opção do Azure Active Directory](common/select-azuread.png)

1. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

1. Para adicionar uma nova aplicação, selecione **+ nova aplicação** na parte superior do painel.

    ![A nova opção de aplicação](common/add-new-app.png)

1. Na caixa de pesquisa, introduza **Messenger de proteger TigerText**. Nos resultados da pesquisa, selecione **Messenger de proteger TigerText**e, em seguida, selecione **Add** para adicionar a aplicação.

    ![TigerText Secure Messenger na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com TigerText Secure Messenger com base num utilizador de teste com o nome **Eduarda Almeida**. Para o início de sessão único funcione, tem de estabelecer uma ligação entre um utilizador do Azure AD e o utilizador relacionado no Messenger de proteger TigerText.

Para configurar e testar o Azure AD início de sessão único com o Messenger de proteger TigerText, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  para permitir aos utilizadores utilizar esta funcionalidade.
1. **[Configurar TigerText Messenger de proteger o início de sessão único](#configure-tigertext-secure-messenger-single-sign-on)**  para configurar as definições de início de sessão únicas no lado do aplicativo.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Criar um utilizador de teste do Messenger de proteger TigerText](#create-a-tigertext-secure-messenger-test-user)**  para que haja um utilizador nomeado Eduarda Almeida no Messenger de proteger TigerText que está ligada ao utilizador do Azure AD com o nome Eduarda Almeida.
1. **[Testar início de sessão único](#test-single-sign-on)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Messenger de proteger TigerText, siga os passos seguintes:

1. Na [portal do Azure](https://portal.azure.com/), na **Messenger de proteger TigerText** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a opção de início de sessão único](common/select-sso.png)

1. Sobre o **selecionar um método de início de sessão único** painel, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

1. Na **definir a segurança de início de sessão único com o SAML** painel, selecione **editar** (o ícone de lápis) para abrir o **configuração básica de SAML** painel.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Na **configuração básica de SAML** painel, siga os passos seguintes:

    ![Informações de início de sessão de único TigerText Messenger de proteger o domínio e URLs](common/sp-identifier.png)

    1. Na **iniciar sessão no URL** , introduza um URL:

       `https://home.tigertext.com`

    1. Na **identificador (ID de entidade)** caixa, escreva um URL ao utilizar o seguinte padrão:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > O **identificador (ID de entidade)** valor não é real. Atualize este valor com o identificador real. Para obter o valor, entre em contato com o [equipa de suporte do Messenger de proteger TigerText](mailto:prosupport@tigertext.com). Também pode consultar os padrões mostrados a **configuração básica de SAML** painel no portal do Azure.

1. Na **definir a segurança de início de sessão único com o SAML** painel, na **certificado de assinatura SAML** secção, selecione **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado e guarde-o no seu computador.

    ![A opção de download do XML de metadados de Federação](common/metadataxml.png)

1. Na **configurar Messenger de proteger TigerText** secção, copie o URL ou URLs que tem de:

   * **URL de início de sessão**
   * **Azure AD Identifier**
   * **URL de fim de sessão**

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>Configurar TigerText Messenger de proteger o início de sessão único

Para configurar o início de sessão único no lado do Messenger de proteger TigerText, terá de enviar o XML de metadados de Federação transferidos e copiados apropriado URLs a partir do portal do Azure para o [equipa de suporte do Messenger de proteger TigerText](mailto:prosupport@tigertext.com). A equipe de Messenger de proteger TigerText irá fazer-se de que a ligação de SAML SSO está definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, vai criar um utilizador de teste com o nome Eduarda Almeida no portal do Azure.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**   > **utilizadores** > **todos os utilizadores**.

    ![Os utilizadores e de "Todos os utilizadores" Opções](common/users.png)

1. Na parte superior do ecrã, selecione **+ novo utilizador**.

    ![Nova opção de utilizador](common/new-user.png)

1. Na **utilizador** painel, siga os passos abaixo:

    ![O painel do utilizador](common/user-properties.png)

    1. Na **Name** , introduza **BrittaSimon**.
  
    1. Na **nome de utilizador** , introduza **BrittaSimon\@\<yourcompanydomain >.\< extensão >**. Por exemplo, **BrittaSimon\@contoso.com**.

    1. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso TigerText Messenger de proteger.

1. No portal do Azure, selecione **aplicações empresariais** > **todas as aplicações** > **Messenger de proteger TigerText**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

1. Na lista de aplicações, selecione **Messenger de proteger TigerText**.

    ![TigerText Secure Messenger na lista de aplicações](common/all-applications.png)

1. No painel esquerdo, sob **MANAGE**, selecione **utilizadores e grupos**.

    ![A opção "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **+ adicionar utilizador**e, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** painel.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

1. No **utilizadores e grupos** painel, selecione **Eduarda Almeida** no **utilizadores** lista e, em seguida, escolha **selecione** na parte inferior do painel.

1. Se estiver à espera de um valor de função na asserção de SAML, em seguida, no **selecionar função** painel, selecione a função adequada para o utilizador a partir da lista. Na parte inferior do painel, escolha **selecione**.

1. Na **adicionar atribuição** painel, selecione **atribuir**.

### <a name="create-a-tigertext-secure-messenger-test-user"></a>Criar um utilizador de teste do Messenger de proteger TigerText

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Messenger de proteger TigerText. Trabalhar com o [equipa de suporte do Messenger de proteger TigerText](mailto:prosupport@tigertext.com) adicionar Eduarda Almeida como um utilizador no Messenger de proteger TigerText. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar início de sessão único

Nesta secção, vai testar seu única início de sessão em configuração do Azure AD com o portal as minhas aplicações.

Quando seleciona **Messenger de proteger TigerText** no portal as minhas aplicações, deve estar automaticamente conectado à subscrição TigerText Messenger de seguro para o qual configura o início de sessão único. Para obter mais informações sobre o portal as minhas aplicações, consulte [aplicações de acesso e a utilização no portal minhas aplicações](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais de integração de aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

* [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)