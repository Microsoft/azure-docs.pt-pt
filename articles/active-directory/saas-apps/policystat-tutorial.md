---
title: 'Tutorial: Integração do Diretório Ativo Azure com | De PolicyStat Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o PolicyStat.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 4d6274f3e1356a8ff8a997e830f0ebee43f50cff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92893160"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Tutorial: Integração do Diretório Ativo Azure com PolicyStat

Neste tutorial, aprende-se a integrar o PolicyStat com o Azure Ative Directory (Azure AD).
Integrar o PolicyStat com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a PolicyStat.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no PolicyStat (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o PolicyStat, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por PolicyStat

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* PolicyStat apoia SSO iniciado **pela SP**

* PolicyStat suporta **o fornecimento de utilizadores just in time**

## <a name="adding-policystat-from-the-gallery"></a>Adicionar PolicyStat da galeria

Para configurar a integração do PolicyStat no AD Azure, é necessário adicionar PolicyStat da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar PolicyStat da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite PolicyStat**, selecione **PolicyStat** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![PolicyStat na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com PolicyStat baseado num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no PolicyStat.

Para configurar e testar o Azure AD com um único sinal de acesso com o PolicyStat, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure PolicyStat Single Sign-On](#configure-policystat-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
4. **[Create PolicyStat test user](#create-policystat-test-user)** - para ter uma contraparte de Britta Simon em PolicyStat que está ligada à representação AD AZure do utilizador.
5. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o PolicyStat, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **PolicyStat,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Informações únicas de sign-on do Domínio e DOS URLs](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<companyname>.policystat.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente PolicyStat](http://www.policystat.com/support/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

5. A sua aplicação PolicyStat espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique **em Editar** o ícone para abrir o diálogo **dos Atributos do Utilizador.**

    ![Screenshot que mostra o diálogo "Atributos do Utilizador" com o ícone "Editar" selecionado.](common/edit-attribute.png)

6. Além de acima, a aplicação PolicyStat espera que alguns mais atributos sejam repercutidos na resposta SAML. Na secção **'Reclamações** de Utilizador' no diálogo **'Atributos do Utilizador',** execute os seguintes passos para adicionar o atributoken SAML, tal como mostrado na tabela abaixo:

    | Name | Atributo de origem |
    |------------------- | -------------------- |
    | uid | ExtractoMailPrefixo ([esto correio]) |

    a. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**
    
    ![Screenshot que mostra a secção "Reclamações do Utilizador" com as ações "Adicionar nova reivindicação" e "Salvar" realçadas.](common/new-save-attribute.png)

    ![Screenshot que mostra o diálogo "Gerir as alegações do utilizador" com as caixas de texto "Nome", "Transformação" e "Parâmetro" realçadas, e o botão "Guardar" selecionado.](./media/policystat-tutorial/attribute01.png)

    b. Na caixa de texto **'Nome',** digite o nome do atributo indicado para esta linha.

    c. Deixe o **Espaço Namespace** em branco.

    d. Selecione Fonte como **Transformação**.

    e. Da lista **De Transformação,** digite o valor de atributo mostrado para esta linha.
    
    f. Da lista **do Parâmetro 1,** digite o valor do atributo indicado para esta linha.

    exemplo, Clique em **Guardar**.

7. Na secção **Configurar PolicyStat,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-policystat-single-sign-on"></a>Configurar políticasStat Single Sign-On

1. Numa janela diferente do navegador web, inicie sessão no site da empresa PolicyStat como administrador.

2. Clique no separador **Administração** e, em seguida, clique em **Configuração Sign-On única** no painel de navegação esquerdo.
   
    ![Menu de Administrador](./media/policystat-tutorial/ic808633.png "Menu de Administrador")

3. Clique nos **metadados IDP** e, em seguida, na secção **de metadados IDP,** execute os seguintes passos:
   
    ![Screenshot que mostra a ação "Os seus Metadados I D P" selecionados.](./media/policystat-tutorial/ic808636.png "Configuração de Sign-On única")
   
    a. Abra o seu ficheiro de metadados descarregado, copie o conteúdo e, em seguida, cole-o na caixa de texto do **Seu Fornecedor de Identidade Metadados.**

    b. Clique em **Guardar Alterações**.

4. Clique em **Configure Atributos** e, em seguida, na secção **Atributos de Configuração,** execute os seguintes passos:
   
    a. Na caixa de texto **Do nome de utilizador Attribute,** escreva **uid**.

    b. Na caixa de texto **namee Attribute,** digite o nome de reclamação do primeiro nome do Azure **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`** .

    c. Na caixa de texto **do último nome, digite** o nome de reclamação do último nome do Azure **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`** .

    d. Na caixa de texto **do Email Attribute,** digite o nome de reclamação do Seu Atributo de E-mail a partir do Azure **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** .

    e. Clique em **Guardar Alterações**.

5. Na secção **Configuração,** selecione **Ativar a Integração de Sinais únicos**.
   
    ![Configuração de Sign-On única](./media/policystat-tutorial/ic808634.png "Configuração de Sign-On única")


### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que a sua própria conta utilize o Azure um único sinal de acesso, permitindo o acesso ao PolicyStat.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **PolicyStat**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **PolicyStat**.

    ![O link PolicyStat na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos,** selecione a sua conta na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-policystat-test-user"></a>Criar utilizador de teste PolicyStat

Nesta secção, um utilizador chamado Britta Simon é criado em PolicyStat. O PolicyStat suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no PolicyStat, um novo é criado após a autenticação.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador Do PolicyStat ou APIs fornecidas pela PolicyStat para fornecer contas de utilizador Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo PolicyStat no Painel de Acesso, deverá ser automaticamente inscrito no PolicyStat para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)