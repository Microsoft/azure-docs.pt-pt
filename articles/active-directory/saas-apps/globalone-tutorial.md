---
title: 'Tutorial: Integração do Azure Ative Directory com a EY GlobalOne Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o EY GlobalOne.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2020
ms.author: jeedes
ms.openlocfilehash: 1754929629bd05dc48419e521fa8e07f7c873217
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91823402"
---
# <a name="tutorial-integrate-ey-globalone-with-azure-active-directory"></a>Tutorial: Integre a EY GlobalOne com o Azure Ative Directory

Neste tutorial, você vai aprender a integrar a EY GlobalOne com o Azure Ative Directory (Azure AD). Quando integrar a EY GlobalOne com Azure AD, pode:

* Controle em Azure AD que tem acesso à EY GlobalOne.
* Capacitar os seus utilizadores a serem automaticamente inscritos na EY GlobalOne com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* EY GlobalOne assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.
* EY GlobalOne apoia **SP e IDP** iniciado SSO 
* A EY GlobalOne suporta o fornecimento **de utilizadores Just In Time.**
* Assim que configurar o EY GlobalOne, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-ey-globalone-from-the-gallery"></a>Adicionar EY GlobalOne da galeria

Para configurar a integração da EY GlobalOne em Azure AD, precisa adicionar o EY GlobalOne da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **de galeria,** escreva **EY GlobalOne** na caixa de pesquisa.
1. Selecione **EY GlobalOne** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-ey-globalone"></a>Configurar e testar Azure AD SSO para eY GlobalOne

Configure e teste Azure AD SSO com EY GlobalOne usando um utilizador de teste chamado **B. Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no EY GlobalOne.

Para configurar e testar o Azure AD SSO com a EY GlobalOne, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com B. Simon.
    * **[Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b. Simon utilize um único sinal de Ad AD.
1. **[Configure o EY GlobalOne](#configure-ey-globalone)** para configurar as definições SSO no lado da aplicação.
    * Crie o utilizador de **[teste EY GlobalOne](#create-ey-globalone-test-user)** para ter uma contraparte de B. Simon na EY GlobalOne que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **EY GlobalOne,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** a aplicação está pré-configurada e os URLs necessários já estão pré-povoados com Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**

1. A aplicação EY GlobalOne espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique **em Editar** o ícone para abrir o diálogo dos Atributos do Utilizador.

    ![Screenshot que mostra a secção "Atributos do utilizador" com o ícone "Editar" selecionado.](common/edit-attribute.png)

1. Além de acima, a aplicação EY GlobalOne espera que alguns mais atributos sejam repercutidos na resposta DAL. Na secção **'Reclamações** de Utilizador' no diálogo **'Atributos do Utilizador',** execute os seguintes passos para adicionar o atributoken SAML, tal como mostrado na tabela abaixo:

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | FirstName | user.givenname |
    | LastName | utilizador.sobrenome |
    | E-mail | user.mail |
    | Empresa | `<YOUR COMPANY NAME>` |

    a. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![Screenshot que mostra a secção "Reclamações do Utilizador" com as ações "Adicionar nova reivindicação" e "Salvar" realçadas.](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **'Nome',** digite o nome do atributo indicado para esta linha.

    c. Deixe o **Espaço Namespace** em branco.

    d. Selecione Fonte como **Atributo**.

    e. A partir da lista **de atributos Source,** digite o valor de atributo mostrado para esta linha.

    f. Clique **em Ok**

    exemplo, Clique em **Guardar**.

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **Certificado (Raw)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento de certificado](common/certificateraw.png)

1. Na secção **Configuração EY GlobalOne,** copie os URL(s) apropriados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B. Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B. Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B. Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá a B. Simon usar o Azure single sign-on, concedendo acesso à EY GlobalOne.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **EY GlobalOne**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B. Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-ey-globalone"></a>Configurar eY GlobalOne

Para configurar um único sign-on no lado **EY GlobalOne,** você precisa enviar o Certificado descarregado **(Raw)** e URLs copiados apropriados do portal Azure para a equipe de [suporte EY GlobalOne](mailto:globalone.support@ey.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-ey-globalone-test-user"></a>Criar utilizador de teste EY GlobalOne

Nesta secção, um utilizador chamado Britta Simon é criado na EY GlobalOne. A EY GlobalOne suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no EY GlobalOne, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo EY GlobalOne no Painel de Acesso, deverá ser automaticamente inscrito no EY GlobalOne para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)