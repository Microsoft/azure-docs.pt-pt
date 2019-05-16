---
title: 'Tutorial: Integração do Active Directory do Azure com o Procore SSO | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Procore SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4be92cae0042da7341b716a6c3c497b6248eed6a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "65745042"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Tutorial: Integração do Active Directory do Azure com o Procore SSO

Neste tutorial, saiba como integrar o Procore SSO com o Azure Active Directory (Azure AD).
Integrar o Procore SSO no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Procore SSO.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Procore SSO (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Procore SSO, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Procore SSO logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta procore SSO **IDP** iniciada SSO

## <a name="adding-procore-sso-from-the-gallery"></a>Adicionando Procore SSO da Galeria

Para configurar a integração do Procore SSO para o Azure AD, terá de adicionar Procore SSO partir da galeria, à sua lista de aplicações de SaaS geridas.

**Para adicionar Procore SSO a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Procore SSO**, selecione **Procore SSO** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![SSO procore na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Procore SSO com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Procore SSO tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com o Procore SSO, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Procore SSO início de sessão único](#configure-procore-sso-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de Procore SSO](#create-procore-sso-test-user)**  - para ter um equivalente da Eduarda Almeida na SSO Procore que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Procore SSO, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Procore SSO** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, o utilizador não tem de realizar qualquer passo como a aplicação já está pré-integrada com o Azure.

    ![Informações de início de sessão de único procore SSO domínio e URLs](common/preintegrated.png)

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configurar o Procore SSO** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-procore-sso-single-sign-on"></a>Configurar o Procore SSO início de sessão único

1. Para configurar o início de sessão único num **Procore SSO** lado, inicie sessão para o site da sua empresa procore como administrador.

2. No menu de caixa de ferramentas para baixo, clique em **administrador** para abrir a página de definições de SSO.

    ![Configurar o início de sessão único](./media/procoresso-tutorial/procore_tool_admin.png)

3. Cole os valores nas caixas, conforme descrito abaixo-

    ![Configurar o início de sessão único](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. Na **único início de sessão no URL do emissor** texto caixa, cole o valor de **do Azure AD identificador** que copiou do portal do Azure.

    b. Na **início de sessão SAML no URL de destino** caixa, cole o valor de **URL de início de sessão** que copiou do portal do Azure.

    c. Agora, abra a **XML de metadados de Federação** baixado anteriormente no portal do Azure e copiar o certificado na marca com o nome **X509Certificate**. Cole o valor copiado para o **Single Sign On x509 certificado** caixa.

4. Clique em **Guardar Alterações**.

5. Depois destas definições, precisa enviar o **nome de domínio** (por exemplo, de **contoso.com**) através do qual está a iniciar sessão em Procore para o [equipa de suporte Procore](https://support.procore.com/) e eles serão Ative o SSO federado para esse domínio.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para Procore SSO.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Procore SSO**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Procore SSO**.

    ![A ligação de Procore SSO na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-procore-sso-test-user"></a>Criar utilizador de teste de Procore SSO

Siga os passos abaixo para criar um utilizador de teste Procore no lado de Procore SSO.

1. Inicie sessão no site da sua empresa procore como um administrador.    

2. No menu de caixa de ferramentas para baixo, clique em **Directory** para abrir a página do diretório da empresa.

    ![Configurar o início de sessão único](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Clique em **adicionar uma pessoa** executar a opção para abrir o formulário e introduza seguindo as opções,

    ![Configurar o início de sessão único](./media/procoresso-tutorial/Procore_user_add.png)

    a. Na **nome próprio** caixa de texto, nome do usuário do tipo como **Eduarda**.

    b. Na **Apelido** textbox, o apelido do utilizador de tipo, como **Simon**.

    c. Na **endereço de E-Mail** caixa de texto, como o endereço de e-mail do utilizador do tipo BrittaSimon@contoso.com.

    d. Selecione **modelo de permissão** como **aplicar o modelo de permissão mais tarde**.

    e. Clique em **Criar**.

4. Verificar e atualizar os detalhes do contacto da recém-adicionada.

    ![Configurar o início de sessão único](./media/procoresso-tutorial/Procore_user_check.png)

5. Clique em **guardar e enviar convite** (se um convite através de correio é necessário) ou **guardar** (guardar diretamente) para concluir o registo de utilizador.
    
    ![Configurar o início de sessão único](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="test-single-sign-on"></a>Testar início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de Procore SSO no painel de acesso, deve ser automaticamente sessão iniciada no Procore SSO para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

