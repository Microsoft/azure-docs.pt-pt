---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com firmex VDR  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Firmex VDR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 670ff192-c23e-49e4-8fd1-516e02d8856c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdfb857d3a68081fda84aef33e6b5a4b4d1bce28
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76761239"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-firmex-vdr"></a>Tutorial: Azure Ative Directory integração individual (SSO) com firmex VDR

Neste tutorial, você vai aprender a integrar Firmex VDR com o Azure Ative Directory (Azure AD). Quando integrar firmex VDR com AD Azure, pode:

* Controlo em Azure AD que tem acesso a Firmex VDR.
* Ative que os seus utilizadores sejam automaticamente inscritos na Firmex VDR com as suas contas Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Firmex VDR única subscrição ativada por Subscrição (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Firmex VDR suporta **SP e IDP** iniciadoS SSO

* Assim que configurar o Firmex, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controles de sessão se estendem do acesso condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-firmex-vdr-from-the-gallery"></a>Adicionando Firmex VDR da galeria

Para configurar a integração do Firmex VDR em Azure AD, precisa de adicionar firmex VDR da galeria à sua lista de aplicações geridas do SaaS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. No **Add da** secção galeria, **digite Firmex VDR** na caixa de pesquisa.
1. Selecione **Firmex VDR** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-firmex-vdr"></a>Configure e teste Azure AD único sinal para Firmex VDR

Configure e teste Azure AD SSO com Firmex VDR utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Firmex VDR.

Para configurar e testar o Azure AD SSO com o Firmex VDR, complete os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configure Firmex VDR SSO](#configure-firmex-vdr-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie o utilizador de teste Firmex VDR](#create-firmex-vdr-test-user)** - para ter uma contrapartida de B.Simon em Firmex VDR que esteja ligada à representação azure AD do utilizador.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Firmex VDR,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , o usuário não precisa executar nenhuma etapa, pois o aplicativo já está previamente integrado ao Azure.

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **de URL sign-on,** escreva o URL: `https://login.firmex.com`

1. Clique em **Guardar**.

1. A aplicação Firmex VDR espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração dos seus atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/default-attributes.png)

1. Além de acima, a aplicação Firmex VDR espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de origem|
    | ------------ | --------- |
    | e-mail | user.mail |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o XML de metadados de **Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na secção **'Configuração Firmex VDR',** copie os URL(s) adequados com base no seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso à Firmex VDR.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Firmex VDR**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-firmex-vdr-sso"></a>Configure Firmex VDR SSO

### <a name="before-you-get-started"></a>Antes de começar

#### <a name="what-youll-need"></a>O que vai precisar

-   Uma subscrição ativa da Firmex
-   Azure AD como o seu serviço SSO
-   O seu administrador de TI para configurar o SSO
-   Uma vez ativado o SSO, todos os utilizadores da sua empresa devem iniciar sessão na Firmex utilizando o SSO e não utilizar em sessão/senha.

#### <a name="how-long-will-this-take"></a>Quanto tempo vai demorar?

Implementar o SSO demora alguns minutos. Não existe praticamente nenhum tempo de inatividade entre o Firmex Support que permite o SSO para o seu site, e os utilizadores da sua empresa autenticam usando SSO. Basta seguir os passos abaixo.

### <a name="step-1---identify-your-companys-domains"></a>Passo 1 - Identifique os domínios da sua empresa

Identifique os domínios com os quais os utilizadores da sua empresa estão a fazer login.

Por exemplo:

- @firmex.com
- @firmex.ca

### <a name="step-2---contact-firmex-support-with-your-domains"></a>Passo 2 - Contacte o Suporte Firmex com os seus domínios

Envie um e-mail para [firmex Support Team](mailto:support@firmex.com) ou ligue para 1888 688 4042 x.11 para falar com firmex Suporte. Transmita as suas informações de domínio. Firmex Support adicionará os domínios ao seu VDR como **domínios reivindicados**. A sua administração tem agora de configurar o SSO.

Aviso: Até que a administração do site coneça os domínios reclamados, os utilizadores da sua empresa não poderão iniciar sessão no VDR. Os utilizadores não-empresa (isto é, utilizadores convidados) ainda podem iniciar sessão usando o seu e-mail/palavra-passe. A configuração deve demorar alguns minutos.

### <a name="step-3---configure-the-claimed-domains"></a>Passo 3 - Configure os domínios reclamados

1. Inicie sessão na Firmex como Administrador de Site.
1. Do canto superior esquerdo, clique no logótipo da sua empresa.
1. Selecione o separador **SSO.** Em seguida, selecione **A Configuração SSO**. Clique no domínio que pretende configurar.

    ![Domínios Reclamados](./media/firmex-vdr-tutorial/edit-sso.png)  

1. Mande o seu Administrador de TI preencher os seguintes campos. Os campos devem ser retirados do seu fornecedor de identidade:  

    ![Configuração SSO](./media/firmex-vdr-tutorial/SSO-config.png)

    a. Na caixa de texto **Id da Entidade,** colá o valor do **Identificador AD Azure,** que copiou do portal Azure.

    b. Na caixa de texto URL do Fornecedor de **Identidade,** colá o valor URL do **Login,** que copiou do portal Azure.

    c. **Certificado de Chave Pública** - Para efeitos de autenticação, uma mensagem SAML pode ser assinada digitalmente pelo emitente. Para verificar a assinatura na mensagem, o recetor de mensagem utiliza uma chave pública conhecida por pertencer ao emitente. Da mesma forma, para encriptar uma mensagem, uma chave de encriptação pública pertencente ao recetor final deve ser conhecida do emitente. Em ambas as situações - assinatura e encriptação - as chaves públicas fidedignas devem ser partilhadas com antecedência.  Este é o **X509Certificate** da **Federação metadados XML**

    d. Clique em **Guardar** para completar a configuração SSO. As alterações têm efeito imediatamente.

1. Neste momento, o SSO está ativado para o seu site.

### <a name="create-firmex-vdr-test-user"></a>Criar o utilizador de teste Firmex VDR

Nesta secção, cria-se um utilizador chamado B.Simon em Firmex. Trabalhe com a [Firmex Support Team](mailto:support@firmex.com) para adicionar os utilizadores na plataforma Firmex. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no azulejo Firmex VDR no Painel de Acesso, deverá ser automaticamente inscrito no Firmex VDR para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Firmex VDR com Azure AD](https://aad.portal.azure.com/)

- [O que é o controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger firmex com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
