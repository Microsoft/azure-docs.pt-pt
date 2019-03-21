---
title: 'Tutorial: Integração do Active Directory do Azure com Samanage | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Samanage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b00e27a0620212fbc09f8b7d91d8f5b9d0971c8f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57885237"
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Tutorial: Integração do Active Directory do Azure com Samanage

Neste tutorial, saiba como integrar Samanage com o Azure Active Directory (Azure AD).
Integrar Samanage no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Samanage.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Samanage (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Samanage, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Samanage logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Samanage **SP** iniciada SSO

## <a name="adding-samanage-from-the-gallery"></a>Adicionando Samanage da Galeria

Para configurar a integração do Samanage com o Azure AD, terá de adicionar Samanage a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Samanage a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Samanage**, selecione **Samanage** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Samanage na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Samanage com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Samanage deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Samanage, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Samanage Single Sign-On](#configure-samanage-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Samanage](#create-samanage-test-user)**  - para ter um equivalente da Eduarda Almeida na Samanage que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Samanage, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Samanage** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Samanage domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão real e o identificador, o que é explicado mais tarde no tutorial. Para obter mais detalhes contacte [equipa de suporte de cliente Samanage](https://www.samanage.com/support). Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar Samanage** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-samanage-single-sign-on"></a>Configurar Samanage Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Samanage como um administrador.

2. Clique em **Dashboard** e selecione **configuração** no painel de navegação à esquerda.
   
    ![Dashboard](./media/samanage-tutorial/tutorial_samanage_001.png "Dashboard")

3. Clique em **início de sessão único**.
   
    ![Início de sessão único](./media/samanage-tutorial/tutorial_samanage_002.png "início de sessão único")

4. Navegue para **através de SAML de início de sessão** secção, execute os seguintes passos:
   
    ![Início de sessão através de SAML](./media/samanage-tutorial/tutorial_samanage_003.png "através de SAML de início de sessão")
 
    a. Clique em **ativar o início de sessão único com o SAML**.  
 
    b. Na **URL de fornecedor de identidade** caixa de texto, cole o valor de **identificador do Azure Ad** que copiou do portal do Azure.    
 
    c. Confirmar a **URL de início de sessão** corresponde a **URL de início de sessão** de **configuração básica de SAML** secção no portal do Azure.
 
    d. Na **URL de fim de sessão** caixa de texto, introduza o valor de **URL de fim de sessão** que copiou do portal do Azure.
 
    e. Na **emissor de SAML** caixa de texto, escreva o id de aplicação URI definido no seu fornecedor de identidade.
 
    f. Abra o seu certificado codificado de base 64 transferido a partir do portal do Azure no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **colar o seu fornecedor de identidade de x.509 certificado abaixo** caixa de texto.
 
    g. Clique em **criar utilizadores caso não existam no Samanage**.
 
    h. Clique em **Atualizar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Samanage.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Samanage**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Samanage**.

    ![A ligação de Samanage na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-samanage-test-user"></a>Criar utilizador de teste Samanage

Para ativar a utilizadores do Azure AD iniciar sessão no Samanage, tem de ser aprovisionados em Samanage.  
No caso de Samanage, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa Samanage como administrador.

2. Clique em **Dashboard** e selecione **configuração** no pan de navegação à esquerda.
   
    ![Programa de configuração](./media/samanage-tutorial/tutorial_samanage_001.png "configuração")

3. Clique nas **utilizadores** separador
   
    ![Os utilizadores](./media/samanage-tutorial/tutorial_samanage_006.png "utilizadores")

4. Clique em **novo utilizador**.
   
    ![Novo utilizador](./media/samanage-tutorial/tutorial_samanage_007.png "novo utilizador")

5. Tipo de **nome** e o **endereço de E-Mail** de uma conta do Azure Active Directory que pretende aprovisionar e clique em **criar utilizador**.
   
    ![Criar utilizador](./media/samanage-tutorial/tutorial_samanage_008.png "criar utilizador")
   
   >[!NOTE]
   >O titular da conta do Azure Active Directory irá receber um e-mail e seguir uma ligação para confirmar a respetiva conta até se tornar Active Directory. Pode utilizar quaisquer outras Samanage utilizador conta criação ferramentas ou APIs fornecidas pelo Samanage para aprovisionar o Azure Active Directory contas de utilizador.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Samanage no painel de acesso, deve ser automaticamente sessão iniciada no Samanage para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

