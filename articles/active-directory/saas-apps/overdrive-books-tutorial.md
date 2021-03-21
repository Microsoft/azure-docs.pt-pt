---
title: 'Tutorial: Integração do Diretório Ativo Azure com overdrive | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Overdrive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2021
ms.author: jeedes
ms.openlocfilehash: 390ccfa828ebaa0d3dec15990ffeb60cbb24360e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648599"
---
# <a name="tutorial-azure-active-directory-integration-with-overdrive"></a>Tutorial: Integração do Diretório Ativo Azure com Overdrive

Neste tutorial, você vai aprender a integrar Overdrive com Azure Ative Directory (Azure AD). Quando integrar overdrive com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Overdrive.
* Ativar os seus utilizadores a serem automaticamente inscritos no Overdrive com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:
 
* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura ativada por Overdrive (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Overdrive suporta SSO iniciado **SP.**

* Overdrive suporta o fornecimento do utilizador **Just In Time.**

## <a name="add-overdrive-from-the-gallery"></a>Adicione Overdrive da galeria

Para configurar a integração de Overdrive no Azure AD, adicione Overdrive da galeria à sua lista de aplicações geridas saaS fazendo o seguinte:
 
1. Inscreva-se no portal Azure com uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione o serviço **Azure Ative Directory.**
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Overdrive** na caixa de pesquisa.
1. No painel de resultados, selecione **Overdrive** e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-overdrive"></a>Configure e teste Azure AD SSO para overdrive

Configure e teste Azure AD SSO com Overdrive usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Overdrive.

Para configurar e testar a Azure AD SSO com Overdrive, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure overdrive SSO](#configure-overdrive-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Overdrive test user](#create-overdrive-test-user)** - para ter uma contraparte de B.Simon em Overdrive que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Overdrive,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `http://<subdomain>.libraryreserve.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte [a equipa de suporte do Cliente Overdrive](https://help.overdrive.com/) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configuração Overdrive,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permite que Britta Simon utilize a Azure single sign-on, concedendo acesso a Overdrive.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **Overdrive**.

2. Na lista de aplicações, selecione **Overdrive**.

3. No menu à esquerda, selecione **Utilizadores e grupos**.

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

## <a name="configure-overdrive-sso"></a>Configurar overdrive SSO

Para configurar um único sinal no lado **overdrive,** é necessário enviar os **Metadados XML da Federação** descarregados e URLs copiados apropriados do portal Azure para a equipa de [suporte overdrive](https://help.overdrive.com/). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-overdrive-test-user"></a>Criar utilizador de teste Overdrive

Nesta secção, um utilizador chamado Britta Simon é criado em Overdrive. Overdrive suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Overdrive, um novo é criado após a autenticação.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador OverDrive ou APIs fornecidas pelo OverDrive para fornecer contas de utilizador Azure AD.
>

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para URL de entrada de entrada de overdrive, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição de overdrive e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Overdrive nas Minhas Apps, este irá redirecionar para URL de sinalização de overdrive. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Overdrive, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).