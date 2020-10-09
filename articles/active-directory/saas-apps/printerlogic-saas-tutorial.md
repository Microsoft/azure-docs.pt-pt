---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com PrinterLogic SaaS / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o PrinterLogic SaaS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: jeedes
ms.openlocfilehash: 8cf73e7eae333bcf2a5cb639f1c9e3d9e70140e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88553585"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-printerlogic-saas"></a>Tutorial: Azure Ative Directory integração única (SSO) com PrinterLogic SaaS

Neste tutorial, você vai aprender a integrar PrinterLogic SaaS com Azure Ative Directory (Azure AD). Quando integra o PrinterLogic SaaS com Azure AD, pode:

* Controle em Azure AD que tem acesso a PrinterLogic SaaS.
* Ative os seus utilizadores a serem automaticamente inscritos no PrinterLogic SaaS com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada por PrinterLogic SaaS (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.



* PrinterLogic SaaS suporta SSO iniciado **SP e IDP**
* PrinterLogic SaaS suporta **provisão de** utilizadores just in time


## <a name="adding-printerlogic-saas-from-the-gallery"></a>Adicionar PrinterLogic SaaS da galeria

Para configurar a integração do PrinterLogic SaaS no AD Azure, é necessário adicionar o PrinterLogic SaaS da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite PrinterLogic SaaS** na caixa de pesquisa.
1. Selecione **PrinterLogic SaaS** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-printerlogic-saas"></a>Configurar e testar a Azure AD um único sinal de inscrição para o PrinterLogic SaaS

Configure e teste Azure AD SSO com PrinterLogic SaaS usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no PrinterLogic SaaS.

Para configurar e testar o Azure AD SSO com o PrinterLogic SaaS, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a ImpressoraLogic SaaS SSO](#configure-printerlogic-saas-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create PrinterLogic SaaS test user](#create-printerlogic-saas-test-user)** - para ter uma contraparte de B.Simon em PrinterLogic SaaS que está ligada à representação AD AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **PrinterLogic SaaS,** encontre a secção **Gerir** e selecione **um único sinal de solução**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://gw.app.printercloud.com/<my_instance>/authn/idp/azuread/saml2/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://gw.app.printercloud.com/<my_instance>/authn/idp/azuread/saml2/acs`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://www.<my_instance>printercloud.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de suporte do Cliente PrinterLogic SaaS](mailto:support@printerlogic.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação PrinterLogic SaaS espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação PrinterLogic SaaS espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com o seu requisito.

    | Nome | Atributo de origem |
    | ---------| ------------ |
    | Função     | user.assignedroles |

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) para saber como configurar o papel em Azure AD

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar PrinterLogic SaaS,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a PrinterLogic SaaS.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **PrinterLogic SaaS**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-printerlogic-saas-sso"></a>Configure impressoraLogic SaaS SSO

Para configurar um único sign-on no lado **da PrinterLogic SaaS,** é necessário enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de [suporte da PrinterLogic SaaS.](mailto:support@printerlogic.com) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-printerlogic-saas-test-user"></a>Criar utilizador de teste PrinterLogic SaaS

Nesta secção, um utilizador chamado Britta Simon é criado na PrinterLogic SaaS. O PrinterLogic SaaS suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no PrinterLogic SaaS, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Da ImpressoraLogic SaaS no Painel de Acesso, deverá ser automaticamente inscrito no SaaS PrinterLogic para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente PrinterLogic SaaS com Azure AD](https://aad.portal.azure.com/)

