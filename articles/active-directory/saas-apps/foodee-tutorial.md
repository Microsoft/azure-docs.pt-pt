---
title: 'Tutorial: Integração do Active Directory do Azure com Foodee | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Foodee.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 7e28bd4a-5624-4c67-aebb-0856d97e82c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8d62e972ddf23cbafe3f91a07947540bea99160
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66228826"
---
# <a name="tutorial-integrate-foodee-with-azure-active-directory"></a>Tutorial: Integrar Foodee com o Azure Active Directory

Neste tutorial, irá aprender como integrar Foodee com o Azure Active Directory (Azure AD). Quando integrar Foodee com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao Foodee.
* Permita que os utilizadores ser automaticamente sessão iniciada para Foodee com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Foodee início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Suporta Foodee **SP e IDP** iniciou o SSO e suporta **Just In Time** aprovisionamento de utilizadores.

## <a name="adding-foodee-from-the-gallery"></a>Adicionando Foodee da Galeria

Para configurar a integração do Foodee com o Azure AD, terá de adicionar Foodee a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **Foodee** na caixa de pesquisa.
1. Selecione **Foodee** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com Foodee com um utilizador de teste **B. Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Foodee.

Para configurar e testar o SSO do Azure AD com Foodee, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  para permitir aos utilizadores utilizar esta funcionalidade.
1. **[Configurar Foodee](#configure-foodee)**  para configurar as definições de SSO no lado do aplicativo.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com Simon B.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar a Simon B. utilizar o Azure AD início de sessão único.
1. **[Criar utilizador de teste Foodee](#create-foodee-test-user)**  para ter um equivalente de Simon B. no Foodee que está ligado à representação de utilizador do Azure AD.
1. **[Testar o SSO](#test-sso)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **Foodee** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, executar o passo seguinte:

    Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:  `https://concierge.food.ee/sso/saml/<INSTANCENAME>/consume`

1. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://concierge.food.ee/sso/saml/<INSTANCENAME>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de resposta e o URL de início de sessão real. Contacte [equipa de suporte de cliente Foodee](mailto:dev@food.ee) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

1. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, encontrar **XML de metadados de Federação** e selecione **transferir** para transferir o certificado e guarde-o no seu computador.

   ![O link de download de certificado](common/metadataxml.png)

1. Sobre o **configurar Foodee** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-foodee"></a>Configurar Foodee

1. Numa janela do browser web diferente, inicie sessão no Foodee como um administrador.

1. Clique em **logótipo de perfil** no canto superior direito da página, em seguida, navegue para **início de sessão único** e execute os seguintes passos:

   ![Configuração de Foodee](./media/foodee-tutorial/config01.png)

   1. Na **IDP nome** texto, escreva o nome, como por exemplo: Azure.
   1. Abra o XML de metadados de Federação no bloco de notas, copie o seu conteúdo e cole-na **XML de METADADOS de IDP** caixa de texto.
   1. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste no portal do Azure chamado Simon B.

1. No painel à esquerda no portal do Azure, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Na **utilizador** propriedades, siga estes passos:
   1. No campo **Nome**, introduza `B. Simon`.  
   1. Na **nome de utilizador** , insira o username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar a Simon B. utilizar o Azure início de sessão único ao conceder acesso para Foodee.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Foodee**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B. Simon** a partir da lista de utilizadores, em seguida, clique no **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-foodee-test-user"></a>Criar utilizador de teste Foodee

Nesta secção, um usuário chamado Simon B. é criado na Foodee. Foodee suporta o aprovisionamento de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no Foodee, uma nova é criada quando tentar acessar Foodee.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico Foodee no painel de acesso, deve ser automaticamente sessão iniciada no Foodee para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
