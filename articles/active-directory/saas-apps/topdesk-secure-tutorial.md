---
title: 'Tutorial: Integração do Active Directory do Azure com TOPdesk - seguro | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e TOPdesk - segura.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a559191bb4e84b3b60529bf20a25e381c72f42a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57840941"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Tutorial: Integração do Active Directory do Azure com TOPdesk - seguro

Neste tutorial, saiba como integrar TOPdesk - proteger com o Azure Active Directory (Azure AD).
Integrar TOPdesk - segura com o Azure AD fornece-lhe as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao TOPdesk - segura.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para TOPdesk - seguro (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com TOPdesk - seguro, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* TOPdesk - seguro logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* TOPdesk - suporta segura **SP** iniciada SSO

## <a name="adding-topdesk---secure-from-the-gallery"></a>Adicionar TOPdesk - seguro a partir da Galeria

Para configurar a integração do TOPdesk - proteger com o Azure AD, terá de adicionar TOPdesk - proteger da galeria, à sua lista de SaaS geridos, as aplicações.

**Para adicionar TOPdesk - seguro a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **TOPdesk - segura**, selecione **TOPdesk - seguro** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![TOPdesk - proteger na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar o Azure AD início de sessão único com TOPdesk – seguro com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único para funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no TOPdesk - seguro tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com TOPdesk - seguro, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar TOPdesk - seguro início de sessão único](#configure-topdesk---secure-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar TOPdesk - utilizador de teste seguro](#create-topdesk---secure-test-user)**  - para ter um equivalente da Eduarda Almeida na TOPdesk - seguro, que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com TOPdesk - proteger, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **TOPdesk - seguro** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Informações de início de sessão de único TOPdesk - seguro de domínio e URLs](common/sp-identifier-reply.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.topdesk.net`

    b. Na **identificador** caixa, escreva um URL com o seguinte padrão: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    c. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.topdesk.net/tas/public/login/saml`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão, identificador e o URL de resposta real. Contacte [TOPdesk - equipa de suporte de cliente proteger](https://www.topdesk.com/us/support/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configurar TOPdesk - seguro** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-topdesk---secure-single-sign-on"></a>Configurar TOPdesk - proteger o início de sessão único

1. Inicie sessão no seu **TOPdesk - seguro** site da empresa como administrador.

2. Na **TOPdesk** menu, clique em **definições**.

    ![As definições](./media/topdesk-secure-tutorial/ic790598.png "definições")

3. Clique em **definições de início de sessão**.

    ![Definições de início de sessão](./media/topdesk-secure-tutorial/ic790599.png "definições de início de sessão")

4. Expanda a **definições de início de sessão** e, em seguida, clique **geral**.

    ![Gerais](./media/topdesk-secure-tutorial/ic790600.png "geral")

5. Na **seguro** secção a **início de sessão SAML** configuração secção, execute os seguintes passos:

    ![Definições técnicas](./media/topdesk-secure-tutorial/ic790855.png "definições técnicas")

    a. Clique em **transferir** para transferir o ficheiro de metadados públicos e, em seguida, guarde-o localmente no seu computador.

    b. Abra o ficheiro de metadados e, em seguida, localize a **AssertionConsumerService** nó.

    ![Serviço de consumidor de asserção](./media/topdesk-secure-tutorial/ic790856.png "serviço de consumidor de asserção")

    c. Copiar o **AssertionConsumerService** valor, cole este valor na caixa de texto no URL de resposta **TOPdesk - proteger o domínio e URLs** secção.

6. Para criar um ficheiro de certificado, execute os seguintes passos:

    ![Certificado](./media/topdesk-secure-tutorial/ic790606.png "certificado")

    a. Abra o ficheiro de metadados baixado a partir do portal do Azure.

    b. Expanda a **RoleDescriptor** nó que tem um **xsi: type** de **inseridas: ApplicationServiceType**.

    c. Copie o valor do **X509Certificate** nó.

    d. Guardar o copiado **X509Certificate** valor localmente no seu computador num arquivo.

7. Na **pública** secção, clique em **Add**.

    ![Adicione](./media/topdesk-secure-tutorial/ic790607.png "adicionar")

8. Sobre o **Assistente de configuração de SAML** caixa de diálogo página, execute os seguintes passos:

    ![Assistente de configuração de SAML](./media/topdesk-secure-tutorial/ic790608.png "Assistente de configuração de SAML")

    a. Para carregar o ficheiro de metadados baixado no portal do Azure, em **metadados de Federação**, clique em **procurar**.

    b. Para carregar o ficheiro de certificado, em **certificados RSA ()**, clique em **procurar**.

    c. Para **(RSA, PKCS8, DER) de chave privada**, pode carregar sua própria chave privada ou pode contactar [TOPdesk - equipa de suporte de cliente proteger](http://www.topdesk.com/us/support) para obter a chave privada.

    d. Para carregar o ficheiro do logótipo da equipe de suporte do TOPdesk, tem em **ícone de logótipo**, clique em **procurar**.

    e. Na **atributo de nome de utilizador** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. Na **nome a apresentar** caixa de texto, escreva um nome para a sua configuração.

    g. Clique em **Guardar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para TOPdesk - segura.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **TOPdesk - seguro**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **TOPdesk - seguro**.

    ![TOPdesk - ligação segura na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-topdesk---secure-test-user"></a>Criar TOPdesk - utilizador de teste seguro

Para permitir que os utilizadores do Azure AD logon TOPdesk - seguro, eles têm de ser aprovisionados em TOPdesk - segura.  
No caso TOPdesk - seguros, aprovisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:

1. Inicie sessão no seu **TOPdesk - seguro** site da empresa como administrador.

2. No menu na parte superior, clique em **TOPdesk \> New \> ficheiros de suporte \> operador**.

    ![Operador](./media/topdesk-secure-tutorial/ic790610.png "operador")

3. Sobre o **operador New** caixa de diálogo, execute os seguintes passos:

    ![Novo operador](./media/topdesk-secure-tutorial/ic790611.png "novo operador")

    a. Clique nas **gerais** separador.

    b. Na **Apelido** caixa de texto, como o tipo de apelido do utilizador **Simon**.

    c. Selecione um **Site** para a conta na **localização** secção.

    d. Na **nome de início de sessão** caixa de texto da **TOPdesk início de sessão** secção, escreva um nome de início de sessão para o utilizador.

    e. Clique em **Guardar**.

> [!NOTE]
> Pode usar qualquer outra TOPdesk - ferramentas de criação de conta de usuário segura ou APIs fornecidas pelos TOPdesk - segura para aprovisionar contas de utilizador do AAD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica em TOPdesk - mosaico seguro no painel de acesso, deve ser automaticamente sessão iniciada no TOPdesk - segura para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

