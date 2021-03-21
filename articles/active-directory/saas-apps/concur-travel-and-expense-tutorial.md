---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com | de viagens e despesas da Concur Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Concur Travel and Expense.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: 56eab06bd1afd18bfd4e23b9acb0b44d7bd1f80b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737036"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur-travel-and-expense"></a>Tutorial: Azure Ative Directory integração única (SSO) com Concur Travel and Expense

Neste tutorial, você aprenderá a integrar Concur Travel and Expense com Azure Ative Directory (Azure AD). Quando integra Viagens e Despesas concur com AZure AD, pode:

* Controlo em Azure AD que tem acesso a Concur Viagens e Despesas.
* Permitir que os seus utilizadores sejam automaticamente inscritos para Concur Viagens e Despesas com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura Concur Viagens e Despesas.
* Uma função de "Administrador da Empresa" na sua conta de utilizador Concur. Pode testar se tem o acesso certo indo para [a Ferramenta de Self-Service SSO Concur](https://www.concursolutions.com/nui/authadmin/ssoadmin). Se não tiver acesso, contacte o suporte do Concur ou gestor de projetos de implementação. 

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO.

* Concur Viagens e Despesas suporta **IDP** e **SP** iniciado SSO
* Concur Viagens e Despesas suporta teste SSO em ambiente de produção e implementação 

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para cada uma das três regiões: EUA, EMEA e China. Assim, apenas um caso pode ser configurado para cada região em um inquilino. 

## <a name="adding-concur-travel-and-expense-from-the-gallery"></a>Adicionar Concur Viagens e Despesas da galeria

Para configurar a integração de Concur Travel and Expense em AD Azure, você precisa adicionar Concur Viagens e Despesas da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva Concur Viagens e Despesas** na caixa de pesquisa.
1. Selecione **Concur Viagens e Despesas** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-concur-travel-and-expense"></a>Configure e teste Azure AD SSO para viajar e despesas concur

Configure e teste Azure AD SSO com Concur Viagens e Despesas usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Concur Viagens e Despesas.

Para configurar e testar a Azure AD SSO com concur Viagens e Despesas, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Concur Viagens e Despesas SSO](#configure-concur-travel-and-expense-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Concur Travel and Expense test user](#create-concur-travel-and-expense-test-user)** - para ter uma contraparte de B.Simon em Concur Viagens e Despesas que está ligada à representação AD Ad Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Concur Travel and Expense,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** a aplicação está pré-configurada no modo iniciado pelo **IDP** e os URLs necessários já estão pré-povoados com Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**

    > [!NOTE]
    > O identificador (ID da entidade) e o URL de resposta (URL do Serviço ao Consumidor de Afirmação) são específicos da região. Por favor, selecione com base no datacenter da sua entidade Concur. Se não conhecer o datacenter da sua entidade Concur, contacte o suporte da Concur. 

5. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Atributo do Utilizador** para editar as definições. O identificador de utilizador único tem de corresponder ao login_id do utilizador Concur. Normalmente, deve alterar **o nome user.userprincipal para** **user.mail**.

    ![Editar atributo do utilizador](common/edit-attribute.png)

6. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar os metadados e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Concur Travel and Expense.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Concur Viagens e Despesas.**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-concur-travel-and-expense-sso"></a>Configure Concur Viagens e Despesas SSO

1. Para automatizar a configuração dentro do Concur Travel and Expense, é necessário instalar a **extensão do navegador Desinsusição De 'As aplicações'** segura, clicando **em Instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar Concur Viagens e Despesas** irá direcioná-lo para a aplicação Concur Viagens e Despesas. A partir daí, forneça as credenciais de administração para assinar em Concur Viagens e Despesas. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o Concur Travel and Expense manualmente, numa janela diferente do navegador web, precisa de carregar o **Metdata XML da Federação** descarregado para [Concur SSO Self-Service Tool](https://www.concursolutions.com/nui/authadmin/ssoadmin) e iniciar seduca no seu site da empresa Concur Travel and Expense como administrador.

1. Clique em **Adicionar**.
1. Introduza um nome personalizado para o seu IdP, por exemplo "Azure AD (EUA)". 
1. Clique **em Upload XML File** e anexe o **MÍsenhão Federação XML** que descarregou anteriormente.
1. Clique **em Adicionar Metadados** para guardar a alteração.

    ![Imagem de ferramenta de self-service Concur SSO](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-concur-travel-and-expense-test-user"></a>Criar utilizador de teste de viagem e despesas concur

Nesta secção, cria-se um utilizador chamado B.Simon in Concur Travel and Expense. Trabalhe com a equipa de suporte da Concur para adicionar os utilizadores na plataforma Concur Travel and Expense. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação. 

> [!NOTE]
> O id de login do B.Simon precisa de combinar com o identificador único de B.Simon no Azure AD. Por exemplo, se o Azure AD único de B.Simon for `B.Simon@contoso.com` . B.Simon's Concur login id também `B.Simon@contoso.com` precisa ser. 

## <a name="configure-concur-mobile-sso"></a>Configurar Concur Mobile SSO
Para ativar o SSO móvel Concur, tem de dar URL de acesso ao **utilizador** da equipa de suporte Concur . Siga os passos abaixo para obter URL de acesso ao **utilizador** a partir de Azure AD:
1. Ir para **aplicações da Enterprise**
1. Clique **em Concur Viagens e Despesas**
1. Clique em **Propriedades**
1. Copie **URL de acesso ao utilizador** e dê este URL para suporte concur

> [!NOTE]
> Self-Service opção de configurar o SSO não está disponível, por isso trabalhe com a equipa de suporte da Concur para ativar o SSO móvel. 

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o Sinal de Viagem e Despesas concur no URL, onde pode iniciar o fluxo de login.

* Vá diretamente ao URL de inscrição de viagens e despesas concur e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Concur Travel and Expense para o qual configura o SSO

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo Concur Travel and Expense nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Concur Travel and Expense para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Concur Viagens e Despesas, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).