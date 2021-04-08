---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Coupa Risk Assess | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Coupa Risk Assess.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: jeedes
ms.openlocfilehash: bad1ccc99575b788e1bb5ea541c6b6690ead6eca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180987"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coupa-risk-assess"></a>Tutorial: Azure Ative Directory integração única (SSO) com Avaliação de Risco coupa

Neste tutorial, você vai aprender como integrar Coupa Risk Assess com Azure Ative Directory (Azure AD). Quando integrar coupa Risk Assess com Azure AD, você pode:

* Controlo em Azure AD que tem acesso a Coupa Risk Assess.
* Permitir que os seus utilizadores sejam automaticamente inscritos na Coupa Risk Assess com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Coupa Risk Avaliar uma única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Coupa Risk Assess apoia **SP e IDP** iniciado SSO

## <a name="adding-coupa-risk-assess-from-the-gallery"></a>Adicionar Coupa Risk Assess da galeria

Para configurar a integração da Coupa Risk Assess em AD Azure, é necessário adicionar coupa Risk Assess da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Coupa Risk Assess** na caixa de pesquisa.
1. Selecione **Coupa Risk Assess** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-coupa-risk-assess"></a>Configure e teste Azure AD SSO para avaliação de risco coupa

Configure e teste Azure AD SSO com Coupa Risk Assess usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Coupa Risk Assess.

Para configurar e testar a Azure AD SSO com coupa Risk Assess, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure coupa Risk Assess SSO](#configure-coupa-risk-assess-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Coupa Risk Assess utilizador](#create-coupa-risk-assess-test-user)** - para ter uma contraparte de B.Simon em Coupa Risk Assess que está ligada à representação Azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página **coupa Risk Assess** integração da aplicação, encontre a secção **Gerir** e selecione um único sinal de **saúde**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:
 
    * Para **encenação:**`https://<STAGING ENTITY>.hiperos.com/sp/<CUSTOMERIDENTIFIER>.login`

    * Para **PROD:**`https://<PROD ENTITY>.hiperos.com/sp/<CUSTOMERIDENTIFIER>.login`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:
 
    * Para **encenação:**`https://<STAGING ENTITY>.hiperos.com/sp/<CUSTOMERIDENTIFIER>.login`

    * Para **PROD:**`https://<PROD ENTITY>.hiperos.com/sp/<CUSTOMERIDENTIFIER>.login`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:

    * Para **encenação:**`https://<STAGING ENTITY>.hiperos.com/sp/<CUSTOMERIDENTIFIER>.login`

    * Para **PROD:**`https://<PROD ENTITY>.hiperos.com/sp/<CUSTOMERIDENTIFIER>.login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a coupa Risk Assess Client support team](mailto:rasupport@coupa.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar coupa Risk Assess,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Coupa Risk Assess.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, **selecione Coupa Risk Assess**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-coupa-risk-assess-sso"></a>Configure Coupa Risk Assess SSO

Para configurar um único sign-on no lado **coupa Risk Assess,** você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipa de [suporte coupa Risk Assess](mailto:rasupport@coupa.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-coupa-risk-assess-test-user"></a>Criar coupa risco avaliar utilizador de teste

Nesta secção, cria-se um utilizador chamado Britta Simon em Coupa Risk Assess. Trabalhe com [a equipa de apoio coupa Risk Assess](mailto:rasupport@coupa.com) para adicionar os utilizadores na plataforma Coupa Risk Assess. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

1. Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para coupa Risk Assess Sign no URL onde pode iniciar o fluxo de login.  

1. Vá diretamente ao URL de avaliação de risco coupa e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na Avaliação de Risco coupa para a qual configura o SSO 

Também pode utilizar o Microsoft Access Panel para testar a aplicação em qualquer modo. Quando clicar no azulejo coupa Risk Assess no Painel de Acesso, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na Avaliação de Risco coupa para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Coupa Risk Assess pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).