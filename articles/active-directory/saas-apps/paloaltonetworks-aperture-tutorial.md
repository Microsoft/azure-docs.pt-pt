---
title: 'Tutorial: Integração do Diretório Ativo Azure com redes Palo Alto - Abertura | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Palo Alto Networks - Abertura.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 42a6bc9bfb06f1c80b719bdda686ae111a8884ab
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222017"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Tutorial: Integração do Diretório Ativo Azure com redes Palo Alto - Abertura

Neste tutorial, você vai aprender a integrar Palo Alto Networks - Abertura com Azure Ative Directory (Azure AD). Quando integrar as Redes Palo Alto - Abertura com Azure AD, pode:

* Controlo em Azure AD que tem acesso à Palo Alto Networks - Abertura.
* Ative os seus utilizadores a serem automaticamente inscritos nas Redes Palo Alto - Abertura com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Palo Alto Networks - Assinatura ativada por abertura única (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Palo Alto Networks - A abertura suporta **SP** e **IDP** iniciado SSO.

## <a name="add-palo-alto-networks---aperture-from-the-gallery"></a>Adicionar Palo Alto Networks - Abertura da galeria

Para configurar a integração das Redes Palo Alto - Abertura em AD Azure, é necessário adicionar a Palo Alto Networks - Abertura da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Palo Alto Networks - Abertura** na caixa de pesquisa.
1. Selecione **Palo Alto Networks - Abertura** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar Azure AD SSO

Nesta secção, configura e testa o Azure AD com a Palo Alto Networks - Abertura com base num utilizador de teste chamado **B.Simon**.
Para um único sinal de saúde a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Palo Alto Networks - A abertura.

Para configurar e testar a Azure AD com a Palo Alto Networks - Abertura, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
2. **[Configure as Redes Palo Alto - Abertura SSO](#configure-palo-alto-networks---aperture-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Criar Redes Palo Alto - Utilizador de teste de abertura](#create-palo-alto-networks---aperture-test-user)** - para ter uma contrapartida de Britta Simon em Palo Alto Networks - Abertura que está ligada à representação AD AZure do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página **palo Alto Networks - Aperture** application integration, encontre a secção **Gerir** e selecione um único sinal **de saúde**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a Palo Alto Networks - Equipa de suporte ao Cliente de Abertura](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

7. Na secção **Configurar redes Palo Alto - Abertura,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, permitindo o acesso à Palo Alto Networks - Abertura.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Palo Alto Networks - Abertura**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-palo-alto-networks---aperture-sso"></a>Configure redes Palo Alto - Abertura SSO

1. Numa janela diferente do navegador web, faça login na Palo Alto Networks - Abertura como Administrador.

2. Na barra de menu superior, clique em **DEFINIÇÕES**.

    ![O separador de definições](./media/paloaltonetworks-aperture-tutorial/settings.png)

3. Navegar para a secção **APLICAÇÃO** clique **em Autenticação** do lado esquerdo do menu.

    ![O separador Auth](./media/paloaltonetworks-aperture-tutorial/authentication.png)
    
4. Na página de **autenticação** execute os seguintes passos:
    
    ![O separador de autenticação](./media/paloaltonetworks-aperture-tutorial/tab.png)

    a. Verifique o **Sign-On único ativado (Os fornecedores SSP suportados são Okta, um login)** do campo **Sign-On único.**

    b. Na caixa de texto **ID do Fornecedor de Identidade,** cole o valor do **Identificador AD Azure,** que copiou do portal Azure.

    c. Clique **em Escolher Ficheiro** para fazer o upload do Certificado Azure AD descarregado no campo de Certificado de Fornecedor de **Identidade.**

    d. Na caixa de texto **SSO URL do Fornecedor de Identidade,** cole o valor do URL de **Login,** que copiou do portal Azure.

    e. Reveja as informações do IdP na secção **Aperture Info** e descarregue o certificado do campo **Aperture Key.**

    f. Clique em **Guardar**.


### <a name="create-palo-alto-networks---aperture-test-user"></a>Criar Redes Palo Alto - Utilizador de teste de abertura

Nesta secção, cria-se um utilizador chamado Britta Simon em Palo Alto Networks - Abertura. Trabalhar com [a Palo Alto Networks - Equipa](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) de suporte ao Cliente de Abertura para adicionar os utilizadores na plataforma Palo Alto Networks - Aperture. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Palo Alto Networks - Aperture Sign on URL onde pode iniciar o fluxo de login.  

* Vá diretamente à Palo Alto Networks - URL de inscrição de abertura e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito nas Redes Palo Alto - Abertura para a qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar nas Redes Palo Alto - Azulejo de abertura nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito nas Redes Palo Alto - Abertura para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurar a Palo Alto Networks - Abertura, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).