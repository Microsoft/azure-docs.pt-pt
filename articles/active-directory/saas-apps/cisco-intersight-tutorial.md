---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a Cisco Intersight | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Cisco Intersight.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 6efec61ec5866547ab6b432cb9c566642f3191ff
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107580790"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-intersight"></a>Tutorial: Azure Ative Directory integração única (SSO) com a Cisco Intersight

Neste tutorial, você vai aprender a integrar a Cisco Intersight com o Azure Ative Directory (Azure AD). Quando integrar a Cisco Intersight com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à Cisco Intersight.
* Permita que os seus utilizadores sejam automaticamente inscritos na Cisco Intersight com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Cisco Intersight assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Cisco Intersight suporta **SP** iniciado SSO.

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-cisco-intersight-from-the-gallery"></a>Adicionar Cisco Intersight da galeria

Para configurar a integração da Cisco Intersight no Azure AD, é necessário adicionar a Cisco Intersight da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Cisco Intersight** na caixa de pesquisa.
1. Selecione **Cisco Intersight** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-cisco-intersight"></a>Configure e teste Azure AD SSO para Cisco Intersight

Configure e teste Azure AD SSO com Cisco Intersight usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Cisco Intersight.

Para configurar e testar o Azure AD SSO com a Cisco Intersight, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure cisco Intersight SSO](#configure-cisco-intersight-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Cisco Intersight test user](#create-cisco-intersight-test-user)** - para ter uma contraparte de B.Simon na Cisco Intersight que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Cisco Intersight,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** o URL: `https://intersight.com`

    b. Na caixa de texto **identifier (Entity ID),** digite o URL: `www.intersight.com`

1. A sua aplicação Cisco Intersight espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra um exemplo para isto. O valor predefinido do **Identificador de Utilizador Exclusivo** é **user.userprincipalname,** mas a Cisco Intersight espera que este seja mapeado com o endereço de e-mail do utilizador. Para isso pode utilizar o atributo **user.mail** da lista ou utilizar o valor de atributo apropriado com base na configuração da sua organização..

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Cisco Intersight espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Name |  Atributo de origem|
    | -------------- | --------- |
    | First_Name | user.givenname |
    | Last_Name | utilizador.sobrenome |
    | membroOf | utilizador.grupos |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configuração Cisco Intersight,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Cisco Intersight.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Cisco Intersight**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-cisco-intersight-sso"></a>Configurar Cisco Intersight SSO

Para configurar um único sign-on no lado **da Cisco Intersight,** você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipa de [suporte da Cisco Intersight](mailto:intersight-feedback@cisco.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-cisco-intersight-test-user"></a>Criar utilizador de teste Cisco Intersight

Nesta secção, cria-se um utilizador chamado Britta Simon na Cisco Intersight. Trabalhe com a [equipa de suporte da Cisco Intersight](mailto:intersight-feedback@cisco.com) para adicionar os utilizadores na plataforma Cisco Intersight. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de sinal de referência da Cisco Intersight, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de assinatura intersight cisco e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Cisco Intersight nas Minhas Apps, este será redirecionado para o URL de sinal de referência intersight cisco. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado a Cisco Intersight, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


