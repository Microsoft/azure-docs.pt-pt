---
title: 'Tutorial: Integração do Active Directory do Azure com Optimizely | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Optimizely.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 6029e24f0a08efeeae0ae7644305e07669b6f8b2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176424"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Tutorial: Integração do Active Directory do Azure com Optimizely

Neste tutorial, saiba como integrar Optimizely com o Azure Active Directory (Azure AD).
Integrar Optimizely no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Optimizely.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Optimizely (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Optimizely, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Optimizely logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Optimizely **SP** iniciada SSO

## <a name="adding-optimizely-from-the-gallery"></a>Adicionando Optimizely da Galeria

Para configurar a integração do Optimizely com o Azure AD, terá de adicionar Optimizely a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Optimizely a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Optimizely**, selecione **Optimizely** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Optimizely na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Optimizely com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Optimizely deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Optimizely, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Optimizely Single Sign-On](#configure-optimizely-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Optimizely](#create-optimizely-test-user)**  - para ter um equivalente da Eduarda Almeida na Optimizely que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Optimizely, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Optimizely** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Optimizely domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://app.optimizely.net/<instance name>`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `urn:auth0:optimizely:contoso`

    > [!NOTE]
    > Estes valores não são o real. Atualizar o valor com o URL de início de sessão real e o identificador, o que é explicado mais tarde no tutorial. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Seu aplicativo Optimizely espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **edite** ícone para abrir **atributos do utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

6. Além dos acima, o aplicativo Optimizely espera mais alguns atributos a serem passados na resposta SAML. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir:

    | Name | Atributo de origem |
    | ---------------| --------------- |
    | e-mail | user.mail |
    
    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar Optimizely** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-optimizely-single-sign-on"></a>Configurar Optimizely Single Sign-On

1. Para configurar o início de sessão único num **Optimizely** , contacte o seu Gestor de conta Optimizely e fornecerá o transferido **certificado (Base64)** e URLs de copiados apropriado.

2. Em resposta ao seu correio eletrónico, Optimizely fornece o início de sessão no URL (SSO iniciado por SP) e os valores de identificador (ID de entidade do fornecedor de serviço).

    a. Cópia a **URL de SSO iniciado por SP** fornecido pelo Optimizely e colar para o **URL de início de sessão** caixa de texto no **configuração básica de SAML** secção no portal do Azure.

    b. Cópia a **ID de entidade do fornecedor de serviço** fornecido pelo Optimizely e colar para o **identificador** caixa de texto no **configuração básica de SAML** secção no portal do Azure.

3. Numa janela do browser diferente, início de sessão na sua aplicação Optimizely.

4. Clique em que o nome de conta no canto superior direito e, em seguida **definições de conta**.

    ![O Azure AD Single Sign-On](./media/optimizely-tutorial/tutorial_optimizely_09.png)

5. No separador de conta, a caixa de verificação **ativar o SSO** sob o início de sessão único no **descrição geral** secção.
  
    ![O Azure AD Single Sign-On](./media/optimizely-tutorial/tutorial_optimizely_10.png)

6. Clicar em **Guardar**

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Optimizely.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Optimizely**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Optimizely**.

    ![A ligação de Optimizely na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-optimizely-test-user"></a>Criar utilizador de teste Optimizely

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Optimizely.

1. Na home page, selecione **colaboradores** separador.

2. Para adicionar o novo funcionário ao projeto, clique em **novo funcionário**.
   
    ![Criar um utilizador de teste do Azure AD](./media/optimizely-tutorial/create_aaduser_10.png)

3. Preencha o endereço de e-mail e atribuir-lhes uma função. Clique em **convidar**.

    ![Criar um utilizador de teste do Azure AD](./media/optimizely-tutorial/create_aaduser_11.png)

4. Eles recebem um convite por e-mail. Usando o endereço de e-mail, têm de iniciar sessão no Optimizely.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Optimizely no painel de acesso, deve ser automaticamente sessão iniciada no Optimizely para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

