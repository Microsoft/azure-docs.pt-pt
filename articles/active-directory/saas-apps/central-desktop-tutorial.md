---
title: 'Tutorial: Integração do Active Directory do Azure com o ambiente de trabalho Central | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o ambiente de trabalho Central.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50ef84c9795065d4186fae9542c18d4df06c85c2
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56880381"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Tutorial: Integração do Active Directory do Azure com o ambiente de trabalho Central

Neste tutorial, saiba como integrar o ambiente de trabalho Central com o Azure Active Directory (Azure AD).
Integrar o ambiente de trabalho Central com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso à área de trabalho Central.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para área de trabalho Central (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o ambiente de trabalho Central, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Central área de trabalho de logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a área de trabalho central **SP** iniciada SSO

## <a name="adding-central-desktop-from-the-gallery"></a>Adicionando o ambiente de trabalho Central da Galeria

Para configurar a integração do ambiente de trabalho Central para o Azure AD, terá de adicionar área de trabalho Central a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar área de trabalho Central a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Central Desktop**, selecione **ambiente de trabalho Central** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Ambiente de trabalho central na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o ambiente de trabalho Central com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Central Desktop deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o ambiente de trabalho Central, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Central Desktop início de sessão único](#configure-central-desktop-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do ambiente de trabalho Central](#create-central-desktop-test-user)**  - para ter um equivalente da Eduarda Almeida na área de trabalho Central que está ligada à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o ambiente de trabalho Central, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), no **ambiente de trabalho Central** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Central de domínio de ambiente de trabalho e URLs único informações de início de sessão](common/sp-identifier-reply.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.centraldesktop.com`

    b. Na **identificador** caixa, escreva um URL com o seguinte padrão:
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|
    | |

    c. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.centraldesktop.com/saml2-assertion.php`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão, identificador e o URL de resposta real. Contacte [equipa de suporte de cliente de ambiente de trabalho Central](https://imeetcentral.com/contact-us) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (bruto)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificateraw.png)

6. Sobre o **configurar o ambiente de trabalho Central** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-central-desktop-single-sign-on"></a>Configurar Central Desktop início de sessão único

1. Inicie sessão no seu **Central Desktop** inquilino.

2. Aceda a **definições**. Selecione **avançadas**e, em seguida, selecione **início de sessão único**.

    ![Configuração - Advanced](./media/central-desktop-tutorial/ic769563.png "configuração - avançada")

3. Sobre o **início de sessão único em definições** página, siga os passos seguintes:

    ![Único configurações de logon](./media/central-desktop-tutorial/ic769564.png "início de sessão único em definições")

    a. Selecione **ativar SAML v2 início de sessão único**.

    b. Na **URL de SSO** caixa, cole a **do Azure Ad identificador** valor que copiou do portal do Azure.

    c. Na **URL de início de sessão de SSO** caixa, cole a **URL de início de sessão** valor que copiou do portal do Azure.

    d. Na **URL de fim de sessão de SSO** caixa, cole a **URL de fim de sessão** valor que copiou do portal do Azure.

4. Na **método de verificação de assinatura de mensagem** secção, siga os passos seguintes:

    ![Método de verificação de assinatura da mensagem](./media/central-desktop-tutorial/ic769565.png "método de verificação de assinatura de mensagem")
    
    a. Selecione **Certificado**.

    b. Na **certificado SSO** lista, selecione **RSH SHA256**.

    c. Abra o seu certificado transferido no bloco de notas. Em seguida, copie o conteúdo de certificado e colá-lo no **certificado SSO** campo.

    d. Selecione **apresentar uma ligação à sua página de início de sessão SAMLv2**.

    e. Selecione **atualização**.

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

Nesta secção, vai ativar Eduarda Almeida a utilizar do Azure-início de sessão único, conceder acesso à área de trabalho Central.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **ambiente de trabalho Central**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Central Desktop**.

    ![A ligação de ambiente de trabalho Central na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-central-desktop-test-user"></a>Criar utilizador de teste do ambiente de trabalho Central

Para utilizadores do Azure AD possam iniciar sessão, tem de ser aprovisionados no aplicativo de área de trabalho Central. Esta secção descreve como criar contas de utilizador do Azure AD no ambiente de trabalho Central.

> [!NOTE]
> Para aprovisionar contas de utilizador do Azure AD, pode utilizar quaisquer outras ferramentas de criação da conta de utilizador de ambiente de trabalho Central ou APIs fornecidas pelo ambiente de trabalho Central.

**Para aprovisionar contas de utilizador ao ambiente de trabalho Central:**

1. Inicie sessão no seu inquilino de ambiente de trabalho Central.

2. Selecione **pessoas** e, em seguida, selecione **adicionar membros internos**.

    ![As pessoas](./media/central-desktop-tutorial/ic781051.png "pessoas")

3. Na **endereço de E-Mail de novos membros** , escreva uma conta do Azure AD que pretende aprovisionar e, em seguida, selecione **próxima**.

    ![Endereços de novos membros de correio eletrónico](./media/central-desktop-tutorial/ic781052.png "endereços dos novos membros de E-Mail")

4. Selecione **membro ou membros adicionar interno**.

    ![Adicionar membro interno](./media/central-desktop-tutorial/ic781053.png "Adicionar membro interno")
  
   > [!NOTE]
   > Os utilizadores que adicionar recebem um e-mail que inclui uma ligação de confirmação para a ativação de suas contas.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de ambiente de trabalho Central no painel de acesso, deve ser automaticamente conectado na área de trabalho Central para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
