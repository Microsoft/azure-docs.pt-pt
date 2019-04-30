---
title: 'Tutorial: Integração do Active Directory do Azure com o LinkedIn elevar | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o LinkedIn elevar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92af3a18be8471013e93afad47146bdbabd4be18
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128792"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Tutorial: Integração do Active Directory do Azure com o LinkedIn elevar

Neste tutorial, saiba como integrar o LinkedIn elevar com o Azure Active Directory (Azure AD).
Integrar o LinkedIn elevar no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao elevar o LinkedIn.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para elevar o LinkedIn (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com elevar o LinkedIn, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Elevar o LinkedIn logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a elevar o LinkedIn **SP e IDP** iniciada SSO

* Oferece suporte a elevar o LinkedIn **Just In Time** aprovisionamento de utilizadores

* Oferece suporte a elevar o LinkedIn [ **automatizada** aprovisionamento de utilizadores](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Adicionando o LinkedIn elevar da Galeria

Para configurar a integração do LinkedIn elevar para o Azure AD, terá de adicionar LinkedIn elevar partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar a LinkedIn elevar a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **LinkedIn elevar**, selecione **LinkedIn elevar** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Elevar o LinkedIn na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o LinkedIn, efetuar a elevação com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no LinkedIn elevar deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o LinkedIn elevar, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o LinkedIn elevar o início de sessão único](#configure-linkedin-elevate-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de elevar o LinkedIn](#create-linkedin-elevate-test-user)**  - para ter um equivalente da Eduarda Almeida no LinkedIn elevar que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o LinkedIn elevar, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **LinkedIn elevar** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Domínio de elevar o LinkedIn e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** texto, introduza o **ID de entidade** valor, irá copiar o valor de ID de entidade do Portal do Linkedin explicado posteriormente neste tutorial.

    b. Na **URL de resposta** texto, introduza o **Url de acesso de consumidor de asserção (ACS)** valor, irá copiar o Url de acesso de consumidor de asserção (ACS) valor a partir do Portal do Linkedin explicado posteriormente neste tutorial.

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Domínio de elevar o LinkedIn e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

6. Aplicação de elevar o LinkedIn espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos, em que **nameidentifier** está mapeada com **user.userprincipalname**. Elevar o LinkedIn aplicativo espera nameidentifier seja mapeado com **user.mail**, por isso terá de editar o mapeamento do atributo ao clicar no ícone de edição e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

7. Além dos acima, o LinkedIn elevar o aplicativo espera mais alguns atributos a serem passados na resposta SAML. Na seção de afirmações de utilizador sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir:

    | Name | Atributo de origem|
    | -------| -------------|
    | Departamento | user.department |

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

9. Sobre o **configurar LinkedIn elevar** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-linkedin-elevate-single-sign-on"></a>Configurar o LinkedIn elevar o início de sessão único

1. Numa janela do browser web diferente, início de sessão no seu inquilino de elevar o LinkedIn como administrador.

1. Na **Centro de contas**, clique em **definições globais** sob **definições**. Além disso, selecione **elevação – elevar o teste de AAD** na lista pendente.

    ![Configurar o início de sessão único](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Clique em **ou clique aqui para carregar e copiar campos individuais do formulário** e execute os seguintes passos:

    ![Configurar o início de sessão único](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. Cópia **Id de entidade** e cole-o no **identificador** caixa de texto **configuração básica de SAML** no portal do Azure.

    b. Cópia **Url de acesso de consumidor de asserção (ACS)** e cole-o para o **URL de resposta** caixa de texto **configuração básica de SAML** no portal do Azure.

1. Aceda a **definições de administração do LinkedIn** secção. Carregue o ficheiro XML que transferiu do portal do Azure ao clicar na opção de arquivo XML de carregar.

    ![Configurar o início de sessão único](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Clique em **no** para ativar o SSO. Estado SSO será alterado de **não ligado** para **ligado**

    ![Configurar o início de sessão único](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para elevar o LinkedIn.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **LinkedIn elevar**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **LinkedIn elevar**.

    ![A ligação de elevar o LinkedIn na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-linkedin-elevate-test-user"></a>Criar utilizador de teste de elevar o LinkedIn

A aplicação de elevar LinkedIn suporta apenas em tempo de aprovisionamento de utilizador e de utilizadores de autenticação serão criados no aplicativo automaticamente. Sobre o administrador de definições de página no flip portal LinkedIn elevar o comutador **automaticamente atribuir licenças** para Active Directory apenas em tempo de aprovisionamento e isso irão também atribuir uma licença ao utilizador. Elevar o LinkedIn também suporta o aprovisionamento automático de utilizadores, pode encontrar mais detalhes [aqui](linkedinelevate-provisioning-tutorial.md) sobre como configurar o aprovisionamento automático de utilizadores.

   ![Criar um utilizador de teste do Azure AD](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico elevar o LinkedIn, no painel de acesso, deve ser automaticamente conectado para o LinkedIn, elevar para a qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)