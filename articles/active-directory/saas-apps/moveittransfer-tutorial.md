---
title: 'Tutorial: Integração do Active Directory do Azure com transferência de MOVEit - integração do Azure AD | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e transferência de MOVEit - integração do Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 2cd63069a584ccef05236f925f99c3740c6a3402
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759547"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Tutorial: Integração do Active Directory do Azure com transferência de MOVEit - integração do Azure AD

Neste tutorial, saiba como integrar a transferência de MOVEit - integração do Azure AD com o Azure Active Directory (Azure AD).
Integração de transferência de MOVEit - integração do Azure AD com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD, quem tem acesso a transferência de MOVEit - integração do Azure AD.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para transferência de MOVEit - integração do Azure AD (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure com transferência de MOVEit - integração do Azure AD, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Transferência de MOVEit - Azure AD integração logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Transferência de MOVEit - integração do Azure AD suporta **SP** iniciada SSO

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Adição de transferência de MOVEit - integração do Azure AD da Galeria

Para configurar a integração de transferência de MOVEit - integração do Azure AD para o Azure AD, terá de adicionar a transferência de MOVEit - integração do AD do Azure da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar a transferência de MOVEit - integração do Azure AD a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **MOVEit transferência - integração do Azure AD**, selecione **MOVEit transferência - integração do Azure AD** no painel de resultados, em seguida, clique em **Add** botão para adicionar o aplicação.

     ![Transferência de MOVEit - integração do Azure AD na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar o Azure AD início de sessão único com transferência de MOVEit - integração do Azure AD com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único para o trabalho, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na transferência de MOVEit - integração do Azure AD tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com transferência de MOVEit - integração do Azure AD, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar a transferência de MOVEit - integração do Azure AD, início de sessão único](#configure-moveit-transfer---azure-ad-integration-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar a transferência de MOVEit - utilizador de teste de integração do Azure AD](#create-moveit-transfer---azure-ad-integration-test-user)**  - para ter um equivalente da Eduarda Almeida na transferência de MOVEit - integração do Azure AD que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com transferência de MOVEit - integração do Azure AD, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **MOVEit transferência - integração do Azure AD** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se tiver **ficheiro de metadados do fornecedor de serviços**, execute os seguintes passos:

    a. Clique em **carregamento de ficheiro de metadados**.

    ![Carregar ficheiro de metadados](common/upload-metadata.png)

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    ![Escolha o ficheiro de metadados](common/browse-upload-metadata.png)

    c. Depois do ficheiro de metadados é carregado com êxito, o **identificador** e **URL de resposta** valor é automaticamente preenchido no **configuração básica de SAML** secção:

    ![Transferência de MOVEit - integração do Azure AD e URLs de domínio únicas início de sessão em informações](common/sp-identifier-reply.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://contoso.com`

    > [!NOTE]
    > O **URL de início de sessão** valor não é real. Atualize o valor com o URL de início de sessão real. Contacte [MOVEit transferência - integração do Azure AD suporte ao cliente](https://community.ipswitch.com/s/support) equipe para obter o valor. Pode baixar o **ficheiro de metadados do fornecedor de serviço** da **URL de metadados do fornecedor de serviço** que é explicado posteriormente no **configurar MOVEit Transfer - integração do Azure AD único Início de sessão** secção do tutorial. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configurar a transferência de MOVEit - integração do Azure AD** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-moveit-transfer---azure-ad-integration-single-sign-on"></a>Configurar a transferência de MOVEit - integração do Azure AD, início de sessão único

1. Inicie sessão seu inquilino de transferência de MOVEit como administrador.

2. No painel de navegação esquerdo, clique em **definições**.

    ![Lado de secção na aplicação de definições](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

3. Clique em **início de sessão único** link, que está sob **políticas de segurança-autenticação de utilizador >**.

    ![Lado de políticas na aplicação de segurança](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

4. Clique na ligação de URL de metadados para transferir o documento de metadados.

    ![URL de metadados do fornecedor de serviço](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
    * Certifique-se **entityID** corresponde ao **identificador** no **configuração básica de SAML** secção.
    * Certifique-se **AssertionConsumerService** corresponde a localização do URL **URL de resposta** no **configuração básica de SAML** secção.
    
    ![Configure o lado de início de sessão na aplicação única](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

5. Clique em **Add Identity Provider** botão para adicionar um novo fornecedor de identidade federada.

    ![Adicionar fornecedor de identidade](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

6. Clique em **procurar...**  para selecionar o ficheiro de metadados que transferiu a partir do portal do Azure, em seguida, clique em **Add Identity Provider** para carregar o ficheiro transferido.

    ![Fornecedor de identidade SAML](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

7. Selecione "**Sim**" como **ativado** no **Federado de editar as definições do fornecedor de identidade...**  página e clique em **guardar**.

    ![Definições do fornecedor de identidade federada](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

8. Na **editar federado identidade de utilizador as definições do fornecedor** página, efetue as seguintes ações:
    
    ![Editar definições do fornecedor de identidade federada](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Selecione **NameID de SAML** como **nome de início de sessão**.
    
    b. Selecione **outras** como **nome completo** e, no **nome de atributo** caixa de texto, coloque o valor: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Selecione **outras** como **E-Mail** e, no **nome de atributo** caixa de texto, coloque o valor: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Selecione **Sim** como **Auto-criar conta no início de sessão**.
    
    e. Clique em **guardar** botão.

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

Nesta secção, vai ativar Eduarda Almeida a utilizar do Azure-início de sessão único, conceder acesso à transferência de MOVEit - integração do Azure AD.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **MOVEit transferência - integração do Azure AD**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **MOVEit transferência - integração do Azure AD**.

    ![A transferência de MOVEit - integração do Azure AD link na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Criar a transferência de MOVEit - utilizador de teste de integração do Azure AD

O objetivo desta secção é criar um utilizador chamado Eduarda Almeida na transferência de MOVEit - integração do Azure AD. Transferência MOVEit - integração do Azure AD suporta o aprovisionamento just-in-time, que tiver ativado. Não existe nenhum item de ação para nesta secção. Um novo utilizador é criado durante uma tentativa de aceder a transferência de MOVEit - integração do Azure AD, se não existir ainda.

>[!NOTE]
>Se precisar de criar manualmente um utilizador, terá de contactar o [MOVEit transferência - equipa de suporte de cliente de integração do Azure AD](https://community.ipswitch.com/s/support).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica na transferência de MOVEit - mosaico de integração do Azure AD no painel de acesso, deve estar automaticamente conectado à transferência de MOVEit - integração do Azure AD para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

