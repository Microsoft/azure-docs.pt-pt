---
title: 'Tutorial: Integração do Active Directory do Azure com vista Empolgação | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e atenção.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4c602678-f6e8-4b26-b909-aaacfcfb1893
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 090e0a8488bb4f79de544774b08c847ea1524ad9
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798599"
---
# <a name="tutorial-integrate-hype-with-azure-active-directory"></a>Tutorial: Integrar a atenção com o Azure Active Directory

Neste tutorial, irá aprender como integrar a atenção no Azure Active Directory (Azure AD). Quando integrar atenção com o Azure AD, pode:

* Controlar no Azure AD, quem tem acesso a atenção.
* Permita que os utilizadores ser automaticamente sessão iniciada para atenção com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter a versão de avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Vista empolgação início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste.

* Atenção suporta **SP** iniciada SSO
* Atenção suporta **Just In Time** aprovisionamento de utilizadores

## <a name="adding-hype-from-the-gallery"></a>Adicionando a atenção da Galeria

Para configurar a integração de atenção para o Azure AD, terá de adicionar atenção a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **atenção** na caixa de pesquisa.
1. Selecione **atenção** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com atenção a utilização de um utilizador de teste chamado **B.Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na vista Empolgação.

Para configurar e testar o SSO do Azure AD com atenção, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o SSO de atenção](#configure-hype-sso)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de atenção](#create-hype-test-user)**  - para ter um equivalente da Eduarda Almeida em atenção que está ligada à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **atenção** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** , digite os valores para os seguintes campos:

    1. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão:
    
        `https://<SUBDOMAIN>.hypeinnovation.com/Shibboleth.sso/Login`

    1. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão:
    
        `https://<SUBDOMAIN>.hypeinnovation.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente de atenção](mailto:itsupport@hype.de) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

1. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, encontrar **metadados XML** e selecione **transferir** Para transferir o certificado e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Sobre o **configurar a vista Empolgação** secção, copie o URL adequado com base nos seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-hype-sso"></a>Configurar o SSO de atenção

Para configurar o início de sessão único num **atenção** lado, terá de enviar o transferido **XML de metadados** e adequadas copiados URLs a partir do portal do Azure para [equipa de suporte de atenção](mailto:itsupport@hype.de). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.


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

Nesta secção, irá ativar B.Simon utilizar o Azure início de sessão único ao conceder acesso a atenção.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **atenção**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

   ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B.Simon** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-hype-test-user"></a>Criar utilizador de teste de atenção

Nesta secção, um usuário chamado Eduarda Almeida é criado na vista Empolgação. Vista empolgação suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no atenção, é criado um novo após a autenticação.

### <a name="test-sso"></a>Teste SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de vista Empolgação no painel de acesso, deve ser automaticamente sessão iniciada na atenção para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
