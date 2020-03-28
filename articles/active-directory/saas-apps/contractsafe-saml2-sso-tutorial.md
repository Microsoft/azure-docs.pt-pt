---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com ContractSafe Saml2 SSO [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o ContractSafe Saml2 SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9d8c9eba-6a90-4c8f-b387-a6ead4af00af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ab2dc71f39164988e0d229fc994548a00447986
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77185624"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-contractsafe-saml2-sso"></a>Tutorial: Integrar o diretório ativo Azure (SSO) com contractsafe Saml2 SSO

Neste tutorial, você aprenderá a integrar contractSafe Saml2 SSO com o Azure Ative Directory (Azure AD). Quando integrar o ContractSafe Saml2 SSO com a Azure AD, pode:

* Controlo quem tem acesso ao ContractSafe Saml2 SSO em Azure AD.
* Ative que os seus utilizadores instem automaticamente o ContractSafe SAmL2 SSO com as suas contas Azure AD.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre software como uma integração de aplicações de serviço (SaaS) com a Azure AD, veja o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa do seguinte:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura ContractSafe SAmL2 SSO com SSO ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. ContractSafe Saml2 SSO suporta SSO iniciado com **IDP.**

## <a name="add-contractsafe-saml2-sso-from-the-gallery"></a>Adicione ContractSafe Saml2 SSO da galeria

Para configurar a integração do ContractSafe Saml2 SSO no Azure AD, é necessário adicionar o ContractSafe Saml2 SSO da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Vá a **Aplicações Empresariais**e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **de galeria,** digite **ContractSafe Saml2 SSO** na caixa de pesquisa.
1. Selecione **ContractSafe Saml2 SSO** no painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-contractsafe-saml2-sso"></a>Configure e teste Azure AD SSO para ContractSafe Saml2 SSO

Configure e teste Azure AD SSO com ContractSafe Saml2 SSO utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no ContractSafe Saml2 SSO.

Para configurar e testar o Azure AD SSO com contractSafe Saml2 SSO, complete os seguintes blocos de construção:

1. [Configure O SSO AD Azure](#configure-azure-ad-sso) para permitir que os seus utilizadores utilizem esta funcionalidade.
   * [Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user) para testar o Azure AD SSO utilizando a conta **B.Simon.**
   * [Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que **b.Simon** utilize O SSO Azure AD.

1. [Configure ContractSafe Saml2 SSO](#configure-contractsafe-saml2-sso) para configurar as definições sso no lado da aplicação.
   * [Crie um utilizador de teste ContractSafe SAmL2 SSO](#create-a-contractsafe-saml2-sso-test-user) para ter uma contrapartida de **B.Simon** no ContractSafe Saml2 SSO que esteja ligado à representação azure AD do utilizador.
2. [Teste sSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **ContractSafe Saml2 SSO,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, selecione o ícone de edição (caneta) para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na configuração de um único sessão com a página **SAML,** introduza os seguintes valores nos campos correspondentes:

    a. Na caixa de texto **do Identificador,** introduza um URL utilizando o seguinte formato:`https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    b. Na caixa de texto **URL resposta,** introduza um URL utilizando o seguinte formato:`https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real e responda URL. Contacte a equipa de suporte ao [cliente ContractSafe Saml2 SSO](mailto:support@contractsafe.com) para obter estes valores. Também pode consultar os formatos mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. ContractSafe Saml2 SSO espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![Atributos padrão comuns](common/default-attributes.png)

1. Além dos atributos predefinidos, a aplicação ContractSafe SAmL2 SSO espera que mais alguns atributos sejam retransmitidos na resposta SAML. Estes atributos são pré-povoados, mas pode revê-los de acordo com os seus requisitos. A lista seguinte mostra os atributos adicionais.

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | nome de e-mail | user.userprincipalname |
    | e-mail | user.onpremiseuserprincipalname |

1. Na configuração de um único sessão com a página **SAML,** na secção certificado de **assinatura SAML,** encontre **metadados da Federação XML**. Selecione **Baixar** para descarregar o certificado e, em seguida, guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na **secção ContractSafe SSO,** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

## <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado **B.Simon**.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**. Selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **de nome do Utilizador,** introduza um endereço de e-mail no `username@companydomain.extension` formato. Um exemplo é `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Selecione **Criar**.

## <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que **a B.Simon** utilize o Azure SSO, concedendo acesso ao ContractSafe Saml2 SSO.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **ContractSafe Saml2 SSO**.
1. Na página geral da aplicação, encontre a secção **Gerir** e, em seguida, selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Atribuição adicionar'.**

   ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. Na caixa de diálogo **De Utilizadores e grupos,** selecione **B.Simon** da lista **de Utilizadores.** Em seguida, selecione o botão **Selecionar** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de papel na afirmação do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, escolha o botão **Select** na parte inferior do ecrã.
1. Na caixa de diálogo **Adicionar Atribuição,** selecione o botão **Atribuir.**

## <a name="configure-contractsafe-saml2-sso"></a>Configure ContractSafe Saml2 SSO

Para configurar o SSO no lado **ContractSafe SAmL2 SSO,** é necessário enviar os **Metadados da Federação XML** descarregados e URLs copiados apropriados do portal Azure para a equipa de [suporte ContractSafe Saml2 SSO](mailto:support@contractsafe.com). A equipa é responsável por definir corretamente a ligação SAML SSO em ambos os lados.

## <a name="create-a-contractsafe-saml2-sso-test-user"></a>Criar um utilizador de teste ContractSafe SSO SSO

Crie um utilizador chamado B.Simon no ContractSafe SAmL2 SSO. Trabalhe com a equipa de [suporte ContractSafe Saml2 SSO](mailto:support@contractsafe.com) para adicionar os utilizadores na plataforma ContractSafe Saml2 SSO. Os utilizadores devem ser criados e ativados antes de utilizar o SSO.

## <a name="test-sso"></a>Teste SSO

Teste a configuração Azure AD SSO utilizando o Painel de Acesso. Ao selecionar o azulejo ContractSafe SSO SSO no Painel de Acesso, deverá ser automaticamente inscrito no ContractSafe Saml2 SSO para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações do SaaS com diretório ativo azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente ContractSafe Saml2 SSO com Azure AD](https://aad.portal.azure.com/)
