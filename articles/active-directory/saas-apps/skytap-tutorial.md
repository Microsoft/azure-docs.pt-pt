---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com Single Sign-on para Skytap [ Azure Ative Diretório) integração com single sign-on para Skytap [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Single Sign-on para a Skytap.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d6cb7ab2-da1a-4015-8e6f-c0c47bb6210f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33a8035f16f531dbb17177d1c2f4d5cd344e5a28
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77565796"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-single-sign-on-for-skytap"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com single sign-on para Skytap

Neste tutorial, você vai aprender a integrar single sign-on para Skytap com Azure Ative Directory (Azure AD). Quando integrar o Single Sign-on para skytap com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a Um Único Sign-on para skytap.
* Ative que os seus utilizadores sejam automaticamente inscritos no Single Sign-on para a Skytap com as suas contas Azure AD.
* Gerencie as suas contas num local central, o portal Azure.

Para saber mais sobre software como uma integração de aplicações de serviço (SaaS) com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* O único sinal para a assinatura ativada pela Skytap (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* O único signo para skytap suporta SP e IDP iniciado SSO.
* Depois de configurar o Single Sign-on para a Skytap, pode impor o controlo da sessão. Isto protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o acesso condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="add-single-sign-on-for-skytap-from-the-gallery"></a>Adicione um único sign-on para skytap da galeria

Para configurar a integração do Single Sign-on para a Skytap no Azure AD, precisa de adicionar um único sign-on para a Skytap da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou de escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite **Um Único Sign-on para skytap** na caixa de pesquisa.
1. Selecione **Single Sign-on para Skytap** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-single-sign-on-for-skytap"></a>Configure e teste Azure AD single sign-on para single sign-on para Skytap

Configure e teste Azure AD SSO com um único sign-on para a Skytap utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, estabeleça uma relação ligada entre um utilizador da AD Azure e o utilizador relacionado em Single Sign-on para a Skytap.

Aqui estão os passos gerais para configurar e testar Azure AD SSO com um único signo para skytap:

1. **[Configure O SSO AD Azure](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.

    a. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de Azure AD com B.Simon.

    b. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o sign-on single para Skytap SSO](#configure-single-sign-on-for-skytap-sso)** para configurar as definições de inscrição únicas no lado da aplicação.

    a. **[Crie um único sign-on para](#create-single-sign-on-for-skytap-test-user)** o utilizador do teste Skytap ter uma contraparte de B.Simon em Single Sign-on para skytap. Esta contrapartida está ligada à representação da AD Azure do utilizador.
1. **[Teste sSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **da Skytap,** encontre a secção **Gerir.** Selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set set single sign-on com** a página SAML, selecione o ícone do lápis para **configuração SAML básica** para editar as definições.

   ![Screenshot de Configurar um único sign-on com página SAML, com ícone de lápis realçado](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado **idp,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **identificador,** digite um URL que utilize o seguinte padrão:`http://pingone.com/<custom EntityID>`

    b. Na caixa de texto **URL de resposta,** digite um URL que utilize o seguinte padrão:`https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

1. Selecione **Definir URLs adicionais**e executar os seguintes passos se pretender configurar a aplicação no modo iniciado por **SP:**

    a. Na caixa de texto **de URL sign-on,** escreva um URL que utilize o seguinte padrão:`https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>`

    
    b. Na caixa de texto **do Estado relé,** digite um URL que utilize o seguinte padrão:`https://pingone.com/1.0/<custom ID>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta, URL de inscrição e estado de retransmissão. Contacte a equipa de suporte ao [Cliente Da Skytap para](mailto:support@skytap.com) obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sessão com a página **SAML,** na secção certificado de **assinatura SAML,** encontre **metadados da Federação XML**. Selecione **Baixar** para descarregar o ficheiro de metadados e guardá-lo no seu computador.

    ![Screenshot do link de descarregamento de certificado](common/metadataxml.png)

1. Na **secção set up Single Sign-on para skytap,** copie os URL ou URLs apropriados, com base no seu requisito.

    ![Screenshot de URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, cria-se um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure,**Users** > selecione **Utilizadores de Diretório** > Ativo Azure**Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está mostrado no campo **Password.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que b.Simon utilize um único sign-on Azure, concedendo acesso a um único sign-on para a Skytap.

1. No portal Azure, selecione **Aplicações Empresariais** > **Todas as aplicações**.
1. Na lista de aplicações, selecione **Single Sign-on para Skytap**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![Screenshot da secção Gerir, com Utilizadores e grupos em destaque](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Na caixa de diálogo **Adicionar Atribuição,** selecione **Utilizadores e grupos**.

    ![Screenshot de Utilizadores e página de grupos, com adicionar utilizador destacado](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Em seguida, escolha o botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de papel na afirmação do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, escolha o botão **Select** na parte inferior do ecrã.
1. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

## <a name="configure-single-sign-on-for-skytap-sso"></a>Configure o único sinal para Skytap SSO

Para configurar um único sinal no single sign-on para o lado Skytap, você precisa enviar os metadados da **Federação descarregados XML**, e URLs copiados apropriados, desde o portal Azure até ao [Single Sign-on para](mailto:support@skytap.com)a equipa de suporte ao Cliente Skytap . Configuram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.


### <a name="create-single-sign-on-for-skytap-test-user"></a>Criar um único sinal para o utilizador do teste Skytap

Nesta secção, cria-se um utilizador chamado B.Simon em Single Sign-on para skytap. Trabalhe com a equipa de suporte ao [Cliente Skytap para](mailto:support@skytap.com) adicionar os utilizadores no Single Sign-on para a plataforma Skytap. Não pode utilizar um único sinal até criar e ativar os utilizadores.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de acesso do Azure AD utilizando o Painel de Acesso.

Quando selecionar o único sinal para o azulejo Skytap no Painel de Acesso, deve ser automaticamente inscrito no Único Sign-on para skytap para o qual configura o SSO. Para mais informações, consulte [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente slack com Azure AD](https://aad.portal.azure.com/)

