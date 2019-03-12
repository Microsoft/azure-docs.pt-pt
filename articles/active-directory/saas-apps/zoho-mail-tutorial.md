---
title: 'Tutorial: Integração do Active Directory do Azure com Zoho | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Zoho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 9874e1f3-ade5-42e7-a700-e08b3731236a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: da91e830f9220ce22228b3b7bedcc241101bd8ea
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775786"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Tutorial: Integração do Active Directory do Azure com Zoho

Neste tutorial, saiba como integrar Zoho com o Azure Active Directory (Azure AD).
Integrar Zoho no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Zoho.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Zoho (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Zoho, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Zoho logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Zoho **SP** iniciada SSO

## <a name="adding-zoho-from-the-gallery"></a>Adicionando Zoho da Galeria

Para configurar a integração do Zoho com o Azure AD, terá de adicionar Zoho a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Zoho a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Zoho**, selecione **Zoho** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Zoho na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Zoho com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Zoho deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Zoho, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Zoho Single Sign-On](#configure-zoho-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Zoho](#create-zoho-test-user)**  - para ter um equivalente da Eduarda Almeida na Zoho que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Zoho, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Zoho** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Zoho domínio e URLs únicas início de sessão em informações](common/sp-signonurl.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<company name>.zohomail.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente Zoho](https://www.zoho.com/mail/contact.html) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar Zoho** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-zoho-single-sign-on"></a>Configurar Zoho Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Zoho correio como um administrador.

2. Vá para o **painel de controlo**.
   
    ![Painel de controlo](./media/zoho-mail-tutorial/ic789607.png "painel de controlo")

3. Clique nas **autenticação SAML** separador.
   
    ![Autenticação SAML](./media/zoho-mail-tutorial/ic789608.png "autenticação SAML")

4. Na **detalhes de autenticação SAML** secção, execute os seguintes passos:
   
    ![Detalhes de autenticação SAML](./media/zoho-mail-tutorial/ic789609.png "detalhes de autenticação SAML")
   
    a. Na **URL de início de sessão** caixa de texto, colar **URL de início de sessão** que copiou do portal do Azure.
   
    b. Na **URL de fim de sessão** caixa de texto, colar **URL de fim de sessão** que copiou do portal do Azure.
   
    c. Na **alterar o URL da palavra-passe** caixa de texto, colar **alterar o URL da palavra-passe** que copiou do portal do Azure.
       
    d. Abra o seu certificado codificado de base 64 transferido a partir do portal do Azure no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **PublicKey** caixa de texto.
   
    e. Como **algoritmo**, selecione **RSA**.
   
    f. Clique em **OK**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Zoho.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Zoho**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **Zoho**.

    ![A ligação de Zoho na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-zoho-test-user"></a>Criar utilizador de teste Zoho

Para habilitar os utilizadores do Azure AD iniciar sessão no correio Zoho, tem de ser aprovisionados em Zoho correio. No caso de correio Zoho, o aprovisionamento é uma tarefa manual.

> [!NOTE]
> Pode utilizar qualquer outras correio Zoho utilizador conta ferramentas de criação ou APIs fornecidas pelo correio Zoho para aprovisionar contas de utilizador do AAD.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para Aprovisionar uma conta de utilizador, execute os seguintes passos:

1. Inicie sessão no seu **Zoho correio** site da empresa como administrador.

1. Aceda a **painel de controlo \> correio & Docs**.

1. Aceda a **detalhes de utilizador \> adicionar utilizador**.
   
    ![Adicionar utilizador](./media/zoho-mail-tutorial/ic789611.png "adicionar utilizador")

1. Sobre o **adicionar utilizadores** caixa de diálogo, execute os seguintes passos:
   
    ![Adicionar utilizador](./media/zoho-mail-tutorial/ic789612.png "adicionar utilizador")
   
    a. Na **nome próprio** caixa de texto, como o tipo o primeiro nome de utilizador **Eduarda**.

    b. Na **sobrenome** caixa de texto, como o tipo o apelido do utilizador **Simon**.

    c. Na **ID de E-Mail** caixa de texto, como o tipo o id de e-mail do utilizador **brittasimon@contoso.com**.

    d. Na **palavra-passe** caixa de texto, introduza a palavra-passe do utilizador.
   
    e. Clique em **OK**.  
      
    > [!NOTE]
    > O titular da conta do Azure Active Directory irá receber um e-mail com uma ligação para confirmar a conta até se tornar Active Directory.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Zoho no painel de acesso, deve ser automaticamente sessão iniciada no Zoho para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

