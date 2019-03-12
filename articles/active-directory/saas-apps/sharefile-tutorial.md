---
title: 'Tutorial: Integração do Active Directory do Azure com o Citrix ShareFile | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Citrix ShareFile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4086c6e591029c926dbf3b7c527ffd8d936bc79b
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760258"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Tutorial: Integração do Active Directory do Azure com o Citrix ShareFile

Neste tutorial, saiba como integrar o Citrix ShareFile com o Azure Active Directory (Azure AD).
Integrar o Citrix ShareFile no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Citrix ShareFile.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o Citrix ShareFile (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Citrix ShareFile, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* O Citrix ShareFile logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta o Citrix ShareFile **SP** iniciada SSO

## <a name="adding-citrix-sharefile-from-the-gallery"></a>Adicionando o Citrix ShareFile da Galeria

Para configurar a integração entre o Citrix ShareFile com o Azure AD, terá de adicionar o Citrix ShareFile a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o Citrix ShareFile a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Citrix ShareFile**, selecione **o Citrix ShareFile** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![O Citrix ShareFile na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Citrix ShareFile com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Citrix ShareFile deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Citrix ShareFile, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o Citrix ShareFile início de sessão único](#configure-citrix-sharefile-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste o Citrix ShareFile](#create-citrix-sharefile-test-user)**  - para ter um equivalente da Eduarda Almeida na Citrix ShareFile que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Citrix ShareFile, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **o Citrix ShareFile** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Citrix ShareFile domínio e URLs únicas início de sessão em informações](common/sp-identifier-reply.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<tenant-name>.sharefile.com/saml/login`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão:

    | |
    |---|
    | `https://<tenant-name>.sharefile.com`|
    | `https://<tenant-name>.sharefile.com/saml/info`|
    | `https://<tenant-name>.sharefile1.com/saml/info`|
    | `https://<tenant-name>.sharefile1.eu/saml/info`|
    | `https://<tenant-name>.sharefile.eu/saml/info`|
    | |
    
    c. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:
    | |
    |---|
    | `https://<tenant-name>.sharefile.com/saml/acs`|
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>`|
    | `https://<tenant-name>.sharefile.com/saml/<URL path>`|

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão, identificador e o URL de resposta real. Contacte [equipa de suporte de cliente do Citrix ShareFile](https://www.citrix.co.in/products/citrix-content-collaboration/support.html) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar o Citrix ShareFile** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-citrix-sharefile-single-sign-on"></a>Configurar o Citrix ShareFile início de sessão único

1. Numa janela do browser web diferente, inicie sessão no seu **Citrix ShareFile** site da empresa como administrador.

2. Na barra de ferramentas na parte superior, clique em **administrador**.

3. No painel de navegação esquerdo, selecione **configurar o início de sessão único**.
   
    ![Administração de contas](./media/sharefile-tutorial/ic773627.png "administração de contas")

4. Sobre o **Single Sign-On / SAML 2.0 Configuration** página de diálogo em **definições básicas**, execute os seguintes passos:
   
    ![Início de sessão único](./media/sharefile-tutorial/ic773628.png "início de sessão único")
   
    a. Clique em **ativar SAML**.
    
    b. Na **Your IDP emissor / ID de entidade** caixa de texto, cole o valor de **identificador do Azure Ad** que copiou do portal do Azure.

    c. Clique em **alteração** junto a **certificado X.509** campo e, em seguida, carregue o certificado que transferiu a partir do portal do Azure.
    
    d. Na **URL de início de sessão** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.
    
    e. Na **URL de fim de sessão** caixa de texto, cole o valor de **URL de fim de sessão** que copiou do portal do Azure.

5. Clique em **guardar** no portal de gestão do Citrix ShareFile.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para o Citrix ShareFile.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **o Citrix ShareFile**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **Citrix ShareFile**.

    ![A ligação de Citrix ShareFile na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-citrix-sharefile-test-user"></a>Criar utilizador de teste o Citrix ShareFile

Para habilitar os utilizadores do Azure AD iniciar sessão no Citrix ShareFile, tem de ser aprovisionados no Citrix ShareFile. No caso do Citrix ShareFile, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **Citrix ShareFile** inquilino.

2. Clique em **gerir utilizadores \> gerir utilizadores Home \> + criar funcionário**.
   
    ![Criar o funcionário](./media/sharefile-tutorial/IC781050.png "criar funcionário")

3. Sobre o **informações básicas** secção, executar passos abaixo:
   
    ![Informações básicas](./media/sharefile-tutorial/IC799951.png "informações básicas")
   
    a. Na **endereço de E-Mail** caixa de texto, escreva o endereço de e-mail da Eduarda Almeida como **brittasimon@contoso.com**.
   
    b. Na **nome próprio** caixa de texto, tipo **nome próprio** do utilizador como **Eduarda**.
   
    c. Na **sobrenome** caixa de texto, tipo **Apelido** do utilizador como **Simon**.

4. Clique em **adicionar utilizador**.
  
    >[!NOTE]
    >O titular da conta do Azure AD irá receber um e-mail e seguir uma ligação para confirmar a respetiva conta até se tornar Active Directory. Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador o Citrix ShareFile ou APIs fornecidas pelo Citrix ShareFile para aprovisionar contas de utilizador do Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico o Citrix ShareFile no painel de acesso, deve ser automaticamente sessão iniciada no Citrix ShareFile para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

