---
title: 'Tutorial: Integração do Active Directory do Azure com o Salesforce | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 239298959efa5c17d2f3d0c0aa5c4c30853c076f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57902040"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Tutorial: Integração do Active Directory do Azure com o Salesforce

Neste tutorial, saiba como integrar o Salesforce com o Azure Active Directory (Azure AD).
Integração de Salesforce com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Salesforce.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o Salesforce (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Salesforce, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Salesforce logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporte do Salesforce **SP** iniciada SSO

* Suporte do Salesforce **Just In Time** aprovisionamento de utilizadores

* Suporte do Salesforce [ **automatizada** aprovisionamento de utilizadores](salesforce-provisioning-tutorial.md)

## <a name="adding-salesforce-from-the-gallery"></a>Adicionar Salesforce a partir da Galeria

Para configurar a integração do Salesforce para o Azure AD, terá de adicionar Salesforce a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Salesforce a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Salesforce**, selecione **Salesforce** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Salesforce na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Salesforce, com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Salesforce tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com o Salesforce, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Salesforce Single Sign-On](#configure-salesforce-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do Salesforce](#create-salesforce-test-user)**  - para ter um equivalente da Eduarda Almeida no Salesforce, que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Salesforce, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Salesforce** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![URLs de domínio do Salesforce e únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **URL de início de sessão** caixa de texto, digite o valor usando o seguinte padrão:

    Conta de empresa: `https://<subdomain>.my.salesforce.com`

    Conta de programador: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. Na **identificador** caixa de texto, digite o valor usando o seguinte padrão:

    Conta de empresa: `https://<subdomain>.my.salesforce.com`

    Conta de programador: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão real e o identificador. Contacte [equipa de suporte de cliente do Salesforce](https://help.salesforce.com/support) obter esses valores.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configurar Salesforce** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-salesforce-single-sign-on"></a>Configurar Salesforce Single Sign-On

1. Abra um novo separador no seu browser e inicie sessão na sua conta de administrador do Salesforce.

2. Clique nas **programa de configuração** sob **ícone de definições** no canto superior direito da página.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/configure1.png)

3. Desloque para baixo para o **configurações** no painel de navegação, clique em **identidade** para expandir a secção relacionada. Em seguida, clique em **definições de início de sessão único**.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-admin-sso.png)

4. Sobre o **definições de início de sessão único** página, clique no **editar** botão.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Se não conseguir ativar definições de início de sessão único para a sua conta do Salesforce, poderá ter de contactar [equipa de suporte de cliente do Salesforce](https://help.salesforce.com/support).

5. Selecione **ativada SAML**e, em seguida, clique em **guardar**.

      ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-enable-saml.png)

6. Para configurar o seu únicas início de sessão em definições de SAML, clique em **novo a partir do ficheiro de metadados**.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-admin-sso-new.png)

7. Clique em **Escolher ficheiro** para carregar o ficheiro XML de metadados que transferiu a partir do portal do Azure e clique em **criar**.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/xmlchoose.png)

8. Sobre o **SAML único configurações de logon** página, campos preencher automaticamente em clique em Guardar.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/salesforcexml.png)

9. No painel de navegação esquerdo no Salesforce, clique em **definições da empresa** para expandir a secção relacionada e, em seguida, clique em **meu domínio**.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-my-domain.png)

10. Desloque para baixo para o **configuração da autenticação** secção e clique nas **editar** botão.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-edit-auth-config.png)

11. Na **configuração da autenticação** secção, verifique o **AzureSSO** como **serviço de autenticação** de sua configuração SAML SSO e clique em  **Guardar**.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Se for selecionado mais do que um serviço de autenticação, é pedido aos utilizadores para selecionar o serviço de autenticação que eles gostam de iniciar sessão com ao iniciar o início de sessão único para o seu ambiente do Salesforce. Se não quer isso aconteça, deve **deixe desmarcada a todos os outros serviços de autenticação**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao Salesforce.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Salesforce**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Salesforce**.

    ![A ligação de Salesforce na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-salesforce-test-user"></a>Criar utilizador de teste do Salesforce

Nesta secção, um usuário chamado Eduarda Almeida é criado no Salesforce. Salesforce suporta o aprovisionamento de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no Salesforce, uma nova é criada quando tentar acessar o Salesforce. Salesforce também suporta o aprovisionamento automático de utilizadores, pode encontrar mais detalhes [aqui](salesforce-provisioning-tutorial.md) sobre como configurar o aprovisionamento automático de utilizadores.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico do Salesforce no painel de acesso, deve ser automaticamente sessão iniciada no Salesforce para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar o aprovisionamento do utilizador](salesforce-provisioning-tutorial.md)
