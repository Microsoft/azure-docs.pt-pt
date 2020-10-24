---
title: 'Tutorial: Integração do Azure Ative Directory com a TOPdesk - Público Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o TOPdesk - Público.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.openlocfilehash: 3688dc2db67c0c1343a74be4652cb52466ef762e
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520443"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Tutorial: Integração do Diretório Ativo Azure com a TOPdesk - Público

Neste tutorial, aprende-se a integrar o TOPdesk - Público com o Azure Ative Directory (Azure AD).
Integração TOPdesk - Público com Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao TOPdesk - Público.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no TOPdesk - Public (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a TOPdesk - Público, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* TOPdesk - Assinatura individual ativada pelo público

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* TOPdesk - Apoios públicos **SP** iniciou SSO

## <a name="adding-topdesk---public-from-the-gallery"></a>Adicionar TOPdesk - Público da galeria

Para configurar a integração do TOPdesk - Público em Azure AD, é necessário adicionar TOPdesk - Público da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar TOPdesk - Público da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **TOPdesk - Público**, selecione **TOPdesk - Público** do painel de resultados e depois clique em Adicionar botão **adicionar** a aplicação.

     ![TOPdesk - Público na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com TOPdesk - Público com base em um utilizador de teste chamado **Britta Simon**.
Para um único sign-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na TOPdesk - O público precisa de ser estabelecido.

Para configurar e testar o Azure AD com TOPdesk - Público, tem de completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configurar o TOPdesk - Sign-On único público](#configure-topdesk---public-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Criar TOPdesk - utilizador de teste público](#create-topdesk---public-test-user)** - para ter uma contrapartida de Britta Simon em TOPdesk - Público que está ligada à representação AD Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar a Azure AD um único sinal de inscrição com TOPdesk - Público, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página **TOPdesk -** Integração de aplicações públicas, selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4.  Na secção **de Configuração Básica SAML,** se tiver **um ficheiro de metadados do Fornecedor de Serviços,** execute os seguintes passos:

    >[!NOTE]
    >Você receberá o **ficheiro de metadados do Fornecedor** de Serviços da secção **Configurar TOPdesk - Public Single Sign-On,** que é explicado mais tarde no tutorial.

    a. Clique **em Carregar o ficheiro de metadados**.
    
    ![Carregar ficheiro de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o ficheiro de metadados e clique em **Upload**.

    ![escolher arquivo de metadados](common/browse-upload-metadata.png)

    c. Após o carregamento com sucesso do ficheiro de metadados, os valores de URL **de identificação** e **resposta** são preenchidos automaticamente na secção de Configuração BÁSICA SAML.

    ![TOPdesk - Domínio Público e URLs informações únicas de súpr no sinal](common/sp-identifier-reply.png)

    d. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<companyname>.topdesk.net`

    e. Na caixa de texto DO URL do **Identifier,** preencha o URL de metadados TOPdesk que pode obter a partir da configuração TOPdesk. Deve utilizar o seguinte padrão: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    f. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > Se os valores de URL **de identificação** e **resposta** não forem preenchidos automaticamente, é necessário inseri-los manualmente. Para o Identificador, siga o padrão acima mencionado e obtenha o valor URL de resposta a partir da secção **Configure TOPdesk - Sign-On único público,** que é explicada mais tarde no tutorial. O valor **URL de inscrição** não é real, pelo que é necessário atualizar o valor com o URL Sign-On real. Contacte [a TOPdesk - Equipa de apoio ao Cliente Público](https://help.topdesk.com/saas/enterprise/user/) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na **secção set up TOPdesk - Secção pública,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-topdesk---public-single-sign-on"></a>Configure TOPdesk - Sign-On De Único Público

1. Inscreva-se no seu **SITE TOPdesk - Empresa Pública** como administrador.

2. No menu **TOPdesk,** clique em **Definições**.
   
    ![Definições](./media/topdesk-public-tutorial/ic790598.png "Definições")

3. Clique em **Definições de Início**de Sessão .
   
    ![Definições de início de sessão](./media/topdesk-public-tutorial/ic790599.png "Definições de início de sessão")

4. Expandir o menu **'Definições de início de sessão'** e, em seguida, clicar em **Geral**.
   
    ![Geral](./media/topdesk-public-tutorial/ic790600.png "Geral")

5. Na secção **Pública** da secção de configuração de **login DA SAML,** execute os seguintes passos:
   
    ![Definições Técnicas](./media/topdesk-public-tutorial/ic790601.png "Definições Técnicas")
   
    a. Clique **em Baixar** para descarregar o ficheiro de metadados públicos e guarde-o localmente no seu computador.
   
    b. Abra o ficheiro de metadados descarregado e, em seguida, localize o nó **De Serviço de Afirmação.**

    ![Serviço De Afirmação](./media/topdesk-public-tutorial/ic790619.png "Serviço De Afirmação")
   
    c. Copie o valor **AfirmaçãoConsumerService,** cole este valor na caixa de texto **URL de resposta** na secção **configuração BÁSICA SAML.**      
   
6. Para criar um ficheiro de certificado, execute os seguintes passos:
    
    ![Certificado](./media/topdesk-public-tutorial/ic790606.png "Certificado")
    
    a. Abra o ficheiro de metadados descarregado do portal Azure.
    
    b. Expandir o nó **RoleDescriptor** que tem um **xsi:tipo** de **fed:ApplicationServiceType**.
    
    c. Copie o valor do nó **X509Certificado.**
    
    d. Guarde o valor **X509Certificado** copiado localmente no seu computador num ficheiro.

7. Na secção **Público,** clique **em Adicionar**.
    
    ![SAML Login](./media/topdesk-public-tutorial/ic790625.png "SAML Login")

8. Na página de diálogo **do assistente de configuração SAML,** execute os seguintes passos:
    
    ![Assistente de Configuração SAML](./media/topdesk-public-tutorial/ic790608.png "Assistente de Configuração SAML")
    
    a. Para fazer o upload do seu ficheiro de metadados descarregado a partir do portal Azure, no portal **Federation Metadas,** clique em **Procurar**.

    b. Para fazer o upload do seu arquivo de certificado, em **Certificado (RSA)**, clique em **Procurar**.

    c. Para fazer o upload do ficheiro de logotipo que obteve da equipa de suporte TOPdesk, no **ícone do logotipo,** clique em **Procurar**.

    d. Na caixa de texto do **nome do utilizador,** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    e. Na caixa de texto **do nome do Visor,** escreva um nome para a sua configuração.

    f. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **do nome do utilizador** brittasimon@yourcompanydomain.extension . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao TOPdesk - Público.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **TOPdesk - Público**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **TOPdesk - Public**.

    ![O TOPdesk - Ligação pública na lista de Candidaturas](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-topdesk---public-test-user"></a>Criar TOPdesk - Utilizador de teste público

Para permitir que os utilizadores da Azure AD assinem na TOPdesk - Público, devem ser a provisionados na TOPdesk - Público. No caso da TOPdesk - Público, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento do utilizador, execute os seguintes passos:

1. Inscreva-se no seu **SITE TOPdesk - Empresa Pública** como administrador.

2. No menu em cima, clique em **TOPdesk \> New Support Files \> \> Person**.
   
    ![Pessoa](./media/topdesk-public-tutorial/ic790628.png "Pessoa")

3. No diálogo New Person, execute os seguintes passos:
   
    ![Nova Pessoa](./media/topdesk-public-tutorial/ic790629.png "Nova Pessoa")
   
    a. Clique no separador Geral.

    b. Na caixa de texto **sobrenome,** escreva sobrenome do utilizador como Simon
 
    c. Selecione um **Site** para a conta.
 
    d. Clique em **Guardar**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador público ou APIs fornecidas pela TOPdesk - Públicas para fornecer contas de utilizador Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Ao clicar no TOPdesk - Azulejo público no Painel de Acesso, deverá ser automaticamente inscrito no TOPdesk - Público para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)