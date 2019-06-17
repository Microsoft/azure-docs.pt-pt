---
title: 'Tutorial: Integração do Active Directory do Azure com os laboratórios Fluxx | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Fluxx laboratórios.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: e624520a9d1f39bc8115ac72e9df0398065928f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67102380"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Tutorial: Integração do Active Directory do Azure com os laboratórios Fluxx

Neste tutorial, saiba como integrar os laboratórios de Fluxx com o Azure Active Directory (Azure AD).
Integrar os laboratórios de Fluxx no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso aos laboratórios Fluxx.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para laboratórios de Fluxx (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Fluxx laboratórios, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Laboratórios de Fluxx único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a laboratórios Fluxx **IDP** iniciada SSO

## <a name="adding-fluxx-labs-from-the-gallery"></a>Adicionando Fluxx laboratórios da Galeria

Para configurar a integração de Fluxx laboratórios com o Azure AD, terá de adicionar Fluxx laboratórios a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Fluxx laboratórios a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Fluxx laboratórios**, selecione **Fluxx laboratórios** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Laboratórios de Fluxx na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com laboratórios Fluxx com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado nos laboratórios de Fluxx deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Fluxx laboratórios, precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Fluxx laboratórios de início de sessão único](#configure-fluxx-labs-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de laboratórios de Fluxx](#create-fluxx-labs-test-user)**  - para ter um equivalente da Eduarda Almeida nos laboratórios de Fluxx que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com laboratórios Fluxx, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Fluxx laboratórios** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **definir a segurança de início de sessão único com o SAML** página, execute os seguintes passos:

    ![Fluxx laboratórios de domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:

    | Ambiente | Padrão de URL|
    |-------------|------------|
    | Produção | `https://<subdomain>.fluxx.io` |
    | Pré-produção | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:

    | Ambiente | Padrão de URL|
    |-------------|------------|
    | Produção | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Pré-produção | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real e o URL de resposta. Contacte [equipa de suporte de cliente de laboratórios Fluxx](mailto:travis@fluxxlabs.com) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **montar laboratórios de Fluxx** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-fluxx-labs-single-sign-on"></a>Configurar Fluxx laboratórios início de sessão único

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Fluxx laboratórios como administrador.

2. Selecione **administrador** abaixo a **definições** secção.

    ![Configuração de laboratórios Fluxx](./media/fluxxlabs-tutorial/config1.png)

3. No painel administração, selecione **Plug-ins** > **integrações** e, em seguida, selecione **SSO-(Disabled) de SAML**

    ![Configuração de laboratórios Fluxx](./media/fluxxlabs-tutorial/config2.png)

4. Na secção de atributo, execute os seguintes passos:

    ![Configuração de laboratórios Fluxx](./media/fluxxlabs-tutorial/config3.png)

    a. Selecione o **SAML SSO** caixa de verificação.

    b. Na **caminho do pedido** caixa de texto, tipo **saml/auth/** .

    c. Na **caminho de retorno de chamada** caixa de texto, tipo **/auth/saml/callback**.

    d. Na **asserção consumidor serviço Url(Single Sign-On URL)** caixa de texto, introduza o **URL de resposta** valor, que introduziu no portal do Azure.

    e. Na **público-alvo (ID de entidade de SP)** caixa de texto, introduza o **identificador** valor, que introduziu no portal do Azure.

    f. Na **URL de destino de SSO de fornecedor de identidade** caixa de texto, colar a **URL de início de sessão** valor, que copiou do portal do Azure.

    g. Abra o certificado com codificação base 64 no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado do fornecedor de identidade** caixa de texto.

    h. Na **identificador de nome de formato** caixa de texto, introduza o valor `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    i. Clique em **Guardar**.

    > [!NOTE]
    > Assim que o conteúdo salvo, o campo será apresentado em branco para segurança, mas o valor foi guardado na configuração.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso aos laboratórios Fluxx.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Fluxx laboratórios**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Fluxx laboratórios**.

    ![A ligação de Fluxx laboratórios na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-fluxx-labs-test-user"></a>Criar utilizador de teste Fluxx laboratórios

Para ativar a utilizadores do Azure AD iniciar sessão no Fluxx laboratórios, tem de ser aprovisionados como Fluxx laboratórios. No caso de Fluxx laboratórios, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa Fluxx laboratórios como um administrador.

2. Clique no apresentado a seguir **ícone**.

    ![Configuração de laboratórios Fluxx](./media/fluxxlabs-tutorial/config6.png)

3. No dashboard, clique no abaixo apresentado ícone para abrir o **novas pessoas** cartão.

    ![Configuração de laboratórios Fluxx](./media/fluxxlabs-tutorial/config4.png)

4. Sobre o **novas pessoas** secção, execute os seguintes passos:

    ![Configuração de laboratórios Fluxx](./media/fluxxlabs-tutorial/config5.png)

    a. Laboratórios de Fluxx utilizam o e-mail como o identificador exclusivo para inícios de sessão do SSO. Preencher o **UID de SSO** campo com o endereço de e-mail do utilizador, que corresponde ao endereço de e-mail, que estão a utilizar como início de sessão com o SSO.

    b. Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de laboratórios de Fluxx no painel de acesso, deve ser automaticamente conectado aos laboratórios Fluxx para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

