---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com LinkedIn Elevate | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o LinkedIn Elevate.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.openlocfilehash: d4410a39cc9b04565d7b753b7821e11c8ece2593
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458545"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-elevate"></a>Tutorial: Azure Ative Directory integração única (SSO) com LinkedIn Elevate

Neste tutorial, você vai aprender a integrar LinkedIn Elevate com Azure Ative Directory (Azure AD). Quando integrar o LinkedIn Elevate com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao LinkedIn Elevate.
* Capacitar os seus utilizadores a serem automaticamente inscritos no LinkedIn Elevate com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* LinkedIn Elevate subscrição única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.



* LinkedIn Elevate suporta **SP e IDP** iniciado SSO
* LinkedIn Elevate suporta **provisão de** utilizadores just in time
* LinkedIn Elevate suporta fornecimento [ **automatizado** de utilizadores](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Adicionar LinkedIn Elevate da galeria

Para configurar a integração do LinkedIn Elevate em Azure AD, é necessário adicionar o LinkedIn Elevate da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite LinkedIn Elevate** na caixa de pesquisa.
1. Selecione **LinkedIn Elevate** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-elevate"></a>Configurar e testar Azure AD único sinal para LinkedIn Elevate

Configure e teste Azure AD SSO com LinkedIn Elevate usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no LinkedIn Elevate.

Para configurar e testar o Azure AD SSO com o LinkedIn Elevate, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o LinkedIn Elevate SSO](#configure-linkedin-elevate-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create LinkedIn Elevate test user](#create-linkedin-elevate-test-user)** - para ter uma contraparte de B.Simon no LinkedIn Elevate que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **LinkedIn Elevate,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier,** insira o valor **de ID** da Entidade, irá copiar o valor de ID da Entidade a partir do Portal linkedin explicado mais tarde neste tutorial.

    b. Na caixa de texto **URL de resposta,** insira o valor **do Url de Acesso ao Consumidor de Afirmação (ACS),** irá copiar o valor do Url de Acesso ao Consumidor (ACS) do Portal linkedin explicado mais tarde neste tutorial.

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

1. A aplicação LinkedIn Elevate espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem seguinte mostra a lista de atributos predefinidos, onde como **identificador** de nome é mapeado com **user.userprincipalname**. A aplicação LinkedIn Elevate espera que o identificador de nomes seja mapeado com **o user.mail,** pelo que é necessário editar o mapeamento do atributo clicando no ícone Editar e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação LinkedIn Elevate espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com o seu requisito.

    | Name | Atributo de origem|
    | -------| -------------|
    | departamento | user.department |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configuração LinkedIn Elevate, copie** os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao LinkedIn Elevate.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, **selecione LinkedIn Elevate**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-linkedin-elevate-sso"></a>Configurar LinkedIn Elevate SSO

1. Numa janela diferente do navegador web, inscreva-se no seu inquilino LinkedIn Elevate como administrador.

1. No **Centro de Contas**, clique em **Definições** **Globais**. Além disso, **selecione Elevate - Elevate AAD Test** a partir da lista de dropdown.

    ![A screenshot mostra as Definições Globais onde pode selecionar Elevate A A D Test.](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Clique em **OR Clique aqui para carregar e copiar campos individuais a partir do formulário** e executar os seguintes passos:

    ![A screenshot mostra single Sign-On onde pode introduzir os valores descritos.](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. Copiar **iD da entidade** e colá-lo na caixa de texto **identifier** na **Configuração Básica SAML** no portal Azure.

    b. Copiar **Url de Acesso ao Consumidor (ACS)** e colá-lo na caixa de texto URL de **resposta** na **Configuração Básica SAML** no portal Azure.

1. Aceda à secção **de Definições de Administração LinkedIn.** Faça o upload do ficheiro XML que descarregou a partir do portal Azure clicando na opção de ficheiro Upload XML.

    ![O Screenshot mostra configurar as definições do fornecedor de serviços LinkedIn S S O onde pode carregar um ficheiro X M L.](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Clique **em** para ativar SSO. O estado de SSO mudará de **Não Ligado** a **Conectado**

    ![A screenshot mostra Sign-On única onde pode selecionar licenças de atribuição automática.](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-elevate-test-user"></a>Criar utilizador de teste LinkedIn Elevate

A Aplicação LinkedIn Elevate suporta o fornecimento do utilizador a tempo e após a autenticação os utilizadores serão criados automaticamente na aplicação. Na página de definições de administração no portal LinkedIn Elevate, ligue o interruptor Atribuir automaticamente licenças para **ativas Apenas** no provisionamento de tempo, o que também irá atribuir uma licença ao utilizador. O LinkedIn Elevate também suporta o fornecimento automático de utilizadores, podendo encontrar mais detalhes [aqui](linkedinelevate-provisioning-tutorial.md) sobre como configurar o fornecimento automático do utilizador.

   ![Criar um utilizador de teste AZure AD](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo LinkedIn Elevate no Painel de Acesso, deverá ser automaticamente inscrito no LinkedIn Elevate para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente LinkedIn Elevate com Azure AD](https://aad.portal.azure.com/)