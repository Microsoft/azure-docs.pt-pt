---
title: 'Tutorial: Integração do Azure Ative Directory com a Zscaler One | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Zscaler One.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 3f825e247aff5c8fc53eb8610f33bd8e0b7fce3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735646"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-one"></a>Tutorial: Integração do Azure Ative Directory com o Zscaler One

Neste tutorial, aprende-se a integrar o Zscaler One com o Azure Ative Directory (Azure AD).
A integração do Zscaler One com Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Zscaler One.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Zscaler One (Sign-on único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.


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

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **digite Zscaler One** na caixa de pesquisa.
1. Selecione **Zscaler One** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-one"></a>Configure e teste Azure AD SSO para Zscaler One

Configure e teste Azure AD SSO com Zscaler One usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Zscaler One.

Para configurar e testar o Azure AD SSO com o Zscaler One, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
2. **[Configure o Zscaler One SSO](#configure-zscaler-one-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create Zscaler One test user](#create-zscaler-one-test-user)** - para ter uma contraparte de Britta Simon em Zscaler One que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Zscaler One,** encontre a secção **Gerir** e selecione **um único sinal de sing.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    Na caixa de texto **URL de entrada de sinais,** digite o URL utilizado pelos seus utilizadores para iniciar sing-on na sua aplicação Zscaler One.

    > [!NOTE]
    > Você atualiza o valor com o URL de Sign-On real. Contacte [a equipa de suporte do Zscaler One Client](https://www.zscaler.com/company/contact) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. A sua aplicação Zscaler One espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique **em Editar** o ícone para abrir o diálogo **dos Atributos do Utilizador.**

    ![A screenshot mostra atributos do utilizador com o ícone editar selecionado.](common/edit-attribute.png)

6. Além de acima, a aplicação Zscaler One espera que alguns mais atributos sejam repercutidos na resposta SAML. Na secção **'Reclamações** de Utilizador' no diálogo **'Atributos do Utilizador',** execute os seguintes passos para adicionar o atributoken SAML, tal como mostrado na tabela abaixo:
    
    | Name | Atributo de origem |
    | ---------| ------------ |
    | membroOf | user.assignedroles |

    a. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    b. Na caixa de texto **'Nome',** digite o nome do atributo indicado para esta linha.

    c. Deixe o **Espaço Namespace** em branco.

    d. Selecione Fonte como **Atributo**.

    e. A partir da lista **de atributos Source,** digite o valor de atributo mostrado para esta linha.
    
    f. Clique em **Guardar**.

    > [!NOTE]
    > Clique [aqui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) para saber como configurar o Papel em Azure AD.

7. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

8. Na secção **Configurar Zscaler Um,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Zscaler One.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Zscaler One**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se tiver configurado as funções como explicado no acima, pode selecioná-lo a partir do Dropdown de **função Select.**
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="configure-zscaler-one-sso"></a>Configurar Zscaler One SSO

1. Para automatizar a configuração dentro do Zscaler One, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique na **Configuração Zscaler One** irá direcioná-lo para a aplicação Zscaler One. A partir daí, forneça as credenciais de administração para assinar no Zscaler One. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Sso de configuração](common/setup-sso.png)

3. Se pretender configurar o Zscaler One manualmente, abra uma nova janela do navegador web e inscreva-se no seu site da empresa Zscaler One como administrador e execute os seguintes passos:

4. Vá à **Administração > Autenticação > Definições de Autenticação** e execute os seguintes passos:
   
    ![A screenshot mostra o site Zscaler One com passos como descrito.](./media/zscaler-one-tutorial/ic800206.png "Administração")

    a. No Tipo de Autenticação, escolha **SAML**.

    b. Clique **em Configurar SAML**.

5. Na janela **Editar SAML,** execute os seguintes passos: e clique em Guardar.  
            
    ![Gerir utilizadores & autenticação](./media/zscaler-one-tutorial/ic800208.png "Gerir utilizadores & autenticação")
    
    a. Na caixa de texto **URL do PORTAL SAML,** cole o **URL de login** que copiou do portal Azure.

    b. Na caixa de texto **atributo de nome de login,** insira o **NameID**.

    c. Clique em **Upload**, para carregar o certificado de assinatura Azure SAML que descarregou a partir do portal Azure no **Certificado SSL Público**.

    d. Alternar o **Enable SAML Auto-Provisioning**.

    e. Na caixa de texto **do nome de visualização** do utilizador, **introduza o nome do ecrã** Se pretender ativar o fornecimento automático de SAML para os atributos do nome de visualização.

    f. Na caixa de texto do Atributo Nome de **Grupo,** **insira o membroOf** se pretender ativar o fornecimento automático da SAML para os atributos dos membros.

    exemplo, No **departamento** **de Atributos de Nome do Departamento,** se pretender ativar o fornecimento automático da SAML para atributos do departamento.

    h. Clique em **Guardar**.

6. Na página de diálogo de autenticação do **utilizador configurar,** execute os seguintes passos:

    ![A screenshot mostra a caixa de diálogo de autenticação do utilizador configurar com ativação selecionada.](./media/zscaler-one-tutorial/ic800207.png)

    a. Passe por cima do menu **de ativação** perto do canto inferior esquerdo.

    b. Clique **em Ativar**.

## <a name="configuring-proxy-settings&quot;></a>Definir configurações de proxy
### <a name=&quot;to-configure-the-proxy-settings-in-internet-explorer&quot;></a>Para configurar as definições de procuração no Internet Explorer

1. Iniciar **o Internet Explorer**.

2. Selecione **as opções** de Internet do menu **Ferramentas** para abrir o diálogo Opções de **Internet.**   

    ![Opções de Internet](./media/zscaler-one-tutorial/ic769492.png &quot;Opções de Internet")

3. Clique no separador **'Ligações'.**   

    ![Ligações](./media/zscaler-one-tutorial/ic769493.png "Ligações")

4. Clique nas **definições de LAN** para abrir o diálogo **de definições DE LAN.**

5. Na secção Proxy server, execute os seguintes passos:   
   
    ![Servidor proxy](./media/zscaler-one-tutorial/ic769494.png "Servidor proxy")

    a. Selecione **Utilize um servidor proxy para o seu LAN**.

    b. Na caixa de texto do Endereço, escreva **gateway. Zscaler One.net.**

    c. Na caixa de texto do Porto, tipo **80**.

    d. Selecione **o servidor de procuração de bypass para endereços locais**.

    e. Clique **em OK** para fechar o diálogo de **definições da Rede local (LAN).**

7. Clique **em OK** para fechar o diálogo **Opções de Internet.**

### <a name="create-zscaler-one-test-user"></a>Criar Zscaler Um utilizador de teste

Nesta secção, um utilizador chamado Britta Simon é criado em Zscaler One. O Zscaler One suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Zscaler One, um novo é criado após a autenticação.

>[!Note]
>Se necessitar de criar um utilizador manualmente, contacte a [equipa de suporte Zscaler One](https://www.zscaler.com/company/contact).

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para O URL de Sessão de Zscaler One, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição Zscaler One e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Zscaler One nas Minhas Apps, este irá redirecionar para O URL de SNL de Zscaler One. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Zscaler One, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
