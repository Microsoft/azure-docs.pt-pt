---
title: 'Tutorial: Integração do Active Directory do Azure com Egnyte | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Egnyte.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 2/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b95c3dbbe08a152ed5d9265f04a5ec989e55275b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56167818"
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Tutorial: Integração do Active Directory do Azure com Egnyte

Neste tutorial, saiba como integrar Egnyte com o Azure Active Directory (Azure AD).
Integrar Egnyte no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Egnyte.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Egnyte (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Egnyte, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Egnyte logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Egnyte **SP** iniciada SSO

## <a name="adding-egnyte-from-the-gallery"></a>Adicionando Egnyte da Galeria

Para configurar a integração do Egnyte com o Azure AD, terá de adicionar Egnyte a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Egnyte a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Egnyte**, selecione **Egnyte** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Egnyte na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Egnyte com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Egnyte deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Egnyte, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Egnyte Single Sign-On](#configure-egnyte-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Egnyte](#create-egnyte-test-user)**  - para ter um equivalente da Eduarda Almeida na Egnyte que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Egnyte, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Egnyte** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Egnyte domínio e URLs únicas início de sessão em informações](common/sp-signonurl.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<companyname>.egnyte.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente Egnyte](https://www.egnyte.com/corp/contact_egnyte.html) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

5. Sobre o **configurar Egnyte** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-egnyte-single-sign-on"></a>Configurar Egnyte Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Egnyte como administrador.

2. Clique em **definições**.
   
    ![As definições](./media/egnyte-tutorial/ic787819.png "definições")

3. No menu, clique em **definições**.

    ![As definições](./media/egnyte-tutorial/ic787820.png "definições")

4. Clique nas **Configuration** separador e, em seguida, clique em **segurança**.

    ![Segurança](./media/egnyte-tutorial/ic787821.png "segurança")

5. Na **autenticação de início de sessão único** secção, execute os seguintes passos:

    ![Na autenticação de início de sessão único](./media/egnyte-tutorial/ic787822.png "na autenticação de início de sessão único")   
    
    a. Como **autenticação de início de sessão único**, selecione **SAML 2.0**.
   
    b. Como **fornecedor de identidade**, selecione **AzureAD**.
   
    c. Colar **URL de início de sessão** copiados a partir do portal do Azure para o **URL de início de sessão do fornecedor de identidade** caixa de texto.
   
    d. Colar **do Azure AD identificador** que copiou do portal do Azure para o **ID de entidade do fornecedor de identidade** caixa de texto.
      
    e. Abra o certificado com codificação base 64 no bloco de notas transferido a partir do portal do Azure, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado do fornecedor de identidade** caixa de texto.
   
    f. Como **predefinido o mapeamento de utilizador**, selecione **endereço de E-Mail**.
   
    g. Como **utilizar o valor de emissor específicos de domínio**, selecione **desativada**.
   
    h. Clique em **Guardar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Egnyte.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Egnyte**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Egnyte**.

    ![A ligação de Egnyte na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-egnyte-test-user"></a>Criar utilizador de teste Egnyte

Para ativar a utilizadores do Azure AD iniciar sessão no Egnyte, tem de ser aprovisionados em Egnyte. No caso de Egnyte, o aprovisionamento é uma tarefa manual.

**Para aprovisionar contas de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **Egnyte** site da empresa como administrador.

2. Aceda a **configurações \> os utilizadores e grupos**.

3. Clique em **adicionar novo utilizador**e, em seguida, selecione o tipo de utilizador que pretende adicionar.
   
    ![Os utilizadores](./media/egnyte-tutorial/ic787824.png "utilizadores")

4. Na **novo utilizador do Power** secção, execute os seguintes passos:
    
    ![Novo utilizador padrão](./media/egnyte-tutorial/ic787825.png "novo utilizador padrão")   

    a. Na **E-Mail** texto, introduza o e-mail do utilizador, como **Brittasimon@contoso.com**.

    b. Na **nome de utilizador** texto, introduza o nome de utilizador do utilizador, como **Brittasimon**.

    c. Selecione **início de sessão único** como **tipo de autenticação**.
   
    d. Clique em **Guardar**.
    
    >[!NOTE]
    >O titular da conta do Azure Active Directory irão receber um e-mail de notificação.
    >

>[!NOTE]
>Pode utilizar quaisquer outras Egnyte utilizador conta criação ferramentas ou APIs fornecidas pelo Egnyte para aprovisionar contas de utilizador do AAD.
>

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Egnyte no painel de acesso, deve ser automaticamente sessão iniciada no Egnyte para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

