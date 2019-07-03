---
title: 'Tutorial: Integração do Active Directory do Azure com Costpoint | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Costpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9ecc5f58-4462-4ade-ab73-0a4f61027504
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f21c7896f0ed2afba3a302b4686289cf331ba510
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536841"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Tutorial: Integrar Costpoint com o Azure Active Directory

Neste tutorial, irá aprender como integrar Costpoint com o Azure Active Directory (Azure AD). Quando integrar Costpoint com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao Costpoint.
* Permita que os utilizadores ser automaticamente sessão iniciada para Costpoint com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Costpoint início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Suporta Costpoint **SP e IDP** iniciada SSO.

## <a name="adding-costpoint-from-the-gallery"></a>Adicionando Costpoint da Galeria

Para configurar a integração do Costpoint com o Azure AD, terá de adicionar Costpoint a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **Costpoint** na caixa de pesquisa.
1. Selecione **Costpoint** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com Costpoint com um utilizador de teste **B.Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Costpoint.

Para configurar e testar o SSO do Azure AD com Costpoint, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Configurar Costpoint](#configure-costpoint)**  para configurar as definições de SSO no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com B.Simon.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar B.Simon utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Costpoint](#create-costpoint-test-user)**  para ter um equivalente de B.Simon no Costpoint que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **Costpoint** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<CostpointURI>`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<CostpointURI>/LoginServlet.cps`

    c. Clique em **definir URLs adicionais**.

    d. Na **estado de reencaminhamento** caixa de texto, escreva um valor utilizando o seguinte padrão:`<SYSTEM VALUE>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e estado de reencaminhamento. Contacte [equipa de suporte de cliente Costpoint](https://www.deltek.com/about/contact-us) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Se desejar configurar a aplicação no **SP** iniciado do modo de executar o passo seguinte:

    Na **URL de início de sessão** caixa de texto, escreva um URL:  `https://costpointteea.deltek.com/cpweb/cploginform.htm`

1. No **definir a segurança de início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em botão Copiar para copiar **Url de metadados de Federação de aplicação** e guarde-o em seu bloco de notas .

   ![O link de download de certificado](common/copy-metadataurl.png)

1. Sobre o **configurar Costpoint** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-costpoint"></a>Configurar Costpoint

Para configurar o início de sessão único num **Costpoint** lado, terá de enviar o **Url de metadados de Federação de aplicação** para [equipa de suporte de cliente Costpoint](https://www.deltek.com/about/contact-us). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste no portal do Azure chamado B.Simon.

1. No painel à esquerda no portal do Azure, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Na **utilizador** propriedades, siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. Na **nome de utilizador** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar B.Simon utilizar o Azure início de sessão único ao conceder acesso para Costpoint.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Costpoint**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B.Simon** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-costpoint-test-user"></a>Criar utilizador de teste Costpoint

Nesta secção, vai criar um usuário chamado B.Simon no Costpoint. Trabalhar com [equipa de suporte de cliente Costpoint](https://www.deltek.com/about/contact-us) para adicionar os utilizadores na plataforma Costpoint. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico Costpoint no painel de acesso, deve ser automaticamente sessão iniciada no Costpoint para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)