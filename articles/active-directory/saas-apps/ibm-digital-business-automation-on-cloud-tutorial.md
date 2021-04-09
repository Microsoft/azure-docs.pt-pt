---
title: 'Tutorial: Azure Ative Directy integração única de sign-on (SSO) com a IBM Digital Business Automation na Cloud | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a IBM Digital Business Automation na Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/08/2020
ms.author: jeedes
ms.openlocfilehash: 91a50ed85b5802657a27c5a545de0013937e9da0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460568"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ibm-digital-business-automation-on-cloud"></a>Tutorial: Azure Ative Directy integração única (SSO) com IBM Digital Business Automation on Cloud

Neste tutorial, você vai aprender a integrar IBM Digital Business Automation na Cloud com Azure Ative Directory (Azure AD). Quando integra a IBM Digital Business Automation na Cloud com a Azure AD, pode:

* Control em Azure AD que tem acesso à IBM Digital Business Automation na Cloud.
* Capacitar os seus utilizadores a serem automaticamente inscritos na IBM Digital Business Automation na Cloud com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* IBM Digital Business Automation on Cloud single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* IBM Digital Business Automation on Cloud suporta **SP e IDP** iniciado SSO
* Uma vez configurada a IBM Digital Business Automation na Cloud, pode impor o controlo de sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-ibm-digital-business-automation-on-cloud-from-the-gallery"></a>Adicionar IBM Digital Business Automation na Cloud a partir da galeria

Para configurar a integração da IBM Digital Business Automation na Cloud em AD Azure, é necessário adicionar a IBM Digital Business Automation na Cloud desde a galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **de galeria,** **digite IBM Digital Business Automation na Cloud** na caixa de pesquisa.
1. Selecione **IBM Digital Business Automation na Cloud** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ibm-digital-business-automation-on-cloud"></a>Configure e teste Azure AD único sign-on para IBM Digital Business Automation na Cloud

Configure e teste Azure AD SSO com IBM Digital Business Automation on Cloud usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na IBM Digital Business Automation na Cloud.

Para configurar e testar o Azure AD SSO com a IBM Digital Business Automation na Cloud, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a IBM Digital Business Automation na Cloud SSO](#configure-ibm-digital-business-automation-on-cloud-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Crie a IBM Digital Business Automation no cloud test user](#create-ibm-digital-business-automation-on-cloud-test-user)** - para ter uma contrapartida de B.Simon na IBM Digital Business Automation na Cloud que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página ibm Digital Business Automation na página de integração de aplicações **Cloud,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** se tiver **um ficheiro de metadados do Fornecedor de Serviços,** execute os seguintes passos:
    
    a. Clique **em Carregar o ficheiro de metadados**.

    b. Clique no **logotipo da pasta** para selecionar o ficheiro de metadados e clique em **Upload**.

    c. Uma vez que o ficheiro de metadados é carregado com sucesso, os valores de URL **de identificação** e **resposta** são auto-povoados na IBM Digital Business Automation na caixa de texto da secção Cloud:

    > [!Note]
    > Se os valores de URL **de identificação** e **resposta** não forem preenchidos automaticamente, preencha os valores manualmente de acordo com o seu requisito.

    > [!Note]
    > Os clientes podem obter o ficheiro de metadados para a sua subscrição cloud da equipa de suporte do [IBM Digital Business Automation na Cloud Client](mailto:supportbpmoncloud@us.ibm.com).

1. Se não tiver **um ficheiro de metadados do Fornecedor de Serviços,** na secção **de Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://www.automationcloud.ibm.com/isam/sps/<TENANT>/saml20`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://www.automationcloud.ibm.com/isam/sps/<TENANT>/saml20/login`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://www.automationcloud.ibm.com/isam/sps/<TENANT>/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a IBM Digital Business Automation na equipa de suporte do Cliente Cloud](mailto:supportbpmoncloud@us.ibm.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configuração IBM Digital Business Automation na** secção Cloud, copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à IBM Digital Business Automation na Cloud.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **IBM Digital Business Automation na Cloud**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-ibm-digital-business-automation-on-cloud-sso"></a>Configurar a IBM Digital Business Automation na Cloud SSO

Para configurar um único sign-on na IBM Digital Business Automation no lado **cloud,** você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para [IBM Digital Business Automation on Cloud support team](mailto:supportbpmoncloud@us.ibm.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-ibm-digital-business-automation-on-cloud-test-user"></a>Criar automatização de negócios digital IBM no utilizador de teste cloud

Nesta secção, cria-se um utilizador chamado Britta Simon na IBM Digital Business Automation na Cloud. Trabalhe com [a IBM Digital Business Automation na equipa de suporte](mailto:supportbpmoncloud@us.ibm.com) cloud para adicionar os utilizadores na IBM Digital Business Automation na plataforma Cloud. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar na IBM Digital Business Automation on Cloud no Painel de Acesso, deverá ser automaticamente inscrito na IBM Digital Business Automation on Cloud para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente a IBM Digital Business Automation na Cloud com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger a IBM Digital Business Automation na Cloud com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)