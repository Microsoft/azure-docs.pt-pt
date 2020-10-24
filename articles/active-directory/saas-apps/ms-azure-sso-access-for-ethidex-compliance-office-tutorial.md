---
title: 'Tutorial: Azure Ative Directy integração única de sign-on (SSO) com MS Azure SSO Access for Ethidex Compliance Office™ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o MS Azure SSO Access for Ethidex Compliance Office™.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: jeedes
ms.openlocfilehash: 7ef219ca147fe96fc65f14bbf3ba6a565adc95ec
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520919"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ms-azure-sso-access-for-ethidex-compliance-office"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com MS Azure SSO Access for Ethidex Compliance Office™

Neste tutorial, você vai aprender a integrar o MS Azure SSO Access for Ethidex Compliance Office™ com o Azure Ative Directory (Azure AD). Quando integrar o MS Azure SSO Access for Ethidex Compliance Office™ com a Azure AD, pode:

* Control em Azure AD que tem acesso ao MS Azure SSO Access for Ethidex Compliance Office™.
* Permitir que os seus utilizadores sejam automaticamente inscritos no MS Azure SSO Access for Ethidex Compliance Office™ com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* MS Azure SSO Access for Ethidex Compliance Office™ assinatura ativada por SSO (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* MS Azure SSO Access for Ethidex Compliance Office™ suporta **IDP** iniciado SSO

## <a name="adding-ms-azure-sso-access-for-ethidex-compliance-office-from-the-gallery"></a>Adicionar MS Azure SSO Acesso para Ethidex Compliance Office™ da galeria

Para configurar a integração do MS Azure SSO Access for Ethidex Compliance Office™ em Azure AD, precisa de adicionar MS Azure SSO Access for Ethidex Compliance Office™ da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **de galeria,** **digite MS Azure SSO Access for Ethidex Compliance Office™** na caixa de pesquisa.
1. Selecione **MS Azure SSO Access for Ethidex Compliance Office™** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ms-azure-sso-access-for-ethidex-compliance-office"></a>Configure e teste Azure AD único sinal de acesso para MS Azure SSO Access for Ethidex Compliance Office™

Configure e teste Azure AD SSO com MS Azure SSO Access for Ethidex Compliance Office™ utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no MS Azure SSO Access for Ethidex Compliance Office™.

Para configurar e testar a Azure AD SSO com a MS Azure SSO Access for Ethidex Compliance Office™, completar os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure MS Azure SSO Access for Ethidex Compliance Office SSO](#configure-ms-azure-sso-access-for-ethidex-compliance-office-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Crie o MS Azure SSO Access for Ethidex Compliance Office test user](#create-ms-azure-sso-access-for-ethidex-compliance-office-test-user)** - para ter uma contraparte de B.Simon em MS Azure SSO Access for Ethidex Compliance Office™ que está ligada à representação Azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), no **MS Azure SSO Access for Ethidex Compliance Office™** página de integração de aplicações, encontre a secção **Gerir** e selecione um único sinal de **sing.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `com.ethidex.prod.<CLIENTID>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://www.ethidex.com/saml2/sp/acs/<CLIENTID>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte [a MS Azure SSO Access for Ethidex Compliance Office™ equipa de suporte](mailto:support@ethidex.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. MS Azure SSO Access for Ethidex Compliance Office™ aplicação espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos SAML. A imagem seguinte mostra a lista de atributos predefinidos, onde como **identificador** de nome é mapeado com **user.userprincipalname**. MS Azure SSO Acesso para Ethidex Compliance Office™ aplicação espera que **o identificador** de nome seja mapeado com **o user.mail**, pelo que é necessário editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o Certificado **(Raw)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificateraw.png)

1. Na **secção Configuração MS Azure SSO Access for Ethidex Compliance Office™** secção, copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao MS Azure SSO Access for Ethidex Compliance Office™.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **MS Azure SSO Access for Ethidex Compliance Office™**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-ms-azure-sso-access-for-ethidex-compliance-office-sso"></a>Configure MS Azure SSO Acesso para Ethidex Compliance Office SSO

Para configurar um único sinal de acesso ao **MS Azure SSO Access for Ethidex Compliance Office™** lado, é necessário enviar o Certificado descarregado **(Raw)** e URLs copiados apropriados do portal Azure para [o MS Azure SSO Access for Ethidex Compliance Office™ equipa de suporte](mailto:support@ethidex.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-ms-azure-sso-access-for-ethidex-compliance-office-test-user"></a>Criar acesso MS Azure SSO para utilizador de teste do Ethidex Compliance Office

Nesta secção, cria um utilizador chamado B.Simon in MS Azure SSO Access for Ethidex Compliance Office™. Trabalhe com [a MS Azure SSO Access for Ethidex Compliance Office™ equipa de suporte](mailto:support@ethidex.com) para adicionar os utilizadores na plataforma MS Azure SSO Access for Ethidex Compliance Office™. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no MS Azure SSO Access for Ethidex Compliance Office™ azulejo no Painel de Acesso, deverá ser automaticamente inscrito no MS Azure SSO Access for Ethidex Compliance Office™ para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o MS Azure SSO Access for Ethidex Compliance Office™ com a Azure AD](https://aad.portal.azure.com/)