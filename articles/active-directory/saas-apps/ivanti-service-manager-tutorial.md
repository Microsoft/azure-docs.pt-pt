---
title: 'Tutorial: Integração do Azure Ative Directory com o Ivanti Service Manager (ISM) Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Ivanti Service Manager (ISM).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 14297c74-0d57-4146-97fa-7a055fb73057
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87ceeeb1a712c37bc74e12e910645013d77e8df6
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85605396"
---
# <a name="tutorial-azure-active-directory-integration-with-ivanti-service-manager-ism"></a>Tutorial: Integração do Diretório Ativo Azure com o Ivanti Service Manager (ISM)

Neste tutorial, aprende-se a integrar o Ivanti Service Manager (ISM) com o Azure Ative Directory (Azure AD).
Integrar o Ivanti Service Manager (ISM) com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Ivanti Service Manager (ISM).
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Ivanti Service Manager (ISM) (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o Ivanti Service Manager (ISM), precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Ivanti Service Manager (ISM) subscrição única ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Ivanti Service Manager (ISM) apoia **SP e IDP** iniciado SSO
* Ivanti Service Manager (ISM) suporta o fornecimento do utilizador **Just In Time**

## <a name="adding-ivanti-service-manager-ism-from-the-gallery"></a>Adicionar Ivanti Service Manager (ISM) da galeria

Para configurar a integração do Ivanti Service Manager (ISM) no Azure AD, é necessário adicionar o Ivanti Service Manager (ISM) da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Ivanti Service Manager (ISM) da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Ivanti Service Manager (ISM)**, selecione **Ivanti Service Manager (ISM)** do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![Ivanti Service Manager (ISM) na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o Ivanti Service Manager (ISM) com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Ivanti Service Manager (ISM).

Para configurar e testar o Azure AD com o Ivanti Service Manager (ISM), tem de completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Ivanti Service Manager (ISM) Single Sign-On](#configure-ivanti-service-manager-ism-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Ivanti Service Manager (ISM) test user](#create-ivanti-service-manager-ism-test-user)** - para ter uma contraparte de Britta Simon em Ivanti Service Manager (ISM) que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o Ivanti Service Manager (ISM), execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **do Ivanti Service Manager (ISM),** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração de 'Sessão única' com** a página SAML, clique em **Editar** o ícone para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![Ivanti Service Manager (ISM) Domínio e URLs informações únicas de súmis](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:
    
        ```https
        https://<customer>.saasit.com/
        https://<customer>.saasiteu.com/
        https://<customer>.saasitau.com/
        ```

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<customer>/handlers/sso/SamlAssertionConsumerHandler.ashx`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![Ivanti Service Manager (ISM) Domínio e URLs informações únicas de súmis](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:`https://<customer>.saasit.com/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte a [equipa de suporte ao cliente ivanti Service Manager (ISM)](https://www.ivanti.com/support/contact) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. Na **configuração de 'Sessão's single com página SAML,** na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Raw)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificateraw.png)

7. Na secção **Configurar Ivanti Service Manager (ISM),** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-ivanti-service-manager-ism-single-sign-on"></a>Configurar o Ivanti Service Manager (ISM) único sign-on

Para configurar um único sign-on no lado **do Ivanti Service Manager (ISM),** é necessário enviar o Certificado descarregado **(Raw)** e URLs copiados apropriados do portal Azure para a equipa de [suporte do Ivanti Service Manager (ISM).](https://www.ivanti.com/support/contact) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador** **brittasimon \@ yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, permite à Britta Simon utilizar o Azure single sign-on, concedendo acesso ao Ivanti Service Manager (ISM).

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** selecione **Ivanti Service Manager (ISM)**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Ivanti Service Manager (ISM)**.

    ![O link Ivanti Service Manager (ISM) na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-ivanti-service-manager-ism-test-user"></a>Criar o utilizador de teste Ivanti Service Manager (ISM)

Nesta secção, um utilizador chamado Britta Simon é criado no Ivanti Service Manager (ISM). O Ivanti Service Manager (ISM) suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Ivanti Service Manager (ISM), um novo é criado após a autenticação.

> [!Note]
> Se precisar de criar um utilizador manualmente, contacte a [equipa de suporte do Ivanti Service Manager (ISM).](https://www.ivanti.com/support/contact)

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo ivanti Service Manager (ISM) no Painel de Acesso, deverá ser automaticamente inscrito no Ivanti Service Manager (ISM) para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

