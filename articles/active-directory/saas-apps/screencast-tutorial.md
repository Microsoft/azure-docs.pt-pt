---
title: 'Tutorial: Integração do Active Directory do Azure com Matic Screencast-S | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Matic Screencast-S.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 525ad47d-5488-40e2-aeaf-ae6183745682
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73a29e106424fe87596bf196c16e9ea190da2d72
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091394"
---
# <a name="tutorial-azure-active-directory-integration-with-screencast-o-matic"></a>Tutorial: Integração do Active Directory do Azure com Matic Screencast-S

Neste tutorial, saiba como integrar Screencast-O Matic com o Azure Active Directory (Azure AD).
Integrar Screencast-O Matic no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Matic Screencast-S.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Screencast-S-Matic (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Matic Screencast-S, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Screencast-O Matic início de sessão único de subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a screencast-S-Matic **SP** iniciada SSO
* Oferece suporte a screencast-S-Matic **Just In Time** aprovisionamento de utilizadores

## <a name="adding-screencast-o-matic-from-the-gallery"></a>Adicionar Screencast-O Matic a partir da Galeria

Para configurar a integração do Screencast-O Matic para o Azure AD, terá de adicionar Screencast-O Matic partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Screencast-O Matic a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Matic Screencast-S**, selecione **Matic Screencast-S** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Screencast-S-Matic na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Screencast-S-Matic com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Screencast-S-Matic deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Matic Screencast-S, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Screencast-O Matic início de sessão único](#configure-screencast-o-matic-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Matic Screencast-S](#create-screencast-o-matic-test-user)**  - para ter um equivalente da Eduarda Almeida no Screencast-S-Matic que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Matic Screencast-S, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Matic Screencast-S** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Screencast-O Matic domínio e URLs únicas início de sessão em informações](common/sp-signonurl.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente Matic Screencast-S](mailto:support@screencast-o-matic.com) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

4. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **metadados XML**entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configurar Matic Screencast-S** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-screencast-o-matic-single-sign-on"></a>Configurar Screencast-O Matic início de sessão único

1. Numa janela do browser web diferente, inicie sessão no Screencast-O Matic como um administrador.

2. Clique em **subscrição**.

    ![A subscrição](./media/screencast-tutorial/tutorial_screencast_sub.png)

3. Sob **página de acesso** secção, clique em **configuração**.

    ![O acesso](./media/screencast-tutorial/tutorial_screencast_setup.png)

4. Sobre o **página de configuração de acesso**, execute os seguintes passos:

   * Sob **URL de acesso** secção, escreva o seu instancename na caixa de texto especificada.

    ![O acesso](./media/screencast-tutorial/tutorial_screencast_access.png)

   * Selecione **utilizador de domínio exigir** sob **restrição de utilizador de SAML (opcional)** secção.

   * Sob **carregar ficheiro de XML de metadados de IDP**, clique em **Escolher ficheiro** para carregar os metadados que transferiu a partir do portal do Azure.

   * Clique em **OK**.

    ![O acesso](./media/screencast-tutorial/tutorial_screencast_save.png)

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

Nesta secção, vai ativar Eduarda Almeida utilizar do Azure-início de sessão único, concedendo acesso para Matic Screencast-S.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Matic Screencast-S**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Matic Screencast-S**.

    ![A ligação Screencast-O Matic na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-screencast-o-matic-test-user"></a>Criar Screencast-O Matic utilizador de teste

Nesta secção, um usuário chamado Eduarda Almeida é criado no Matic Screencast-S. Matic screencast-S suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no Matic Screencast-S, é criado um novo após a autenticação. Se precisar de criar manualmente um utilizador, contacte [equipa de suporte de cliente Matic Screencast-S](mailto:support@screencast-o-matic.com).

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Screencast-O Matic no painel de acesso, deve ser automaticamente sessão iniciada na Screencast-S-Matic para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
