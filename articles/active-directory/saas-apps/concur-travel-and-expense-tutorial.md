---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Concur Travel and Expense / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Concur Travel and Expense.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/10/2019
ms.author: jeedes
ms.openlocfilehash: 6797d169dbdfdd63ae9cc96ac9fed133bc8d1adc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88544487"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur-travel-and-expense"></a>Tutorial: Azure Ative Directory integração única (SSO) com Concur Travel and Expense

Neste tutorial, você aprenderá a integrar Concur Travel and Expense com Azure Ative Directory (Azure AD). Quando integra Viagens e Despesas concur com AZure AD, pode:

* Controlo em Azure AD que tem acesso a Concur Viagens e Despesas.
* Permitir que os seus utilizadores sejam automaticamente inscritos para Concur Viagens e Despesas com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

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

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva Concur Viagens e Despesas** na caixa de pesquisa.
1. Selecione **Concur Viagens e Despesas** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-concur-travel-and-expense"></a>Configurar e testar Azure AD único sign-on para Concur Viagens e Despesas

Configure e teste Azure AD SSO com Concur Viagens e Despesas usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Concur Viagens e Despesas.

Para configurar e testar a Azure AD SSO com Viagens e Despesas Concur, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Concur Viagens e Despesas SSO](#configure-concur-travel-and-expense-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Concur Travel and Expense test user](#create-concur-travel-and-expense-test-user)** - para ter uma contraparte de B.Simon em Concur Viagens e Despesas que está ligada à representação AD Ad Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Concur Travel and Expense,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
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

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Concur Viagens e Despesas.**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-concur-travel-and-expense-sso"></a>Configure Concur Viagens e Despesas SSO

1. Para configurar um único sign-on no lado **de Viagens e Despesas Concur,** você precisa carregar o **metdata da Federação** descarregado XML para [Concur SSO Self-Service Tool](https://www.concursolutions.com/nui/authadmin/ssoadmin) e iniciar sessão com uma conta com o papel de "Administrador da Empresa". 

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
Para ativar o SSO móvel Concur, tem de dar URL de acesso ao **utilizador**da equipa de suporte Concur . Siga os passos abaixo para obter URL de acesso ao **utilizador** a partir de Azure AD:
1. Ir para **aplicações da Enterprise**
1. Clique **em Concur Viagens e Despesas**
1. Clique em **Propriedades**
1. Copie **URL de acesso ao utilizador** e dê este URL para suporte concur

> [!NOTE]
> Self-Service opção de configurar o SSO não está disponível, por isso trabalhe com a equipa de suporte da Concur para ativar o SSO móvel. 

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo de Viagem e Despesas Concur no Painel de Acesso, deverá ser automaticamente inscrito no Concur Travel and Expense para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente concur Viagens e Despesas com Azure AD](https://aad.portal.azure.com/)

