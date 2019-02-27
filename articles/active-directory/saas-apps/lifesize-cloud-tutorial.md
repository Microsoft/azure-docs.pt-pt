---
title: 'Tutorial: Integração do Active Directory do Azure com a nuvem Lifesize | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Lifesize Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1be975a7a0e004e29aaee9433c727d1d456ff03c
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56866135"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Tutorial: Integração do Active Directory do Azure com a nuvem Lifesize

Neste tutorial, saiba como integrar Lifesize Cloud no Azure Active Directory (Azure AD).
Integrar Lifesize Cloud no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso à nuvem Lifesize.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Lifesize Cloud (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Lifesize Cloud, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Lifesize Cloud logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta a nuvem Lifesize **SP** iniciada SSO

* Suporta a nuvem Lifesize **automatizada** aprovisionamento de utilizadores

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Adicionar Lifesize Cloud a partir da Galeria

Para configurar a integração da Lifesize Cloud com o Azure AD, terá de adicionar Lifesize Cloud a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Lifesize Cloud a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Lifesize Cloud**, selecione **Lifesize Cloud** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Lifesize Cloud na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Lifesize Cloud com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na Lifesize Cloud deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Lifesize Cloud, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Lifesize Cloud início de sessão único](#configure-lifesize-cloud-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste da Lifesize Cloud](#create-lifesize-cloud-test-user)**  - para ter um equivalente da Eduarda Almeida na Cloud de Lifesize que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Lifesize Cloud, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Lifesize Cloud** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Lifesize Cloud domínio e URLs únicas início de sessão em informações](common/sp-identifier-relay.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://login.lifesizecloud.com/ls/?acs`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://login.lifesizecloud.com/<companyname>`

    c. Clique em **definir URLs adicionais**.

    d. Na **estado de reencaminhamento** caixa de texto, escreva um URL com o seguinte padrão: `https://webapp.lifesizecloud.com/?ent=<identifier>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão, identificador e o estado de reencaminhamento real. Contacte [equipa de suporte de cliente de Cloud Lifesize](https://www.lifesize.com/en/support) obter o URL de início de sessão e valores de identificador e pode obter o valor de estado de reencaminhamento da configuração de SSO, que é explicado mais tarde no tutorial. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar Lifesize Cloud** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-lifesize-cloud-single-sign-on"></a>Configurar Lifesize Cloud início de sessão único

1. Para obter SSO configurado para a sua aplicação, início de sessão na aplicação Lifesize Cloud com privilégios de administrador.

2. No canto superior direito clique no seu nome e, em seguida, clique nas **definições avançadas**.

    ![Configurar o início de sessão único](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

3. Nas definições de avanço agora, clique no **SSO configuração** ligação. Isso abrirá a página de configuração de SSO para a sua instância.

    ![Configurar o início de sessão único](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

4. Agora, configure os seguintes valores na configuração do SSO da interface do Usuário.

    ![Configurar o início de sessão único](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)

    a. Na **emissor do fornecedor de identidade** caixa de texto, cole o valor de **Azure Ad identificador** que copiou do portal do Azure.

    b.  Na **URL de início de sessão** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.

    c. Abra o certificado com codificação base 64 no bloco de notas transferido a partir do portal do Azure, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado X.509** caixa de texto.
  
    d. Nos mapeamentos de atributos de SAML para a caixa de texto nome, introduza o valor como `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    e. No mapeamento de atributo de SAML para o **Apelido** caixa de texto introduza o valor como `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    f. No mapeamento de atributo de SAML para o **E-Mail** caixa de texto introduza o valor como `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

5. Para verificar a configuração, pode clicar no **teste** botão.

    >[!NOTE]
    >Para fins de teste concluída com êxito, precisa concluir o Assistente de configuração no Azure AD e também fornecem acesso a utilizadores ou grupos que podem realizar o teste.

6. Ativar o SSO, verificando no **ativar o SSO** botão.

7. Agora, clique no **atualização** botão, para que todas as definições são guardadas. Isso irá gerar o valor de RelayState. Copie o valor de RelayState, que é gerado na caixa de texto, cole-a no **estado de reencaminhamento** caixa de texto em **Lifesize Cloud domínio e URLs** secção.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso à nuvem Lifesize.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Lifesize Cloud**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Lifesize Cloud**.

    ![A ligação de Lifesize Cloud na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-lifesize-cloud-test-user"></a>Criar utilizador de teste da Lifesize Cloud

Nesta secção, vai criar um usuário chamado Eduarda Almeida na Lifesize Cloud. Lifesize cloud suporta o aprovisionamento automático de utilizadores. Após a autenticação com êxito do Azure AD, o utilizador será aprovisionado automaticamente no aplicativo.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de Lifesize Cloud no painel de acesso, deve obter a página de início de sessão da aplicação de Lifesize Cloud. Aqui tem de introduzir o seu nome de utilizador e, depois disso, será redirecionado para a página inicial do aplicativo.

Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
