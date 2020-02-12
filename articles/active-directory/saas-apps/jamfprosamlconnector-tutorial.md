---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com jamf Pro  Diretório Ativo) integração com jamf Pro  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc99e23e1b885de25bd2159d7916790cad851108
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77150319"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-jamf-pro"></a>Tutorial: Integração azure Ative Directory SSO com jamf Pro

Neste tutorial, você vai aprender a integrar o Jamf Pro com o Azure Ative Directory (Azure AD). Quando integrar o Jamf Pro com o Azure AD, pode:

* Utilize a AD Azure para controlar quem tem acesso ao Jamf Pro.
* Inscreva-se automaticamente nos seus utilizadores para o Jamf Pro com as suas contas Azure AD.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o single sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição Jamf Pro que está ativada por um único sinal (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. O Jamf Pro suporta o **SSO iniciado por SP** e **o SSO iniciado pelo IDP.**

## <a name="add-jamf-pro-from-the-gallery"></a>Adicione jamf Pro da galeria

Para configurar a integração do Jamf Pro em Azure AD, precisa adicionar o Jamf Pro da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou de escola ou a sua conta pessoal da Microsoft.
1. No painel esquerdo, selecione o serviço **de Diretório Ativo Azure.**
1. Vá a **Aplicações Empresariais**e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** introduza o *Jamf Pro* na caixa de pesquisa.
1. Selecione **o Jamf Pro** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-sso-in-azure-ad-for-jamf-pro"></a>Configure e teste SSO em Azure AD para Jamf Pro

Configure e teste Azure AD SSO com Jamf Pro utilizando um utilizador de teste chamado B.Simon. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Jamf Pro.

Nesta secção, configura e testa o Azure AD SSO com o Jamf Pro.

1. [Configure o SSO em AD Azure](#configure-sso-in-azure-ad) para que os seus utilizadores possam utilizar esta funcionalidade.
    1. [Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user) para testar o Azure AD SSO com a conta B.Simon.
    1. [Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para que a B.Simon possa utilizar o SSO em Azure AD.
1. [Configure o SSO no Jamf Pro](#configure-sso-in-jamf-pro) para configurar as definições sso no lado da aplicação.
    1. [Crie um utilizador](#create-a-jamf-pro-test-user) de teste Jamf Pro para ter uma contrapartida de B.Simon no Jamf Pro que esteja ligada à representação da AD Azure do utilizador.
1. [Teste a configuração SSO](#test-the-sso-configuration) para verificar se a configuração funciona.

## <a name="configure-sso-in-azure-ad"></a>Configure SSO em Azure AD

Nesta secção, ativa o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Do Jamf Pro,** encontre a secção **Gerir** e selecione **Single Sign-On**.
1. Na página **Select a Single Sign-On Method,** selecione **SAML**.
1. Na configuração do single sign-on com a página **SAML,** selecione o ícone da caneta para **configuração SAML básica** para editar as definições.

   ![Editar a página de configuração Básica do SAML.](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo **iniciado pelo IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **Identificador,** introduza um URL que utilize a seguinte fórmula: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. Na caixa de texto **URL resposta,** introduza um URL que utilize a seguinte fórmula: `https://<subdomain>.jamfcloud.com/saml/SSO`

1. Selecione **Definir URLs adicionais**. Se pretender configurar a aplicação no modo **iniciado por SP,** na caixa de texto **url sign-on,** introduza um URL que utilize a seguinte fórmula: `https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Você terá o valor do identificador real da secção **de entrada única** no portal Jamf Pro, o que é explicado mais tarde no tutorial. Pode extrair o valor real do subdomínio do valor do identificador e utilizar essa informação de subdomínio como URL de inscrição e url de resposta. Pode também consultar as fórmulas mostradas na secção **basic SAML Configuração** no portal Azure.

1. Na configuração do Single Sign-On com a página **SAML,** vá à secção de Certificado de **Assinatura SAML,** selecione o botão **de cópia** para copiar o URL de Metadados da Federação de **Aplicações**e, em seguida, guarde-o para o seu computador.

    ![O link de descarregamento do Certificado de Assinatura SAML](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, cria-se um utilizador de teste no portal Azure chamado B.Simon.

1. No painel esquerdo do portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.
   1. No campo de **nome do Utilizador,** insira [nome]@[domínio da empresa]. [extensão]. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, concedes ao B.Simon acesso ao Jamf Pro.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Jamf Pro**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![Selecione Utilizadores e grupos](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador**e, em seguida, selecione Utilizadores e **grupos** na caixa de diálogo **'Atribuição de Adicionar'.**

    ![Selecione o botão Adicionar Utilizador](common/add-assign-user.png)

1. Na caixa de diálogo **Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, selecione o botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de papel na afirmação do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador. Em seguida, selecione o botão **Selecionar** na parte inferior do ecrã.
1. Na caixa de diálogo **Adicionar Atribuição,** selecione o botão **Atribuir.**

## <a name="configure-sso-in-jamf-pro"></a>Configure SSO no Jamf Pro

1. Para automatizar a configuração dentro do Jamf Pro, instale a extensão de **navegador 'Sessão' de 'Sessão' de 'Seleção'** das Minhas Aplicações selecionando a **extensão**.

    ![Página de extensão de navegador de navegador de aplicativos de aplicativo sintetária de aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, **selecione Configurar o Jamf Pro**. Quando a aplicação Jamf Pro abrir, forneça as credenciais do administrador para iniciar sessão. A extensão do navegador configurará automaticamente a aplicação e automatizará os passos 3 a 7.

    ![Configuração da página de configuração no Jamf Pro](common/setup-sso.png)

3. Para configurar o Jamf Pro manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Jamf Pro como administrador. Em seguida, dê os seguintes passos.

4. Selecione o **ícone Definições** a partir do canto superior direito da página.

    ![Selecione o ícone de definições no Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Selecione **um único sinal .**

    ![Selecione single sign-on no Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Na página **De Sessão Individual,** tome os seguintes passos.

    ![A página de inscrição única no Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

  a. Selecione **Editar**.

  b. Selecione a caixa de verificação de **autenticação de sinal único ativada.**

    c. Selecione **Azure** como opção do menu de entrega do Fornecedor de **Identidade.**

    d. Copie o valor de **ID da ENTIDADE** e cole-o no campo **Identificador (ID** da Entidade) na secção **de Configuração SAML Básica** no portal Azure.

    > [!NOTE]
    > Utilize o valor no campo `<SUBDOMAIN>` para completar o URL de início de sessão e responder URL na secção **de Configuração SAML Básica** no portal Azure.

    e. Selecione URL de **Metadados** do menu de entrega de dados do fornecedor de **identidade.** No campo que aparece, colhe o valor de url de **metadados da Federação** de Aplicações que copiou do portal Azure.

    f. (Opcional) Editar o valor de validade do símbolo ou selecionar "Desativar a expiração do token SAML".

7. Na mesma página, desloque-se até à secção **de Mapeamento** do Utilizador. Em seguida, dê os seguintes passos.

    ![A secção de mapeamento do utilizador da página De Sessão Única no Jamf Pro.](./media/jamfprosamlconnector-tutorial/tutorial-jamfprosamlconnector-single.png)

    a. Selecione a opção **NameID** para **o mapeamento**do utilizador do fornecedor de identidade . Por padrão, esta opção está definida para **NameID,** mas pode definir um atributo personalizado.

    b. Selecione **e-mail** para **Jamf Pro User Mapping**. O Jamf Pro mapeia os atributos SAML enviados pelo IdP primeiro pelos utilizadores e depois por grupos. Quando um utilizador tenta aceder ao Jamf Pro, o Jamf Pro obtém informações sobre o utilizador do Fornecedor de Identidade e compara-os com todas as contas de utilizador do Jamf Pro. Se a conta de utilizador que está a chegar não for encontrada, então o Jamf Pro tenta compará-la pelo nome de grupo.

    c. Colar o valor `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` no campo NOME DO **GRUPO DE IDENTIDADE.**

    d. Na mesma página, desloque-se até à secção **de Segurança** e selecione **Permitir que os utilizadores contornem a autenticação de Sinal Único**. Como resultado, os utilizadores não serão redirecionados para a página de inscrição do Fornecedor de Identidade para autenticação e podem iniciar sessão diretamente no Jamf Pro. Quando um utilizador tenta aceder ao Jamf Pro através do Fornecedor de Identidade, ocorre a autenticação e autorização SSO iniciadas pelo IDP.

    e. Selecione **Guardar**.

### <a name="create-a-jamf-pro-test-user"></a>Criar um utilizador de teste Jamf Pro

Para que os utilizadores da AD Azure assinem o Jamf Pro, devem ser aprovisionados no Jamf Pro. O provisionamento no Jamf Pro é uma tarefa manual.

Para fornecer uma conta de utilizador, tome as seguintes medidas:

1. Inscreva-se no site da empresa Jamf Pro como administrador.

2. Selecione o ícone **Definições** no canto superior direito da página.

    ![O ícone de definições no Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Selecione **Contas e Grupos de Utilizadores do Jamf Pro**.

    ![O ícone de Contas e Grupos Jamf Pro nas definições do Jamf Pro](./media/jamfprosamlconnector-tutorial/user1.png)

4. Selecione **Novo**.

    ![Página de definições do sistema de contas e grupos jamf Pro](./media/jamfprosamlconnector-tutorial/user2.png)

5. Selecione **Criar A Conta Padrão**.

    ![A opção Criar a Conta Padrão na página Jamf Pro User Accounts & Groups](./media/jamfprosamlconnector-tutorial/user3.png)

6. Na caixa de diálogo **New Account,** tome os seguintes passos.

    ![Novas opções de configuração de conta nas definições do sistema Jamf Pro](./media/jamfprosamlconnector-tutorial/user4.png)

    a. No campo **USERNAME,** insira `Britta Simon`, o nome completo do utilizador do teste.

    b. Selecione as opções para **O NÍVEL DE ACESSO,** **DEFINIÇÃO DE PRIVILÉGIOs**e ESTADO DE **ACESSO** que estejam de acordo com a sua organização.

    c. No campo **NOME COMPLETO,** introduza `Britta Simon`.

    d. No campo DE ENDEREÇO DE **E-MAIL,** insira o endereço de e-mail da conta de Britta Simon.

    e. No campo **PASSWORD,** introduza a palavra-passe do utilizador.

    f. No campo **CHECK PASSWORD,** introduza novamente a palavra-passe do utilizador.

    g. Selecione **Guardar**.

## <a name="test-the-sso-configuration"></a>Teste a configuração SSO

Nesta secção, testa a configuração de um único sinal de acesso do Azure AD utilizando o Painel de Acesso.

Quando selecionar o azulejo Jamf Pro no Painel de Acesso, deve ser automaticamente inscrito na conta Jamf Pro para a qual configurao O SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para integração de aplicações SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Inscrição única para candidaturas no Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Jamf Pro com a Azure AD](https://aad.portal.azure.com/)
