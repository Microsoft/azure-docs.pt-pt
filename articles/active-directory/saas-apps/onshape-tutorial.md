---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Onshape | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Onshape.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: jeedes
ms.openlocfilehash: 2ead9bd72691dc120afb60c025ce563684716038
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199327"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-onshape"></a>Tutorial: Azure Ative Directory integração única (SSO) com Onshape

Neste tutorial, você vai aprender a integrar Onshape com Azure Ative Direy (Azure AD). Quando integrar a Onshape com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Onshape.
* Permita que os seus utilizadores sejam automaticamente inscritos no Onshape com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada por onshape única (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Onshape apoia **SP e IDP** iniciado SSO
* Onshape suporta **provisão do** utilizador Just In Time
> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.


## <a name="adding-onshape-from-the-gallery"></a>Adicionando Onshape da galeria

Para configurar a integração do Onshape no AD Azure, precisa adicionar Onshape da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Onshape** na caixa de pesquisa.
1. Selecione **Onshape** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-onshape"></a>Configurar e testar Azure AD SSO para onshape

Configure e teste Azure AD SSO com Onshape usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Onshape.

Para configurar e testar a Azure AD SSO com Onshape, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Onshape SSO](#configure-onshape-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Onshape test user](#create-onshape-test-user)** - para ter uma contraparte de B.Simon em Onshape que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Onshape,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. Se for solicitado para guardar a sua única definição de sinal de inscrição, selecione **Sim**. 
1. A aplicação Onshape espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Onshape espera que alguns mais atributos mostrados abaixo sejam passados para ele na resposta SAML. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Name |  Atributo de origem|
    | --------------- | --------- |
    | nomePróprio | user.givenname |
    | apelido | utilizador.sobrenome |
    | nome da empresa | <COMPANY_NAME> |

    > [!NOTE]
    > Tem _de_ alterar o valor do atributo **Nome** de empresa para o *prefixo* de domínio da sua empresa Onshape. Por exemplo, se aceder à aplicação Onshape utilizando um URL `https://acme.onshape.com` como, o prefixo de domínio é *acme*. O valor do atributo deve ser apenas o prefixo, não todo o nome DNS.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configuração Onshape,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Onshape.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Onshape**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-onshape-sso"></a>Configurar Onshape SSO

Para obter informações sobre como configurar um único sinal no lado **on** , consulte [Integração com Microsoft Azure AD](https://cad.onshape.com/help/Content/MS_AzureAD.htm).

### <a name="create-onshape-test-user"></a>Criar utilizador de teste onshape

Nesta secção, um utilizador chamado Britta Simon é criado em Onshape. O Onshape suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Onshape, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Onshape Assinar em URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de inscrição onshape e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Onshape para o qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo Onshape nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Onshape para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Onshape, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


