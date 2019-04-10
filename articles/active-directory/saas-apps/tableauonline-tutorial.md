---
title: 'Tutorial: Integração do Active Directory do Azure com o Tableau Online | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Tableau Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: jeedes
ms.openlocfilehash: 5ccf978ab33226dc029d534a343a87a796ab69e8
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278110"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Tutorial: Integração do Active Directory do Azure com o Tableau Online

Neste tutorial, saiba como integrar o Tableau Online com o Azure Active Directory (Azure AD).
Integrar o Tableau Online com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD, quem tem acesso a Tableau Online.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o Tableau Online (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Tableau Online, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Tableau Online logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Online a tableau **SP** iniciada SSO

## <a name="adding-tableau-online-from-the-gallery"></a>Adicionar Tableau Online a partir da Galeria

Para configurar a integração do Tableau Online com o Azure AD, terá de adicionar Tableau Online na Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Tableau Online a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Tableau Online**, selecione **Tableau Online** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Tableau Online na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Tableau Online com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Tableau Online tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com o Tableau Online, terá de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o Tableau Online início de sessão único](#configure-tableau-online-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Online de Tableau](#create-tableau-online-test-user)**  - para ter um equivalente da Eduarda Almeida Tableau online que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Tableau Online, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Tableau Online** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Domínio de Online tableau e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva o URL: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva o URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > Obterá o `<entityid>` valor da **configurar Tableau Online** secção neste tutorial. O valor de ID de entidade será **identificador do Azure AD** valor no **configurar Tableau Online** secção.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configurar o Tableau Online** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-tableau-online-single-sign-on"></a>Configurar o Tableau Online início de sessão único

1. Numa janela do browser diferente, início de sessão na sua aplicação Tableau Online. Aceda a **configurações** e, em seguida **autenticação**.

    ![Configurar o início de sessão único](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Para ativar SAML, em **tipos de autenticação** secção. Verifique **ativar o método de autenticação adicional** e, em seguida, verificar **SAML** caixa de verificação.

    ![Configurar o início de sessão único](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Desloque para baixo até **importar o ficheiro de metadados no Tableau Online** secção.  Clique em Procurar e importar o ficheiro de metadados, que transferiu a partir do Azure AD. Em seguida, clique em **aplicar**.

   ![Configurar o início de sessão único](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. Na **corresponde ao asserções** secção, insira o nome de asserção de fornecedor de identidade correspondente para **endereço de e-mail**, **nome**, e **Apelido**. Para obter essas informações do Azure AD: 
  
    a. No portal do Azure, avance os **Tableau Online** página de integração de aplicativo.

    b. Na * * os atributos de utilizador e afirmações *** secção, clique no ícone de edição.

   ![Configurar o início de sessão único](./media/tableauonline-tutorial/attributesection.png)

    c. Copie o valor de espaço de nomes para esses atributos: givenname, e-mail e o apelido, utilizando os seguintes passos:

   ![O Azure AD Single Sign-On](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Clique em **user.givenname** valor

    e. Copie o valor a partir da **espaço de nomes** caixa de texto.

    ![Configurar o início de sessão único](./media/tableauonline-tutorial/attributesection2.png)

    f. Para copiar o espaço de nomes valores para o e-mail e o apelido, repita os passos acima.

    g. Mude para o aplicativo Tableau Online, em seguida, configure as **atributos de utilizador e afirmações** secção da seguinte forma:

    * E-mail: **correio** ou **userprincipalname**

    * Nome próprio: **givenname**

    * Apelido: **Apelido**

    ![Configurar o início de sessão único](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso ao Tableau Online.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Tableau Online**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Tableau Online**.

    ![A ligação Tableau Online na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-tableau-online-test-user"></a>Criar utilizador de teste Tableau Online

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Tableau Online.

1. No **Tableau Online**, clique em **definições** e, em seguida **autenticação** secção. Desloque para baixo até **gerir utilizadores** secção. Clique em **adicionar utilizadores** e, em seguida, clique em **Introduza endereços de E-Mail**.
  
    ![Criar um utilizador de teste do Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Selecione **adicionar utilizadores para a autenticação (SAML)**. Na **endereços de e-mail de Enter** adicionar caixa de texto britta.simon@contoso.com
  
    ![Criar um utilizador de teste do Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Clique em **adicionar utilizadores**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Tableau Online no painel de acesso, deve iniciar sessão automaticamente Tableau online para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
