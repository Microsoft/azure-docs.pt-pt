---
title: 'Tutorial: Integração do Diretório Ativo Azure com meta redes connector | Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o Meta Networks Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: b14a75dba2860c9dee58e40673d3299fdde277e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516873"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Tutorial: Integração do Diretório Ativo Azure com o Conector meta networks

Neste tutorial, aprende-se a integrar o Meta Networks Connector com o Azure Ative Directory (Azure AD).
A integração do Conector meta networks com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso ao Meta Networks Connector.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Meta Networks Connector (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD do Azure com o Conector meta networks, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Meta Networks Connector única subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Meta Networks Connector suporta **SSO** iniciado SP e **IDP**
 
* Meta Networks Connector suporta o fornecimento do utilizador **just in time**

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Adicionar Conector Meta Networks da galeria

Para configurar a integração do Meta Networks Connector no Azure AD, é necessário adicionar o Meta Networks Connector da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar meta-redes connector da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Meta Networks Connector**, selecione **Meta Networks Connector** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![Meta Networks Connector na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com meta networks connector com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de saúde a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Conector Meta Networks.

Para configurar e testar o único sinal de Azure AD com o Conector Meta Networks, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o conector meta networks único sinal-on](#configure-meta-networks-connector-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Meta Networks Connector test user](#create-meta-networks-connector-test-user)** - para ter uma contraparte de Britta Simon no Meta Networks Connector que está ligada à representação AZure AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o Conector Meta Networks, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Meta Networks Connector,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![A screenshot mostra a Configuração BÁSICA SAML, onde pode introduzir o Identificador, Responder U R L e selecionar Guardar.](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![Screenshot mostra Definir U R Ls adicionais onde pode introduzir um sinal em U R L.](common/both-advanced-urls.png)

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. Na caixa de texto **do Estado de retransmissão,** digite um URL utilizando o seguinte padrão: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`

    > [!NOTE]
    > Estes valores não são reais. Atualizar estes valores com o identificador real, URL de resposta e URL de Sign-On são explicados mais tarde no tutorial.

6. A aplicação Meta Networks Connector espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique **em Editar** o ícone para abrir o diálogo **dos Atributos do Utilizador.**

    ![A screenshot mostra atributos do utilizador com o ícone editar selecionado.](common/edit-attribute.png)
    
7. Além de acima, a aplicação Meta Networks Connector espera que alguns mais atributos sejam repercutidos na resposta SAML. Na secção **'Reclamações** de Utilizador' no diálogo **'Atributos do Utilizador',** execute os seguintes passos para adicionar o atributoken SAML, tal como mostrado na tabela abaixo:
    
    | Name | Atributo de origem | Espaço de Nomes|
    | ---------------| --------------- | -------- |
    | nome de primeiro nome | user.givenname | |
    | último nome | utilizador.sobrenome | |
    | endereço de e-mail| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | name | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | telefone | user.telephonenumber | |

    a. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![A Screenshot mostra as reclamações do Utilizador com a opção de adicionar uma nova reclamação.](common/new-save-attribute.png)

    ![A screenshot mostra a caixa de diálogo de reclamações do utilizador Manage onde pode introduzir os valores descritos.](common/new-attribute-details.png)

    b. Na caixa de texto **'Nome',** digite o nome do atributo indicado para esta linha.

    c. Deixe o **Espaço Namespace** em branco.

    d. Selecione Fonte como **Atributo**.

    e. A partir da lista **de atributos Source,** digite o valor de atributo mostrado para esta linha.

    f. Clique **em Ok**

    exemplo, Clique em **Guardar**.

8. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

9. Na secção **De Configuração meta-redes Connector,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-meta-networks-connector-single-sign-on"></a>Configurar meta redes connector único Sign-On

1. Abra um novo separador no seu navegador e inicie sessão na sua conta de administrador meta networks Connector.
    
    > [!NOTE]
    > Meta Networks Connector é um sistema seguro. Por isso, antes de aceder ao portal, é necessário adicionar o seu endereço IP público a uma lista de autorizações do seu lado. Para obter o seu endereço IP público, siga o link abaixo especificado [aqui.](https://whatismyipaddress.com/) Envie o seu endereço IP para a equipa de suporte do [Cliente Meta Networks Connector](mailto:support@metanetworks.com) para obter o seu endereço IP adicionado a uma lista de autorizações.
    
2. Vá ao **Administrador** e selecione **Definições**.
    
    ![A screenshot mostra definições selecionadas a partir do menu Administração.](./media/metanetworksconnector-tutorial/configure3.png)
    
3. Certifique-se de que **o Tráfego de Internet** e o **MFA de Força VPN** estão prontos para desligar.
    
    ![A screenshot mostra desligar estas definições.](./media/metanetworksconnector-tutorial/configure1.png)
    
4. Vá ao **Administrador** e selecione **SAML**.
    
    ![A screenshot mostra SAML selecionado a partir do menu Administração.](./media/metanetworksconnector-tutorial/configure4.png)
    
5. Execute os seguintes passos na página **DETAILS:**
    
    ![A screenshot mostra a página DETAILS onde pode introduzir os valores descritos.](./media/metanetworksconnector-tutorial/configure2.png)
    
    a. Copie o valor **do URL SSO** e **cole-o** na caixa de texto URL de entrada na secção **De Tector de Meta Networks e URLs.**
    
    b. Copiar o valor **do URL do destinatário** e colá-lo na caixa de texto URL de **resposta** na secção **Deector e URLs meta redes.**
    
    c. Copiar o valor **do URI (Entity ENTITY Y)** e colá-lo na caixa de texto **identifier (Entity ID)** na secção **Meta Networks Connector Domain and URLs.**
    
    d. Ativar o SAML
    
6. No separador **GENERAL.** Efetuar os seguintes passos:

    ![A screenshot mostra a página GENERAL onde pode introduzir os valores descritos.](./media/metanetworksconnector-tutorial/configure5.png)

    a. No **URL de Sign-On único fornecedor de identidade,** cole o valor URL de **login** que copiou a partir do portal Azure.

    b. No **Emitente fornecedor de identidade,** cole o valor **do identificador Azure AD** que copiou do portal Azure.

    c. Abra o certificado descarregado do portal Azure em bloco de notas, cole-o na caixa de texto **do Certificado X.509.**

    d. Ativar o **Provisionamento Just-in-Time**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome,** **insira BrittaSimon**.
  
    b. No campo **nome do utilizador,** **escreva brittasimon \@ yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, permite à Britta Simon utilizar o Azure single sign-on, permitindo o acesso ao Meta Networks Connector.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **Meta Networks Connector**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Meta Networks Connector**.

    ![O conector meta redes na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-meta-networks-connector-test-user"></a>Criar utilizador de teste de conector meta redes

Nesta secção, um utilizador chamado Britta Simon é criado no Meta Networks Connector. O Meta Networks Connector suporta o provisionamento just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Meta Networks Connector, um novo é criado quando tenta aceder ao Conector Meta Networks.

>[!Note]
>Se necessitar de criar um utilizador manualmente, contacte a equipa de [suporte do Cliente Meta Networks Connector](mailto:support@metanetworks.com).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo meta-redes do conector no Painel de Acesso, deverá ser automaticamente inscrito no Conector meta redes para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)