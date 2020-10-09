---
title: 'Tutorial: Integração do Azure Ative Directory com a Cognidox Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Cognidox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/18/2019
ms.author: jeedes
ms.openlocfilehash: 0db29103c208fdf2478a93d3605b998a4acc1062
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88551630"
---
# <a name="tutorial-integrate-cognidox-with-azure-active-directory"></a>Tutorial: Integrar a Cognidox com o Azure Ative Directory

Neste tutorial, você vai aprender a integrar Cognidox com Azure Ative Directory (Azure AD). Quando integrar o Cognidox com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Cognidox.
* Permita que os seus utilizadores sejam automaticamente inscritos na Cognidox com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Cognidox assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Cognidox apoia **SP e IDP** iniciaM SSO
* Cognidox suporta **provisão de** utilizadores just in time

## <a name="adding-cognidox-from-the-gallery"></a>Adicionar Cognidox da galeria

Para configurar a integração da Cognidox no AD Azure, é necessário adicionar o Cognidox da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **escreva Cognidox** na caixa de pesquisa.
1. Selecione **Cognidox** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com Cognidox usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Cognidox.

Para configurar e testar o Azure AD SSO com a Cognidox, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Cognidox SSO](#configure-cognidox-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
5. **[Create Cognidox test user](#create-cognidox-test-user)** - para ter uma contraparte de B.Simon em Cognidox que está ligada à representação AD Ad Azure do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Cognidox,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `urn:net.cdox.<YOURCOMPANY>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<YOURCOMPANY>.cdox.net/auth/postResponse`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<YOURCOMPANY>.cdox.net/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte a [equipa de suporte do Cliente Cognidox](mailto:support@cognidox.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. A aplicação Cognidox espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique em Editar o ícone para abrir o diálogo dos Atributos do Utilizador.

    ![image](common/edit-attribute.png)

6. Além de acima, a aplicação Cognidox espera que alguns mais atributos sejam repercutidos na resposta SAML. Na secção 'Reclamações de Utilizador' no diálogo 'Atributos do Utilizador', execute os seguintes passos para adicionar o atributoken SAML, tal como mostrado na tabela abaixo: 

    | Name | Espaço de Nomes  |  Transformação | Parâmetro 1 |
    | ---------------| --------------- | --------- |
    | wanshort | http: \/ /appinux.com/windowsaccountname2 | ExtractoMailPrefixo() | user.userprincipalname |


    a. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    b. Na caixa de texto **'Nome',** digite o nome do atributo indicado para esta linha.

    c. Na caixa de texto **Namespace,** digite o espaço de nome mostrado para esta linha.

    d. Selecione Fonte como **Transformação**.

    e. Da lista **de Transformação,** digite o valor indicado para esta linha.

    f. Da lista **do Parâmetro 1,** digite o valor indicado para esta linha.

    exemplo, Clique em **Guardar**.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configurar Cognidox,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-cognidox-sso"></a>Configure Cognidox SSO

Para configurar um único sign-on no lado **cognidox,** você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipe de [suporte Cognidox](mailto:support@cognidox.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Cognidox.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Cognidox**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-cognidox-test-user"></a>Criar utilizador de teste Cognidox

Nesta secção, um utilizador chamado B.Simon é criado em Cognidox. A Cognidox suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na Cognidox, um novo é criado após a autenticação.

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Cognidox no Painel de Acesso, deverá ser automaticamente inscrito no Cognidox para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

