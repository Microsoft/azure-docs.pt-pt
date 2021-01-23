---
title: 'Tutorial: Integração do Azure Ative Directory com zscaler dois | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Zscaler Two.
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
ms.openlocfilehash: 8c3d76f24cce860538dc3bd5dcf29b59708bdb5d
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726278"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>Tutorial: Integração do Azure Ative Directory com zscaler Dois

Neste tutorial, você vai aprender a integrar Zscaler Two com Azure Ative Directory (Azure AD). Quando integrar o Zscaler 2 com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Zscaler 2.
* Ative os seus utilizadores a serem automaticamente inscritos no Zscaler Two com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o Zscaler Two, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Zscaler Dois assinatura ativada por assinatura única.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Zscaler Dois suporta **SP** iniciado SSO

* Zscaler Dois suporta **provisão de** utilizadores just in time

## <a name="adding-zscaler-two-from-the-gallery"></a>Adicionando Zscaler Dois da galeria

Para configurar a integração do Zscaler Two em AD Azure, precisa adicionar zscaler Dois da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **digite Zscaler 2** na caixa de pesquisa.
1. Selecione **Zscaler Dois** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-two"></a>Configure e teste Azure AD SSO para Zscaler Dois

Configure e teste Azure AD SSO com Zscaler Two usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Zscaler Two.

