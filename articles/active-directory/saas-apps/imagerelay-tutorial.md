---
title: 'Tutorial: Integração do Active Directory do Azure com o reencaminhamento de imagem | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o reencaminhamento de imagem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63fbab5fffbcc30d0242d223fd8a6b5796c2aeae
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901904"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Tutorial: Integração do Active Directory do Azure com o reencaminhamento de imagem

Neste tutorial, saiba como integrar o reencaminhamento de imagem com o Azure Active Directory (Azure AD).
Integrar o reencaminhamento de imagem no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao reencaminhamento de imagem.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o reencaminhamento de imagem (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o reencaminhamento de imagem, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Imagem reencaminhamento logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta de reencaminhamento de imagem **SP** iniciada SSO

## <a name="adding-image-relay-from-the-gallery"></a>Adicionando o reencaminhamento de imagem da Galeria

Para configurar a integração do reencaminhamento de imagem com o Azure AD, terá de adicionar o reencaminhamento de imagem a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o reencaminhamento de imagem da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **reencaminhamento de imagem**, selecione **reencaminhamento de imagem** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Reencaminhamento de imagem na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o reencaminhamento de imagem com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no reencaminhamento de imagem deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o reencaminhamento de imagem, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar a imagem do reencaminhamento do início de sessão único](#configure-image-relay-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de reencaminhamento de imagem](#create-image-relay-test-user)**  - para ter um equivalente da Eduarda Almeida no reencaminhamento de imagem que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o reencaminhamento de imagem, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **reencaminhamento de imagem** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Domínio de reencaminhamento de imagem e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.imagerelay.com/`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente de reencaminhamento de imagem](http://support.imagerelay.com/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar o reencaminhamento de imagem** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-image-relay-single-sign-on"></a>Configurar imagem reencaminhamento início de sessão único

1. Na outra janela do browser, inicie sessão no site da sua empresa reencaminhamento de imagem como um administrador.

2. Na barra de ferramentas na parte superior, clique nas **utilizadores e permissões** carga de trabalho.

    ![Configurar o início de sessão único](./media/imagerelay-tutorial/tutorial_imagerelay_06.png) 

3. Clique em **criar nova permissão**.

    ![Configurar o início de sessão único](./media/imagerelay-tutorial/tutorial_imagerelay_08.png)

4. Na **início de sessão definições único** carga de trabalho, selecione a **este grupo pode apenas iniciar sessão através do início de sessão único** caixa de verificação e, em seguida, clique em **guardar**.

    ![Configurar o início de sessão único](./media/imagerelay-tutorial/tutorial_imagerelay_09.png) 

5. Aceda a **definições da conta**.

    ![Configurar o início de sessão único](./media/imagerelay-tutorial/tutorial_imagerelay_10.png) 

6. Vá para o **início de sessão único em definições** carga de trabalho.

    ![Configurar o início de sessão único](./media/imagerelay-tutorial/tutorial_imagerelay_11.png)

7. Sobre o **definições de SAML** caixa de diálogo, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/imagerelay-tutorial/tutorial_imagerelay_12.png)

    a. Na **URL de início de sessão** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.

    b. Na **URL de fim de sessão** caixa de texto, cole o valor de **URL de fim de sessão** que copiou do portal do Azure.

    c. Como **formato do nome do Id**, selecione **urn: oasis: nomes: tc: SAML:1.1:nameid-formato: endereço de correio eletrónico**.

    d. Como **opções de vinculação de mensagens em fila para pedidos de fornecedor de serviços (imagem de reencaminhamento)**, selecione **POST enlace**.

    e. Sob **x.509 certificado**, clique em **atualizar certificado**.

    ![Configurar o início de sessão único](./media/imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Abrir o certificado transferido no bloco de notas, copie o conteúdo e, em seguida, cole-o para o **x.509 certificado** caixa de texto.

    ![Configurar o início de sessão único](./media/imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. Na **aprovisionamento de utilizadores ativaram** secção, selecione a **ativar o aprovisionamento de utilizadores ativaram**.

    ![Configurar o início de sessão único](./media/imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Selecione o grupo de permissões (por exemplo, **básicos de SSO**) que tem permissão para iniciar sessão apenas por meio de início de sessão único.

    ![Configurar o início de sessão único](./media/imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Clique em **Guardar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para o reencaminhamento de imagem.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **imagem reencaminhamento**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **reencaminhamento de imagem**.

    ![A ligação de reencaminhamento de imagem na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-image-relay-test-user"></a>Criar utilizador de teste de reencaminhamento de imagem

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no reencaminhamento de imagem.

**Para criar um usuário chamado Eduarda Almeida no reencaminhamento de imagem, execute os seguintes passos:**

1. Início de sessão no site da sua empresa reencaminhamento de imagem como administrador.

2. Aceda a **utilizadores e permissões** e selecione **Create SSO User**.

    ![Configurar o início de sessão único](./media/imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. Introduza o **E-Mail**, **FirstName**, **Apelido**, e **empresa** do utilizador que pretende aprovisionar e selecione o grupo de permissões (para exemplo básicos de SSO) que é o grupo que pode iniciar sessão apenas por meio de início de sessão único.

    ![Configurar o início de sessão único](./media/imagerelay-tutorial/tutorial_imagerelay_22.png)

4. Clique em **Criar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de reencaminhamento de imagem no painel de acesso, deve ser automaticamente conectado para o reencaminhamento de imagem para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)