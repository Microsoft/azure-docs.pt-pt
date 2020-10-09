---
title: 'Tutorial: Integração do Azure Ative Directory com a ProMaster (by Inlogik) Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o ProMaster (por Inlogik).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.openlocfilehash: 67b6dc544eeda81bc24cc74088441304b6723006
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88553466"
---
# <a name="tutorial-azure-active-directory-integration-with-promaster-by-inlogik"></a>Tutorial: Integração do Azure Ative Directory com a ProMaster (por Inlogik)

Neste tutorial, aprende-se a integrar o ProMaster (by Inlogik) com o Azure Ative Directory (Azure AD).
A integração do ProMaster (by Inlogik) com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao ProMaster (by Inlogik).
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no ProMaster (by Inlogik) (Sign-on único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o ProMaster (by Inlogik), precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* ProMaster (by Inlogik) subscrição ativada única

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* ProMaster (by Inlogik) suporta **SP** e **IDP** iniciado SSO
* Uma vez configurado ProMaster (by Inlogik) pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-promaster-by-inlogik-from-the-gallery"></a>Adicionar ProMaster (por Inlogik) da galeria

Para configurar a integração do ProMaster (by Inlogik) no AD Azure, é necessário adicionar ProMaster (by Inlogik) da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva ProMaster (by Inlogik)** na caixa de pesquisa.
1. Selecione **ProMaster (by Inlogik)** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com proMaster (by Inlogik) com base num utilizador de teste chamado **B.Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no ProMaster (by Inlogik).

Para configurar e testar o único sinal de Azure AD com o ProMaster (by Inlogik), é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure ProMaster (by Inlogik) SSO](#configure-promaster-by-inlogik-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Crie o utilizador de teste ProMaster (by Inlogik)](#create-promaster-by-inlogik-test-user)** - para ter uma contraparte de B.Simon in ProMaster (by Inlogik) que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com proMaster (by Inlogik), execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **ProMaster (by Inlogik),** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:

    ```http
    https://secure.inlogik.com/<COMPANYNAME>
    https://<CUSTOMDOMAIN>/SAMLBASE
    ```

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    ```http
    https://secure.inlogik.com/<COMPANYNAME>/saml/acs
    https://<CUSTOMDOMAIN>/SAMLBASE/saml/acs
    ```

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:

    ```http
    https://secure.inlogik.com/<COMPANYNAME>
    https://<CUSTOMDOMAIN>/SAMLBASE
    ```

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de apoio ao cliente proMaster (pela Inlogik)](https://www.inlogik.com/contact) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao ProMaster (by Inlogik).

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **ProMaster (por Inlogik)**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-promaster-by-inlogik-sso"></a>Configure ProMaster (por Inlogik) SSO

Para configurar o lado **proMaster (by Inlogik)** de um único sinal de inscrição, é necessário enviar o Url de **metadados da Federação de Aplicações** para a [equipa de suporte ProMaster (by Inlogik).](https://www.inlogik.com/contact) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-promaster-by-inlogik-test-user"></a>Criar utilizador de teste ProMaster (by Inlogik)

Nesta secção, cria-se um utilizador chamado B.Simon in ProMaster (by Inlogik). Trabalhe com a [equipa de suporte ProMaster (by Inlogik)](https://www.inlogik.com/contact) para adicionar os utilizadores na plataforma ProMaster (by Inlogik). Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo ProMaster (by Inlogik) no Painel de Acesso, deverá ser automaticamente inscrito no ProMaster (by Inlogik) para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente proMaster (por Inlogik) com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o ProMaster (pela Inlogik) com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
