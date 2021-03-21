---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com ContractSafe Saml2 SSO | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o ContractSafe Saml2 SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.openlocfilehash: 2d236b9910e2eda8e574d020544d625a68aefb81
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92455322"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-contractsafe-saml2-sso"></a>Tutorial: Integrar o Azure Ative Directory single sign-on (SSO) com ContractSafe Saml2 SSO

Neste tutorial, você vai aprender a integrar ContractSafe Saml2 SSO com Azure Ative Directory (Azure AD). Quando integrar o ContractSafe Saml2 SSO com a AD Azure, pode:

* Controle quem tem acesso a ContractSafe Saml2 SSO em Azure AD.
* Capacitar os seus utilizadores a iniciarem automaticamente o sSO ContractSafe Saml2 com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre software como integração de aplicações de serviço (SaaS) com Azure AD, veja o que é o acesso à [aplicação e um único acesso com o Azure Ative Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa do seguinte:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição SSO ContractSafe Saml2 com SSO ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. ContractSafe Saml2 SSO suporta SSO iniciado pelo **IDP.**

## <a name="add-contractsafe-saml2-sso-from-the-gallery"></a>Adicione ContractSafe Saml2 SSO da galeria

Para configurar a integração do ContractSafe Saml2 SSO em AD Azure, é necessário adicionar ContractSafe Saml2 SSO da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite ContractSafe Saml2 SSO** na caixa de pesquisa.
1. Selecione **ContractSafe Saml2 SSO** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-contractsafe-saml2-sso"></a>Configure e teste Azure AD SSO para ContractSafe Saml2 SSO

Configure e teste Azure AD SSO com ContractSafe Saml2 SSO utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no ContractSafe Saml2 SSO.

Para configurar e testar o Azure AD SSO com o ContractSafe Saml2 SSO, complete os seguintes blocos de construção:

1. [Configure Azure AD SSO](#configure-azure-ad-sso) para permitir que os seus utilizadores utilizem esta funcionalidade.
   * Crie um utilizador de [teste AD Azure](#create-an-azure-ad-test-user) para testar O Azure AD SSO utilizando a conta **B.Simon.**
   * [Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que **a B.Simon** utilize o Azure AD SSO.

1. [Configure o ContractSafe Saml2 SSO](#configure-contractsafe-saml2-sso) para configurar as definições SSO no lado da aplicação.
   * [Crie um utilizador de teste ContractSafe Saml2 SSO](#create-a-contractsafe-saml2-sso-test-user) para ter uma contraparte de **B.Simon** no ContractSafe Saml2 SSO que está ligada à representação AD Azure do utilizador.
2. [Teste SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir que o Azure AD SSO no portal Azure:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **ContractSafe Saml2 SSO,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, selecione o ícone de edição (caneta) para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. No **set-on único com** a página SAML, insira os seguintes valores nos campos correspondentes:

    a. Na caixa de texto **identifier,** introduza um URL utilizando o seguinte formato: `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    b. Na caixa de texto **URL de resposta,** introduza um URL utilizando o seguinte formato: `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real e responda URL. Contacte a [equipa de suporte ao cliente ContractSafe Saml2 SSO](mailto:support@contractsafe.com) para obter estes valores. Também pode consultar os formatos indicados na secção **Configuração Básica SAML** no portal Azure.

1. ContractSafe Saml2 SSO espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![Atributos padrão comuns](common/default-attributes.png)

1. Além dos atributos predefinidos, a aplicação ContractSafe Saml2 SSO espera que mais alguns atributos sejam repercutidos na resposta SAML. Estes atributos são pré-povoados, mas pode revê-los de acordo com os seus requisitos. A lista a seguir mostra os atributos adicionais.

    | Name | Atributo de origem|
    | ---------------| --------------- |
    | nome de e-mail | user.userprincipalname |
    | e-mail | user.onpremisesuserprincipalname |

1. Na **configuração de um único sinal com** a página SAML, na secção certificado de assinatura **SAML,** encontre **o Metadados da Federação XML**. Selecione **Baixar** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configuração ContractSafe Saml2 SSO,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

## <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado **B.Simon**.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**. Selecione **Utilizadores** e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do Utilizador,** insira um endereço de e-mail no `username@companydomain.extension` formato. Um exemplo é `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Selecione **Criar**.

## <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que **B.Simon** utilize o Azure SSO, concedendo acesso ao ContractSafe Saml2 SSO.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **ContractSafe Saml2 SSO**.
1. Na página geral da aplicação, encontre a secção **Gerir** e, em seguida, selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Adicionar Atribuição'.**

   ![O link do utilizador adicionar](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista **de Utilizadores.** Em seguida, selecione o botão **Selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, escolha o botão **Selecione** na parte inferior do ecrã.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione o botão **'Atribuir'.**

## <a name="configure-contractsafe-saml2-sso"></a>Configurar ContractSafe Saml2 SSO

Para configurar o SSO no lado **SSO do ContractSafe Saml2,** é necessário enviar os **metadados XML da Federação** descarregados e URLs copiados apropriados do portal Azure para a equipa de [suporte ContractSafe Saml2 SSO](mailto:support@contractsafe.com). A equipa é responsável por definir corretamente a ligação SSO SAML de ambos os lados.

## <a name="create-a-contractsafe-saml2-sso-test-user"></a>Criar um utilizador de teste SSO ContractSafe Saml2

Crie um utilizador chamado B.Simon in ContractSafe Saml2 SSO. Trabalhe com a [equipa de suporte ContractSafe Saml2 SSO](mailto:support@contractsafe.com) para adicionar os utilizadores na plataforma ContractSafe Saml2 SSO. Os utilizadores devem ser criados e ativados antes de utilizar o SSO.

## <a name="test-sso"></a>Teste SSO

Teste a sua configuração AZure AD SSO utilizando o Painel de Acesso. Quando selecionar o azulejo ContractSafe Saml2 SSO no Painel de Acesso, deverá ser automaticamente inscrito no ContractSafe Saml2 SSO para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com Diretório Ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente ContractSafe Saml2 SSO com Azure AD](https://aad.portal.azure.com/)