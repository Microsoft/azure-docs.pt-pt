---
title: 'Tutorial: Integração do Active Directory do Azure com LearnUpon | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e LearnUpon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecdcd8c6024b3cacb422b556718bbbdbb5d601c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67098247"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Tutorial: Integração do Active Directory do Azure com LearnUpon

Neste tutorial, saiba como integrar LearnUpon com o Azure Active Directory (Azure AD).
Integrar LearnUpon no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao LearnUpon.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para LearnUpon (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com LearnUpon, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* LearnUpon logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.


* Suporta LearnUpon **IDP** iniciada SSO

* Suporta LearnUpon **Just In Time** aprovisionamento de utilizadores


## <a name="adding-learnupon-from-the-gallery"></a>Adicionando LearnUpon da Galeria

Para configurar a integração do LearnUpon com o Azure AD, terá de adicionar LearnUpon a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar LearnUpon a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **LearnUpon**, selecione **LearnUpon** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![LearnUpon na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com LearnUpon com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no LearnUpon deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com LearnUpon, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar LearnUpon Single Sign-On](#configure-learnupon-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste LearnUpon](#create-learnupon-test-user)**  - para ter um equivalente da Eduarda Almeida na LearnUpon que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com LearnUpon, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **LearnUpon** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![LearnUpon domínio e URLs únicas início de sessão em informações](common/idp-reply.png)

    Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:  `https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de resposta real. Contacte [equipa de suporte de cliente LearnUpon](https://www.learnupon.com/features/support/) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Na **definir a segurança de início de sessão único com o SAML** página, localize a **THUMBPRINT** -Isto será adicionado às suas definições de SAML LearnUpon.

    ![O link de download de certificado](common/certificateraw.png)

6. Sobre o **configurar LearnUpon** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-learnupon-single-sign-on"></a>Configurar LearnUpon Single Sign-On

1. Abra outra instância do browser e iniciar sessão no LearnUpon, com uma conta de administrador.

1. Clique nas **definições** separador.

    ![Configurar o início de sessão único](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. Clique em **Single Sign On - SAML**e, em seguida, clique em **definições gerais** para configurar as definições de SAML.
   
    ![Configurar o início de sessão único](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. Na **definições gerais** secção, execute os seguintes passos:
   
    ![Configurar o início de sessão único](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Selecione **ativada**.

    b. Selecione **versão** como **2.0**.

    c. Selecione **ignorar condições** como **não**.

    d. Na **nome do parâmetro de mensagem de Token SAML** caixa de texto, tipo o nome do parâmetro de mensagem de pedido para o URL de consumidor SAML indicado acima, que contém a asserção de SAML a ser verificado e autenticados – por exemplo **SAMLResponse** .

    e. Na **formato do nome do identificador** caixa de texto, tipo, o valor que indica onde, em sua asserção de SAML o identificador de utilizadores (endereço de E-Mail) reside - por exemplo `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.
  
    f. Na **identificar a localização do fornecedor** caixa de texto, digite o valor que indica em que os utilizadores são enviados para se clicarem no seu ícone carregado a partir do seu ecrã de início de sessão de portal do Azure.
  
    g. Na **termine URL** caixa de texto, colar a **URL de fim de sessão** valor, que copiou do portal do Azure.

    h. Clique em **gerir impressões de dedo**em seguida, carregue a impressão digital do seu certificado transferido.

1. Clique em **definições de utilizador**e, em seguida, execute os seguintes passos:

     ![Configurar o início de sessão único](./media/learnupon-tutorial/tutorial_learnupon_11.png)  

    a. Na **formato de identificador de nome próprio** caixa de texto, tipo, o valor que indica onde no seu asserção de SAML a firstname utilizadores reside - por exemplo: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
  
    b. Na **formato de identificador de nome passado** caixa de texto, tipo, o valor que indica onde no seu asserção de SAML lastname utilizadores reside - por exemplo: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para LearnUpon.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **LearnUpon**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **LearnUpon**.

    ![A ligação de LearnUpon na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-learnupon-test-user"></a>Criar utilizador de teste LearnUpon

Nesta secção, um usuário chamado Eduarda Almeida é criado na LearnUpon. LearnUpon suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no LearnUpon, é criado um novo após a autenticação. Se precisar de criar manualmente um utilizador, terá de contactar [equipa de suporte de LearnUpon](https://www.learnupon.com/features/support/).

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico LearnUpon no painel de acesso, deve ser automaticamente sessão iniciada no LearnUpon para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)