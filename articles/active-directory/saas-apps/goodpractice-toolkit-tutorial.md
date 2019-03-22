---
title: 'Tutorial: Integração do Active Directory do Azure com o Toolkit de GoodPractice | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Kit de ferramentas de GoodPractice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 65b2979d-9e2f-4530-bc08-546975269ebc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd8453c93b4e48a076d8efbe1d438d15d8131fc9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57864410"
---
# <a name="tutorial-azure-active-directory-integration-with-goodpractice-toolkit"></a>Tutorial: Integração do Active Directory do Azure com o Toolkit de GoodPractice

Neste tutorial, saiba como integrar o Kit de ferramentas de GoodPractice com o Azure Active Directory (Azure AD).
Integrar o Kit de ferramentas de GoodPractice no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao GoodPractice Kit de ferramentas.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada ao Kit de ferramentas de GoodPractice (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com GoodPractice Kit de ferramentas, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Kit de ferramentas de GoodPractice logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Kit de ferramentas de GoodPractice suporta **SP** iniciada SSO
* Kit de ferramentas de GoodPractice suporta **Just In Time** aprovisionamento de utilizadores

## <a name="adding-goodpractice-toolkit-from-the-gallery"></a>Adicionando GoodPractice Kit de ferramentas da Galeria

Para configurar a integração do Toolkit de GoodPractice com o Azure AD, terá de adicionar GoodPractice Toolkit a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar GoodPractice Toolkit a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **GoodPractice Toolkit**, selecione **GoodPractice Toolkit** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Kit de ferramentas de GoodPractice na lista de resultados](common/search-new-app.png)
   ## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Toolkit de GoodPractice com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Kit de ferramentas de GoodPractice deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Toolkit de GoodPractice, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar GoodPractice Kit de ferramentas de início de sessão único](#configure-goodpractice-toolkit-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Criar utilizador de teste do Kit de ferramentas de GoodPractice](#create-goodpractice-toolkit-test-user)**  - para ter um equivalente da Eduarda Almeida no Kit de ferramentas de GoodPractice que está ligada à representação de utilizador do Azure AD.
5. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Toolkit de GoodPractice, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **GoodPractice Toolkit** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![GoodPractice Kit de ferramentas de domínio e URLs únicas início de sessão em informações](common/sp-intiated.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://app.goodpractice.net/#/<subscriptionUrl>/s/<locationId> `.

    > [!Note]
    > O valor de URL de início de sessão não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente do Kit de ferramentas de GoodPractice](mailto:support@goodpractice.com) para obter o valor.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configurar GoodPractice Toolkit** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-goodpractice-toolkit-single-sign-on"></a>Configurar o Toolkit de GoodPractice início de sessão único

Para configurar o início de sessão único num **GoodPractice Toolkit** lado, terá de enviar o transferido **XML de metadados de Federação** e adequadas copiados URLs a partir do portal do Azure para [GoodPractice A equipa de suporte do Kit de ferramentas](mailto:support@goodpractice.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

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

Nesta secção, vai ativar Eduarda Almeida utilizar do Azure-início de sessão único, concedendo acesso ao GoodPractice Kit de ferramentas.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **GoodPractice Toolkit**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **GoodPractice Toolkit**.

    ![A ligação do Kit de ferramentas de GoodPractice na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-goodpractice-toolkit-test-user"></a>Criar utilizador de teste do Kit de ferramentas de GoodPractice

Nesta secção, um usuário chamado Eduarda Almeida é criado no Kit de ferramentas de GoodPractice. Kit de ferramentas de GoodPractice suporta **just-in-time aprovisionamento**, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no GoodPractice Toolkit, uma nova é criada quando tentar acessar GoodPractice Kit de ferramentas.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico do Kit de ferramentas de GoodPractice no painel de acesso, deve ser automaticamente conectado ao Kit de ferramentas GoodPractice para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

