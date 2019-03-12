---
title: 'Tutorial: Integração do Active Directory do Azure com Envi MMIS | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Envi MMIS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab89f8ee-2507-4625-94bc-b24ef3d5e006
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08aab209d4772435c8b349026f036ce15ff73ced
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731611"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Tutorial: Integração do Active Directory do Azure com Envi MMIS

Neste tutorial, saiba como integrar Envi MMIS com o Azure Active Directory (Azure AD).
Integrar Envi MMIS no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Envi MMIS.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Envi MMIS (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Envi MMIS, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Envi MMIS logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Envi MMIS **SP** e **IDP** iniciada SSO

## <a name="adding-envi-mmis-from-the-gallery"></a>Adicionando Envi MMIS da Galeria

Para configurar a integração do Envi MMIS com o Azure AD, terá de adicionar Envi MMIS a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Envi MMIS a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Envi MMIS**, selecione **Envi MMIS** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Envi MMIS na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Envi MMIS com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Envi MMIS deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Envi MMIS, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Envi MMIS início de sessão único](#configure-envi-mmis-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Envi MMIS](#create-envi-mmis-test-user)**  - para ter um equivalente da Eduarda Almeida na MMIS Envi que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Envi MMIS, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Envi MMIS** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **configuração de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Envi MMIS domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://www.<CUSTOMER DOMAIN>.com/Account`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://www.<CUSTOMER DOMAIN>.com/Account/Acs`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Envi MMIS domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://www.<CUSTOMER DOMAIN>.com/Account`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificador, o URL de resposta e início de sessão real. Contacte [equipa de suporte de cliente de MMIS Envi](mailto:support@ioscorp.com) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. No **configuração de início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

7. Sobre o **configurar Envi MMIS** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-envi-mmis-single-sign-on"></a>Configurar Envi MMIS início de sessão único

1. Numa janela do browser web diferente, inicie sessão no seu site Envi MMIS como administrador.

2. Clique em **meu domínio** separador.

    ![Configurar o botão único início de sessão em Guardar](./media/envimmis-tutorial/configure1.png)

3. Clique em **Editar**.

    ![Configurar o botão único início de sessão em Guardar](./media/envimmis-tutorial/configure2.png)

4. Selecione **utilizar a autenticação remota** caixa de verificação e, em seguida, selecione **redirecionamento de HTTP** partir o **tipo de autenticação** lista pendente.

    ![Configurar o botão único início de sessão em Guardar](./media/envimmis-tutorial/configure3.png)

5. Selecione **recursos** separador e, em seguida, clique em **carregar metadados**.

    ![Configurar o botão único início de sessão em Guardar](./media/envimmis-tutorial/configure4.png)

6. Na **carregar metadados** pop-up, execute os seguintes passos:

    ![Configurar o botão único início de sessão em Guardar](./media/envimmis-tutorial/configure5.png)

    a. Selecione **arquivo** opção da **carregar de** lista pendente.

    b. Carregar o ficheiro de metadados baixado a partir do portal do Azure ao selecionar o **escolha o ícone de arquivo**.

    c. Clique em **OK**.

7. Depois de carregar o ficheiro de metadados baixado, os campos irão obter preenchidos automaticamente. Clique em **Update**

    ![Configurar o botão único início de sessão em Guardar](./media/envimmis-tutorial/configure6.png)

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Envi MMIS.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Envi MMIS**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Envi MMIS**.

    ![A ligação de Envi MMIS na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador na lista, em seguida, clique nas **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-envi-mmis-test-user"></a>Criar utilizador de teste Envi MMIS

Para ativar a utilizadores do Azure AD iniciar sessão no Envi MMIS, tem de ser aprovisionados em Envi MMIS. No caso de Envi MMIS, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa Envi MMIS como um administrador.

2. Clique em **lista de utilizadores** separador.

    ![Adicionar o funcionário](./media/envimmis-tutorial/user1.png)

3. Clique em **adicionar utilizador** botão.

    ![Adicionar o funcionário](./media/envimmis-tutorial/user2.png)

4. Na **adicionar utilizador** secção, execute os seguintes passos:

    ![Adicionar o funcionário](./media/envimmis-tutorial/user3.png)

    a. Na **nome de utilizador** caixa de texto, como o tipo o nome de utilizador da conta da Eduarda Almeida **brittasimon@contoso.com**.
    
    b. Na **nome próprio** como a caixa de texto, o primeiro nome typu BrittaSimon **Eduarda**.

    c. Na **sobrenome** caixa de texto, como o tipo o sobrenome de BrittaSimon **Simon**.

    d. Introduza o título do utilizador no **Title** da caixa de texto.
    
    e. Na **endereço de E-Mail** caixa de texto, como o tipo de endereço de e-mail da conta da Eduarda Almeida **brittasimon@contoso.com**.

    f. Na **nome de utilizador de SSO** caixa de texto, como o tipo o nome de utilizador da conta da Eduarda Almeida **brittasimon@contoso.com**.

    g. Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Envi MMIS no painel de acesso, deve ser automaticamente sessão iniciada no MMIS Envi para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

