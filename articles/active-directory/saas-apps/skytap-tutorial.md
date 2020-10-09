---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Single Sign-on para Skytap / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Single Sign-on para skytap.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: jeedes
ms.openlocfilehash: 8d34ca8ed01144ee282f6411640894807a09ef08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88527881"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-single-sign-on-for-skytap"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com single sign-on para Skytap

Neste tutorial, você vai aprender a integrar single sign-on para Skytap com Azure Ative Directory (Azure AD). Quando integrar o Single Sign-on para Skytap com Azure AD, pode:

* Controle em Azure AD que tem acesso a Single Sign-on para Skytap.
* Permita que os seus utilizadores sejam automaticamente inscritos no Single Sign-on para Skytap com as suas contas AD Azure.
* Gerencie as suas contas numa localização central, o portal Azure.

Para saber mais sobre software como integração de aplicações de serviço (SaaS) com Azure AD, consulte O que é o acesso à [aplicação e um único acesso com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura única para o sign-on único skytap (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* O Sign-on único para skytap suporta o SSO iniciado pelo SP e IDP.
* Depois de configurar o Sign-on Único para o Skytap, pode impor o controlo da sessão. Isto protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o acesso condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-single-sign-on-for-skytap-from-the-gallery"></a>Adicione um único sign-on para Skytap da galeria

Para configurar a integração do Single Sign-on para Skytap em AD Azure, você precisa adicionar Single Sign-on para Skytap da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Single Sign-on para Skytap** na caixa de pesquisa.
1. Selecione **Single Sign-on para Skytap** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-single-sign-on-for-skytap"></a>Configurar e testar a Azure AD um único sinal para o sign-on único para skytap

Configure e teste Azure AD SSO com single sign-on para Skytap utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, estabeleça uma relação ligada entre um utilizador Azure AD e o utilizador relacionado em Single Sign-on para Skytap.

Aqui estão os passos gerais para configurar e testar Azure AD SSO com single sign-on para Skytap:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.

    a. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com B.Simon.

    b. **[Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que a B.Simon utilize um único sinal de Ad AD.
1. **[Configurar o Sign-on Único para o Skytap SSO](#configure-single-sign-on-for-skytap-sso)** configurar as definições de inscrição única no lado da aplicação.

    a. **[Crie um único sign-on para o utilizador de teste Skytap](#create-single-sign-on-for-skytap-test-user)** para ter uma contraparte de B.Simon em Single Sign-on para Skytap. Esta contrapartida está ligada à representação Azure AD do utilizador.
1. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página **single Sign-on for Skytap** application integration, encontre a secção **Gerir.** Selecione **um único sinal de inscrição**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, selecione o ícone de lápis para **Configuração SAML Básica** para editar as definições.

   ![Screenshot de Configurar um único sinal com página SAML, com ícone de lápis em destaque](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier,** digite um URL que utiliza o seguinte padrão: `http://pingone.com/<custom EntityID>`

    b. Na caixa de texto **URL de resposta,** digite um URL que utiliza o seguinte padrão: `https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

1. Selecione **Definir URLs adicionais**e executar os seguintes passos se quiser configurar a aplicação no modo iniciado **sp:**

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL que utiliza o seguinte padrão: `https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>`

    
    b. Na caixa de texto **do Estado de Retransmissão,** digite um URL que utiliza o seguinte padrão: `https://pingone.com/1.0/<custom ID>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta, URL de inscrição e Estado de retransmissão. Contacte a [equipa de suporte ao cliente da Skytap para](mailto:support@skytap.com) obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sinal com** a página SAML, na secção certificado de assinatura **SAML,** encontre **o Metadados da Federação XML**. Selecione **Baixar** para descarregar o ficheiro de metadados e guardá-lo no seu computador.

    ![Screenshot do link de descarregamento do certificado](common/metadataxml.png)

1. Na **secção 'set up' Single Para Skytap,** copie os URL ou URLs apropriados, com base no seu requisito.

    ![Screenshot dos URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, cria-se um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >  **Users**  >  **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavras-passe Show** e, em seguida, anote o valor mostrado no campo **Palavra-passe.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que B.Simon utilize o único sinal de Azure, permitindo o acesso a Single Sign-on para Skytap.

1. No portal Azure, selecione **Aplicações empresariais**  >  **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Single Sign-on for Skytap**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![Screenshot da secção Gerir, com utilizadores e grupos em destaque](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Na caixa de diálogo **de atribuição de** adicionar, selecione **Utilizadores e grupos**.

    ![Screenshot da página de Utilizadores e grupos, com o utilizador Adicionar em destaque](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Em seguida, escolha o botão **Selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, escolha o botão **Selecione** na parte inferior do ecrã.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

## <a name="configure-single-sign-on-for-skytap-sso"></a>Configurar um único sign-on para Skytap SSO

Para configurar um único sinal no sign-on único para o lado Skytap, você precisa enviar o **metdata XML da Federação**descarregado, e URLs copiados apropriados, do portal Azure para a [equipa de suporte do Cliente Skytap.](mailto:support@skytap.com) Configuram esta definição para que a ligação SSO SAML seja corretamente definida em ambos os lados.


### <a name="create-single-sign-on-for-skytap-test-user"></a>Criar um único sinal para o utilizador de teste Skytap

Nesta secção, cria-se um utilizador chamado B.Simon em Single Sign-on para Skytap. Trabalhe com a [equipa de suporte do Cliente Skytap para](mailto:support@skytap.com) adicionar os utilizadores na plataforma Single Sign-on para skytap. Não pode utilizar uma única sessão de inscrição até criar e ativar os utilizadores.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de acesso Azure AD utilizando o Painel de Acesso.

Quando selecionar o sign-on único para o azulejo Skytap no Painel de Acesso, deverá ser automaticamente inscrito no Sign-on Único para Skytap para o qual configura sSO. Para mais informações, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Slack com Azure AD](https://aad.portal.azure.com/)

