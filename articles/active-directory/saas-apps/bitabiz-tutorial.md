---
title: 'Tutorial: Integração do Active Directory do Azure com BitaBIZ | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e BitaBIZ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a51e677-c62b-4aee-9c61-56926aaaa899
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb23d8096b56e7f4e2b08133b5a5b15fcbe193a3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453376"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Tutorial: Integração do Active Directory do Azure com BitaBIZ

Neste tutorial, saiba como integrar BitaBIZ com o Azure Active Directory (Azure AD).
Integrar BitaBIZ no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao BitaBIZ.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para BitaBIZ (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com BitaBIZ, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* BitaBIZ logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta BitaBIZ **SP e IDP** iniciada SSO

## <a name="adding-bitabiz-from-the-gallery"></a>Adicionando BitaBIZ da Galeria

Para configurar a integração do BitaBIZ com o Azure AD, terá de adicionar BitaBIZ a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar BitaBIZ a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **BitaBIZ**, selecione **BitaBIZ** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![BitaBIZ na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com BitaBIZ com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no BitaBIZ deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com BitaBIZ, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar BitaBIZ Single Sign-On](#configure-bitabiz-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste BitaBIZ](#create-bitabiz-test-user)**  - para ter um equivalente da Eduarda Almeida na BitaBIZ que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com BitaBIZ, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **BitaBIZ** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, executar o passo seguinte:

    ![BitaBIZ domínio e URLs únicas início de sessão em informações](common/idp-identifier.png)

    Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:  `https://www.bitabiz.com/<instanceId>`

    > [!NOTE]
    > O valor no URL acima é de apenas demonstração. Atualize o valor com o identificador real, o que é explicado mais tarde no tutorial.

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![image](common/both-preintegrated-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva o URL:  `https://www.bitabiz.com/dashboard`

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

7. Sobre o **configurar BitaBIZ** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-bitabiz-single-sign-on"></a>Configurar BitaBIZ Single Sign-On

1. Numa janela do browser web diferente, início de sessão no seu inquilino BitaBIZ como administrador.

2. Clique em **SETUP ADMIN**.

    ![Configuração de BitaBIZ](./media/bitabiz-tutorial/settings1.png)

3. Clique em **integrações do Microsoft** sob **adicionar valor** secção.

    ![Configuração de BitaBIZ](./media/bitabiz-tutorial/settings2.png)

4. Desloque para baixo para a secção **Microsoft Azure AD (ativar início de sessão único)** e execute os seguintes passos:

    ![Configuração de BitaBIZ](./media/bitabiz-tutorial/settings3.png)

    a. Copie o valor do **ID de entidade ("identificador" no Azure AD)** caixa de texto e cole-o no **identificador** caixa de texto a **configuração básica de SAML** secção no portal do Azure . 

    b. Na **do Azure AD único início de sessão no URL do serviço** caixa de texto, colar **URL de início de sessão**, que copiou do portal do Azure.

    c. Na **ID de entidade do Azure AD SAML** caixa de texto, colar **Azure Ad identificador**, que copiou do portal do Azure.

    d. Abra sua transferido **Certificate(Base64)** do ficheiro no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **do Azure AD certificado de assinatura (codificado por Base64)** caixa de texto.

    e. Adicionar o seu domínio de email de negócios ou seja, nome mycompany.com na **nome de domínio** caixa de texto para atribuir o SSO para os utilizadores na sua empresa com este domínio de e-mail (não obrigatório).

    f. Mark **SSO ativado** a conta de BitaBIZ.

    g. Clique em **guardar a configuração do Azure AD** para guardar e ativar a configuração de SSO.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para BitaBIZ.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **BitaBIZ**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **BitaBIZ**.

    ![A ligação de BitaBIZ na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-bitabiz-test-user"></a>Criar utilizador de teste BitaBIZ

Para ativar a utilizadores do Azure AD iniciar sessão no BitaBIZ, tem de ser aprovisionados em BitaBIZ.  
No caso de BitaBIZ, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa BitaBIZ como administrador.

2. Clique em **SETUP ADMIN**.

    ![BitaBIZ adicionar utilizador](./media/bitabiz-tutorial/settings1.png)

3. Clique em **adicionar utilizadores** sob **organização** secção.

    ![BitaBIZ adicionar utilizador](./media/bitabiz-tutorial/user1.png)

4. Clique em **adicionar novo funcionário**.

    ![BitaBIZ adicionar utilizador](./media/bitabiz-tutorial/user2.png)

5. Sobre o **adicionar novo funcionário** caixa de diálogo página, execute os seguintes passos:

    ![BitaBIZ adicionar utilizador](./media/bitabiz-tutorial/user3.png)

    a. Na **nome próprio** caixa de texto, tipo, o primeiro nome de utilizador, como a Eduarda.

    b. Na **Apelido** caixa de texto, digite o apelido do utilizador, como Simon.

    c. Na **E-Mail** caixa de texto, como o tipo de endereço de e-mail do utilizador Brittasimon@contoso.com.

    d. Selecione uma data no **data de contratação**.

    e. Existem outros atributos de utilizador não obrigatórias que podem ser definidos para o utilizador. Consulte a [documento de configuração de funcionário](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee) para obter mais detalhes.

    f. Clique em **Save funcionário**.

    > [!NOTE]
    > O titular da conta do Azure Active Directory recebe uma mensagem de e-mail e segue uma ligação para confirmar a respetiva conta até se tornar Active Directory.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico BitaBIZ no painel de acesso, deve ser automaticamente sessão iniciada no BitaBIZ para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
