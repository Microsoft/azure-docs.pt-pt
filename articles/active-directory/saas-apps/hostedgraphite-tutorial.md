---
title: 'Tutorial: Integração do Active Directory do Azure com Graphite alojado | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Graphite alojado.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a1ac4d7f-d079-4f3c-b6da-0f520d427ceb
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c44b89b66c1908c00e075606d6b0201bd3ea6af6
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56872952"
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Tutorial: Integração do Active Directory do Azure com Graphite alojado

Neste tutorial, saiba como integrar Graphite alojado no Azure Active Directory (Azure AD).
Integrar Graphite alojado no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Graphite alojado.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Graphite alojado (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Graphite alojados, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Alojado Graphite única início de sessão ativada subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Hospedado suporta Graphite **SP e IDP** iniciada SSO
* Hospedado suporta Graphite **Just In Time** aprovisionamento de utilizadores

## <a name="adding-hosted-graphite-from-the-gallery"></a>Adicionando Graphite hospedado da Galeria

Para configurar a integração do Graphite alojado no Azure AD, terá de adicionar Graphite alojada a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Graphite hospedado da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Graphite alojado**, selecione **alojados Graphite** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Graphite alojado na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Graphite alojado com base num utilizador de teste **Eduarda Almeida**.
Para início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Graphite alojado tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com Graphite alojados, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar alojado Graphite início de sessão único](#configure-hosted-graphite-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste alojado Graphite](#create-hosted-graphite-test-user)**  - para ter um equivalente da Eduarda Almeida na Graphite hospedado, o que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Graphite alojado, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), no **alojados Graphite** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Graphite domínio e URLs alojada único informações de início de sessão](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://www.hostedgraphite.com/metadata/<user id>`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://www.hostedgraphite.com/complete/saml/<user id>`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Graphite domínio e URLs alojada único informações de início de sessão](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://www.hostedgraphite.com/login/saml/<user id>/`

    > [!NOTE]
    > Tenha em atenção que estes não são os valores reais. Terá de atualizar estes valores com o identificador real, o URL de resposta e URL de início de sessão. Para obter estes valores, pode ir para acesso -> configuração SAML em seu lado do aplicativo ou o contato [equipa de suporte de alojado Graphite](mailto:help@hostedgraphite.com).

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

7. Na **configurar alojado Graphite** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-hosted-graphite-single-sign-on"></a>Configurar Graphite alojada início de sessão único

1. Início de sessão no seu inquilino Graphite hospedado como um administrador.

2. Vá para o **página de configuração de SAML** na barra lateral (**acesso -> Configuração de SAML**).

    ![Configure o lado de início de sessão na aplicação única](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

3. Confirme estes URls corresponde à sua configuração feita no **configuração básica de SAML** seção do portal do Azure.

    ![Configure o lado de início de sessão na aplicação única](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

4. Na **entidade ou ID do emissor** e **URL de início de sessão de SSO** caixas de texto, cole o valor de **identificador do Azure Ad** e **URL de início de sessão** que copiou no portal do Azure.

    ![Configure o lado de início de sessão na aplicação única](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)

5. Selecione **só de leitura** como **predefinido a função de utilizador**.

    ![Configure o lado de início de sessão na aplicação única](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

6. Abra o certificado com codificação base 64 no bloco de notas transferido a partir do portal do Azure, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado X.509** caixa de texto.

    ![Configure o lado de início de sessão na aplicação única](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

7. Clique em **guardar** botão.

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

Nesta secção, vai ativar Eduarda Almeida utilizar do Azure-início de sessão único, concedendo acesso para Graphite alojado.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **alojado Graphite**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **alojada Graphite**.

    ![A ligação de Graphite alojado na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-hosted-graphite-test-user"></a>Criar utilizador de teste Graphite alojado

Nesta secção, um usuário chamado Eduarda Almeida é criado na Graphite alojado. Graphite alojada suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no Graphite alojado, é criado um novo após a autenticação.

> [!NOTE]
> Se precisar de criar manualmente um utilizador, terá de contactar a equipa de suporte de Graphite hospedado por <mailto:help@hostedgraphite.com>.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Graphite hospedado no painel de acesso, deve ser automaticamente sessão iniciada no Graphite alojado, para que configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

