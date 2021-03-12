---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com beyondTrust Remote Support | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o BeyondTrust Remote Support.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: jeedes
ms.openlocfilehash: 1996024d163a4bf7cfa741110038bb8db5b883e8
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632751"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>Tutorial: Azure Ative Directory integração única (SSO) com Suporte Remoto BeyondTrust

Neste tutorial, você vai aprender a integrar BeyondTrust Remote Support com Azure Ative Directory (Azure AD). Quando integrar o Suporte Remoto BeyondTrust com AD Azure, pode:

* Control em Azure AD que tem acesso a BeyondTrust Remote Support.
* Ativar os seus utilizadores a serem automaticamente inscritos no BeyondTrust Remote Support com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Além do Suporte Remoto De Suporte Remoto (SSO) ativada por subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* BeyondTrust Remote Support suporta **SSO** iniciado SP
* BeyondTrust Remote Support suporta **apenas a provisão** do utilizador no tempo

## <a name="adding-beyondtrust-remote-support-from-the-gallery"></a>Adicionar Suporte Remoto BeyondTrust da galeria

Para configurar a integração do BeyondTrust Remote Support em Azure AD, precisa de adicionar o Suporte Remoto BeyondTrust da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite BeyondTrust Remote Support** na caixa de pesquisa.
1. Selecione **BeyondTrust Remote Support** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-beyondtrust-remote-support"></a>Configure e teste Azure AD SSO para suporte remoto BeyondTrust

Configure e teste Azure AD SSO com Suporte Remoto BeyondTrust usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no BeyondTrust Remote Support.

