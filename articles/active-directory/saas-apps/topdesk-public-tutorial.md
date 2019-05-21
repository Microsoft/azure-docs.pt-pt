---
title: 'Tutorial: Integração do Active Directory do Azure com TOPdesk - público | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e TOPdesk - público.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.openlocfilehash: c4052dd3b4c8f49b19193109eb5e8d2306646960
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65888561"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Tutorial: Integração do Active Directory do Azure com TOPdesk - público

Neste tutorial, saiba como integrar TOPdesk - público com o Azure Active Directory (Azure AD).
Integrar TOPdesk - público com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao TOPdesk - público.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para TOPdesk - público (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com TOPdesk - público, precisa dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* TOPdesk - pública logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta a TOPdesk - Public **SP** iniciada SSO

## <a name="adding-topdesk---public-from-the-gallery"></a>Adicionar TOPdesk - público a partir da Galeria

Para configurar a integração do TOPdesk - público para o Azure AD, terá de adicionar TOPdesk - público a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar TOPdesk - público a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **TOPdesk - Public**, selecione **TOPdesk - público** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![TOPdesk - público na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar o Azure AD início de sessão único com TOPdesk - público com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único para o trabalho, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no TOPdesk - público tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com TOPdesk - público, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar TOPdesk - pública início de sessão único](#configure-topdesk---public-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar TOPdesk - utilizador de teste público](#create-topdesk---public-test-user)**  - para ter um equivalente da Eduarda Almeida na TOPdesk - público que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com TOPdesk - público, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **TOPdesk - público** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4.  Sobre o **configuração básica de SAML** secção, se tiver **ficheiro de metadados do fornecedor de serviços**, execute os seguintes passos:

    >[!NOTE]
    >Obterá o **ficheiro de metadados do fornecedor de serviços** da **configurar TOPdesk - pública Single Sign-On** secção que é explicada mais tarde no tutorial.

    a. Clique em **carregamento de ficheiro de metadados**.
    
    ![Carregar ficheiro de metadados](common/upload-metadata.png)

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    ![Escolha o ficheiro de metadados](common/browse-upload-metadata.png)

    c. Depois do ficheiro de metadados é carregado com êxito, o **identificador** e **URL de resposta** valores ficam automaticamente preenchida na seção de configuração básica de SAML.

    ![Informações de início de sessão de único TOPdesk - domínio público e URLs](common/sp-identifier-reply.png)

    d. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.topdesk.net`

    e. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.topdesk.net/tas/public/login/verify`

    > [!NOTE] 
    > Se o **identificador** e **URL de resposta** valores não obtém automaticamente preenchido, terá de reintroduzi-los manualmente. Para identificador, seguem o padrão, tal como mencionado acima, e obtém o valor de URL de resposta dos **TOPdesk configurar - pública Single Sign-On** secção que é explicada mais tarde no tutorial. O **URL de início de sessão** valor não é real, por isso terá de atualizar o valor com o URL de início de sessão real. Contacte [TOPdesk - equipa de suporte de cliente público](https://help.topdesk.com/saas/enterprise/user/) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configurar TOPdesk - público** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-topdesk---public-single-sign-on"></a>Configurar TOPdesk - pública início de sessão único

1. Inicie sessão no seu **TOPdesk - público** site da empresa como administrador.

2. Na **TOPdesk** menu, clique em **definições**.
   
    ![As definições](./media/topdesk-public-tutorial/ic790598.png "definições")

3. Clique em **definições de início de sessão**.
   
    ![Definições de início de sessão](./media/topdesk-public-tutorial/ic790599.png "definições de início de sessão")

4. Expanda a **definições de início de sessão** e, em seguida, clique **geral**.
   
    ![Gerais](./media/topdesk-public-tutorial/ic790600.png "geral")

5. Na **pública** secção a **início de sessão SAML** configuração secção, execute os seguintes passos:
   
    ![Definições técnicas](./media/topdesk-public-tutorial/ic790601.png "definições técnicas")
   
    a. Clique em **transferir** para transferir o ficheiro de metadados públicos e, em seguida, guarde-o localmente no seu computador.
   
    b. Abra o ficheiro de metadados baixado e, em seguida, localize a **AssertionConsumerService** nó.

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Copiar o **AssertionConsumerService** valor, cole este valor na **URL de resposta** caixa de texto no **configuração básica de SAML** secção.      
   
6. Para criar um ficheiro de certificado, execute os seguintes passos:
    
    ![Certificado](./media/topdesk-public-tutorial/ic790606.png "certificado")
    
    a. Abra o ficheiro de metadados baixado a partir do portal do Azure.
    
    b. Expanda a **RoleDescriptor** nó que tem um **xsi: type** de **inseridas: ApplicationServiceType**.
    
    c. Copie o valor do **X509Certificate** nó.
    
    d. Guardar o copiado **X509Certificate** valor localmente no seu computador num arquivo.

7. Na **pública** secção, clique em **Add**.
    
    ![Início de sessão SAML](./media/topdesk-public-tutorial/ic790625.png "início de sessão SAML")

8. Sobre o **Assistente de configuração de SAML** caixa de diálogo página, execute os seguintes passos:
    
    ![Assistente de configuração de SAML](./media/topdesk-public-tutorial/ic790608.png "Assistente de configuração de SAML")
    
    a. Para carregar o ficheiro de metadados baixado no portal do Azure, em **metadados de Federação**, clique em **procurar**.

    b. Para carregar o ficheiro de certificado, em **certificados RSA ()**, clique em **procurar**.

    c. Para carregar o ficheiro do logótipo da equipe de suporte do TOPdesk, tem em **ícone de logótipo**, clique em **procurar**.

    d. Na **atributo de nome de utilizador** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. Na **nome a apresentar** caixa de texto, escreva um nome para a sua configuração.

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
  
    b. Na **nome de utilizador** tipo de campo brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para TOPdesk - público.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **TOPdesk - público**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **TOPdesk - público**.

    ![TOPdesk - ligação pública na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-topdesk---public-test-user"></a>Criar TOPdesk - utilizador de teste público

Para permitir que os utilizadores do Azure AD inicie sessão no TOPdesk - público, tem de ser aprovisionados em TOPdesk - público. No caso de TOPdesk - públicos, o aprovisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:

1. Inicie sessão no seu **TOPdesk - público** site da empresa como administrador.

2. No menu na parte superior, clique em **TOPdesk \> New \> ficheiros de suporte \> pessoa**.
   
    ![Pessoa](./media/topdesk-public-tutorial/ic790628.png "pessoa")

3. Na caixa de diálogo nova pessoa, execute os seguintes passos:
   
    ![Nova pessoa](./media/topdesk-public-tutorial/ic790629.png "nova pessoa")
   
    a. Clique na guia Geral.

    b. Na **Apelido** caixa de texto, digite o apelido do utilizador, como Simon
 
    c. Selecione um **Site** para a conta.
 
    d. Clique em **Guardar**.

> [!NOTE]
> Pode utilizar qualquer outro TOPdesk - ferramentas de criação de conta de utilizador público ou APIs fornecidas pelo TOPdesk - público para aprovisionar contas de utilizador do Azure AD.

### <a name="test-single-sign-on"></a>Testar início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar o TOPdesk - mosaico de público no painel de acesso, deve ser automaticamente sessão iniciada no TOPdesk - público para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
