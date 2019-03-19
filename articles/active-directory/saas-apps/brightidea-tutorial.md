---
title: 'Tutorial: Integração do Active Directory do Azure com Brightidea | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Brightidea.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3adae3e0-f43b-492f-b373-6a512d2d6046
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc45c63d3168a15b4701dcaff7bb531f759d47ad
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57888711"
---
# <a name="tutorial-azure-active-directory-integration-with-brightidea"></a>Tutorial: Integração do Active Directory do Azure com Brightidea

Neste tutorial, saiba como integrar Brightidea com o Azure Active Directory (Azure AD).
Integrar Brightidea no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Brightidea.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Brightidea (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Brightidea, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Brightidea logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.


* Suporta Brightidea **SP e IDP** iniciada SSO
* Suporta Brightidea **Just In Time** aprovisionamento de utilizadores


## <a name="adding-brightidea-from-the-gallery"></a>Adicionando Brightidea da Galeria

Para configurar a integração do Brightidea com o Azure AD, terá de adicionar Brightidea a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Brightidea a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Brightidea**, selecione **Brightidea** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Brightidea na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Brightidea com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Brightidea deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Brightidea, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Brightidea Single Sign-On](#configure-brightidea-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Brightidea](#create-brightidea-test-user)**  - para ter um equivalente da Eduarda Almeida na Brightidea que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Brightidea, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Brightidea** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Na **configuração básica de SAML** secção, se tiver **ficheiro de metadados do fornecedor de serviços** e deseja configurar no **IDP** intiated modo execute os seguintes passos:

    a. Clique em **carregamento de ficheiro de metadados**.

    ![Carregar ficheiro de metadados](common/upload-metadata.png)

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    ![Escolha o ficheiro de metadados](common/browse-upload-metadata.png)

    c. Depois do ficheiro de metadados é carregado com êxito, o **identificador** e **URL de resposta** automaticamente povoada na caixa de texto do Brightidea secção de introdução de valores:

    ![image](common/idp-intiated.png)

    > [!Note]
    > Se o **identificador** e **URL de resposta** valores não obter polulated automática, em seguida, preencha os valores manualmente de acordo com seus requisitos.

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![image](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<SUBDOMAIN>.brightidea.com`

4. No **configuração de início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configurar Brightidea** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-brightidea-single-sign-on"></a>Configurar Brightidea Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no Brightidea com as credenciais de administrador.

2. Para obter a funcionalidade SSO no seu sistema Brightidea, navegue até **configuração do Enterprise** -> **separador autenticação**. Aí, verá dois separadores de sub-rotina: Seleção de autenticação e perfis SAML.

    ![Configuração de Brightidea](./media/brightidea-tutorial/configure1.png)

3. Selecione **seleção de autenticação**. Por predefinição, apenas mostra dois métodos padrão: Início de sessão Brightidea e registo. Quando um método SSO adicionado, irá aparecer na lista.

    ![Configuração de Brightidea](./media/brightidea-tutorial/configure2.png)

4. Selecione **perfis de SAML** e execute os seguintes passos:

    ![Configuração de Brightidea](./media/brightidea-tutorial/configure3.png)

    a. Clique nas **transferir metadados** e carregar no **configuração básica de SAML** secção no portal do Azure.

    b. Clique no **adicionar novo** botão sob os **definição de fornecedor de identidade** e execute os seguintes passos:
    
    ![Configuração de Brightidea](./media/brightidea-tutorial/configure4.png)
    
   * Introduza o **nome do perfil SAML** , como por exemplo, de `Azure Ad SSO`
    
   * Para **carregar metadados**, clique em Escolher ficheiro e carregue o ficheiro de metadados baixado do portal do Azure.

     > [!NOTE]
     > Depois de carregar o ficheiro de metadados, os campos restantes **único início de sessão no serviço, o emissor do fornecedor de identidade, carregar chave pública** será preenchida automaticamente.

   * Na **E-Mail** caixa de texto, introduza o valor como `mail`.
     
   * Na **pseudónimo** caixa de texto, introduza o valor como `givenName`.
     
   * Clique em **guardar alterações**.  

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** , insira **BrittaSimon**.
  
    b. Na **nome de utilizador** , digite **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Brightidea.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Brightidea**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Brightidea**.

    ![A ligação de Brightidea na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-brightidea-test-user"></a>Criar utilizador de teste Brightidea

Nesta secção, um usuário chamado Eduarda Almeida é criado na Brightidea. Brightidea suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no Brightidea, é criado um novo após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Brightidea no painel de acesso, deve ser automaticamente sessão iniciada no Brightidea para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

