---
title: 'Tutorial: Integração do Active Directory do Azure com o da Palo Alto Networks - abertura | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e da Palo Alto Networks - abertura.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a5ea18d3-3aaf-4bc6-957c-783e9371d0f1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: beb84671db01020f0118502e35128387d36845d6
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361426"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Tutorial: Integração do Active Directory do Azure com o da Palo Alto Networks - abertura

Neste tutorial, saiba como integrar da Palo Alto Networks - abertura com o Azure Active Directory (Azure AD).
Integração da Palo Alto Networks - abertura com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso da Palo Alto Networks - abertura.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Palo Alto Networks - abertura (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com da Palo Alto Networks - abertura, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição de ativado da Palo Alto Networks - abertura início de sessão único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta a abertura da Palo Alto Networks - **SP** e **IDP** iniciada SSO

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Adição da Palo Alto Networks - abertura da Galeria

Para configurar a integração da Palo Alto Networks - abertura para o Azure AD, terá de adicionar da Palo Alto Networks - abertura da galeria, à sua lista de aplicações de SaaS geridas.

**Para adicionar da Palo Alto Networks - abertura a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Palo Alto Networks - abertura**, selecione **Palo Alto Networks - abertura** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Da Palo Alto Networks - abertura na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar o Azure AD início de sessão único da Palo Alto Networks – abertura com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único para o trabalho, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado da Palo Alto Networks - abertura deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único da Palo Alto Networks - abertura, precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar da Palo Alto Networks - abertura Single Sign-On](#configure-palo-alto-networks---aperture-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar da Palo Alto Networks - utilizador de teste de abertura](#create-palo-alto-networks---aperture-test-user)**  - para ter um equivalente da Eduarda Almeida na Palo Alto Networks - abertura que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único da Palo Alto Networks - abertura, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Palo Alto Networks - abertura** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Informações de início de sessão de único da Palo Alto Networks - domínio de abertura e URLs](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Informações de início de sessão de único da Palo Alto Networks - domínio de abertura e URLs](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificador, o URL de resposta e início de sessão real. Contacte [Palo Alto Networks - equipa de suporte de cliente de abertura](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

7. Sobre o **configurar da Palo Alto Networks - abertura** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-palo-alto-networks---aperture-single-sign-on"></a>Configurar da Palo Alto Networks - abertura início de sessão único

1. Numa janela do browser web diferente, início de sessão para Palo Alto Networks - abertura como administrador.

2. Na barra de menus superior, clique em **definições**.

    ![O separador de definições](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

3. Navegue para **APLICATIVO** secção clique **autenticação** à esquerda do menu de formulário.

    ![O separador de autenticação](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
4. Sobre o **autenticação** página execute os seguintes passos:
    
    ![O separador de autenticação](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Verifique a **ativar único início de sessão-On(Supported SSP Providers are Okta, One login)** partir **Single Sign-On** campo.

    b. Na **ID do fornecedor de identidade** caixa de texto, cole o valor de **do Azure AD identificador**, que copiou do portal do Azure.

    c. Clique em **Escolher ficheiro** para carregar o certificado transferido a partir do Azure AD no **certificado do fornecedor de identidade** campo.

    d. Na **URL de SSO de fornecedor de identidade** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    e. Reveja as informações do IdP partir **informações de abertura** secção e transfira o certificado a partir **chave de abertura** campo.

    f. Clique em **Guardar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para Palo Alto Networks - abertura.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **da Palo Alto Networks - abertura**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Palo Alto Networks - abertura**.

    ![Da Palo Alto Networks - ligação de abertura na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-palo-alto-networks---aperture-test-user"></a>Criar da Palo Alto Networks - utilizador de teste de abertura

Nesta secção, vai criar um usuário chamado Eduarda Almeida da Palo Alto Networks - abertura. Trabalhar com [Palo Alto Networks - equipa de suporte de cliente de abertura](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) para adicionar os utilizadores da Palo Alto Networks - plataforma de abertura. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica em da Palo Alto Networks - mosaico de abertura no painel de acesso, deve ser automaticamente conectado para Palo Alto Networks - abertura para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

