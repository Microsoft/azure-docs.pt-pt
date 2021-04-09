---
title: 'Tutorial: Integração do Diretório Ativo Azure com obras de linha | Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e a LINE WORKS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: jeedes
ms.openlocfilehash: a8b344cd9e4428c3508534edaec4fd98fb67d832
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182050"
---
# <a name="tutorial-azure-active-directory-integration-with-line-works"></a>Tutorial: Integração do Diretório Ativo Azure com OBRAS DE LINHA

Neste tutorial, aprende-se a integrar a LINE WORKS com o Azure Ative Directory (Azure AD).
Integrar a LINHA WORKS com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso a LINHA WORKS.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na LINE WORKS (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração Ad da Azure com a LINE WORKS, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* LINE WORKS Assinatura única ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* LINE WORKS suporta **SSO** iniciado SP

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-line-works-from-the-gallery"></a>Adicionar OBRAS DE LINHA da galeria

Para configurar a integração de OBRAS DE LINHA em Azure AD, é necessário adicionar obras de linha da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** escreva **LINHAS** na caixa de pesquisa.
1. Selecione **LINE WORKS** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar Azure AD SSO

Configure e teste Azure AD SSO com LINE WORKS utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em TRABALHOS DE LINHA.

Para configurar e testar a Azure AD SSO com a LINE WORKS, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
2. **[Configure linha WORKS SSO](#configure-line-works-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    * **[Create LINE WORKS test user](#create-line-works-test-user)** - ter uma contraparte de Britta Simon em LINE WORKS que está ligada à representação AD AD do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **LINE WORKS,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://auth.worksmobile.com/d/login/<domain>/`

    b. Na caixa de texto **URL response,** digite um URL utilizando o seguinte padrão: `https://auth.worksmobile.com/acs/ <domain>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com URL e URL de resposta Sign-On reais. Contacte [a equipa de suporte da LINE WORKS](https://line.worksmobile.com/jp/en/contactus/) para obter os valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **'Configurar LINHA WORKS',** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon utilize o Azure single sign-on, concedendo acesso a LINE WORKS.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **LINE WORKS**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-line-works-sso"></a>Configure linha trabalha SSO

Para configurar um único sinal de inscrição no lado **de TRABALHOs** DE LINHA, leia os [documentos SSO da LINHA E](https://developers.worksmobile.com/jp/document/1001080101) configufique uma definição DE TRABALHOS DE LINHA.

> [!NOTE]
> Tem de converter o ficheiro certificado descarregado de .cert para .pem


### <a name="create-line-works-test-user"></a>Criar utilizador de teste DE TRABALHOS DE LINHA

Nesta secção, cria-se um utilizador chamado Britta Simon in LINE WORKS. Linha de acesso [FUNCIONA página de administração](https://admin.worksmobile.com) e adicionar os utilizadores na plataforma LINE WORKS.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

1. Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de início de inscrição de trabalhos de linha, onde pode iniciar o fluxo de login. 

2. Vá diretamente para o URL de inscrição de trabalhos de linha e inicie o fluxo de login a partir daí.

3. Pode utilizar o Microsoft Access Panel. Quando clicar no azulejo DE LINHA WORKS no Painel de Acesso, este irá redirecionar para o URL de início de sção de trabalhos de linha. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado LINE WORKS pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).