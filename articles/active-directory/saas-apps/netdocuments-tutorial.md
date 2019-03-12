---
title: 'Tutorial: Integração do Active Directory do Azure com NetDocuments | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e NetDocuments.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: 35fd09b898adb49d378de6e1e92011d34016ac95
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57765871"
---
# <a name="tutorial-azure-active-directory-integration-with-netdocuments"></a>Tutorial: Integração do Active Directory do Azure com NetDocuments

Neste tutorial, saiba como integrar NetDocuments com o Azure Active Directory (Azure AD).
Integrar NetDocuments no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao NetDocuments.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para NetDocuments (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com NetDocuments, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* NetDocuments único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta NetDocuments **SP** iniciada SSO

## <a name="adding-netdocuments-from-the-gallery"></a>Adicionando NetDocuments da Galeria

Para configurar a integração do NetDocuments com o Azure AD, terá de adicionar NetDocuments a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar NetDocuments a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **NetDocuments**, selecione **NetDocuments** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![NetDocuments na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com NetDocuments com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no NetDocuments deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com NetDocuments, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar NetDocuments Single Sign-On](#configure-netdocuments-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste NetDocuments](#create-netdocuments-test-user)**  - para ter um equivalente da Eduarda Almeida na NetDocuments que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com NetDocuments, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **NetDocuments** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![NetDocuments domínio e URLs únicas início de sessão em informações](common/sp-reply.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<user identifier>`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<user identifier>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o URL de resposta. Contacte [equipa de suporte de cliente NetDocuments](https://support.netdocuments.com/hc/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configurar NetDocuments** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-netdocuments-single-sign-on"></a>Configurar NetDocuments Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa NetDocuments como um administrador.

2. Aceda a **administrador**.

3. Clique em **adicionar e remover utilizadores e grupos**.
   
    ![Repositório](./media/netdocuments-tutorial/ic795047.png "repositório")

4. Clique em **configurar opções de autenticação avançadas**.
    
    ![Configurar opções de autenticação avançadas](./media/netdocuments-tutorial/ic795048.png "configurar opções de autenticação avançadas")

5. Sobre o **identidade federada** caixa de diálogo, execute os seguintes passos:
   
    ![Federado Identitty](./media/netdocuments-tutorial/ic795049.png "federado Identitty")
   
    a. Como **federado o tipo de servidor de identidade**, selecione **serviços de Federação do Active Directory**.
   
    b. Clique em **Escolher ficheiro**, para carregar o ficheiro de metadados baixado que transferiu a partir do portal do Azure.
   
    c. Clique em **OK**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para NetDocuments.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **NetDocuments**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **NetDocuments**.

    ![A ligação de NetDocuments na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-netdocuments-test-user"></a>Criar utilizador de teste NetDocuments

Para ativar a utilizadores do Azure AD iniciar sessão no NetDocuments, tem de ser aprovisionados em NetDocuments.  
No caso de NetDocuments, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Brilhar em sua **NetDocuments** site da empresa como administrador.

2. No menu na parte superior, clique em **administrador**.
   
    ![Admin](./media/netdocuments-tutorial/ic795051.png "Admin")

3. Clique em **adicionar e remover utilizadores e grupos**.
   
    ![Repositório](./media/netdocuments-tutorial/ic795047.png "repositório")

4. Na **endereço de E-Mail** caixa de texto, escreva o endereço de e-mail de uma conta válida do Azure Active Directory que pretende aprovisionar e, em seguida, clique em **adicionar utilizador**.
   
    ![Endereço de e-mail](./media/netdocuments-tutorial/ic795053.png "endereço de E-Mail")
   
    >[!NOTE]
    >O titular da conta do Azure Active Directory irá receber uma mensagem de e-mail que inclui uma ligação para confirmar a conta até se tornar Active Directory. Pode utilizar quaisquer outras NetDocuments utilizador conta criação ferramentas ou APIs fornecidas pelo NetDocuments para aprovisionar o Azure Active Directory contas de utilizador.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico NetDocuments no painel de acesso, deve ser automaticamente sessão iniciada no NetDocuments para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