Para configurar e testar O SSO Azure AD com Suporte Remoto BeyondTrust, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o BeyondTrust Remote Support SSO](#configure-beyondtrust-remote-support-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * Crie o utilizador de **[teste de suporte remoto BeyondTrust](#create-beyondtrust-remote-support-test-user)** - para ter uma contraparte de B.Simon no Suporte Remoto BeyondTrust que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **BeyondTrust Remote Support,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa **identifier,** digite um URL utilizando o seguinte padrão: `https://<HOSTNAME>.bomgar.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<HOSTNAME>.bomgar.com/saml/sso`
    
    c. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<HOSTNAME>.bomgar.com/saml`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL Sign-On. Obterá estes valores explicados mais tarde no tutorial.

1. A aplicação BeyondTrust Remote Support espera as afirmações DO SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação BeyondTrust Remote Support espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name |  Atributo de origem|
    | ---------------| ----------|
    | Nome de utilizador | user.userprincipalname |
    | FirstName | user.givenname |
    | LastName | utilizador.sobrenome |
    | E-mail | user.mail |
    | Grupos | utilizador.grupos |

    > [!NOTE]
    > Ao atribuir grupos AD Azure para a aplicação BeyondTrust Remote Support, a opção "Grupos devolvidos em reclamação" terá de ser modificada de Nenhum para o SecurityGroup. Os Grupos serão importados para a aplicação como iDs de objeto. O ID do Objeto do Grupo AZure AD pode ser encontrado verificando as propriedades na interface Azure Ative Directory. Isto será necessário para fazer referência e atribuir grupos AZure AD às políticas corretas de grupo.

1. Ao definir o identificador de utilizador único, este valor deve ser definido como NameID-Formato: **Persistente**. Exigimos que este seja um identificador persistente para identificar e associar corretamente o utilizador às políticas de grupo corretas para permissões. Clique no ícone de edição para abrir o diálogo **"Atributos & Reclamações** do Utilizador" para editar o valor do identificador de utilizador único.

1. Na secção **'Obter Reclamação',** clique no **formato do identificador de nome Escolher** e desaver o valor para **Persistente** e clique em **Guardar**.

    ![Atributos e Reclamações do Utilizador](./media/bomgarremotesupport-tutorial/attribute-unique-user-identifier.png)

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **De Suporte Remoto BeyondTrust,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao BeyondTrust Remote Support.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **BeyondTrust Remote Support**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-beyondtrust-remote-support-sso"></a>Configurar BeyondTrust Suporte Remoto SSO

1. Numa janela diferente do navegador web, inscreva-se no BeyondTrust Remote Support como administrador.

1. Navegue para **os Utilizadores &**  >  **Fornecedores de Segurança.**

1. Clique no ícone **editar** nos **fornecedores SAML**.

    ![Ícone de edição de fornecedores SAML](./media/bomgarremotesupport-tutorial/saml-providers.png)

1. Expandir a secção **de Definições do Fornecedor de Serviços.**

1. Clique em **Metadados do Fornecedor de Serviços de Descarregamento** ou pode copiar os valores **de ID** e **URL da** Entidade e utilizar estes valores na secção configuração básica do **SAML** no portal Azure.

    ![Baixar Metadados do Fornecedor de Serviços](./media/bomgarremotesupport-tutorial/service-provider-metadata.png)


1. Na secção Definições do Fornecedor de Identidade, clique nos **Metadados do Fornecedor de Identidade de Upload** e localize o ficheiro Metadados XML que descarregou a partir do portal Azure.

1.  O **ID da Entidade**, URL de serviço de Sign-On **único** e certificado **de servidor** será automaticamente carregado, e a **Ligação do Protocolo url SSO** terá de ser alterada para **HTTP POST**.

    ![A screenshot mostra a secção definições do fornecedor de identidade onde executa estas ações.](./media/bomgarremotesupport-tutorial/identity-provider.png)

1. Clique em **Guardar**.

### <a name="create-beyondtrust-remote-support-test-user"></a>Criar utilizador de teste de suporte remoto BeyondTrust

Nesta secção, um utilizador chamado Britta Simon é criado no BeyondTrust Remote Support. BeyondTrust Remote Support suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no BeyondTrust Remote Support, um novo é criado após a autenticação.

Siga o procedimento abaixo, que é obrigatório para configurar o Suporte Remoto BeyondTrust.

Vamos configurar as Definições de Provisão do Utilizador aqui. Os valores utilizados nesta secção serão referenciados a partir da secção **Atributos & Reclamações** do Utilizador no portal Azure. Configuramos que este é o valor padrão que já são importados no momento da criação, no entanto, o valor pode ser personalizado se necessário.

![A screenshot mostra as Definições de Provisão do Utilizador onde pode configurar os valores do utilizador.](./media/bomgarremotesupport-tutorial/user-attribute.png)

> [!NOTE]
> Os grupos e o atributo de e-mail não são necessários para esta implementação. Se utilizar grupos Azure AD e atribuí-los às políticas do Grupo de Suporte Remoto BeyondTrust para permissões, o ID do objeto do grupo terá de ser referenciado através das suas propriedades no portal Azure e colocado na secção 'Grupos Disponíveis'. Uma vez concluído este problema, o Grupo ID/AD do objeto estará agora disponível para atribuição a uma política de grupo para permissões.

![A screenshot mostra a secção I T com o tipo de membro, origem, tipo e objeto I D.](./media/bomgarremotesupport-tutorial/config-user-2.png)

![A screenshot mostra a página Definições Básicas para uma política de grupo.](./media/bomgarremotesupport-tutorial/group-policy.png)

> [!NOTE]
> Em alternativa, pode ser definida uma política de grupo predefinido no Fornecedor de Segurança SAML2. Ao definir esta opção, irá atribuir a todos os utilizadores que autenticam através da SAML as permissões especificadas dentro da política de grupo. A política dos Membros Gerais está incluída no BeyondTrust Remote Support/Privileged Remote Access com permissões limitadas, que podem ser usadas para testar a autenticação e atribuir aos utilizadores as políticas corretas. Os utilizadores não irão preencher a lista de Utilizadores SAML2 através de /login > Utilizadores & Security até à primeira tentativa de autenticação bem sucedida. Informações adicionais sobre as políticas do grupo podem ser encontradas no seguinte link: `https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm`

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de suporte remoto BeyondTrust, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de suporte remoto BeyondTrust e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo de Suporte Remoto BeyondTrust nas Minhas Apps, este irá redirecionar para URL de suporte remoto BeyondTrust. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Suporte Remoto BeyondTrust, pode impor controlos de sessão, o que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)