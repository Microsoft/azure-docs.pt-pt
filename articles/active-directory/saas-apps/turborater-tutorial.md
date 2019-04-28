---
title: 'Tutorial: Integração do Active Directory do Azure com TurboRater | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e TurboRater.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: abb116b8-8024-4cc6-bc81-f32ef490ea17
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/8/2019
ms.author: jeedes
ms.openlocfilehash: 0c279a224a7692a64621e24cc8fe2213b78b54ce
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62735623"
---
# <a name="tutorial-azure-active-directory-integration-with-turborater"></a>Tutorial: Integração do Active Directory do Azure com TurboRater

Neste tutorial, saiba como integrar TurboRater com o Azure Active Directory (Azure AD).

Integrar TurboRater no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao TurboRater.
* Pode permitir que os utilizadores ter automaticamente sessão iniciada no TurboRater (início de sessão único) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central: portal do Azure.

Para obter detalhes sobre o software como uma integração de aplicação de serviço (SaaS) com o Azure AD, consulte [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com TurboRater, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma subscrição de TurboRater com início de sessão único ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

Suporta TurboRater IDP iniciada pelo início de sessão único (SSO).

## <a name="add-turborater-from-the-azure-marketplace"></a>Adicionar TurboRater no Azure Marketplace

Para configurar a integração do TurboRater com o Azure AD, terá de adicionar TurboRater no Azure Marketplace à sua lista de aplicações de SaaS geridas:

1. Inicie sessão no [portal do Azure](https://portal.azure.com?azure-portal=true).
1. No painel esquerdo, selecione **Azure Active Directory**.

    ![A opção do Azure Active Directory](common/select-azuread.png)

1. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![A opção de aplicativos empresariais](common/enterprise-applications.png)

1. Para adicionar uma nova aplicação, selecione **+ nova aplicação** na parte superior do painel.

    ![A nova opção de aplicação](common/add-new-app.png)

1. Na caixa de pesquisa, introduza **TurboRater**. Nos resultados da pesquisa, selecione **TurboRater**e, em seguida, selecione **Add** para adicionar a aplicação.

    ![TurboRater na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com TurboRater com base num utilizador de teste com o nome **Eduarda Almeida**. Para o início de sessão único funcione, tem de estabelecer uma ligação entre um utilizador do Azure AD e o utilizador relacionado TurboRater.

Para configurar e testar o Azure AD início de sessão único com TurboRater, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  para permitir aos utilizadores utilizar esta funcionalidade.
1. **[Configurar TurboRater início de sessão único](#configure-turborater-single-sign-on)**  para configurar as definições de início de sessão únicas no lado do aplicativo.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Criar um utilizador de teste TurboRater](#create-a-turborater-test-user)**  para que haja um utilizador nomeado Eduarda Almeida na TurboRater que está ligada ao utilizador do Azure AD com o nome Eduarda Almeida.
1. **[Testar início de sessão único](#test-single-sign-on)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com TurboRater, siga os passos seguintes:

1. Na [portal do Azure](https://portal.azure.com/), na **TurboRater** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a opção de início de sessão único](common/select-sso.png)

1. Sobre o **selecionar um método de início de sessão único** painel, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

1. Na **definir a segurança de início de sessão único com o SAML** página, selecione **editar** (o ícone de lápis) para abrir o **configuração básica de SAML** painel.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Na **configuração básica de SAML** painel, siga os passos abaixo:

    ![Informações de início de sessão de único TurboRater domínio e URLs](common/idp-intiated.png)

    1. Na **identificador (ID de entidade)** , introduza um URL:

       `https://www.itcdataservices.com`

    1. Na **URL de resposta (URL do serviço de consumidor de asserção)** , introduza um URL ao utilizar o seguinte padrão:

       | Ambiente | do IdP |
       | ---------------| --------------- |
       | Teste  | `https://ratingqa.itcdataservices.com/webservices/imp/saml/login` |
       | Em direto  | `https://www.itcratingservices.com/webservices/imp/saml/login` |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real e o URL de resposta. Para obter estes valores, entre em contato com o [equipa de suporte de TurboRater](https://www.getitc.com/support). Também pode consultar os padrões mostrados a **configuração básica de SAML** painel no portal do Azure.

1. Na **definir a segurança de início de sessão único com o SAML** painel, na **certificado de assinatura SAML** secção, selecione **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado e guarde-o no seu computador.

    ![A opção de download do XML de metadados de Federação](common/metadataxml.png)

1. Na **configurar TurboRater** secção, copie o URL ou URLs que tem de:

   * **URL de início de sessão**
   * **Azure AD Identifier**
   * **URL de fim de sessão**

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-turborater-single-sign-on"></a>Configurar TurboRater início de sessão único

Para configurar o início de sessão único no lado do TurboRater, terá de enviar o XML de metadados de Federação transferidos e copiados apropriado URLs a partir do portal do Azure para o [equipa de suporte de TurboRater](https://www.getitc.com/support). A equipe de TurboRater será Certifique-se de que a ligação de SAML SSO está definida corretamente em ambos os lados.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para TurboRater.

1. No portal do Azure, selecione **aplicações empresariais** > **todas as aplicações** > **TurboRater**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

1. Na lista de aplicações, selecione **TurboRater**.

    ![TurboRater na lista de aplicações](common/all-applications.png)

1. No painel esquerdo, sob **MANAGE**, selecione **utilizadores e grupos**.

    ![A opção "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **+ adicionar utilizador**e, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** painel.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

1. No **utilizadores e grupos** painel, selecione **Eduarda Almeida** no **utilizadores** lista e, em seguida, escolha **selecione** na parte inferior do painel.

1. Se estiver à espera de um valor de função na asserção de SAML, em seguida, no **selecionar função** painel, selecione a função adequada para o utilizador a partir da lista. Na parte inferior do painel, escolha **selecione**.

1. Na **adicionar atribuição** painel, selecione **atribuir**.

### <a name="create-a-turborater-test-user"></a>Criar um utilizador de teste TurboRater

Nesta secção, vai criar um usuário chamado Eduarda Almeida no TurboRater. Trabalhar com o [equipa de suporte de TurboRater](https://www.getitc.com/support) adicionar Eduarda Almeida como um utilizador no TurboRater. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar seu única início de sessão em configuração do Azure AD com o portal as minhas aplicações.

Quando seleciona **TurboRater** no portal as minhas aplicações, deve estar automaticamente conectado à subscrição TurboRater para o qual configura o início de sessão único. Para obter mais informações sobre o portal as minhas aplicações, consulte [aplicações de acesso e a utilização no portal minhas aplicações](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais para integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

* [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)