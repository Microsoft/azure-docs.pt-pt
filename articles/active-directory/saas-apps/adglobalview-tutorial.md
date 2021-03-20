---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a ADP Globalview (Deprecada) | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o ADP Globalview (Deprecado).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 08b1294436ead372234104008a48ca23e56de389
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589315"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp-globalview-deprecated"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a ADP Globalview (Depretada)

Neste tutorial, você vai aprender a integrar a ADP Globalview (Deprecada) com Azure Ative Directory (Azure AD). Quando integrar a ADP Globalview (Deprecada) com a Azure AD, pode:

* Control em Azure AD que tem acesso à ADP Globalview (Deprecada).
* Capacitar os seus utilizadores a serem automaticamente inscritos na ADP Globalview (Deprecada) com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* ADP Globalview (Deprecada) assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* A ADP Globalview (Deprecated) apoia **o IDP** iniciado SSO.

## <a name="adding-adp-globalview-deprecated-from-the-gallery"></a>Adicionar ADP Globalview (Deprecado) da galeria

Para configurar a integração da ADP Globalview (Depreifed) no Azure AD, é necessário adicionar a ADP Globalview (Depreifed) da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite ADP Globalview (Depregatada)** na caixa de pesquisa.
1. Selecione **ADP Globalview (Deprecada)** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-adp-globalview-deprecated"></a>Configure e teste Azure AD SSO para a ADP Globalview (Preprecada)

Configure e teste Azure AD SSO com ADP Globalview (Deprecado) usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na ADP Globalview (Depreciada).

Para configurar e testar o Azure AD SSO com a ADP Globalview (Deprecada), execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o SSO da ADP Globalview (Deprefiado)](#configure-adp-globalview-deprecated-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create ADP Globalview (Depreifed) test user](#create-adp-globalview-deprecated-test-user)** - para ter uma contraparte de B.Simon in ADP Globalview (Depreifed) que está ligada à representação AD Ad do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **ADP Globalview (Deprecated),** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    
    Na caixa de texto **do identificador,** digite um URL utilizando um dos seguintes padrões:

    | Identificador |
    | ----------- |
    | `https://<subdomain>.globalview.adp.com/federate` |
    | `https://<subdomain>.globalview.adp.com/federate2` |
    |

    > [!NOTE]
    > Este valor não é real. Atualize o valor com o identificador real. Contacte [a equipa de apoio ao cliente da ADP Globalview (Deprecada)](https://www.adp.com/contact-us/overview.aspx) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configuração ADP Globalview (Deprecada), copie** os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à ADP Globalview (Deprecated).

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **ADP Globalview (Deprecada)**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-adp-globalview-deprecated-sso"></a>Configurar a ADP Globalview (precotada) SSO

Para configurar um único sign-on no lado **ADP Globalview (Deprecated),** você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a [equipa de apoio ADP Globalview (Deprecated).](https://www.adp.com/contact-us/overview.aspx) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-adp-globalview-deprecated-test-user"></a>Criar utilizador de teste ADP Globalview (Deprecado)

Nesta secção, cria-se um utilizador chamado B.Simon in ADP Globalview (Deprecated). Trabalhe com [a equipa de suporte da ADP Globalview (Deprecated)](https://www.adp.com/contact-us/overview.aspx) para adicionar os utilizadores na plataforma ADP Globalview (Deprecated). Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no ADP Globalview (Deprecado) para o qual configura o SSO

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo ADP Globalview (Deprecated) nas Minhas Apps, deverá ser automaticamente inscrito no ADP Globalview (Deprecated) para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado a ADP Globalview (Depreifed) pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).