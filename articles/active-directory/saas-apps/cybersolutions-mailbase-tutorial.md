---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com CyberSolutions MAILBASEΣ\CMSS Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o CyberSolutions MAILBASEΣ\CMSS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: 9a1bed217f12646687654f37145a4a796d0487a1
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455005"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cybersolutions-mailbasecmss"></a>Tutorial: Azure Ative Directy integração única de sign-on (SSO) com CyberSolutions MAILBASEΣ@CMSS

Neste tutorial, você aprenderá a integrar CyberSolutions MAILBASEΣ\CMSS com Azure Ative Directory (Azure AD). Quando integrar a CyberSolutions MAILBASEΣ\CMSS com Azure AD, pode:

* Controlo em Azure AD que tem acesso a CyberSolutions MAILBASEΣ\CMSS.
* Ative os seus utilizadores a serem automaticamente inscritos na CyberSolutions MAILBASEΣ\CMSS com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* CyberSolutions MAILBASEΣ\CMSS única assinatura ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* CyberSolutions MAILBASEΣ\CMSS suporta **SP e IDP** iniciado SSO

## <a name="adding-cybersolutions-mailbasecmss-from-the-gallery"></a>Adicionar CyberSolutions MAILBASEΣ\CMSS da galeria

Para configurar a integração da CyberSolutions MAILBASEΣ\CMSS em Azure AD, é necessário adicionar CyberSolutions MAILBASEΣ\CMSS da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite CyberSolutions MAILBASEΣ\CMSS** na caixa de pesquisa.
1. Selecione **CyberSolutions MAILBASEΣ\CMSS** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-cybersolutions-mailbasecmss"></a>Configure e teste Azure AD SSO para CyberSolutions MAILBASEΣ\CMSS

Configure e teste Azure AD SSO com CyberSolutions MAILBASEΣ\CMSS usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AD Azure e o utilizador relacionado na CyberSolutions MAILBASEΣ\CMSS.

Para configurar e testar o Azure AD SSO com cyberSolutions MAILBASEΣ\CMSS, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a CyberSolutions MAILBASE SSO](#configure-cybersolutions-mailbase-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. Crie o utilizador de **[teste CyberSolutions MAILBASE](#create-cybersolutions-mailbase-test-user)** - para ter uma contraparte de B.Simon em CyberSolutions MAILBASEΣ\CMSS que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de **aplicações CyberSolutions MAILBASEΣ\CMSS,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.cybercloud.jp/saml/module.php/saml/sp/metadata.php/mb_generic_sp`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.cybercloud.jp/cgi-bin/mbase/mblogin/saml2-acs/mb_generic_sp`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<SUBDOMAIN>.cybercloud.jp/cgi-bin/mbase/mblogin?saml_domain=<domain>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte a Equipa de Suporte ao [Cliente CyberSolutions MAILBASEΣ\CMSS](mailto:tech@cybersolutions.co.jp) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configuração CyberSolutions MAILBASEΣ\CMSS,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon utilize o Azure single sign-on, concedendo acesso a CyberSolutions MAILBASEΣ\CMSS.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **CyberSolutions MAILBASEΣ\CMSS**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-cybersolutions-mailbase-sso"></a>Configure CiberSolutions MAILBASE SSO

Para configurar um único sinal de acesso à equipa de apoio **da CyberSolutions MAILBASEΣ\CMSS,** é necessário enviar a equipa de apoio da **Federação De Metadados XML** e urls copiados apropriados do portal Azure para a equipa de [apoio da CyberSolutions MAILBASEΣ\CMSS](mailto:tech@cybersolutions.co.jp). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-cybersolutions-mailbase-test-user"></a>Criar o utilizador de teste CyberSolutions MAILBASE

Nesta secção, cria-se um utilizador chamado Britta Simon em CyberSolutions MAILBASEΣ\CMSS. Trabalhe com [a equipa de apoio da CyberSolutions MAILBASEΣ\CMSS](mailto:tech@cybersolutions.co.jp) para adicionar os utilizadores na plataforma CyberSolutions MAILBASEΣ\CMSS. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para CyberSolutions MAILBASEΣ\CMSS Assinar no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao CYBERSolutions MAILBASEΣ\CMSS Iniciar o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no CyberSolutions MAILBASEΣ\CMSS para o qual configura o SSO 

Também pode utilizar o Microsoft Access Panel para testar a aplicação em qualquer modo. Quando clicar no azulejo CyberSolutions MAILBASEΣ\CMSS no Painel de Acesso, se configurado no modo SP será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no CyberSolutions MAILBASEΣ\CMSS para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos Seguintes

Assim que configurar a CyberSolutions MAILBASEΣ\CMSS pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).