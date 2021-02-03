---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Noção | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Notion.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: jeedes
ms.openlocfilehash: 520eb25bcb138c96b24166816d3374255fb7c3b2
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493993"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-notion"></a>Tutorial: Azure Ative Directory integração única (SSO) com Noção

Neste tutorial, você vai aprender a integrar a Noção com O Diretório Ativo Azure (Azure AD). Quando integrar a Noção com AZure AD, pode:

* Controlo em Azure AD que tem acesso à Noção.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Noção com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Noção de assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Noção suporta **SP e IDP** iniciado SSO
* Noção suporta **provisão do** utilizador Just In Time
> [!NOTE]
> O identificador desta aplicação é um valor de cadeia fixo para que um espaço de trabalho noção possa ser configurado em um inquilino.


## <a name="adding-notion-from-the-gallery"></a>Adicionar noção da galeria

Para configurar a integração da Notion em Azure AD, é necessário adicionar a Noção da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Noção** na caixa de pesquisa.
1. Selecione **Notion** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-notion"></a>Configurar e testar Azure AD SSO para noção

Configure e teste Azure AD SSO com Noção usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Noção.

Para configurar e testar a Azure AD SSO com Noção, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Notion SSO](#configure-notion-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Notion test user](#create-notion-test-user)** - para ter uma contraparte de B.Simon in Notion que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Notion,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** insira o URL com o seguinte padrão que pode obter a partir do seu espaço de trabalho Noção **Definições &** Segurança dos Membros > **& identidade** URL único de > **entrada:**`https://www.notion.so/sso/saml/<CUSTOM_ID>`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de inscrição,** insira o seguinte URL:  `https://www.notion.so/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de resposta real e Sign-On URL. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação Notion espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Notion espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Name |  Atributo de origem|
    | ----------- | --------- |
    | e-mail | user.mail |
    | nomePróprio | user.givenname |
    | apelido | utilizador.sobrenome |


1. Na **configuração de um único sinal de inscrição com** a página SAML, na secção **certificado de assinatura SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações**. Vá ao seu espaço de trabalho **Notion** **Definições & identidade** de segurança & dos membros , e cole o valor que  >  copiou no campo URL de **metadados IDP.**

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Noção.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Notion**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-notion-sso"></a>Configuração SSO

Vá ao seu espaço de trabalho **Noção** **Definições & identidade** de segurança & dos membros  >  , e cole o valor url de **metadados da Federação de Aplicações** que copiou no campo **URL de metadados IDP.**

Na mesma página de definições, nos **domínios de email** clique no **suporte de contacto** para adicionar o(s) domínio de e-mail da sua organização.

Depois de os seus domínios de e-mail serem aprovados e adicionados, ative o SSO SAML utilizando o **alternador Enable SAML.**

Após um teste bem sucedido, pode impor sSO SAML utilizando o **toggle Aplico SAML.** Por favor, note que os seus administradores do espaço de trabalho Notion mantêm a capacidade de iniciar sessão com e-mail, mas todos os outros membros terão de usar o SSO SAML para iniciar sessão na Noção.

### <a name="create-notion-test-user"></a>Criar utilizador de teste de noção

Nesta secção, um utilizador chamado Britta Simon é criado na Notion. A noção suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Notion, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Notion Sign no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de inscrição no notion e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na Noção para a qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo Notion nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na Noção para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurada Noção pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).