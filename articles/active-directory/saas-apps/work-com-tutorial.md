---
title: 'Tutorial: Integração do Active Directory do Azure com Work.com | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Work.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9a978c8e32acb504ac97e3ca039deb8906e1543
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59274438"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Tutorial: Integração do Active Directory do Azure com Work.com

Neste tutorial, saiba como integrar Work.com com o Azure Active Directory (Azure AD).
Integrar Work.com no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Work.com.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Work.com (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Work.com, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Work.com logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Work.com **SP** iniciada SSO

## <a name="adding-workcom-from-the-gallery"></a>Adicionando Work.com da Galeria

Para configurar a integração do Work.com com o Azure AD, terá de adicionar Work.com a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Work.com a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Work.com**, selecione **Work.com** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Work.com na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Work.com com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Work.com deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Work.com, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Work.com Single Sign-On](#configure-workcom-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Work.com](#create-workcom-test-user)**  - para ter um equivalente da Eduarda Almeida na Work.com que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

>[!NOTE]
>Para configurar o início de sessão único, terá de configurar um nome de domínio personalizado Work.com ainda. Tem de definir, pelo menos, um nome de domínio, testar o seu nome de domínio e implementá-la em toda a organização.

Para configurar o Azure AD início de sessão único com Work.com, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Work.com** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Work.com domínio e URLs únicas início de sessão em informações](common/sp-signonurl.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `http://<companyname>.my.salesforce.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente Work.com](https://help.salesforce.com/articleView?id=000159855&type=3) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar Work.com** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-workcom-single-sign-on"></a>Configurar Work.com Single Sign-On

1. Inicie sessão no seu inquilino Work.com como administrador.

2. Aceda a **configuração**.
   
    ![Programa de configuração](./media/work-com-tutorial/ic794108.png "configuração")

3. No painel de navegação esquerdo, no **Administer** secção, clique em **gestão de domínios** para expandir a secção relacionada e, em seguida, clique em **meu domínio** para abrir o **Meu domínio** página. 
   
    ![Meu domínio](./media/work-com-tutorial/ic767825.png "meu domínio")

4. Para verificar que seu domínio foi configurado corretamente, certifique-se de que está a ser "**passo 4 implementadas para os utilizadores**" e rever seu "**minhas configurações de domínio**".
   
    ![Domínio implementado para o usuário](./media/work-com-tutorial/ic784377.png "implementado utilizador de domínio")

5. Inicie sessão no seu inquilino Work.com.

6. Aceda a **configuração**.
    
    ![Programa de configuração](./media/work-com-tutorial/ic794108.png "configuração")

7. Expanda a **controlos de segurança** e, em seguida, clique **configurações de logon único**.
    
    ![Único configurações de logon](./media/work-com-tutorial/ic794113.png "único configurações de logon")

8. Sobre o **definições de início de sessão único** caixa de diálogo página, execute os seguintes passos:
    
    ![SAML ativada](./media/work-com-tutorial/ic781026.png "SAML ativada")
    
    a. Selecione **SAML ativada**.
    
    b. Clique em **Novo**.

9. Na **SAML único configurações de logon** secção, execute os seguintes passos:
    
    ![SAML única início de sessão definição](./media/work-com-tutorial/ic794114.png "SAML única início de sessão na definição")
    
    a. Na **nome** caixa de texto, escreva um nome para a sua configuração.  
       
    > [!NOTE]
    > Fornecer um valor para **Name** preencher automaticamente o **nome da API** caixa de texto.
    
    b. Na **emissor** caixa de texto, cole o valor de **do Azure AD identificador** que copiou do portal do Azure.
    
    c. Para carregar o certificado transferido a partir do portal do Azure, clique em **procurar**.
    
    d. Na **Id de entidade** caixa de texto, tipo `https://salesforce-work.com`.
    
    e. Como **tipo de identidade de SAML**, selecione **asserção contém o ID de Federação do objeto de utilizador**.
    
    f. Como **localização de identidade de SAML**, selecione **identidade é no elemento NameIdentfier da declaração de assunto**.
    
    g. Na **URL de início de sessão do fornecedor de identidade** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.

    h. Na **URL de fim de sessão do fornecedor de identidade** caixa de texto, cole o valor de **URL de fim de sessão** que copiou do portal do Azure.
    
    i. Como **fornecedor iniciada solicitar vínculo de serviço**, selecione **HTTP Post**.
    
    j. Clique em **Guardar**.

10. No portal clássico do Work.com, no painel de navegação esquerdo, clique em **gestão de domínios** para expandir a secção relacionada e, em seguida, clique em **meu domínio** para abrir o **meu domínio** página. 
    
    ![Meu domínio](./media/work-com-tutorial/ic794115.png "meu domínio")

11. No **meu domínio** página, além da **marca de página de início de sessão** secção, clique em **editar**.
    
    ![Página de início de sessão de imagem corporativa](./media/work-com-tutorial/ic767826.png "imagem corporativa da página início de sessão")

12. No **uma imagem corporativa de página de início de sessão** na página a **serviço de autenticação** secção, o nome do seu **as definições de SSO SAML** é apresentado. Selecione-o e, em seguida, clique em **guardar**.
    
    ![Página de início de sessão de imagem corporativa](./media/work-com-tutorial/ic784366.png "imagem corporativa da página início de sessão")

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Work.com.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Work.com**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Work.com**.

    ![A ligação de Work.com na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-workcom-test-user"></a>Criar utilizador de teste Work.com

Para utilizadores do Azure Active Directory poder iniciar sessão, tem de ser aprovisionados para Work.com. No caso de Work.com, o aprovisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:

1. Inicie sessão site da sua empresa Work.com como um administrador.

2. Aceda a **configuração**.
   
    ![Programa de configuração](./media/work-com-tutorial/IC794108.png "configuração")

3. Aceda a **gerir utilizadores \> utilizadores**.
   
    ![Gerir utilizadores](./media/work-com-tutorial/IC784369.png "gerir utilizadores")

4. Clique em **novo utilizador**.
   
    ![Todos os utilizadores](./media/work-com-tutorial/IC794117.png "todos os utilizadores")

5. Na seção Editar utilizador, execute os seguintes passos, nos atributos de um Azure válido conta AD que pretende aprovisionar em caixas de texto relacionadas:
   
    ![Edição do utilizador](./media/work-com-tutorial/ic794118.png "edição do utilizador")
   
    a. Na **nome próprio** caixa de texto, tipo a **nome próprio** do utilizador **Eduarda**.
    
    b. Na **sobrenome** caixa de texto, tipo a **Apelido** do utilizador **Simon**.
    
    c. Na **Alias** caixa de texto, tipo a **nome** do utilizador **BrittaS**.
    
    d. Na **E-Mail** caixa de texto, tipo a **endereço de e-mail** do utilizador Brittasimon@contoso.com.
    
    e. Na **nome de utilizador** caixa de texto, escreva um nome de utilizador do utilizador, como Brittasimon@contoso.com.
    
    f. Na **nome Nick** caixa de texto, escreva um **nick nome** do utilizador **Simon**.
    
    g. Selecione **função**, **licença de utilizador**, e **perfil**.
    
    h. Clique em **Guardar**.  
      
    > [!NOTE]
    > O titular da conta do Azure AD irá receber um e-mail, incluindo uma ligação para confirmar a conta até se tornar Active Directory.
    > 

### <a name="test-single-sign-on"></a>Testar início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Work.com no painel de acesso, deve ser automaticamente sessão iniciada no Work.com para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

