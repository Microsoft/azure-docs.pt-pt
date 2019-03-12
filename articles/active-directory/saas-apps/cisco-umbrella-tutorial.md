---
title: 'Tutorial: Integração do Active Directory do Azure com o Cisco guarda-chuva | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e a síntese de Cisco.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 675dca98-f119-4463-8350-d6a45d5601e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06bfc29b1efdd9263ca4a568dda700ed100731c4
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57732496"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella"></a>Tutorial: Integração do Active Directory do Azure com o guarda-chuva de Cisco

Neste tutorial, saiba como integrar o Cisco guarda-chuva com o Azure Active Directory (Azure AD).
Integrar o Cisco guarda-chuva no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao guarda-chuva Cisco.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para síntese de Cisco (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com guarda-chuva do Cisco, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Cisco guarda-chuva logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a síntese de Cisco **SP e IDP** iniciada SSO

## <a name="adding-cisco-umbrella-from-the-gallery"></a>Adicionando Cisco guarda-chuva da Galeria

Para configurar a integração de síntese da Cisco para o Azure AD, terá de adicionar Cisco guarda-chuva a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Cisco guarda-chuva a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Cisco guarda-chuva**, selecione **Cisco guarda-chuva** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Síntese de Cisco na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar o Azure AD início de sessão único com [nome da aplicação] com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado em [nome da aplicação] deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com [nome da aplicação], tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Cisco guarda-chuva início de sessão único](#configure-cisco-umbrella-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de síntese de Cisco](#create-cisco-umbrella-test-user)**  - para ter um equivalente da Eduarda Almeida na Cisco guarda-chuva que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com [nome da aplicação], execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Cisco guarda-chuva** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, o utilizador não tem de realizar qualquer passo como a aplicação já está pré-integrada com o Azure.

    ![Cisco guarda-chuva domínio e URLs únicas início de sessão em informações](common/both-preintegrated-signon.png)

    a. Se desejar configurar a aplicação no **SP** intiated modo, execute os seguintes passos:

    b. Clique em **definir URLs adicionais**.

    c. Na **URL de início de sessão** caixa de texto, escreva um URL: `https://login.umbrella.com/sso`

5. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **metadados XML**entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configurar guarda-chuva do Cisco** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-cisco-umbrella-single-sign-on"></a>Configurar Cisco guarda-chuva início de sessão único

1. Numa janela do browser diferente, início de sessão no site da sua empresa Cisco guarda-chuva como administrador.

2. Do lado esquerdo do menu, clique em **administrador** e navegue até à **autenticação** e, em seguida, clique em **SAML**.

    ![O administrador](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_admin.png)

3. Escolher **outras** e clique em **PRÓXIMA**.

    ![A outra](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_other.png)

4. Sobre o **Cisco guarda-chuva metadados**, página, clique em **PRÓXIMA**.

    ![Os metadados](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_metadata.png)

5. Sobre o **carregar metadados** separador, se tiver configurado previamente SAML, selecione **clique aqui para alterá-los** opção e siga os passos abaixo.

    ![A próxima](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_next.png)

6. Na **opção r: Carregar ficheiro XML**, carregue o **XML de metadados de Federação** ficheiro que transferiu do portal do Azure e depois de carregar metadados a valores abaixo obter automaticamente preenchido automaticamente, em seguida, clique em **seguinte**.

    ![O choosefile](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_choosefile.png)

7. Sob **validar configuração de SAML** secção, clique em **configuração de SAML do seu teste**.

    ![O teste](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_test.png)

8. Clique em **GUARDAR**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para síntese de Cisco.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Cisco guarda-chuva**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **Cisco guarda-chuva**.

    ![A ligação de síntese de Cisco na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-cisco-umbrella-test-user"></a>Criar utilizador de teste de síntese de Cisco

Para ativar a utilizadores do Azure AD iniciar sessão no guarda-chuva do Cisco, tem de ser aprovisionados para síntese de Cisco.  
No caso de síntese da Cisco, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Numa janela do browser diferente, início de sessão no site da sua empresa Cisco guarda-chuva como administrador.

2. Do lado esquerdo do menu, clique em **administrador** e navegue até à **contas**.

    ![A conta](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_account.png)

3. Sobre o **contas** página, clique em **Add** no canto superior direito da página e execute os seguintes passos.

    ![O utilizador](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_createuser.png)

    a. Na **nome próprio** , insira o firstname, como **Eduarda**.

    b. Na **sobrenome** campo, introduza o apelido como **simon**.

    c. Partir do **escolha função de administrador delegado**, selecione a sua função.
  
    d. Na **endereço de E-Mail** , insira o endereço de correio eletrónico do utilizador, como **brittasimon@contoso.com**.

    e. Na **palavra-passe** campo, introduza a palavra-passe.

    f. Na **Confirmar palavra-passe** campo, introduza novamente a palavra-passe.

    g. Clique em **criar**.

### <a name="test-single-sign-on"></a>Testar início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de síntese de Cisco no painel de acesso, deve ser automaticamente sessão iniciada no guarda-chuva de Cisco para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
