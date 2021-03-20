---
title: 'Tutorial: Azure Ative Directy integração única de sign-on (SSO) com RetrieverMediaDatabase | Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o RetrieverMediaDatabase.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/25/2020
ms.author: jeedes
ms.openlocfilehash: 786925799d19bf01e8edebbf85de04d92213298b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98732849"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-retrievermediadatabase"></a>Tutorial: Azure Ative Directy integração única (SSO) com RetrieverMediaDatabase

Neste tutorial, você vai aprender a integrar o RetrieverMediaDatabase com o Azure Ative Directory (Azure AD). Quando integrar o RetrieverMediaDatabase com Azure AD, pode:

* Controlo em Azure AD que tem acesso a RetrieverMediaDatabase.
* Ativar os seus utilizadores a serem automaticamente inscritos no RetrieverMediaDatabase com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada pelo RetrieverMediaDatabase (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* RetrieverMediaDatabase suporta **IDP** iniciado SSO

## <a name="adding-retrievermediadatabase-from-the-gallery"></a>Adicionar RetrieverMediaDatabase da galeria

Para configurar a integração do RetrieverMediaDatabase no AD Azure, é necessário adicionar o RetrieverMediaDatabase da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite RetrieverMediaDatabase** na caixa de pesquisa.
1. Selecione **RetrieverMediaDatabase** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-retrievermediadatabase"></a>Configure e teste Azure AD SSO para RetrieverMediaDatabase

Configure e teste Azure AD SSO com RetrieverMediaDatabase usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no RetrieverMediaDatabase.

Para configurar e testar o Azure AD SSO com o RetrieverMediaDatabase, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o RetrieverMediaDatabase SSO](#configure-retrievermediadatabase-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create RetrieverMediaDatabase utilizador de teste](#create-retrievermediadatabase-test-user)** - para ter uma contraparte de B.Simon in RetrieverMediaDatabase que está ligada à representação AD AZure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **RetrieverMediaDatabase,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** a aplicação está pré-configurada e os URLs necessários já estão pré-povoados com Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**


1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** clique no botão de cópia para copiar o Url de **metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)
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

Nesta secção, você permitirá que B.Simon utilize o Azure single sign-on, concedendo acesso ao RetrieverMediaDatabase.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **RetrieverMediaDatabase**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-retrievermediadatabase-sso"></a>Configure RetrieverMediaDatabase SSO

Para configurar um único sinal no lado **do RetrieverMediaDatabase,** é necessário enviar o **url de metadados da Federação de Aplicações** para a [equipa de suporte RetrieverMediaDatabase](mailto:support@retriever.nl). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-retrievermediadatabase-test-user"></a>Criar utilizador de teste RetrieverMediaDatabase

Nesta secção, cria-se um utilizador chamado Britta Simon in RetrieverMediaDatabase. Trabalhe com [a equipa de suporte RetrieverMediaDatabase](mailto:support@retriever.nl) para adicionar os utilizadores na plataforma RetrieverMediaDatabase. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

1. Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito na RetrieverMediaDatabase para a qual configura o SSO

1. Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo RetrieverMediaDatabase nas Minhas Apps, deverá ser automaticamente inscrito na Base de Dados RetrieverMedia para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o RetrieverMediaDatabase, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).