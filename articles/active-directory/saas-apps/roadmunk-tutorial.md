---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com Roadmunk'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Roadmunk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 26424106098a8385faff5ab6d3de33d98576ef4e
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381308"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-roadmunk"></a>Tutorial: Azure Ative Directory integração única (SSO) com Roadmunk

Neste tutorial, você vai aprender a integrar Roadmunk com Azure Ative Directory (Azure AD). Quando integrar o Roadmunk com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Roadmunk.
* Permita que os seus utilizadores sejam automaticamente inscritos na Roadmunk utilizando as suas contas AD Azure.
* Gerencie as suas contas numa localização central, o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura Roadmunk que está habilitada para uma única sso (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

A Roadmunk apoia o SSO que é iniciado pelo prestador de *serviços* (SP) e pelo *fornecedor de identidade* (IDP).

## <a name="add-roadmunk-from-the-gallery"></a>Adicione Roadmunk da galeria

Para integrar o Roadmunk no Azure AD, a partir da galeria, adicione Roadmunk à sua lista de aplicações geridas pela SaaS:

1. Inscreva-se no portal Azure utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione **Azure Active Directory**.
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** na caixa de pesquisa, **digite Roadmunk.**
1. Selecione **Roadmunk** a partir dos resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-roadmunk"></a>Configurar e testar Azure AD SSO para roadmunk

Configure e teste Azure AD SSO com Roadmunk utilizando um utilizador de teste chamado *B.Simon*. Para fazer o SSO funcionar, você precisa estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Roadmunk.

Aqui está uma visão geral de como configurar e testar Azure AD SSO com Roadmunk:

1. [Configure Azure AD SSO](#configure-azure-ad-sso) para que os seus utilizadores possam utilizar esta funcionalidade.
    1. [Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user) para testar O Azure AD SSO utilizando b.Simon.
    1. [Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que a B.Simon utilize o Azure AD SSO.
1. [Configure o Roadmunk SSO](#configure-roadmunk-sso) para configurar as definições SSO no lado da aplicação.
    1. [Crie um utilizador de teste Roadmunk](#create-roadmunk-test-user) para que possa ligar a contraparte de B.Simon em Roadmunk à representação AD AZure do utilizador.
1. [Teste SSO](#test-sso) para se certificar de que a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir que o Azure AD SSO no portal Azure:

1. No portal Azure, na página de integração da aplicação **Roadmunk,** encontre a secção **Gerir** e, em seguida, selecione **um único sinal de inscrição**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, selecione o ícone de caneta para **configuração SAML básica** para editar as definições.

   ![Screenshot mostrando o ícone editar para configuração SAML básica.](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** se tiver um ficheiro de metadados SP e pretender configurar no modo iniciado pelo IDP, siga estes passos:

    a. Selecione **carregar o ficheiro de metadados**.

    ![Screenshot mostrando a ligação para carregar o ficheiro de metadados.](common/upload-metadata.png)

    b. Selecione o ícone de pasta para escolher o ficheiro de metadados que descarregou no passo 4 do procedimento "Configure Roadmunk SSO". Em seguida, selecione **Carregar**.

    ![Screenshot mostrando como escolher o ficheiro de metadados.](common/browse-upload-metadata.png)

    Após o carregamento do ficheiro de metadados, na secção **de Configuração BÁSICA SAML,** os valores de URL **de identificação** e **resposta** são automaticamente povoados.

    ![Screenshot mostrando a secção de configuração básica do SAML. Destacam-se o campo Identifier e o campo URL de resposta.](common/idp-intiated.png)

    > [!Note]
    > Se os valores de URL **de identificação** e **resposta** não forem preenchidos automaticamente, preencha os valores manualmente.

1. Se pretender configurar a aplicação no modo iniciado pelo SP, selecione **Definir URLs adicionais**. No campo **URL de inscrição,** escreva `https://login.roadmunk.com`

    ![Screenshot mostrando onde definir um URL de inscrição para o modo iniciado pelo SP.](common/metadata-upload-additional-signon.png)

1. Na **configuração de um único sinal com** a página SAML, na secção certificado de assinatura **SAML,** encontre **o Metadados da Federação XML**. Em seguida, **selecione Baixar** para descarregar o certificado e guardá-lo no seu computador.

    ![Screenshot mostrando o link de descarregamento para o certificado de assinatura SAML.](common/metadataxml.png)

1. Na secção **Configuração Roadmunk,** copie o URL ou URLs de que necessita.

    ![Screenshot mostrando onde copiar URLs de configuração.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure. Vai nomear o utilizador *B.Simon.*

1. A partir do painel esquerdo no portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >  **Users**  >  **Todos os utilizadores**.
1. Na parte superior da janela, selecione **Novo utilizador**.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome** , introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, introduza `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use Azure SSO, concedendo acesso a Roadmunk.

1. No portal Azure, selecione **Aplicações empresariais**  >  **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Roadmunk**.
1. Na página geral da aplicação, encontre a secção **Gerir** e, em seguida, selecione **Utilizadores e grupos**.
1. Selecione **Adicionar utilizador**. Em seguida, na caixa de diálogo **de atribuição de adicionar,** selecione **Utilizadores e grupos**.
1. Na caixa de diálogo **de Utilizadores e grupos,** na lista **de Utilizadores,** selecione **B.Simon**. Em seguida, na parte inferior da caixa de diálogo, escolha **Select**.
1. Se espera que uma função seja atribuída aos utilizadores, escolha-a no menu **Select a role** drop-down. Se não tiver sido criada qualquer função para esta aplicação, a função **De Acesso Predefinido** é selecionada.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

## <a name="configure-roadmunk-sso"></a>Configure Roadmunk SSO

1. Inscreva-se no site da Roadmunk como administrador.

1. Na parte inferior da página, selecione o ícone do utilizador e, em seguida, selecione **Definições de Conta**.

    ![Screenshot mostrando onde selecionar as definições da conta de utilizador.](./media/roadmunk-tutorial/account.png)

1. Aceda **Company** às  >  **Definições de Autenticação da** Empresa .

1. Na página **Definições de Autenticação,** siga estes passos:

    ![Screenshot mostrando a página Definições de Autenticação.](./media/roadmunk-tutorial/saml-sso.png)

    a. Ligue **o sinal único SAML (SSO)**.

    b. Na secção **Passo 1,** faça o upload do ficheiro XML dos metadados ou forneça o URL para os metadados.

    c. Na secção **Passo 2,** descarregue o ficheiro Metadados roadmunk e guarde-o no seu computador.

    d. Se pretender iniciar sso utilizando sSO, na secção **Passo 3,** selecione **Executar apenas Sign-In SAML**.

    e. Selecione **Guardar**.


### <a name="create-roadmunk-test-user"></a>Criar utilizador de teste Roadmunk

1. Inscreva-se no site da Roadmunk como administrador.

1. Selecione o ícone do utilizador na parte inferior da página e, em seguida, selecione **Definições de Conta**.

    ![Screenshot mostrando como abrir as definições de conta para o utilizador de teste.](./media/roadmunk-tutorial/account.png)

1. Abra o separador **Utilizadores** e, em seguida, selecione **Convidar Utilizador**.

    ![Screenshot mostrando o separador Utilizadores. Destaca-se o botão 'Utilizador convidado'. Na janela aberta, destacam-se os campos de Email e Role.](./media/roadmunk-tutorial/create-user.png)

1. No formulário que aparece, preencha as informações necessárias e, em seguida, selecione **Convidar**.


## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração Azure AD SSO utilizando o painel de acesso.

No portal My Apps, quando selecionar o azulejo **roadmunk,** deverá ser automaticamente inscrito na conta Roadmunk para a qual configura sSO. Para obter mais informações, consulte [iniciar sômis e inicie aplicações a partir do portal My Apps.](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)

## <a name="next-steps"></a>Próximos passos

Depois de configurar roadmunk, pode impor o controlo da sessão. O controlo da sessão protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o acesso condicional. 

Saiba como [impor o controlo da sessão utilizando](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)o Microsoft Cloud App Security .


