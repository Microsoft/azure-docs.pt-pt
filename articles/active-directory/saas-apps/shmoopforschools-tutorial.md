---
title: 'Tutorial: Integração do Diretório Ativo Azure com Shmoop For Schools | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Shmoop For Schools.
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
ms.openlocfilehash: d1d29e939ab01f6311b665eaec8425b7cd38f207
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98729482"
---
# <a name="tutorial-integrate-shmoop-for-schools-with-azure-active-directory"></a>Tutorial: Integrar Shmoop para escolas com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar Shmoop For Schools com Azure Ative Directory (Azure AD). Quando integrar o Shmoop For Schools com Azure AD, pode:

* Controle em Azure AD que tem acesso a Shmoop For Schools.
* Permita que os seus utilizadores sejam automaticamente inscritos no Shmoop For Schools com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Shmoop For Schools single sign-on (SSO) ativada subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Shmoop For Schools apoia **SP** iniciado SSO
* Shmoop For Schools suporta provisão de utilizadores **justos no tempo**

## <a name="adding-shmoop-for-schools-from-the-gallery"></a>Adicionando Shmoop para escolas da galeria

Para configurar a integração do Shmoop For Schools em AD Azure, precisa adicionar Shmoop For Schools da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **escreva Shmoop For Schools** na caixa de pesquisa.
1. Selecione **Shmoop For Schools** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-shmoop-for-schools"></a>Configure e teste Azure AD SSO para Shmoop For Schools

Configure e teste Azure AD SSO com Shmoop For Schools usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Shmoop For Schools.

Para configurar e testar a Azure AD SSO com Shmoop For Schools, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
2. **[Configure o Shmoop For Schools SSO](#configure-shmoop-for-schools-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create Shmoop For Schools test user](#create-shmoop-for-schools-test-user)** - para ter uma contrapartida de B.Simon em Shmoop For Schools que está ligada à representação AZure AD do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **Shmoop For Schools,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com página SAML,** clique no ícone de lápis para **configuração SAML básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://schools.shmoop.com/<uniqueid>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de apoio ao cliente da Shmoop For Schools](mailto:support@shmoop.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação Shmoop For Schools espera as afirmações DO SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Shmoop For Schools espera que poucos mais atributos sejam repercutidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name |  Atributo de origem|
    | --------- | --------------- |
    | papel      | user.assignedroles |

    > [!NOTE]
    > A Shmoop for School apoia duas funções para os utilizadores: **Professor** e **Aluno.** Crie estas funções em Azure AD para que os utilizadores possam ser atribuídos as funções apropriadas. Para entender como configurar papéis em Azure AD, consulte [aqui.](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações** e guarde-o no seu computador.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Shmoop For Schools.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Shmoop For Schools**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se tiver configurado as funções como explicado no acima, pode selecioná-lo a partir do Dropdown de **função Select.**
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-shmoop-for-schools-sso"></a>Configurar o Shmoop para escolas SSO

Para configurar um único sign-on no lado **shmoop para escolas,** você precisa enviar o **url de metadados da Federação de Aplicações** para [a equipa de apoio shmoop for schools](mailto:support@shmoop.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-shmoop-for-schools-test-user"></a>Criar utilizador de teste shmoop para escolas

Nesta secção, um utilizador chamado B.Simon é criado em Shmoop For Schools. O Shmoop For Schools suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Shmoop For Schools, um novo é criado após a autenticação.

> [!NOTE]
> Se precisar de criar um utilizador manualmente, contacte a equipa de apoio do [Shmoop For Schools](mailto:support@shmoop.com).

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para O URL de inscrição de escolas para escolas, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição de Shmoop For Schools e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Shmoop For Schools nas Minhas Apps, este será redirecionado para O URL de inscrição de Escolas para Escolas. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Shmoop For Schools, pode impor o controlo de sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
