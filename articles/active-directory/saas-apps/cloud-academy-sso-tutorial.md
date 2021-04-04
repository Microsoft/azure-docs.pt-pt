---
title: 'Tutorial: Azure Ative Directory SSO integração com cloud Academy - SSO'
description: Neste tutorial, você aprenderá a configurar um único sign-on entre Azure Ative Directory e Cloud Academy - SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/15/2020
ms.author: jeedes
ms.openlocfilehash: fa46d6e5c7f1007e3a90e22eb9d4f46e18251a28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98729837"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-cloud-academy---sso"></a>Tutorial: Azure Ative Directory integração única com cloud Academy - SSO

Neste tutorial, você vai aprender a integrar cloud Academy - SSO com Azure Ative Directory (Azure AD). Quando integras a Cloud Academy - SSO com Azure AD, podes:

* Use a Azure AD para controlar quem pode aceder à Cloud Academy - SSO.
* Permita que os seus utilizadores sejam automaticamente inscritos na Cloud Academy - SSO com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição cloud academy - SSO com um único sign-on (SSO) ativado.

## <a name="tutorial-description"></a>Descrição tutorial

Neste tutorial, você vai configurar e testar Azure AD SSO em um ambiente de teste.

* Cloud Academy - SSO suporta **SSO** iniciado SP
* Cloud Academy - SSO suporta provisão de utilizadores **just in time**

## <a name="add-cloud-academy---sso-from-the-gallery"></a>Adicionar Cloud Academy - SSO da galeria

Para configurar a integração da Cloud Academy - SSO em AD Azure, você precisa adicionar Cloud Academy - SSO da galeria à sua lista de aplicações geridas saaS:

1. Inscreva-se no portal Azure com uma conta de trabalho ou escola ou com uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione **Azure Active Directory**.
1. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **de galeria,** insira **a Cloud Academy - SSO** na caixa de pesquisa.
1. Selecione **Cloud Academy - SSO** no painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-cloud-academy---sso"></a>Configurar e testar Azure AD SSO para cloud Academy - SSO

Você vai configurar e testar Azure AD SSO com Cloud Academy - SSO usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador correspondente na Cloud Academy - SSO.

Para configurar e testar Azure AD SSO com Cloud Academy - SSO, você completará estes passos de alto nível:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem a funcionalidade.
    1. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com um único sinal de sessão.
    1. **[Conceder acesso ao utilizador de teste](#grant-access-to-the-test-user)** para permitir ao utilizador utilizar um único sinal de Azure.
1. **[Configurar um único sign-on para Cloud Academy - SSO](#configure-single-sign-on-for-cloud-academy)** no lado da aplicação.
    1. **[Criar um Cloud Academy - Utilizador de teste SSO](#create-a-cloud-academy-test-user)** como contrapartida da representação AD AZure do utilizador.
1. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir que o Azure AD SSO no portal Azure:

1. No portal Azure, na página de integração de aplicações **Cloud Academy - SSO,** na secção **Gerir,** selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. Na **configuração de Sign-On única com** a página SAML, selecione o botão de lápis para **configuração SAML Básica** para editar as definições:

   ![Screenshot que mostra o botão de lápis para editar a configuração básica do SAML.](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL de entrada de assinatura,** digite um dos seguintes URLs:
    
    | URL de início de sessão |
    |--------------|
    | `https://cloudacademy.com/login/enterprise/` |
    | `https://app.qa.com/login/enterprise/` |
    |
    
    b. Na caixa de texto **URL de resposta,** digite um dos seguintes URLs:
    
    | URL de Resposta |
    |--------------|
    | `https://cloudacademy.com/labs/social/complete/saml/` |
    | `https://app.qa.com/labs/social/complete/saml/` |
    |
1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** selecione o botão de cópia para copiar o **Url de Metadados da Federação de Aplicações**. Salve a URL.

    ![Screenshot que mostra o botão de cópia para o URL de metadados da federação de aplicações.](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste chamado B.Simon no portal Azure.

1. No painel esquerdo do portal Azure, selecione **Azure Ative Directory**. Selecione **Utilizadores** e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** complete estes passos:
   1. Na caixa **Nome,** insira **B.Simon**.  
   1. Na caixa **do nome do utilizador,** \<username> @ \<companydomain> introduza \<extension> . . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione **Mostrar a palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa **palavra-passe.**
   1. Selecione **Criar**.

### <a name="grant-access-to-the-test-user"></a>Conceder acesso ao utilizador de teste

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo a esse utilizador acesso à Cloud Academy - SSO.

1. No portal Azure, selecione **aplicações Enterprise** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Cloud Academy - SSO**.
1. Na página geral da aplicação, na secção **Gerir,** selecione **Utilizadores e grupos**:
1. **Selecione Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **de atribuição de adicionar:**
1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** na lista **de Utilizadores** e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

## <a name="configure-single-sign-on-for-cloud-academy"></a>Configurar um único sign-on para cloud academy

1. Numa janela de navegador diferente, inscreva-se no site da cloud Academy - SSO como administrador.

1. Selecione o nome da sua empresa e, em seguida, selecione **Definições & Integrações** no menu que aparece:

    ![Screenshot que mostra a opção Definições & Integrações.](./media/cloud-academy-sso-tutorial/config-1.PNG)

1. Na página **Definições & Integrações,** no separador **Integrações,** selecione o cartão **SSO:**

    ![Screenshot que mostra o cartão SSO no separador Integrações.](./media/cloud-academy-sso-tutorial/config-2.PNG)

1. Complete os seguintes passos nesta página:

    ![Screenshot que mostra as Inegrations > página SSO.](./media/cloud-academy-sso-tutorial/config-3.PNG)

    a. Na caixa **URL do ID da Entidade,** insira o valor de ID da entidade que copiou a partir do portal Azure.

    b. Na caixa **DE URL SSO,** cole o valor URL de login que copiou do portal Azure.

    c. Abra o certificado Base64 descarregado a partir do portal Azure no Bloco de Notas. Cole o seu conteúdo na caixa **de certificados.**

    d. Na caixa **de formato de identificação do nome,** mantenha o valor predefinido: `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` .

1. Selecione **Guardar**.

    > [!NOTE]
    > Para obter mais informações sobre como configurar a Cloud Academy - SSO, consulte [configurar o sign-on único](https://support.cloudacademy.com/hc/articles/360043908452-Setting-Up-Single-Sign-On).

### <a name="create-a-cloud-academy-test-user"></a>Criar um utilizador de teste da Cloud Academy

Nesta secção, um utilizador chamado Britta Simon é criado na Cloud Academy - SSO. Cloud Academy - SSO suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na Cloud Academy - SSO, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Cloud Academy - URL de SSO, onde pode iniciar o fluxo de login. 

* Vá para cloud Academy - SSO Sign-on URL diretamente e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar na Cloud Academy - Azulejo SSO nas Minhas Apps, isto irá redirecionar para Cloud Academy - SSO Sign-on URL. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Assim que configurar a Cloud Academy - SSO, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).