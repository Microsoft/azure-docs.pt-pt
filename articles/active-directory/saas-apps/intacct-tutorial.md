---
title: 'Tutorial: Integração do Diretório Ativo Azure com Sage Intacct | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Sage Intacct.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 5a216e39ca32b16de405c7924d08da52c6eae4c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736983"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>Tutorial: Integrar Sage Intacct com diretório ativo Azure

Neste tutorial, você vai aprender a integrar Sage Intacct com Azure Ative Directory (Azure AD). Quando integrar sage Intacct com Azure AD, você pode:

* Controlo em Azure AD que tem acesso a Sage Intacct.
* Permita que os seus utilizadores sejam automaticamente inscritos na Sage Intacct com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Sage Intacct assinatura única ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Sage Intacct apoia **IDP** iniciado SSO

## <a name="adding-sage-intacct-from-the-gallery"></a>Adicionando Sage Intacct da galeria

Para configurar a integração da Sage Intacct no Azure AD, é necessário adicionar sage Intacct da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** digite **Sage Intacct** na caixa de pesquisa.
1. Selecione **Sage Intacct** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-sage-intacct"></a>Configure e teste Azure AD SSO para Sage Intacct

Configure e teste Azure AD SSO com Sage Intacct usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Sage Intacct.

Para configurar e testar o Azure AD SSO com Sage Intacct, complete os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
2. **[Configure sage Intacct SSO](#configure-sage-intacct-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create Sage Intacct test user](#create-sage-intacct-test-user)** - para ter uma contraparte de B.Simon em Sage Intacct que está ligada à representação AD AD do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Sage Intacct,** encontre a secção **Gerir** e selecione **'Único sinal de snúncio'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com página SAML,** clique no ícone de lápis para **configuração SAML básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL:  `https://www.intacct.com/ia/acct/sso_response.phtml`

1. A aplicação Sage Intacct espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique **em Editar** o ícone para abrir o diálogo dos Atributos do Utilizador..

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação Sage Intacct espera que alguns mais atributos sejam repercutidos na resposta SAML. No **diálogo "Atributos & Reclamações** do Utilizador", execute os seguintes passos para adicionar o atributoken SAML, tal como mostrado na tabela abaixo:

    | Nome do atributo  |  Atributo de origem|
    | ---------------| --------------- |
    | Nome da Empresa | **ID da empresa Sage Intacct** |
    | name | O valor deve ser igual ao **ID do utilizador** Sage Intacct, que introduz na **secção de utilizador de teste Create Sage Intacct,** que é explicada mais tarde no tutorial |

    a. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    b. Na caixa de texto **'Nome',** digite o nome do atributo indicado para esta linha.

    c. Deixe o **Espaço Namespace** em branco.

    d. Selecione Fonte como **Atributo**.

    e. A partir da lista **de atributos Fonte,** escreva ou selecione o valor do atributo indicado para esta linha.

    f. Clique **em Ok**

    exemplo, Clique em **Guardar**.

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Sage Intacct,** copie os URL(s) apropriados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Sage Intacct.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Sage Intacct**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função &quot;Acesso Predefinido&quot; selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name=&quot;configure-sage-intacct-sso&quot;></a>Configurar Sage Intacct SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Sage Intacct como administrador.

1. Clique no separador **'Empresa'** e, em seguida, clique em **Informações da Empresa.**

    ![Empresa](./media/intacct-tutorial/ic790037.png &quot;Empresa")

1. Clique no separador **Segurança** e, em seguida, clique em **Editar**.

    ![Segurança](./media/intacct-tutorial/ic790038.png "Segurança")

1. Na secção placa única na secção **SSO,** execute os seguintes passos:

    ![Sinal único em](./media/intacct-tutorial/ic790039.png "sinal único em")

    a. **Selecione Iniciar Iniciar um único sinal .**

    b. Como **tipo de fornecedor de identidade,** selecione **SAML 2.0**.

    c. Na caixa de texto **URL do Emitente,** cole o valor do **Identificador AD AZure,** que copiou do portal Azure.

    d. Na caixa de texto **URL de login,** cole o valor do URL de **login,** que copiou do portal Azure.

    e. Abra o certificado codificado **base-64** no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa **de Certificado.**

    f. Clique em **Guardar**.

### <a name="create-sage-intacct-test-user"></a>Criar utilizador de teste Sage Intacct

Para configurar os utilizadores Azure AD para que possam iniciar sage Intacct, devem ser a provisionados na Sage Intacct. Para sage Intacct, o provisionamento é uma tarefa manual.

**Para a disponibilização de contas de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu inquilino **Sage Intacct.**

1. Clique no separador **'Empresa'** e, em seguida, clique em **Utilizadores**.

    ![Utilizadores](./media/intacct-tutorial/ic790041.png "Utilizadores")

1. Clique no **separador Adicionar.**

    ![Adicionar](./media/intacct-tutorial/ic790042.png "Adicionar")

1. Na secção Informação do **Utilizador,** execute os seguintes passos:

    ![A screenshot mostra a secção de Informação do Utilizador onde pode introduzir as informações neste passo.](./media/intacct-tutorial/ic790043.png "Informação do Utilizador")

    a. Introduza o **ID** do Utilizador , o **apelido**, **o nome próprio,** o **endereço de e-mail,** o **título,** e o **telefone** de uma conta AD Azure que pretende introduzir na secção Informação do **Utilizador.**

    > [!NOTE]
    > Certifique-se de que o **ID do Utilizador** em imagens acima e o valor **do Atributo Fonte** que está mapeado com o atributo **nome** na secção **Atributos** do Utilizador no portal Azure devem ser os mesmos.

    b. Selecione os **privilégios de Administração** de uma conta AD Azure que deseja obter.

    c. Clique em **Guardar**. 
    
    d. O titular da conta Azure AD recebe um e-mail e segue um link para confirmar a sua conta antes de ficar ativa.

1. Clique **no separador 'Sign-on' único e certifique-se** de que o **ID do utilizador SSO federado** na imagem abaixo e o valor **do Atributo Fonte** que está mapeado com a secção `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` **atributos** do utilizador no portal Azure devem ser os mesmos.

    ![A screenshot mostra a secção de Informação do Utilizador onde pode introduzir o utilizador Federated S O i d.](./media/intacct-tutorial/ic790044.png "Informação do Utilizador")

> [!NOTE]
> Para fornecer contas de utilizador Azure AD, pode utilizar outras ferramentas de criação de conta de utilizador Sage Intacct ou APIs fornecidas pela Sage Intacct.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no Sage Intacct para o qual configura o SSO

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Sage Intacct nas Minhas Apps, deverá ser automaticamente inscrito no Sage Intacct para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Sage Intacct, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).