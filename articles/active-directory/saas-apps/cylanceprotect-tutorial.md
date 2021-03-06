---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a CylancePROTECT | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o CylancePROTECT.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: a5daf83346db901288d5bc614f341c354d0c008f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481349"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cylanceprotect"></a>Tutorial: Azure Ative Directory integração única (SSO) com CylancePROTECT

Neste tutorial, você vai aprender a integrar CylancePROTECT com Azure Ative Directory (Azure AD). Quando integrar a CylancePROTECT com a Ad Azure, pode:

* Controlo em Azure AD que tem acesso à CylancePROTECT.
* Capacitar os seus utilizadores a serem automaticamente inscritos na CylancePROTECT com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* CylancePROTECT assinatura única ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* CylancePROTECT suporta **IDP** iniciado SSO.

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="add-cylanceprotect-from-the-gallery"></a>Adicione CylancePROTECT da galeria

Para configurar a integração da CylancePROTECT no AD Azure, é necessário adicionar a CylancePROTECT da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite CylancePROTECT** na caixa de pesquisa.
1. Selecione **CylancePROTECT** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-cylanceprotect"></a>Configure e teste Azure AD SSO para CylancePROTECT

Configure e teste Azure AD SSO com CylancePROTECT usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AD Azure e o utilizador relacionado na CylancePROTECT.

Para configurar e testar a Azure AD SSO com a CylancePROTECT, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure CylancePROTECT SSO](#configure-cylanceprotect-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create CylancePROTECT test user](#create-cylanceprotect-test-user)** - para ter uma contraparte de B.Simon em CylancePROTECT que está ligada à representação AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **CylancePROTECT,** encontre a secção **Gerir** e selecione **um único sinal de sessão.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. No **set-on único com** a página SAML, insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier,** digite um dos seguintes URLs:
    
    | Region | Valor URL |
    |----------|---------|
    | Nordeste Asia-Pacific (APNE1)| `https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Asia-Pacific Sudeste (AU) | `https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml` |
    | Europa Central (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | América do Norte|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | América do Sul (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

    b. Na caixa de texto **URL de resposta,** digite um dos seguintes URLs:
    
    | Region | Valor URL |
    |----------|---------|
    | Nordeste Asia-Pacific (APNE1)|`https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Asia-Pacific Sudeste (AU)|`https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Europa Central (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | América do Norte|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | América do Sul (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

1. A aplicação CylancePROTECT espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem seguinte mostra a lista de atributos predefinidos, onde como **identificador** de nome é mapeado com **user.userprincipalname**. A aplicação CylancePROTECT espera que **o identificador** de identificação seja mapeado com **user.mail** e remova todas as outras reclamações restantes, pelo que é necessário editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar CylancePROTECT,** copie os URL(s) apropriados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

> [!NOTE]
> Abra o certificado codificado Base64 descarregado num editor de texto e copie apenas o texto entre tags **START** e **END** para colar no portal Cylance Admin.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a CylancePROTECT.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **CylancePROTECT**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-cylanceprotect-sso"></a>Configure CylancePROTECT SSO

Para configurar um único sinal no lado **CylancePROTECT,** é necessário enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de [suporte CylancePROTECT.](https://www.cylance.com/en-us/resources/support/support-overview.html) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados. Para mais informações, utilize documentação cylance: [https://support.cylance.com/s/](https://support.cylance.com/s/) .

### <a name="create-cylanceprotect-test-user"></a>Criar utilizador de teste CylancePROTECT

Nesta secção, cria-se um utilizador chamado Britta Simon in CylancePROTECT. Trabalhe com o administrador da consola para adicionar os utilizadores na plataforma CylancePROTECT. O titular da conta Azure Ative Directory receberá um e-mail e seguirá um link para confirmar a sua conta antes de ficar ativa.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no CylancePROTECT para o qual configura o SSO.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo CylancePROTECT nas Minhas Apps, deverá ser automaticamente inscrito no CylancePROTECT para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado CylancePROTECT, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
