---
title: 'Tutorial: Integração do Active Directory do Azure com o LinkedIn Learning | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o LinkedIn Learning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3717ed095c162255b0c0cdf09cac13fe2033979a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57905006"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-learning"></a>Tutorial: Integração do Active Directory do Azure com o LinkedIn Learning

Neste tutorial, saiba como integrar o LinkedIn Learning no Azure Active Directory (Azure AD).
Integrar o LinkedIn Learning no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao LinkedIn Learning.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o LinkedIn Learning (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o LinkedIn Learning, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* LinkedIn Learning logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* LinkedIn Learning suporta **SP e IDP** iniciada SSO
* LinkedIn Learning suporta **Just In Time** aprovisionamento de utilizadores

## <a name="adding-linkedin-learning-from-the-gallery"></a>Adicionar LinkedIn Learning a partir da Galeria

Para configurar a integração do LinkedIn Learning no Azure AD, terá de adicionar LinkedIn Learning a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o LinkedIn Learning a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **LinkedIn Learning**, selecione **LinkedIn Learning** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![LinkedIn Learning na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o LinkedIn Learning com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no LinkedIn Learning deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o LinkedIn Learning, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o LinkedIn Learning início de sessão único](#configure-linkedin-learning-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do LinkedIn Learning](#create-linkedin-learning-test-user)**  - para ter um equivalente da Eduarda Almeida no LinkedIn Learning que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o LinkedIn Learning, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **LinkedIn Learning** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IdP iniciada pelo** modo execute os seguintes passos:

    ![URLs de domínio do LinkedIn Learning e únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, introduza o **ID de entidade** copiados a partir do Portal do LinkedIn. 

    b. Na **URL de resposta** caixa de texto, introduza o **Url de serviço de consumidor de asserção (ACS)** copiados a partir do Portal do LinkedIn.

    c. Se desejar configurar a aplicação no **SP iniciada** modo, em seguida, clique em **definir URLs adicionais** opção o **configuração básica de SAML** seção aqui que irá especificar o URL de início de sessão. Para criar a cópia de Url de início de sessão do **Url de serviço de consumidor de asserção (ACS)** e substitua /saml//início de sessão /. Depois que tiver sido feita, o URL de início de sessão deve ter o seguinte padrão:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![URLs de domínio do LinkedIn Learning e únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Estes valores não são valor real. Atualizar estes valores com o identificador real e o URL de resposta, o que é explicado posteriormente no **configurar o LinkedIn Learning Single Sign-On** secção do tutorial.

5. A aplicação do LinkedIn Learning espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos, em que **nameidentifier** está mapeada com **user.userprincipalname**. Aplicação LinkedIn Learning espera **nameidentifier** seja mapeado com **user.mail**, por isso terá de editar o mapeamento do atributo clicando no **editar** ícone e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

6. Além dos acima, o aplicativo do LinkedIn Learning espera mais alguns atributos a serem passados na resposta SAML. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir:
    
    | Name | Atributo de origem |
    | ---------------| --------------- |
    | e-mail  | user.mail  |
    | Departamento  | user.department  |
    | FirstName  | user.givenname  |
    | Apelido  | user.surname  |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

7. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

### <a name="configure-linkedin-learning-single-sign-on"></a>Configurar o LinkedIn Learning início de sessão único

1. Numa janela do browser web diferente, início de sessão no seu inquilino do LinkedIn Learning como administrador.

2. Na **Centro de contas**, clique em **definições globais** sob **definições**. Além disso, selecione **Learning - predefinição** na lista pendente.

    ![Configurar o início de sessão único](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. Clique em **ou clique aqui para carregar e copiar campos individuais do formulário** e copie **Id de entidade** e **Url do serviço de consumidor de asserção (ACS)** e cole-a no  **Configuração básica de SAML** secção no portal do Azure.

    ![Configurar o início de sessão único](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. Aceda a **definições de administração do LinkedIn** secção. Carregue o ficheiro XML que transferiu a partir do portal do Azure ao clicar o **arquivo XML carregar** opção.

    ![Configurar o início de sessão único](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. Clique em **no** para ativar o SSO. Estado SSO é alterado de **não ligado** para **ligado**

    ![Configurar o início de sessão único](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para o LinkedIn Learning.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **LinkedIn Learning**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **LinkedIn Learning**.

    ![A ligação do LinkedIn Learning na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-linkedin-learning-test-user"></a>Criar utilizador de teste do LinkedIn Learning

Aplicação do LinkedIn Learning suporta apenas em tempo de aprovisionamento de utilizador e de utilizadores de autenticação são criados automaticamente no aplicativo. Sobre o administrador de definições de página no flip de portal de aprendizagem do LinkedIn o comutador **automaticamente atribuir licenças** para Active Directory apenas em tempo de aprovisionamento e isso irão também atribuir uma licença ao utilizador.

   ![Criar um utilizador de teste do Azure AD](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico do LinkedIn Learning no painel de acesso, deve ser automaticamente conectado para o LinkedIn Learning para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

