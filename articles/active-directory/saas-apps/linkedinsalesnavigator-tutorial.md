---
title: 'Tutorial: Integração do Active Directory do Azure com o LinkedIn Sales Navigator | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o LinkedIn Sales Navigator.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81a216e720523767f428036290aea7151c2dca34
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708164"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-sales-navigator"></a>Tutorial: Integração do Active Directory do Azure com o LinkedIn Sales Navigator

Neste tutorial, saiba como integrar o LinkedIn Sales Navigator com o Azure Active Directory (Azure AD).
Integrar o LinkedIn Sales Navigator no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso para o LinkedIn Sales Navigator.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o LinkedIn Sales Navigator (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o LinkedIn Sales Navigator, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* LinkedIn Sales Navigator logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta o LinkedIn Sales Navigator **SP e IDP** iniciada SSO

* Suporta o LinkedIn Sales Navigator **Just In Time** aprovisionamento de utilizadores

* Suporta o LinkedIn Sales Navigator [ **automatizada** aprovisionamento de utilizadores](linkedinsalesnavigator-provisioning-tutorial.md)

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>Adicionando o LinkedIn Sales Navigator da Galeria

Para configurar a integração do LinkedIn Sales Navigator para o Azure AD, terá de adicionar o LinkedIn Sales Navigator partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o LinkedIn Sales Navigator partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **LinkedIn Sales Navigator**, selecione **LinkedIn Sales Navigator** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Na lista de resultados do LinkedIn Sales Navigator](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o LinkedIn Sales Navigator com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no LinkedIn Sales Navigator deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o LinkedIn Sales Navigator, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o LinkedIn Sales Navigator início de sessão único](#configure-linkedin-sales-navigator-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do LinkedIn Sales Navigator](#create-linkedin-sales-navigator-test-user)**  - para ter um equivalente da Eduarda Almeida no LinkedIn Sales Navigator que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o LinkedIn Sales Navigator, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **LinkedIn Sales Navigator** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![LinkedIn Sales Navigator domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** texto, introduza o **ID de entidade** valor, irá copiar o valor de ID de entidade do Portal do Linkedin explicado posteriormente neste tutorial.

    b. Na **URL de resposta** texto, introduza o **Url de acesso de consumidor de asserção (ACS)** valor, irá copiar o Url de acesso de consumidor de asserção (ACS) valor a partir do Portal do Linkedin explicado posteriormente neste tutorial.

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![LinkedIn Sales Navigator domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

6. Aplicação do LinkedIn Sales Navigator espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos, em que **nameidentifier** está mapeada com **user.userprincipalname**. Aplicação do LinkedIn Sales Navigator espera nameidentifier seja mapeado com **user.mail**, por isso terá de editar o mapeamento do atributo ao clicar no ícone de edição e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

7. Além dos acima, o aplicativo do LinkedIn Sales Navigator espera mais alguns atributos a serem passados na resposta SAML. Na seção de afirmações de utilizador sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir:

    | Name | Atributo de origem|
    | --- | --- |
    | e-mail| user.mail |
    | Departamento| user.department |
    | FirstName| user.givenname |
    | Apelido| user.surname |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

8. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

9. Sobre o **configurar o LinkedIn Sales Navigator** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-linkedin-sales-navigator-single-sign-on"></a>Configurar o LinkedIn Sales Navigator início de sessão único

1. Numa janela do browser web diferente, início de sessão na sua **LinkedIn Sales Navigator** Web site como um administrador.

1. Na **Centro de contas**, clique em **definições globais** sob **definições**. Além disso, selecione **Sales Navigator** na lista pendente.

    ![Configurar o início de sessão único](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. Clique em **ou clique aqui para carregar e copiar campos individuais do formulário** e execute os seguintes passos:

    ![Configurar o início de sessão único](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

    a. Cópia **Id de entidade** e cole-o no **identificador** caixa de texto **configuração básica de SAML** no portal do Azure.

    b. Cópia **Url de acesso de consumidor de asserção (ACS)** e cole-o para o **URL de resposta** caixa de texto **configuração básica de SAML** no portal do Azure.

1. Aceda a **definições de administração do LinkedIn** secção. Carregar o ficheiro XML que transferiu do portal do Azure ao clicar no **arquivo XML carregar** opção.

    ![Configurar o início de sessão único](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. Clique em **no** para ativar o SSO. Estado SSO é alterado de **não ligado** para **ligado**

    ![Configurar o início de sessão único](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para o LinkedIn Sales Navigator.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **LinkedIn Sales Navigator**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **LinkedIn Sales Navigator**.

    ![A ligação do LinkedIn Sales Navigator na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-linkedin-sales-navigator-test-user"></a>Criar utilizador de teste do LinkedIn Sales Navigator

Aplicativo de navegador de vendas vinculado oferece suporte apenas no Time (JIT) de aprovisionamento de utilizador e de utilizadores de autenticação são criados automaticamente no aplicativo. Ativar **atribuir licenças automaticamente** para atribuir uma licença ao utilizador.

   ![Criar um utilizador de teste do Azure AD](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico do LinkedIn Sales Navigator no painel de acesso, deve ser automaticamente conectado para o LinkedIn Sales Navigator para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

