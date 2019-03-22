---
title: 'Tutorial: Integração do Active Directory do Azure com o GitHub | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e do GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65badda86fa29dc68f2eef1e9c0883dc8b40107a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57852904"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Tutorial: Integração do Active Directory do Azure com o GitHub

Neste tutorial, saiba como integrar o GitHub com o Azure Active Directory (Azure AD).
Integração do GitHub com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao GitHub.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o GitHub (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o GitHub, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma organização do GitHub criada no [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise), que requer o [plano de faturação do GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* O GitHub suporta **SP** iniciada SSO

* O GitHub suporta [ **automatizada** aprovisionamento de utilizadores](github-provisioning-tutorial.md)

## <a name="adding-github-from-the-gallery"></a>Adicionar GitHub a partir da Galeria

Para configurar a integração do GitHub para o Azure AD, terá de adicionar GitHub a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar GitHub a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **GitHub**, selecione **GitHub** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![GitHub na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o GitHub com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no GitHub tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com o GitHub, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o GitHub Single Sign-On](#configure-github-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do GitHub](#create-github-test-user)**  - para ter um equivalente da Eduarda Almeida no GitHub que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o GitHub, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **GitHub** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![URLs de domínio do GitHub e únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://github.com/orgs/<entity-id>/sso`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Tenha em atenção que estes não são os valores reais. Terá de atualizar estes valores com o início de sessão real URL e o identificador. Aqui iremos sugerir-lhe utilizar o valor único de cadeia de caracteres no identificador de. Aceda à secção de administrador do GitHub para obter estes valores.

5. Seu aplicativo GitHub espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos, em que **nameidentifier** está mapeada com **user.userprincipalname**. Aplicações GitHub espera **nameidentifier** seja mapeado com **user.mail**, por isso, precisa editar o mapeamento do atributo, clicando em **editar** ícone e alterar o atributo mapeamento.

    ![image](common/edit-attribute.png)

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

7. Sobre o **configurar o GitHub** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-github-single-sign-on"></a>Configurar o GitHub Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no seu site de organização do GitHub como um administrador.

2. Navegue para **configurações** e clique em **segurança**

    ![Definições](./media/github-tutorial/tutorial_github_config_github_03.png)

3. Verifique os **autenticação SAML ativar** caixa, revelando os campos de configuração início de sessão único. Em seguida, utilize o início de sessão no URL valor único para atualizar o URL único início de sessão na configuração do Azure AD.

    ![Definições](./media/github-tutorial/tutorial_github_config_github_13.png)

4. Configure os seguintes campos:

    ![Definições](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. Na **iniciar sessão no URL** caixa de texto, colar **URL de início de sessão** valor que copiou do portal do Azure.

    b. Na **emissor** caixa de texto, colar **do Azure AD identificador** valor que copiou do portal do Azure.

    c. Abra o certificado transferido a partir do portal do Azure no bloco de notas, cole o conteúdo do **certificado público** caixa de texto.

    d. Clique em **editar** ícone para editar o **método de assinatura** e **Digest método** de **RSA SHA1** e **SHA1**para **RSA-SHA256** e **SHA256** conforme mostrado abaixo.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. Clique em **configuração SAML do teste** para confirmar que não existem falhas de validação ou erros durante a SSO.

    ![Definições](./media/github-tutorial/tutorial_github_config_github_06.png)

6. Clicar em **Guardar**

> [!NOTE]
> Início de sessão único no GitHub efetua a autenticação para uma organização específica no GitHub e não substitui a autenticação do GitHub em si. Por conseguinte, se a sessão do utilizador github.com tiver expirado, poderá ser-lhe pedido para autenticar com o ID de/palavra-passe do GitHub durante o processo de início de sessão único.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso ao GitHub.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **GitHub**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **GitHub**.

    ![A ligação do GitHub na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-github-test-user"></a>Criar utilizador de teste do GitHub

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no GitHub. O GitHub suporta o aprovisionamento automático de utilizadores, que está por predefinição, ativada. Pode encontrar mais detalhes [aqui](github-provisioning-tutorial.md) sobre como configurar o aprovisionamento automático de utilizadores.

**Se precisar de criar manualmente o utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa GitHub como administrador.

2. Clique em **pessoas**.

    ![As pessoas](./media/github-tutorial/tutorial_github_config_github_08.png "pessoas")

3. Clique em **membro de convite**.

    ![Convidar utilizadores](./media/github-tutorial/tutorial_github_config_github_09.png "convidar utilizadores")

4. Sobre o **membro de convite** caixa de diálogo página, execute os seguintes passos:

    a. Na **E-Mail** caixa de texto, escreva o endereço de e-mail da conta da Eduarda Almeida.

    ![Convidar pessoas](./media/github-tutorial/tutorial_github_config_github_10.png "convidar pessoas")

    b. Clique em **Enviar convite**.

    ![Convidar pessoas](./media/github-tutorial/tutorial_github_config_github_11.png "convidar pessoas")

    > [!NOTE]
    > O titular da conta do Azure Active Directory irá receber um e-mail e seguir uma ligação para confirmar a respetiva conta até se tornar Active Directory.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico do GitHub no painel de acesso, deve ser automaticamente sessão iniciada no GitHub para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
