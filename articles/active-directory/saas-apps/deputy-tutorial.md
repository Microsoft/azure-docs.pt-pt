---
title: 'Tutorial: Integração do Active Directory do Azure com o representante | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o representante.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5665c3ac-5689-4201-80fe-fcc677d4430d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b0b3921640ebf6ed21860cfac75ffca195096db
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57869749"
---
# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>Tutorial: Integração do Active Directory do Azure com o representante

Neste tutorial, saiba como integrar o representante com o Azure Active Directory (Azure AD).
Integrar o representante no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao representante.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para representante (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o representante, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Representante logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta o representante **SP** e **IDP** iniciada SSO

## <a name="adding-deputy-from-the-gallery"></a>Adicionando o representante da Galeria

Para configurar a integração do representante no Azure AD, terá de adicionar representante a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o representante da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **representante**, selecione **representante** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Representante na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o representante com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no representante deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o representante, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o representante Single Sign-On](#configure-deputy-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do representante](#create-deputy-test-user)**  - para ter um equivalente da Eduarda Almeida no representante que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o representante, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **representante** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![URLs de domínio do representante e únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:

    |  |
    | ----|
    | `https://<subdomain>.<region>.au.deputy.com` |
    | `https://<subdomain>.<region>.ent-au.deputy.com` |
    | `https://<subdomain>.<region>.na.deputy.com`|
    | `https://<subdomain>.<region>.ent-na.deputy.com`|
    | `https://<subdomain>.<region>.eu.deputy.com` |
    | `https://<subdomain>.<region>.ent-eu.deputy.com` |
    | `https://<subdomain>.<region>.as.deputy.com` |
    | `https://<subdomain>.<region>.ent-as.deputy.com` |
    | `https://<subdomain>.<region>.la.deputy.com` |
    | `https://<subdomain>.<region>.ent-la.deputy.com` |
    | `https://<subdomain>.<region>.af.deputy.com` |
    | `https://<subdomain>.<region>.ent-af.deputy.com` |
    | `https://<subdomain>.<region>.an.deputy.com` |
    | `https://<subdomain>.<region>.ent-an.deputy.com` |
    | `https://<subdomain>.<region>.deputy.com` |

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:
    
    | |
    |----|
    | `https://<subdomain>.<region>.au.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-au.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.na.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-na.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.eu.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-eu.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.as.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-as.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.la.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-la.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.af.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-af.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.an.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-an.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.deputy.com/exec/devapp/samlacs` |

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![URLs de domínio do representante e únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<your-subdomain>.<region>.deputy.com`

    >[!NOTE]
    > Sufixo de região de representante é opcional, ou deverá utilizar um dos seguintes: au | ND | eu | como | la | af | um | ent au | ent ND | ent UE | ent-como | Ent-la | Ent af | Ent – uma

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificador, o URL de resposta e início de sessão real. Contacte [equipa de suporte de cliente de representante](https://www.deputy.com/call-centers-customer-support-scheduling-software) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

7. Sobre o **configurar representante** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-deputy-single-sign-on"></a>Configurar o representante Single Sign-On

1. Navegue para o seguinte URL:[https://(your-subdomain).deputy.com/exec/config/system_config]( https://(your-subdomain).deputy.com/exec/config/system_config). Aceda a **definições de segurança** e clique em **editar**.
   
    ![Configurar o início de sessão único](./media/deputy-tutorial/tutorial_deputy_004.png)

2. Sobre isso **definições de segurança** página, executar passos abaixo.

    ![Configurar o início de sessão único](./media/deputy-tutorial/tutorial_deputy_005.png)
    
    a. Ativar **início de sessão Social**.
   
    b. Abra o seu certificado codificado Base64 transferido a partir do portal do Azure no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado de OpenSSL** caixa de texto.
   
    c. Na caixa de texto URL de SSO SAML, escreva `https://<your subdomain>.deputy.com/exec/devapp/samlacs?dpLoginTo=<saml sso url>`
    
    d. Na caixa de texto URL de SSO SAML, substitua `<your subdomain>` com o subdomínio.
   
    e. Na caixa de texto URL de SSO SAML, substitua `<saml sso url>` com o **URL de início de sessão** copiou do portal do Azure.
   
    f. Clique em **guardar as definições de**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso ao representante.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **representante**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **representante**.

    ![A ligação de representante na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-deputy-test-user"></a>Criar utilizador de teste do representante

Para ativar a utilizadores do Azure AD iniciar sessão no representante, tem de ser aprovisionados no representante. Em caso de representante, aprovisionamento é uma tarefa manual.

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para Aprovisionar uma conta de utilizador, execute os seguintes passos:

1. Inicie sessão no site da sua empresa representante como administrador.

2. No painel de navegação superior, clique em **pessoas**.
   
    ![As pessoas](./media/deputy-tutorial/tutorial_deputy_001.png "pessoas")

3. Clique nas **adicionar pessoas** botão e clique em **adicionar uma única pessoa**.
   
    ![Adicionar pessoas](./media/deputy-tutorial/tutorial_deputy_002.png "adicionar pessoas")

4. Execute os passos seguintes e clique em **guardar e convidar**.
   
    ![Novo utilizador](./media/deputy-tutorial/tutorial_deputy_003.png "novo utilizador")

    a. Na **Name** caixa de texto, nome do tipo de utilizador, como **BrittaSimon**.
   
    b. Na **E-Mail** caixa de texto, escreva o endereço de e-mail de uma conta do Azure AD que pretende aprovisionar.
   
    c. Na **trabalha na** caixa de texto, escreva o nome de empresa.
   
    d. Clique em **guardar e convidar** botão.

5. O titular da conta do AAD recebe uma mensagem de e-mail e segue uma ligação para confirmar a respetiva conta até se tornar Active Directory. Pode utilizar quaisquer outras representante utilizador conta criação ferramentas ou APIs fornecidas pelo representante para aprovisionar contas de utilizador do AAD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de representante no painel de acesso, deve ser automaticamente sessão iniciada no representante para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