Para configurar e testar o Azure AD SSO com zscaler Dois, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Zscaler Two SSO](#configure-zscaler-two-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Zscaler 2 test user](#create-zscaler-two-test-user)** - para ter uma contraparte de B.Simon em Zscaler Dois que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Zscaler Three,** encontre a secção **Gerir** e selecione **um único sinal de sing.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de entrada de sinais,** digite o URL utilizado pelos seus utilizadores para iniciar sing-on na sua aplicação ZScaler Two.

    > [!NOTE]
    > Você atualiza o valor com o URL de Sign-On real. Contacte a [equipa de suporte do Zscaler Two Client](https://www.zscaler.com/company/contact) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. A sua aplicação Zscaler Two espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique **em Editar** o ícone para abrir o diálogo **dos Atributos do Utilizador.**

    ![A screenshot mostra atributos do utilizador com o ícone editar selecionado.](common/edit-attribute.png)

6. Além de acima, a aplicação Zscaler Two espera que alguns mais atributos sejam repercutidos na resposta DAL. Na secção **'Reclamações** de Utilizador' no diálogo **'Atributos do Utilizador',** execute os seguintes passos para adicionar o atributoken SAML, tal como mostrado na tabela abaixo:
    
    | Name | Atributo de origem |
    | ---------| ------------ |
    | membroOf | user.assignedroles |

    a. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![A Screenshot mostra as reclamações do Utilizador com a opção de adicionar uma nova reclamação.](common/new-save-attribute.png)

    ![A screenshot mostra a caixa de diálogo de reclamações do utilizador Manage onde pode introduzir os valores descritos.](common/new-attribute-details.png)

    b. Na caixa de texto **'Nome',** digite o nome do atributo indicado para esta linha.

    c. Deixe o **Espaço Namespace** em branco.

    d. Selecione Fonte como **Atributo**.

    e. A partir da lista **de atributos Source,** digite o valor de atributo mostrado para esta linha.
    
    f. Clique em **Guardar**.

    > [!NOTE]
    > Clique [aqui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) para saber como configurar o Papel em Azure AD.

7. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

8. Na secção **Configurar Zscaler Dois,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a Zscaler Two.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Zscaler 2**.
2. Na lista de candidaturas, selecione **Zscaler 2**.
3. No menu à esquerda, selecione **Utilizadores e grupos**.
4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
5. No diálogo **de Utilizadores e grupos,** selecione o utilizador como **Britta Simon** da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

    ![A screenshot mostra a caixa de diálogo dos Utilizadores e grupos onde pode selecionar um utilizador.](./media/zscaler-two-tutorial/tutorial_zscalertwo_users.png)

6. A partir do diálogo **'Escolha',** escolha a função de utilizador apropriada na lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

    ![A screenshot mostra a caixa de diálogo Select Role onde pode escolher uma função de utilizador.](./media/zscaler-two-tutorial/tutorial_zscalertwo_roles.png)

7. No diálogo **'Adicionar Atribuição'** selecione o botão **'Atribuir'.**

    ![A screenshot mostra a caixa de diálogo de atribuição de adicionar onde pode selecionar Atribua.](./media/zscaler-two-tutorial/tutorial_zscalertwo_assign.png)

## <a name="configure-zscaler-two-sso"></a>Configure Zscaler Dois SSO

1. Para automatizar a configuração dentro do Zscaler 2, é necessário instalar a **extensão do navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique na **Configuração Zscaler 2** irá direcioná-lo para a aplicação Zscaler 2. A partir daí, forneça as credenciais de administração para assinar no Zscaler 2. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Sso de configuração](common/setup-sso.png)

3. Se pretender configurar o Zscaler Dois manualmente, abra uma nova janela do navegador web e inscreva-se no seu site da empresa Zscaler Two como administrador e execute os seguintes passos:

4. Vá à **Administração > Autenticação > Definições de Autenticação** e execute os seguintes passos:
   
    ![A screenshot mostra o site Zscaler One com passos como descrito.](./media/zscaler-two-tutorial/ic800206.png "Administração")

    a. No Tipo de Autenticação, escolha **SAML**.

    b. Clique **em Configurar SAML**.

5. Na janela **Editar SAML,** execute os seguintes passos: e clique em Guardar.  
            
    ![Gerir utilizadores & autenticação](./media/zscaler-two-tutorial/ic800208.png "Gerir utilizadores & autenticação")
    
    a. Na caixa de texto **URL do PORTAL SAML,** cole o **URL de login** que copiou do portal Azure.

    b. Na caixa de texto **atributo de nome de login,** insira o **NameID**.

    c. Clique em **Upload**, para carregar o certificado de assinatura Azure SAML que descarregou a partir do portal Azure no **Certificado SSL Público**.

    d. Alternar o **Enable SAML Auto-Provisioning**.

    e. Na caixa de texto **do nome de visualização** do utilizador, **introduza o nome do ecrã** Se pretender ativar o fornecimento automático de SAML para os atributos do nome de visualização.

    f. Na caixa de texto do Atributo Nome de **Grupo,** **insira o membroOf** se pretender ativar o fornecimento automático da SAML para os atributos dos membros.

    exemplo, No **departamento** **de Atributos de Nome do Departamento,** se pretender ativar o fornecimento automático da SAML para atributos do departamento.

    h. Clique em **Guardar**.

6. Na página de diálogo de autenticação do **utilizador configurar,** execute os seguintes passos:

    ![A screenshot mostra a caixa de diálogo de autenticação do utilizador configurar com ativação selecionada.](./media/zscaler-two-tutorial/ic800207.png)

    a. Passe por cima do menu **de ativação** perto do canto inferior esquerdo.

    b. Clique **em Ativar**.

## <a name="configuring-proxy-settings"></a>Definir configurações de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para configurar as definições de procuração no Internet Explorer

1. Iniciar **o Internet Explorer**.

2. Selecione **as opções** de Internet do menu **Ferramentas** para abrir o diálogo Opções de **Internet.**   
    
     ![Opções de Internet](./media/zscaler-two-tutorial/ic769492.png "Opções de Internet")

3. Clique no separador **'Ligações'.**   
  
     ![Ligações](./media/zscaler-two-tutorial/ic769493.png "Ligações")

4. Clique nas **definições de LAN** para abrir o diálogo **de definições DE LAN.**

5. Na secção Proxy server, execute os seguintes passos:   
   
    ![Servidor proxy](./media/zscaler-two-tutorial/ic769494.png "Servidor proxy")

    a. Selecione **Utilize um servidor proxy para o seu LAN**.

    b. Na caixa de texto do Endereço, escreva **gateway. Zscaler Two.net.**

    c. Na caixa de texto do Porto, tipo **80**.

    d. Selecione **o servidor de procuração de bypass para endereços locais**.

    e. Clique **em OK** para fechar o diálogo de **definições da Rede local (LAN).**

6. Clique **em OK** para fechar o diálogo **Opções de Internet.**


### <a name="create-zscaler-two-test-user"></a>Criar Zscaler Dois utilizador de teste

Nesta secção, um utilizador chamado Britta Simon é criado em Zscaler Two. O Zscaler Two suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Zscaler Two, um novo é criado após a autenticação.

>[!Note]
>Se necessitar de criar um utilizador manualmente, contacte a [equipa de suporte Zscaler Two](https://www.zscaler.com/company/contact).

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para O URL de entrada de dois sinais de Zscaler, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição de Zscaler Two e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Zscaler Two nas Minhas Apps, este irá redirecionar para O URL de inscrição de Zscaler Two. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Próximos passos

Uma vez configurado Zscaler Dois, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
