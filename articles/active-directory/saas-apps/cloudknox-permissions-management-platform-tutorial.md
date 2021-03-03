---
title: 'Tutorial: Integração única de sign-on (SSO) do Azure Ative Directory com a Plataforma de Gestão de Permissões CloudKnox | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a CloudKnox Permissions Management Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 8c1b45d78d4fb61d239fef178cbcb9fa6efd9539
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645693"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudknox-permissions-management-platform"></a>Tutorial: Azure Ative Directy integração única de sign-on (SSO) com a Plataforma de Gestão de Permissões CloudKnox

Neste tutorial, você vai aprender como integrar a Plataforma de Gestão de Permissões CloudKnox com O Diretório Ativo Azure (Azure AD). Quando integrar a Plataforma de Gestão de Permissões CloudKnox com AZure AD, pode:

* Control em Azure AD que tem acesso à Plataforma de Gestão de Permissões CloudKnox.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Plataforma de Gestão de Permissões CloudKnox com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* CloudKnox Permisss Management Platform única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* CloudKnox Permisss Management Platform suporta **IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-cloudknox-permissions-management-platform-from-the-gallery"></a>Adicionar plataforma de gestão de permissões CloudKnox da galeria

Para configurar a integração da Plataforma de Gestão de Permissões cloudKnox em AD Azure, precisa de adicionar a Plataforma de Gestão de Permissões CloudKnox da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** digite a Plataforma de **Gestão de Permissões CloudKnox** na caixa de pesquisa.
1. Selecione a Plataforma de **Gestão de Permissões CloudKnox** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-cloudknox-permissions-management-platform"></a>Configure e teste Azure AD SSO para a Plataforma de Gestão de Permissões CloudKnox

Configure e teste Azure AD SSO com CloudKnox Permissions Management Platform usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Plataforma de Gestão de Permissões cloudKnox.

Para configurar e testar o Azure AD SSO com a Plataforma de Gestão de Permissões CloudKnox, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Plataforma de Gestão de Permissões CloudKnox SSO](#configure-cloudknox-permissions-management-platform-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. Crie o utilizador de **[teste da Plataforma de Gestão de Permissões CloudKnox](#create-cloudknox-permissions-management-platform-test-user)** - para ter uma contrapartida de B.Simon na CloudKnox Permissions Management Platform que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da **plataforma de gestão de permissões CloudKnox,** encontre a secção **Gerir** e selecione um único sinal **de sing.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. No **set-on único com** a página SAML, insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://app.cloudknox.io/saml/<ID>`

    > [!NOTE]
    > O valor URL de resposta não é real. Atualizar o valor com o URL de resposta real. Contacte a [equipa de suporte do cliente da Plataforma de Gestão de Permissões CloudKnox](mailto:support@cloudknox.io) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação CloudKnox Permisss Management Platform espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação cloudKnox Permisss Management Platform espera que alguns mais atributos sejam repercutidos na resposta SAML, que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Name |  Atributo de origem|
    | --------------- | --------- |
    | First_Name | user.givenname |
    | Grupos | utilizador.grupos |
    | Last_Name | utilizador.sobrenome |
    | Email_Address | user.mail |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção Configurar a **Plataforma de Gestão de Permissões CloudKnox,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use o Azure single sign-on, concedendo acesso à Plataforma de Gestão de Permisses cloudKnox.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **CloudKnox Permissions Management Platform**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-cloudknox-permissions-management-platform-sso"></a>Configurar cloudKnox Permisss Management Platform SSO

Para configurar um único sign-on no lado da Plataforma de **Gestão de Permissões cloudKnox,** você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipa de [suporte da Plataforma de Gestão de Permissões CloudKnox](mailto:support@cloudknox.io). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-cloudknox-permissions-management-platform-test-user"></a>Criar cloudKnox permissões gestão de utilizadores de teste

Nesta secção, cria um utilizador chamado Britta Simon na CloudKnox Permissions Management Platform. Trabalhe com a equipa de [suporte da Plataforma de Gestão de Permissões CloudKnox](mailto:support@cloudknox.io) para adicionar os utilizadores na plataforma cloudKnox Permissions Management Platform. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito na Plataforma de Gestão de Permissões CloudKnox para a qual configura o SSO

* Pode utilizar as minhas apps do Microsoft. Quando clicar na Plataforma de Gestão de Permissões CloudKnox nas Minhas Apps, deverá ser automaticamente inscrita na Plataforma de Gestão de Permissões CloudKnox para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Assim que configurar a Plataforma de Gestão de Permissões cloudKnox, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).