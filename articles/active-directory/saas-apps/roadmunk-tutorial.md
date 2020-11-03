---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Roadmunk Microsoft Docs'
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
ms.openlocfilehash: 2ee794237c644cd6ff9652f4bc2e47a8ff590161
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93243942"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-roadmunk"></a>Tutorial: Azure Ative Directory integração única (SSO) com Roadmunk

Neste tutorial, você vai aprender a integrar Roadmunk com Azure Ative Directory (Azure AD). Quando integrar o Roadmunk com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Roadmunk.
* Permita que os seus utilizadores sejam automaticamente inscritos na Roadmunk com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Inscrição única (SSO) ativada roadmunk.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Roadmunk apoia **SP e IDP** iniciaM SSO

## <a name="adding-roadmunk-from-the-gallery"></a>Adicionando Roadmunk da galeria

Para configurar a integração de Roadmunk em Azure AD, você precisa adicionar Roadmunk da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **da galeria,** escreva **Roadmunk** na caixa de pesquisa.
1. Selecione **Roadmunk** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-roadmunk"></a>Configurar e testar Azure AD SSO para roadmunk

Configure e teste Azure AD SSO com Roadmunk usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Roadmunk.

Para configurar e testar a Azure AD SSO com roadmunk, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure roadmunk SSO](#configure-roadmunk-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Roadmunk test user](#create-roadmunk-test-user)** - para ter uma contraparte de B.Simon em Roadmunk que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Roadmunk,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** se tiver **um ficheiro de metadados do Fornecedor de Serviços** e desejar configurar no modo iniciado pelo **IDP,** execute os seguintes passos:

    a. Clique **em Carregar o ficheiro de metadados**.

    ![Carregar ficheiro de metadados](common/upload-metadata.png)

    b. Clique no **logótipo da pasta** para selecionar o ficheiro de metadados que descarregou no passo 4 na secção **SSO do Roadmunk Configure** e clique em **Upload**.

    ![escolher arquivo de metadados](common/browse-upload-metadata.png)

    c. Após o carregamento com sucesso do ficheiro de metadados, os valores de URL **de identificação** e **resposta** são preenchidos automaticamente na secção de Configuração BÁSICA SAML.

    ![image1](common/idp-intiated.png)

    > [!Note]
    > Se os valores de URL **de identificação** e **resposta** não forem automaticamente poluídos, preencha os valores manualmente de acordo com o seu requisito.

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![image2](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite o URL:  `https://login.roadmunk.com`

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configuração roadmunk,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory** , selecione **Utilizadores** , e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome** , introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Roadmunk.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Roadmunk**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-roadmunk-sso"></a>Configure Roadmunk SSO

1. Inscreva-se no site da Roadmunk como administrador.

1. Clique no **Ícone do Utilizador** na parte inferior da página e selecione **Definições** de Conta .

    ![Definições de conta](./media/roadmunk-tutorial/account.png)

1. Aceda às **Definições de Autenticação > da Empresa**.

1. Nas Definições de Autenticação, execute os seguintes passos.

    ![Definições de Autenticação](./media/roadmunk-tutorial/saml-sso.png)

    a. Ativar o **sinal único SAML (SSO)**.

    b. No passo 1, ou pode carregar o ficheiro **Metadadata XML** ou pode dar o URL do mesmo.

    c. No passo 2, descarregue o ficheiro **De Metadados Roadmunk** e guarde-o no seu computador.

    d. Ativar o **Sign-In DE Aonde-americanos Apenas** no passo 3, se pretender iniciar sessão com sSO.

    e. Clique em **Guardar**.


### <a name="create-roadmunk-test-user"></a>Criar utilizador de teste Roadmunk

1. Inscreva-se no site da Roadmunk como administrador.

1. Clique no **Ícone do Utilizador** na parte inferior da página e selecione **Definições** de Conta .

    ![Utilizador de teste de Definições de Conta](./media/roadmunk-tutorial/account.png)

1. Vá ao **separador Utilizadores** e clique no **Utilizador Convidado** convidado.

    ![Convidar Utilizador](./media/roadmunk-tutorial/create-user.png)

1. Introduza os campos necessários no formulário e clique no **Convidar**.


## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo roadmunk no Painel de Acesso, deverá ser automaticamente inscrito no Roadmunk para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos Seguintes

Uma vez configurado Roadmunk, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


