---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com a Nitro Productivity Suite [ Azure Ative Diretório) integração com a Nitro Productivity Suite [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Nitro Productivity Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 98c96c9f-18a6-4a20-919b-74fb113ee465
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/02/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ca675e43f4d20898d9f97c175da71f8d5c59e4f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676911"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nitro-productivity-suite"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com a Nitro Productivity Suite

Neste tutorial, você vai aprender a integrar a Suite de Produtividade Nitro com o Azure Ative Directory (Azure AD). Quando integrar a Suite de Produtividade nitrosa com o Azure AD, pode:

* Controlo em Azure AD que tem acesso à Suite de Produtividade Nitro.
* Permita que os seus utilizadores sejam automaticamente inscritos na Nitro Productivity Suite com as suas contas Azure AD.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre software como uma integração de aplicações de serviço (SaaS) com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa do seguinte:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura da Nitro Productivity Suite [Enterprise.](https://www.gonitro.com/pricing)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Nitro Productivity Suite suporta **SP** e **IDP** iniciado SSO.
* Nitro Productivity Suite suporta o fornecimento de utilizadores **justo no tempo.**
* Depois de configurar a Suite de Produtividade nitro, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o acesso condicional. Para mais informações, consulte Saiba como impor o controlo da [sessão com](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)o Microsoft Cloud App Security .

## <a name="add-nitro-productivity-suite-from-the-gallery"></a>Adicione nitro productivity suite da galeria

Para configurar a integração da Nitro Productivity Suite em Azure AD, você precisa adicionar Nitro Productivity Suite da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou de escola, ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione **Azure Ative Directory**.
1. Vá a **Aplicações Empresariais**e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **de galeria,** digite A Suite de **Produtividade nitrona** caixa de pesquisa.
1. Selecione **Nitro Productivity Suite** a partir dos resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-nitro-productivity-suite"></a>Configure e teste Azure AD single sign-on para Nitro Productivity Suite

Configure e teste Azure AD SSO com Nitro Productivity Suite, utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação ligada entre um utilizador da AD Azure e o utilizador relacionado na Nitro Productivity Suite.

Para configurar e testar o Azure AD SSO com a Nitro Productivity Suite, complete os seguintes blocos de construção:

1. [Configure O SSO AD Azure](#configure-azure-ad-sso) para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. [Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user) para testar o único sinal de Azure AD com B.Simon.
    1. [Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que b.Simon utilize um único sinal de AD Azure.
1. Configure o Suite de Produtividade nitroso [SSO](#configure-nitro-productivity-suite-sso) para configurar as definições de inscrição únicas no lado da aplicação.
    1. [Crie um utilizador](#create-a-nitro-productivity-suite-test-user) de teste nitro productivity suite para ter uma contrapartida de B.Simon na Nitro Productivity Suite, ligada à representação da AD Azure do utilizador.
1. [Teste sSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **da Nitro Productivity Suite,** encontre a secção **Gerir.** Selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. Na secção certificado de **assinatura SAML,** encontre **certificado (Base64)**. Selecione **Baixar** para descarregar o certificado e guardá-lo no seu computador.

    ![Screenshot da secção de certificado de assinatura SAML, com link de descarregamento em destaque](common/certificatebase64.png)
    
1. Na secção Configurar a **Suite de Produtividade** nitrosa, selecione o ícone da cópia ao lado do **URL de Login**.
    
    ![Screenshot da secção Suite de Produtividade Nitro, com URLs e ícones de cópia em destaque](common/copy-configuration-urls.png)
    
1. No [portal Nitro Admin,](https://admin.gonitro.com/)na página **Definições da Empresa,** encontre a secção **De Acesso Único.** **Selecione Configurar SAML SSO**.

    a. Colá-lo do url de **login** do passo anterior para o campo **URL sign in.**
    
    b. Faça upload do **Certificado (Base64)** a partir do passo anterior no campo de certificado de **assinatura X509.**
    
    c. Selecione **Submeter**.
    
    d. **Selecione ativar um único sinal .**


1. Volte ao [portal Azure.](https://portal.azure.com/) Na configuração do single sign-on com a página **SAML,** selecione o ícone do lápis para **configuração Básica sAML** para editar as definições.

   ![Screenshot de configurar um único sign-on com página SAML, com ícone de lápis realçado](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado **idp,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **identificador,** copie e cole o campo de ID da **entidade SAML** do [portal Nitro Admin](https://admin.gonitro.com/). Deve ter o seguinte padrão:`urn:auth0:gonitro-prod:<ENVIRONMENT>`

    b. Na caixa de texto **URL de resposta,** copie e cole o campo **URL ACS** do [portal Nitro Admin](https://admin.gonitro.com/). Deve ter o seguinte padrão:`https://gonitro-prod.eu.auth0.com/login/callback?connection=<ENVIRONMENT>`

1. Selecione **Definir URLs adicionais**e executar o seguinte passo se pretender configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva o URL:`https://sso.gonitro.com/login`

1. Selecione **Guardar**.

1. A aplicação Nitro Productivity Suite espera que as afirmações do SAML estejam num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos saml. A imagem que se segue mostra a lista de atributos predefinidos.

    ![Screenshot de atributos padrão](common/default-attributes.png)

1. Além dos atributos anteriores, a aplicação Nitro Productivity Suite espera que mais alguns atributos sejam retransmitidos na resposta SAML. Estes atributos são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Nome  |  Atributo de origem|
    | ---------------| --------------- |
    | empregadoNúmero |  utilizador.objectid |


### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, cria-se um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure,**Users** > selecione **Utilizadores de Diretório** > Ativo Azure**Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva a palavra-passe para baixo.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que a B.Simon utilize um único sign-on Azure, concedendo acesso à Nitro Productivity Suite.

1. No portal Azure, selecione **Aplicações Empresariais** > **Todas as aplicações**.
1. Na lista de aplicações, selecione **Nitro Productivity Suite**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![Screenshot da secção Gerir, com Utilizadores e grupos em destaque](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Em seguida, na caixa de diálogo **adicionar atribuição,** selecione **Utilizadores e grupos**.

    ![Screenshot de Utilizadores e página de grupos, com adicionar utilizador destacado](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Em seguida, escolha **Selecionar** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de papel na afirmação do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, escolha **Selecionar** na parte inferior do ecrã.
1. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

## <a name="configure-nitro-productivity-suite-sso"></a>Configure Nitro Productivity Suite SSO

Para configurar um único sinal no lado da Nitro Productivity Suite, envie o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de suporte da [Nitro Productivity Suite](https://www.gonitro.com/support). A equipa de apoio garante que a ligação SAML SSO está corretamente definida em ambos os lados.

### <a name="create-a-nitro-productivity-suite-test-user"></a>Criar um utilizador de teste de Nitro Productivity Suite

A Nitro Productivity Suite suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhuma ação adicional a tomar. Se um utilizador já não existir na Nitro Productivity Suite, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de acesso do Azure AD utilizando o Painel de Acesso.

Ao selecionar o azulejo da Suite de Produtividade Nitro no Painel de Acesso, é automaticamente inscrito na Suite de Produtividade nitrosa para a qual configura o SSO. Para mais informações, consulte [o Iniciar Sessão e inicie aplicações a partir do portal My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a Suite de Produtividade nitro com a AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Proteja a Suíte de Produtividade nitrocom visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

