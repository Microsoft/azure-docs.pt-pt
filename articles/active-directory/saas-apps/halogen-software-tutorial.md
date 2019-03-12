---
title: 'Tutorial: Integração do Active Directory do Azure com o Software de Halogen | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Software de Halogen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a016a63a5731de78222ddabb6691e18879aef334
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57777127"
---
# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Tutorial: Integração do Active Directory do Azure com o Software de Halogen

Neste tutorial, saiba como integrar o Software de Halogen com o Azure Active Directory (Azure AD).
Integrar o Software de Halogen no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Halogen Software.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o Software de Halogen (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Software de Halogen, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Halogen Software logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a halogen Software **SP** iniciada SSO

## <a name="adding-halogen-software-from-the-gallery"></a>Adicionar Halogen Software a partir da Galeria

Para configurar a integração de Halogen Software com o Azure AD, terá de adicionar Halogen Software a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Halogen Software a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Halogen Software**, selecione **Halogen Software** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Software de halogen na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Software de Halogen com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Halogen Software deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Software de Halogen, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Halogen Software início de sessão único](#configure-halogen-software-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de Halogen Software](#create-halogen-software-test-user)**  - para ter um equivalente da Eduarda Almeida na Halogen Software que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Software de Halogen, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Halogen Software** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Halogen Software domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://global.hgncloud.com/<companyname>`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão:

    | |
    |--|
    | `https://global.halogensoftware.com/<companyname>`|
    | `https://global.hgncloud.com/<companyname>`|
    | |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente de Software Halogen](https://support.halogensoftware.com/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configurar Halogen Software** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-halogen-software-single-sign-on"></a>Configurar Halogen Software início de sessão único

1. Numa janela do browser diferente, início de sessão na sua **Halogen Software** como um administrador.

2. Clique nas **opções** separador.
  
    ![O que é o Azure AD Connect](./media/halogen-software-tutorial/tutorial_halogen_12.png)

3. No painel de navegação esquerdo, clique em **configuração SAML do**.
  
    ![O que é o Azure AD Connect](./media/halogen-software-tutorial/tutorial_halogen_13.png)

4. Sobre o **configuração SAML do** página, execute os seguintes passos:

    ![O que é o Azure AD Connect](./media/halogen-software-tutorial/tutorial_halogen_14.png)

    a. Como **Identificador exclusivo**, selecione **NameID**.

    b. Como **exclusivo mapas do identificador a para**, selecione **Username**.
  
    c. Para carregar o ficheiro de metadados baixado, clique em **navegue** para selecionar o ficheiro e, em seguida **carregar o ficheiro**.

    d. Para testar a configuração, clique em **executar teste**.

    > [!NOTE]
    > Terá de aguardar que a mensagem "*o teste SAML estiver concluído. Feche esta janela*". Em seguida, feche a janela do browser aberta. O **ativar SAML** caixa de verificação só está ativada se o teste for concluído.

    e. Selecione **ativar SAML**.

    f. Clique em **guardar alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso ao Halogen Software.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Halogen Software**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Halogen Software**.

    ![A ligação de Halogen Software na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-halogen-software-test-user"></a>Criar utilizador de teste de Halogen Software

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no Halogen Software.

**Para criar um usuário chamado Eduarda Almeida no Halogen Software, execute os seguintes passos:**

1. Inicie sessão no seu **Halogen Software** como um administrador.

2. Clique nas **Centro de utilizador** separador e, em seguida, clique em **Create User**.

    ![O que é o Azure AD Connect](./media/halogen-software-tutorial/tutorial_halogen_300.png)  

3. Sobre o **novo utilizador** caixa de diálogo página, execute os seguintes passos:

    ![O que é o Azure AD Connect](./media/halogen-software-tutorial/tutorial_halogen_301.png)

    a. Na **nome próprio** caixa de texto, nome do utilizador, como o tipo **Eduarda**.

    b. Na **sobrenome** caixa de texto, último nome de tipo do utilizador, como **Simon**.

    c. Na **nome de utilizador** caixa de texto, tipo **Eduarda Almeida**, o nome de utilizador como no portal do Azure.

    d. Na **palavra-passe** caixa de texto, digite uma senha para Eduarda.

    e. Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de Halogen Software no painel de acesso, deve ser automaticamente conectado para o Software de Halogen para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)