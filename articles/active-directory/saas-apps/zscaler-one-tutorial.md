---
title: 'Tutorial: Integração do Azure Ative Directory com a Zscaler One Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Zscaler One.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: d55e02bd981a220fee55a0abd540206a2701850a
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896859"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-one"></a>Tutorial: Integração do Azure Ative Directory com o Zscaler One

Neste tutorial, aprende-se a integrar o Zscaler One com o Azure Ative Directory (Azure AD).
A integração do Zscaler One com Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Zscaler One.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Zscaler One (Sign-on único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o Zscaler One, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Zscaler Uma única subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Zscaler One suporta **SSO** iniciado sp

* Zscaler One suporta **o fornecimento de utilizadores just in time**

## <a name="adding-zscaler-one-from-the-gallery"></a>Adicionando Zscaler One da galeria

Para configurar a integração do Zscaler One em Azure AD, é necessário adicionar o Zscaler One da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Zscaler One da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, tipo **Zscaler One** , selecione **Zscaler One** do painel de resultados e clique em **Adicionar** botão para adicionar a aplicação.

     ![Zscaler Um na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com zscaler One com base num utilizador de teste chamado **Britta Simon** .
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Zscaler One.

Para configurar e testar o Azure AD com zscaler One, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure Zscaler One Single Sign-On](#configure-zscaler-one-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Zscaler One test user](#create-zscaler-one-test-user)** - para ter uma contraparte de Britta Simon em Zscaler One que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com Zscaler One, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Zscaler One,** selecione **Single sign-on** .

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Zscaler One Domain e URLs informações únicas de súming](common/sp-signonurl.png)

    Na caixa de texto **URL de entrada de sinais,** digite o URL utilizado pelos seus utilizadores para iniciar sing-on na sua aplicação Zscaler One.

    > [!NOTE]
    > Você atualiza o valor com o URL de Sign-On real. Contacte [a equipa de suporte do Zscaler One Client](https://www.zscaler.com/company/contact) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. A sua aplicação Zscaler One espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique **em Editar** o ícone para abrir o diálogo **dos Atributos do Utilizador.**

    ![A screenshot mostra atributos do utilizador com o ícone editar selecionado.](common/edit-attribute.png)

6. Além de acima, a aplicação Zscaler One espera que alguns mais atributos sejam repercutidos na resposta SAML. Na secção **'Reclamações** de Utilizador' no diálogo **'Atributos do Utilizador',** execute os seguintes passos para adicionar o atributoken SAML, tal como mostrado na tabela abaixo:
    
    | Nome | Atributo de origem |
    | ---------| ------------ |
    | membroOf     | user.assignedroles |

    a. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![A Screenshot mostra as reclamações do Utilizador com a opção de adicionar uma nova reclamação.](common/new-save-attribute.png)

    ![A screenshot mostra a caixa de diálogo de reclamações do utilizador Manage onde pode introduzir os valores descritos.](common/new-attribute-details.png)

    b. Na caixa de texto **'Nome',** digite o nome do atributo indicado para esta linha.

    c. Deixe o **Espaço Namespace** em branco.

    d. Selecione Fonte como **Atributo** .

    e. A partir da lista **de atributos Source,** digite o valor de atributo mostrado para esta linha.
    
    f. Clique em **Guardar** .

    > [!NOTE]
    > Clique [aqui](../develop/active-directory-enterprise-app-role-management.md) para saber como configurar o papel em Azure AD

7. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

8. Na secção **Configurar Zscaler Um,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-zscaler-one-single-sign-on"></a>Configurar Zscaler One Single Sign-On

1. Para automatizar a configuração dentro do Zscaler One, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão** .

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique na **Configuração Zscaler One** irá direcioná-lo para a aplicação Zscaler One. A partir daí, forneça as credenciais de administração para assinar no Zscaler One. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Sso de configuração](common/setup-sso.png)

3. Se pretender configurar o Zscaler One manualmente, abra uma nova janela do navegador web e inscreva-se no seu site da empresa Zscaler One como administrador e execute os seguintes passos:

4. Vá à **Administração > Autenticação > Definições de Autenticação** e execute os seguintes passos:
   
    ![A screenshot mostra o site Zscaler One com passos como descrito.](./media/zscaler-one-tutorial/ic800206.png "Administração")

    a. No Tipo de Autenticação, escolha **SAML** .

    b. Clique **em Configurar SAML** .

5. Na janela **Editar SAML,** execute os seguintes passos: e clique em Guardar.  
            
    ![Gerir utilizadores & autenticação](./media/zscaler-one-tutorial/ic800208.png "Gerir utilizadores & autenticação")
    
    a. Na caixa de texto **URL do PORTAL SAML,** cole o **URL de login** que copiou do portal Azure.

    b. Na caixa de texto **atributo de nome de login,** insira o **NameID** .

    c. Clique em **Upload** , para carregar o certificado de assinatura Azure SAML que descarregou a partir do portal Azure no **Certificado SSL Público** .

    d. Alternar o **Enable SAML Auto-Provisioning** .

    e. Na caixa de texto **do nome de visualização** do utilizador, **introduza o nome do ecrã** Se pretender ativar o fornecimento automático de SAML para os atributos do nome de visualização.

    f. Na caixa de texto do Atributo Nome de **Grupo,** **insira o membroOf** se pretender ativar o fornecimento automático da SAML para os atributos dos membros.

    exemplo, No **departamento** **de Atributos de Nome do Departamento,** se pretender ativar o fornecimento automático da SAML para atributos do departamento.

    h. Clique em **Guardar** .

6. Na página de diálogo de autenticação do **utilizador configurar,** execute os seguintes passos:

    ![A screenshot mostra a caixa de diálogo de autenticação do utilizador configurar com ativação selecionada.](./media/zscaler-one-tutorial/ic800207.png)

    a. Passe por cima do menu **de ativação** perto do canto inferior esquerdo.

    b. Clique **em Ativar** .

## <a name="configuring-proxy-settings"></a>Definir configurações de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para configurar as definições de procuração no Internet Explorer

1. Iniciar **o Internet Explorer** .

2. Selecione **as opções** de Internet do menu **Ferramentas** para abrir o diálogo Opções de **Internet.**   
    
     ![Opções de Internet](./media/zscaler-one-tutorial/ic769492.png "Opções de Internet")

3. Clique no separador **'Ligações'.**   
  
     ![Ligações](./media/zscaler-one-tutorial/ic769493.png "Ligações")

4. Clique nas **definições de LAN** para abrir o diálogo **de definições DE LAN.**

5. Na secção Proxy server, execute os seguintes passos:   
   
    ![Servidor proxy](./media/zscaler-one-tutorial/ic769494.png "Servidor proxy")

    a. Selecione **Utilize um servidor proxy para o seu LAN** .

    b. Na caixa de texto do Endereço, escreva **gateway. Zscaler One.net.**

    c. Na caixa de texto do Porto, tipo **80** .

    d. Selecione **o servidor de procuração de bypass para endereços locais** .

    e. Clique **em OK** para fechar o diálogo de **definições da Rede local (LAN).**

7. Clique **em OK** para fechar o diálogo **Opções de Internet.**

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory** , selecione **Utilizadores** , e, em seguida, selecione **Todos os utilizadores** .

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon** .
  
    b. No tipo de campo **do nome do utilizador** brittasimon@yourcompanydomain.extension . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a Zscaler One.

1. No portal Azure, selecione **Aplicações empresariais** , selecione **Todas as aplicações,** em seguida, selecione **Zscaler One** .

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Zscaler One** .

    ![O link Zscaler One na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos** .

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos,** selecione o utilizador como **Britta Simon** da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

    ![A screenshot mostra a caixa de diálogo dos Utilizadores e grupos onde pode selecionar um utilizador.](./media/zscaler-one-tutorial/tutorial_zscalerone_users.png)

6. A partir do diálogo **'Escolha',** escolha a função de utilizador apropriada na lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

    ![A screenshot mostra a caixa de diálogo Select Role onde pode escolher uma função de utilizador.](./media/zscaler-one-tutorial/tutorial_zscalerone_roles.png)

7. No diálogo **'Adicionar Atribuição'** selecione o botão **'Atribuir'.**

    ![A screenshot mostra a caixa de diálogo de atribuição de adicionar onde pode selecionar Atribua.](./media/zscaler-one-tutorial/tutorial_zscalerone_assign.png)

### <a name="create-zscaler-one-test-user"></a>Criar Zscaler Um utilizador de teste

Nesta secção, um utilizador chamado Britta Simon é criado em Zscaler One. O Zscaler One suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Zscaler One, um novo é criado após a autenticação.

>[!Note]
>Se necessitar de criar um utilizador manualmente, contacte a [equipa de suporte Zscaler One](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Zscaler One no Painel de Acesso, deverá ser automaticamente inscrito no Zscaler One para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)