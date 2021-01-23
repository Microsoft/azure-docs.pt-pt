---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com a Yello Enterprise | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Yello Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: dc9c8e622840d30f1d18ab783cc20e2659a1b5e3
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735565"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-yello-enterprise"></a>Tutorial: Azure Ative Directory integração única (SSO) com a Yello Enterprise

Neste tutorial, você vai aprender a integrar a Yello Enterprise com o Azure Ative Directory (Azure AD). Quando integrar a Yello Enterprise com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à Yello Enterprise.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Yello Enterprise com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada pela Yello Enterprise (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Yello Enterprise apoia **IDP** iniciado SSO

## <a name="adding-yello-enterprise-from-the-gallery"></a>Adicionar Yello Enterprise da galeria

Para configurar a integração da Yello Enterprise no Azure AD, é necessário adicionar a Yello Enterprise da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **escreva Yello Enterprise** na caixa de pesquisa.
1. **Selecione Yello Enterprise** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-yello-enterprise"></a>Configure e teste Azure AD SSO para a Yello Enterprise

Configure e teste Azure AD SSO com a Yello Enterprise usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Yello Enterprise.

Para configurar e testar a Azure AD SSO com a Yello Enterprise, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Yello Enterprise SSO](#configure-yello-enterprise-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Yello Enterprise test user](#create-yello-enterprise-test-user)** - para ter uma contraparte de B.Simon na Yello Enterprise que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Yello Enterprise,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:  `https://www.yello.co/<IDP_NAME>`

    > [!NOTE]
    > Este valor não é real. Atualize o valor com o identificador real. Contacte [a equipa de apoio ao cliente da Yello Enterprise](mailto:support@yello.co) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação Yello Enterprise espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Yello Enterprise espera que alguns mais atributos sejam repercutidos na resposta SAML, que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name | Atributo de origem|
    | ------------ | --------- |
    | ID de colaborador | utilizador.employeeid |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar a Yello Enterprise, copie** os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Yello Enterprise.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, **selecione Yello Enterprise**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-yello-enterprise-sso"></a>Configure Yello Enterprise SSO

Para configurar um único sign-on no lado **da Yello Enterprise,** você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipa de [suporte yello Enterprise](mailto:support@yello.co). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-yello-enterprise-test-user"></a>Criar utilizador de teste Yello Enterprise

Nesta secção, cria-se um utilizador chamado Britta Simon na Yello Enterprise. Trabalhe com a [equipa de apoio da Yello Enterprise](mailto:support@yello.co) para adicionar os utilizadores na plataforma Yello Enterprise. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito na Yello Enterprise para a qual configura o SSO

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo yello Enterprise nas Minhas Apps, deverá ser automaticamente inscrito na Yello Enterprise para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Próximos passos

Assim que configurar a Yello Enterprise, pode impor o controlo da sessão, o que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).