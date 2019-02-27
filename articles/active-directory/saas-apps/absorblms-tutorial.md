---
title: 'Tutorial: Integração do Active Directory do Azure com absorver LMS | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e absorver LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 998a46bc6255898ab4754de57a2570a77896cf85
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56876743"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Tutorial: Integração do Active Directory do Azure com absorver LMS

Neste tutorial, saiba como integrar absorver LMS com o Azure Active Directory (Azure AD).
Integração de LMS absorver com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao absorver LMS.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para absorver LMS (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com absorver LMS, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Absorver LMS única início de sessão ativada subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Absorver suporta LMS **IDP** iniciada SSO

## <a name="adding-absorb-lms-from-the-gallery"></a>Adicionando absorver LMS da Galeria

Para configurar a integração de LMS absorver com o Azure AD, terá de adicionar absorver LMS a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar absorver LMS a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **absorver LMS**, selecione **absorver LMS** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Absorver LMS na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com LMS absorver com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no absorver LMS deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com absorver LMS, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar absorver LMS início de sessão único](#configure-absorb-lms-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de LMS absorver](#create-absorb-lms-test-user)**  - para ter um equivalente da Eduarda Almeida na LMS absorver que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com absorver LMS, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **absorver LMS** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **configuração básica de SAML** caixa de diálogo.

    ![Absorver LMS domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    Se estiver a utilizar **absorver 5 - interface do Usuário** utilize a seguinte configuração:

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://company.myabsorb.com/account/saml`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://company.myabsorb.com/account/saml`

    Se estiver a utilizar **absorver 5 - nova experiência de aprendiz** utilize a seguinte configuração:

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://company.myabsorb.com/api/rest/v2/authentication/saml`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://company.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real e o URL de resposta. Contacte [equipa de suporte de cliente de LMS absorver](https://support.absorblms.com/hc/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **metadados XML**entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configurar absorver LMS** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-absorb-lms-single-sign-on"></a>Configurar absorver LMS início de sessão único

1. Numa nova janela do browser web, inicie sessão no site da sua empresa absorver LMS como administrador.

2. Selecione o **conta** botão na parte superior direita.

    ![O botão de conta](./media/absorblms-tutorial/1.png)

3. No painel de conta, selecione **as definições do Portal**.

    ![A ligação de definições do Portal](./media/absorblms-tutorial/2.png)

4. Selecione o **gerir definições de SSO** separador.

    ![O separador de utilizadores](./media/absorblms-tutorial/managesso.png)

5. Sobre o **gerir único configurações de logon** página, efetue o seguinte:

    ![A página de configuração de início de sessão único](./media/absorblms-tutorial/settings.png)

    a. Na **nome** caixa de texto, introduza o nome, como o Azure AD Marketplace SSO.

    b. Selecione **SAML** como um **método**.

    c. No bloco de notas, abra o certificado que transferiu a partir do portal do Azure. Remover os **---BEGIN CERTIFICATE---** e **---END CERTIFICATE---** etiquetas. Em seguida, na **chave** caixa, cole os conteúdos restantes.

    d. Na **modo** caixa, selecione **fornecedor de identidade iniciada pelo**.

    e. Na **propriedade de Id** caixa, selecione o atributo que configurou como o identificador de utilizador no Azure AD. Por exemplo, se *userPrincipalName* está selecionado no Azure AD, selecione **Username**.

    f. Selecione **Sha256** como um **tipo de assinatura**.

    g. Na **URL de início de sessão** caixa, cole a **URL de acesso de utilizador** partir do aplicativo **propriedades** página do portal do Azure.

    h. Na **URL de fim de sessão**, cole a **URL de fim de sessão** valor que copiou do **configurar início de sessão** janela do portal do Azure.

    i. Botão de alternar **redirecionar automaticamente** ao **no**.

6. Selecione **guardar.**

    ![O botão de alternar apenas permitir o SSO início de sessão](./media/absorblms-tutorial/save.png)

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para absorver LMS.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **absorver LMS**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **absorver LMS**.

    ![A ligação de LMS absorver na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-absorb-lms-test-user"></a>Criar utilizador de teste de LMS absorver

Para utilizadores do Azure AD iniciar sessão no absorver LMS, eles tem de ser definidos no absorver LMS. No caso de absorver LMS, o aprovisionamento é uma tarefa manual.

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa absorver LMS como um administrador.

2. Na **usuários** painel, selecione **utilizadores**.

    ![A ligação de utilizadores](./media/absorblms-tutorial/absorblms_userssub.png)

3. Selecione **utilizador** separador.

    ![A adicionar novo na lista pendente](./media/absorblms-tutorial/absorblms_createuser.png)

4. Sobre o **adicionar utilizador** página, efetue o seguinte:

    ![A página Adicionar utilizador](./media/absorblms-tutorial/user.png)

    a. Na **nome próprio** , escreva o nome, tal como **Eduarda**.

    b. Na **sobrenome** , escreva o apelido, tal como **Simon**.

    c. Na **nome de utilizador** , escreva um nome completo, tal como **Eduarda Almeida**.

    d. Na **palavra-passe** caixa, escrever a palavra-passe do utilizador.

    e. Na **Confirmar palavra-passe** caixa, escreva novamente a palavra-passe.

    f. Definir o **é Active** alternar para **Active Directory**.

5. Selecione **guardar.**

    ![O botão de alternar apenas permitir o SSO início de sessão](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > Por predefinição, o aprovisionamento de utilizador não está ativado no SSO. Se o cliente deseja ativar esta funcionalidade, preciso defini-lo até conforme mencionado na [isso](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) documentação. Também tenha em atenção que o utilizador Provisioing só está disponível no **absorver 5 - nova experiência de aprendiz** com ACS URL -`https://company.myabsorb.com/api/rest/v2/authentication/saml`

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de LMS absorver no painel de acesso, deve ser automaticamente sessão iniciada no LMS absorver, para que configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
