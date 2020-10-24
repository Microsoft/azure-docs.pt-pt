---
title: 'Tutorial: Azure Ative Directy integração única de sign-on (SSO) com NegometrixPortal Single Sign On (SSO) [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o NegometrixPortal Single Sign On (SSO).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/06/2019
ms.author: jeedes
ms.openlocfilehash: d972868cf9c5d67824eab781bc99a7cac5f7b313
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92507145"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-negometrixportal-single-sign-on-sso"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com NegometrixPortal Single Sign On (SSO)

Neste tutorial, você aprenderá a integrar o NegometrixPortal Single Sign On (SSO) com o Azure Ative Directory (Azure AD). Quando integrar o NegometrixPortal Single Sign On (SSO) com Azure AD, pode:

* Control em Azure AD que tem acesso a NegometrixPortal Single Sign On (SSO).
* Ative os seus utilizadores a serem automaticamente inscritos no NegometrixPortal Single Sign On (SSO) com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* NegometrixPortal Single Sign On (SSO) única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* NegometrixPortal Single Sign On (SSO) suporta **SSO** iniciado SP

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-negometrixportal-single-sign-on-sso-from-the-gallery"></a>Adicionar NegometrixPortal Single Sign On (SSO) da galeria

Para configurar a integração do NegometrixPortal Single Sign On (SSO) em Azure AD, é necessário adicionar o NegometrixPortal Single Sign On (SSO) da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite NegometrixPortal Single Sign On (SSO)** na caixa de pesquisa.
1. Selecione **NegometrixPortal Single Sign On (SSO)** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-negometrixportal-single-sign-on-sso"></a>Configurar e testar a Azure AD um único sinal para NegometrixPortal Single Sign On (SSO)

Configure e teste Azure AD SSO com NegometrixPortal Single Sign On (SSO) usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no NegometrixPortal Single Sign On (SSO).

Para configurar e testar o Azure AD SSO com NegometrixPortal Single Sign On (SSO), complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure NegometrixPortal Single Sign On (SSO) SSO](#configure-negometrixportal-single-sign-on-sso-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create NegometrixPortal Single Sign On (SSO) test user](#create-negometrixportal-single-sign-on-sso-test-user)** - para ter uma contraparte de B.Simon in NegometrixPortal Single Sign On (SSO) que está ligada à representação Azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **NegometrixPortal (SSO),** encontre a secção **Gestão** e selecione **um único sinal.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://portal.negometrix.com/sso/<CUSTOMURL>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte [a equipa de suporte ao cliente NegometrixPortal Para](mailto:sander.hoek@negometrix.com) obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação NegometrixPortal Single Sign On (SSO) espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação NegometrixPortal Single Sign On (SSO) espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome | Atributo de origem|
    | ---------------|  --------- |
    | upn | user.userprincipalname |

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a NegometrixPortal Single Sign On (SSO).

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **NegometrixPortal Single Sign On (SSO)**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-negometrixportal-single-sign-on-sso-sso"></a>Configure NegometrixPortal Single Sign On (SSO) SSO

Para configurar um único sign-on no lado **do Sinal Único Do NegometrixPortal On (SSO),** é necessário enviar o Url de **Metadados da Federação de Aplicações** para a [equipa de suporte Do Signo Único Do Banco (SSO) do NegometrixPortal.](mailto:sander.hoek@negometrix.com) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-negometrixportal-single-sign-on-sso-test-user"></a>Criar negometrixPortal Single Sign On (SSO) utilizador de teste

Nesta secção, cria-se um utilizador chamado B.Simon in NegometrixPortal Single Sign On (SSO). Trabalhe com [a equipa de suporte NegometrixPortal Single Sign On (SSO)](mailto:sander.hoek@negometrix.com) para adicionar os utilizadores na plataforma NegometrixPortal Single Sign On (SSO). Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo De sinal Único Do NegometrixPortal (SSO) no Painel de Acesso, deverá ser automaticamente inscrito no Sinal Único Do NegometrixPortal (SSO) para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente NegometrixPortal Single Sign On (SSO) com Azure AD](https://aad.portal.azure.com/)