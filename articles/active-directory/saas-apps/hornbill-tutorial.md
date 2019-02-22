---
title: 'Tutorial: Integração do Active Directory do Azure com Hornbill | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Hornbill.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 173061e4-ac1d-458f-bb9b-e9a2493aab0e
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2072252a1222560049e73571f57bbc217fcd20f3
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56585480"
---
# <a name="tutorial-azure-active-directory-integration-with-hornbill"></a>Tutorial: Integração do Active Directory do Azure com Hornbill

Neste tutorial, saiba como integrar Hornbill com o Azure Active Directory (Azure AD).
Integrar Hornbill no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Hornbill.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Hornbill (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Hornbill, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Hornbill logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta hornbill **SP** iniciada SSO
* Suporta hornbill **Just In Time** aprovisionamento de utilizadores

## <a name="adding-hornbill-from-the-gallery"></a>Adicionando Hornbill da Galeria

Para configurar a integração do Hornbill com o Azure AD, terá de adicionar Hornbill a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Hornbill a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Hornbill**, selecione **Hornbill** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Hornbill na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Hornbill com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Hornbill deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Hornbill, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Hornbill Single Sign-On](#configure-hornbill-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Hornbill](#create-hornbill-test-user)**  - para ter um equivalente da Eduarda Almeida na Hornbill que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Hornbill, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Hornbill** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **configuração de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Hornbill domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<SUBDOMAIN>.hornbill.com/<INSTANCE_NAME>/`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<SUBDOMAIN>.hornbill.com/<INSTANCE_NAME>/lib/saml/auth/simplesaml/module.php/saml/sp/metadata.php/saml`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente Hornbill](https://www.hornbill.com/support/?request/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em botão Copiar para copiar **Url de metadados de Federação de aplicação** e guarde-o no seu computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

### <a name="configure-hornbill-single-sign-on"></a>Configurar Hornbill início de sessão único

1. Numa janela do browser web diferente, inicie sessão no Hornbill como um administrador de segurança.

2. Na Home page, clique em **sistema**.

    ![Sistema de hornbill](./media/hornbill-tutorial/tutorial_hornbill_system.png)

3. Navegue para **segurança**.

    ![Segurança de hornbill](./media/hornbill-tutorial/tutorial_hornbill_security.png)

4. Clique em **perfis SSO**.

    ![Hornbill único](./media/hornbill-tutorial/tutorial_hornbill_sso.png)

5. No lado direito da página, clique em **adicionar logótipo**.

    ![Adicionar hornbill](./media/hornbill-tutorial/tutorial_hornbill_addlogo.png)

6. Sobre o **detalhes do perfil** barra, clique em **logótipo de metadados de SAML de importação**.

    ![Logótipo de hornbill](./media/hornbill-tutorial/tutorial_hornbill_logo.png)

7. Na página de pop-up a **URL** caixa de texto, colar a **Url de metadados de Federação de aplicação**, que copiou do portal do Azure e clique em **processo**.

    ![Processo de hornbill](./media/hornbill-tutorial/tutorial_hornbill_process.png)

8. Depois de clicar em processo, os valores ficam preenchida automaticamente em automaticamente **detalhes do perfil** secção.

    ![Hornbill page1](./media/hornbill-tutorial/tutorial_hornbill_ssopage.png)

    ![Hornbill page2](./media/hornbill-tutorial/tutorial_hornbill_ssopage1.png)

    ![Para page3 de hornbill](./media/hornbill-tutorial/tutorial_hornbill_ssopage2.png)

9. Clique em **guardar alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** , insira **BrittaSimon**.
  
    b. Na **nome de utilizador** , digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Hornbill.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Hornbill**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Hornbill**.

    ![A ligação de Hornbill na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-hornbill-test-user"></a>Criar utilizador de teste Hornbill

Nesta secção, um usuário chamado Eduarda Almeida é criado na Hornbill. Hornbill suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no Hornbill, é criado um novo após a autenticação.

> [!Note]
> Se precisar de criar manualmente um utilizador, contacte [equipa de suporte de cliente Hornbill](https://www.hornbill.com/support/?request/).

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Hornbill no painel de acesso, deve ser automaticamente sessão iniciada no Hornbill para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

