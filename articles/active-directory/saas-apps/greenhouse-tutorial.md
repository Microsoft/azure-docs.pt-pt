---
title: 'Tutorial: Integração do Diretório Ativo Azure com | de Estufa Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Greenhouse.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2021
ms.author: jeedes
ms.openlocfilehash: 6a14b16e34faa827228594bf6d4f0bd9ed48cf72
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221760"
---
# <a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>Tutorial: Integração do Diretório Ativo Azure com Greenhouse

Neste tutorial, você vai aprender a integrar Greenhouse com Azure Ative Direy (Azure AD). Quando integrar a Greenhouse com a Ad Azure, pode:

* Controlo em Azure AD que tem acesso a Greenhouse.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Greenhouse com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura de sso individual com efeito de estufa (SSO).

> [!NOTE] 
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública. 

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* A Greenhouse apoia **o SP e o IDP** iniciado sSO.

## <a name="adding-greenhouse-from-the-gallery"></a>Adicionando estufa da galeria

Para configurar a integração da Greenhouse em Azure AD, você precisa adicionar Greenhouse da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **digite Greenhouse** na caixa de pesquisa.
1. Selecione **Greenhouse** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-greenhouse"></a>Configure e teste Azure AD SSO para estufa

Configure e teste Azure AD SSO com Greenhouse usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Greenhouse.

Para configurar e testar a Azure AD SSO com greenhouse, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
2. **[Configure greenhouse SSO](#configure-greenhouse-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create Greenhouse test user](#create-greenhouse-test-user)** - ter uma contraparte de Britta Simon em Greenhouse que está ligada à representação AD AZure do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **Greenhouse,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<COMPANYNAME>.greenhouse.io`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando um dos seguintes padrões:
    
    | URL de Resposta|
    | -------------- |
    | `https://<COMPANYNAME>.greenhouse.io/users/saml/consume` |
    | `https://app.greenhouse.io/<ENTITY ID>/users/saml/consume` |
    |

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<COMPANYNAME>.greenhouse.io`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e sinal no URL. Contacte [a equipa de suporte do Cliente greenhouse](https://www.greenhouse.io/contact) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configurar greenhouse,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Greenhouse.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, **selecione Greenhouse**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-greenhouse-sso"></a>Configure estufa SSO

1. Numa janela diferente do navegador web, inscreva-se no site da Greenhouse como administrador.

1. Vá ao **Configure > Dev Center > Único Sign-On**.

    ![screenshot para a página sso](./media/greenhouse-tutorial/configure.png)

1. Execute os seguintes passos na página **'Sign-On' único.**

    ![screenshot para a página de configuração sso](./media/greenhouse-tutorial/sso-page.png)

    a. Copiar **SSO Afirmação Valor URL do consumidor,** colar este valor na caixa de texto **URL resposta** na secção **configuração DE SAML básico** no portal Azure.

    b. Na caixa de texto **ID/Emitente** da Entidade, cole o valor **identificador Azure AD** que copiou do portal Azure.

    c. Na caixa de texto **URL de Sign-On única,** cole o valor URL de **login** que copiou a partir do portal Azure.

    d. Abra o **Metdata XML** da Federação descarregada do portal Azure para o Bloco de Notas e cole o conteúdo na caixa de texto **de impressão digital de certificado IdP.**

    e. Selecione o valor do **formato do identificador** de nome a partir do dropdown.

    f. Clique **em Iniciar Testes.**

    >[!NOTE]
    >Em alternativa, também pode carregar o ficheiro **XML dos metadados da Federação** clicando na opção **Escolha Ficheiro.**

### <a name="create-greenhouse-test-user&quot;></a>Criar utilizador de teste de estufa

A fim de permitir que os utilizadores de Azure AD acedam à Estufa, devem ser a provisionados na Greenhouse. No caso da Estufa, o provisionamento é uma tarefa manual.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador de Estufa ou APIs fornecidas pela Greenhouse para fornecer contas de utilizador Azure AD. 

**Para a disponibilização de uma conta de utilizador, execute os seguintes passos:**

1. Faça login no site da sua empresa **Greenhouse** como administrador.

2. Vá ao **Configure > Utilizadores > novos utilizadores**
   
    ![Utilizadores](./media/greenhouse-tutorial/create-user-1.png &quot;Utilizadores")

4. Na secção **Adicionar Novos Utilizadores,** execute os seguintes passos:
   
    ![Adicionar novo utilizador](./media/greenhouse-tutorial/create-user-2.png "Adicionar novo utilizador")

    a. Na caixa de texto do **utilizador Enter,** digite o endereço de e-mail de uma conta válida do Azure Ative Directory que pretende.

    b. Clique em **Guardar**.    
   
      >[!NOTE]
      >Os detentores de conta Azure Ative Directory receberão um e-mail incluindo um link para confirmar a conta antes de se tornar ativa.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Greenhouse Sign on URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de inscrição de greenhouse e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na Estufa para a qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo greenhouse nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na Estufa para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)



## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Greenhouse, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).