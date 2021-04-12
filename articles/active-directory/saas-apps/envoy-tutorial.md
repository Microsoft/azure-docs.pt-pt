---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com o enviado | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Envoy.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/01/2021
ms.author: jeedes
ms.openlocfilehash: bbf961e7b99efe29fd8b13c2104c33e42ae7d4be
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286540"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-envoy"></a>Tutorial: Azure Ative Directory integração única (SSO) com o Enviado

Neste tutorial, você vai aprender a integrar o Enviado com o Azure Ative Directory (Azure AD). Quando integrar o Enviado com a Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Enviado.
* Permita que os seus utilizadores sejam automaticamente inscritos no Enviado com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Enviado assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Enviado apoia **SP** iniciou SSO.

* O enviado suporta o fornecimento do utilizador **Just In Time.**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="add-envoy-from-the-gallery"></a>Adicione enviado da galeria

Para configurar a integração do Enviado no AZure AD, precisa adicionar o Enviado da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **escreva** enviado na caixa de pesquisa.
1. Selecione **o enviado** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-envoy"></a>Configurar e testar Azure AD SSO para enviado

Configure e teste Azure AD SSO com o Enviado utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Enviado.

Para configurar e testar a Azure AD SSO com o Enviado, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Enviado SSO](#configure-envoy-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Envoy test user](#create-envoy-test-user)** - para ter uma contraparte de B.Simon em enviado que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações do **Enviado,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://app.envoy.com/a/saml/auth/<company-ID-from-Envoy>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte [a equipa de apoio ao Cliente do Enviado](https://envoy.com/contact/) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na secção **Certificado de Assinatura SAML,** clique em Editar o botão **Editar** para abrir o diálogo **do Certificado de Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

1. Na secção **Certificado de Assinatura SAML,** copie o **Valor de Impressão Digital** e guarde-o no seu computador.

    ![Valor da impressão digital do polegar da cópia](common/copy-thumbprint.png)

1. Na secção **'Enviar por palavras',** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure um único sinal, concedendo acesso ao Enviado.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Envoy.**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-envoy-sso"></a>Configure enviado SSO

1. Para automatizar a configuração dentro do Enviado, é necessário instalar a extensão do **navegador 'As aplicações' Secure's,** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Setup Envoy** irá direcioná-lo para a aplicação envoy. A partir daí, forneça as credenciais de administrador para assinar em Enviado. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o Enviado manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa do Enviado como administrador e execute os seguintes passos:

4. Na barra de ferramentas na parte superior, clique em **Definições**.

    ![Envoy](./media/envoy-tutorial/envoy-1.png "Envoy")

5. Clique **na Empresa**.

    ![Empresa](./media/envoy-tutorial/envoy-2.png "Empresa")

6. Clique **em SAML**.

    ![SAML](./media/envoy-tutorial/envoy-3.png "SAML")

7. Na secção de configuração **de autenticação SAML,** execute os seguintes passos:

    ![Autenticação SAML](./media/envoy-tutorial/envoy-4.png "Autenticação SAML")
    
    >[!NOTE]
    >O valor para o ID de localização HQ é gerado automaticamente pela aplicação.
    
    a. Na caixa de texto **fingerprint,** cole o valor de **impressão digital** do certificado, que copiou do portal Azure.
    
    b. Cole o valor **URL do login,** que copiou para formar o portal Azure na caixa de texto **URL DO FORNECEDOR DE IDENTIDADE HTTP SAML.**
    
    c. Clique **em Guardar as alterações**.

### <a name="create-envoy-test-user"></a>Criar utilizador de teste de enviado

Nesta secção, um utilizador chamado Britta Simon é criado em Enviado. O enviado suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Enviado, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de inscrição do Enviado, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição do Enviado e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo do Enviado nas Minhas Apps, este será redirecionado para o URL de assinatura do Enviado. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado enviado pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
