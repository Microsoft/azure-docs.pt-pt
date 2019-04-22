---
title: 'Tutorial: Integração do Active Directory do Azure com RingCentral | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: cf7fd46526eec2f4ac295381498eb14a06b2f36c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59697316"
---
# <a name="tutorial-azure-active-directory-integration-with-ringcentral"></a>Tutorial: Integração do Active Directory do Azure com RingCentral

Neste tutorial, saiba como integrar RingCentral com o Azure Active Directory (Azure AD).
Integrar RingCentral no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao RingCentral.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para RingCentral (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com RingCentral, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* RingCentral logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta RingCentral **SP** iniciada SSO

## <a name="adding-ringcentral-from-the-gallery"></a>Adicionando RingCentral da Galeria

Para configurar a integração do RingCentral com o Azure AD, terá de adicionar RingCentral a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar RingCentral a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **RingCentral**, selecione **RingCentral** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![RingCentral na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com RingCentral com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no RingCentral deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com RingCentral, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar RingCentral Single Sign-On](#configure-ringcentral-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste RingCentral](#create-ringcentral-test-user)**  - para ter um equivalente da Eduarda Almeida na RingCentral que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com RingCentral, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **RingCentral** página de integração de aplicações, selecione **início de sessão único**.

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

    c. Depois do ficheiro de metadados é carregado com êxito, o **identificador** e **URL de resposta** valores ficam automaticamente povoada na **configuração básica de SAML** secção.

    ![RingCentral domínio e URLs únicas início de sessão em informações](common/sp-identifier-reply.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL:
    
    | |
    |--|
    | `https://service.ringcentral.com`|
    | `https://service.ringcentral.com.au`|
    | `https://service.ringcentral.co.uk`|
    | `https://service.ringcentral.eu`|

    > [!Note]
    > Obtém o **ficheiro de metadados do fornecedor de serviços** na página de configuração de SSO RingCentral do que é explicada mais tarde no tutorial.

5. Se não tiver **ficheiro de metadados do fornecedor de serviços**, execute os seguintes passos:

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL:

    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    b. Na **identificador** caixa de texto, escreva um URL:

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    c. Na **URL de resposta** caixa de texto, escreva um URL:

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

    ![RingCentral domínio e URLs únicas início de sessão em informações](common/sp-identifier-reply.png)

6. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em botão Copiar para copiar **Url de metadados de Federação de aplicação** e guarde-o no seu computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

### <a name="configure-ringcentral-single-sign-on"></a>Configurar RingCentral Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no RingCentral como um administrador de segurança.

1. Na parte superior, clique em **ferramentas**.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

1. Navegue para **início de sessão único**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

1. Na **início de sessão único** página, em **configuração de SSO** secção, do **passo 1** clique em **editar** e execute os seguintes passos:

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

1. Sobre o **configurar o início de sessão único** página, execute os seguintes passos:

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Clique em **procurar** para carregar o ficheiro de metadados que transferiu a partir do portal do Azure.

    b. Depois de carregar metadados os valores ficam preenchidos automaticamente no **informações gerais de SSO** secção.

    c. Sob **mapeamento do atributo** secção, selecione **atributo de correio eletrónico de mapa para** como `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Clique em **Guardar**.

    e. Partir **passo 2** clique em **transferir** para transferir o **ficheiro de metadados do fornecedor de serviços** e carregá-la no **configuração básica de SAML** secção para preencher o automática a **identificador** e **URL de resposta** valores no portal do Azure.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Na mesma página, navegue até **ativar o SSO** secção e execute os seguintes passos:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    * Selecione **ativar o serviço SSO**.

    * Selecione **permitir que os utilizadores iniciar sessão com credenciais SSO ou RingCentral**.

    * Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo `brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para RingCentral.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **RingCentral**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **RingCentral**.

    ![A ligação de RingCentral na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-ringcentral-test-user"></a>Criar utilizador de teste RingCentral

Nesta secção, vai criar um usuário chamado Eduarda Almeida no RingCentral. Trabalhar com [equipa de suporte de cliente RingCentral](https://success.ringcentral.com/RCContactSupp) para adicionar os utilizadores na plataforma RingCentral. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico RingCentral no painel de acesso, deve ser automaticamente sessão iniciada no RingCentral para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
