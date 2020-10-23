---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com O VDR Firmex Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Firmex VDR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: jeedes
ms.openlocfilehash: 6dbd39b5c56192ad2ca957c5500338b50e8c8963
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92453390"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-firmex-vdr"></a>Tutorial: Azure Ative Directy integração única de sign-on (SSO) com O VDR Firmex

Neste tutorial, você vai aprender a integrar Firmex VDR com Azure Ative Direy (Azure AD). Quando integrar o Firmex VDR com AZure AD, pode:

* Controlo em Azure AD que tem acesso a VDR Firmex.
* Ative os seus utilizadores a serem automaticamente inscritos no VDR Firmex com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Firmex VDR assinatura única ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Firmex VDR suporta **SP e IDP** iniciado SSO

* Uma vez configurado o Firmex, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-firmex-vdr-from-the-gallery"></a>Adicionar VDR firmex da galeria

Para configurar a integração do Firmex VDR no AD AZure, é necessário adicionar o VDR Firmex da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Firmex VDR** na caixa de pesquisa.
1. Selecione **Firmex VDR** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-firmex-vdr"></a>Configurar e testar Azure AD único sinal para Firmex VDR

Configure e teste Azure AD SSO com Firmex VDR usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AD Azure e o utilizador relacionado em Firmex VDR.

Para configurar e testar o Azure AD SSO com o Firmex VDR, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure firmex VDR SSO](#configure-firmex-vdr-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * Crie o utilizador de **[teste Firmex VDR](#create-firmex-vdr-test-user)** - para ter uma contraparte de B.Simon em Firmex VDR que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Firmex VDR,** encontre a secção **Gerir** e selecione **um único sinal de sing.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **único sign-on de Configuração com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure.

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite o URL:  `https://login.firmex.com`

1. Clique em **Guardar**.

1. A aplicação Firmex VDR espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Firmex VDR espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name | Atributo de origem|
    | ------------ | --------- |
    | e-mail | user.mail |

1. Na **configuração de um único sessão com** a página SAML, na secção Certificado de **Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Set-up Firmex VDR,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao VDR Firmex.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Firmex VDR**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-firmex-vdr-sso"></a>Configurar Firmex VDR SSO

### <a name="before-you-get-started"></a>Antes de começar

#### <a name="what-youll-need"></a>O que vai precisar

-   Uma subscrição ativa do Firmex
-   Azure AD como o seu serviço SSO
-   O seu administrador de TI para configurar o SSO
-   Uma vez que o SSO esteja ativado, todos os utilizadores da sua empresa devem iniciar sessão no Firmex utilizando sSO e não utilizar um login/palavra-passe.

#### <a name="how-long-will-this-take"></a>Quanto tempo vai demorar?

Implementar sSO leva alguns minutos. Praticamente não existe tempo de inatividade entre o Suporte Firmex que permite o SSO para o seu site, e os utilizadores da sua empresa autenticam-se utilizando o SSO. Basta seguir os passos abaixo.

### <a name="step-1---identify-your-companys-domains"></a>Passo 1 - Identifique os domínios da sua empresa

Identifique os domínios com os quais os utilizadores da sua empresa estão a iniciar sessão.

Por exemplo:

- @firmex.com
- @firmex.ca

### <a name="step-2---contact-firmex-support-with-your-domains"></a>Passo 2 - Contacte o Suporte firmex com os seus domínios

Envie um e-mail [para a Equipa de Suporte firmex](mailto:support@firmex.com) ou ligue para 1888 688 4042 x.11 para falar com o Suporte firmex. Passe ao longo da informação de domínio. O Firmex Support adicionará os domínios ao seu VDR como **domínios reclamados**. O seu administrador tem agora de configurar a SSO.

Aviso: Até que a administração do seu site configure os domínios reclamados, os utilizadores da sua empresa não poderão iniciar sessão no VDR. Os utilizadores não-empresa (isto é, utilizadores convidados) ainda podem iniciar sessão através do seu email/password. A configuração deve demorar alguns minutos.

### <a name="step-3---configure-the-claimed-domains"></a>Passo 3 - Configurar os domínios reclamados

1. Faça login no Firmex como administrador do site.
1. Do canto superior esquerdo, clique no logotipo da empresa.
1. Selecione o separador  **SSO.**  Em seguida, selecione  **a configuração SSO**. Clique no domínio que pretende configurar.

    ![Domínios Reclamados](./media/firmex-vdr-tutorial/edit-sso.png)  

1. Que o seu administrador de TI preencha os seguintes campos. Os campos devem ser retirados do seu fornecedor de identidade:  

    ![Configuração SSO](./media/firmex-vdr-tutorial/SSO-config.png)

    a. Na caixa de texto **ID** da Entidade, cole o valor do **identificador Azure AD,** que copiou a partir do portal Azure.

    b. Na caixa de texto **URL do Fornecedor de Identidade,** cole o valor URL de **login,** que copiou a partir do portal Azure.

    c. **Certificado chave público**  - Para efeitos de autenticação, uma mensagem SAML pode ser assinada digitalmente pelo emitente. Para verificar a assinatura na mensagem, o recetor de mensagens utiliza uma chave pública conhecida por pertencer ao emitente. Da mesma forma, para encriptar uma mensagem, uma chave de encriptação pública pertencente ao recetor final deve ser do conhecimento do emitente. Em ambas as situações - a assinatura e a encriptação - as chaves públicas fidedignas devem ser partilhadas com antecedência.  Este é o **Certificado X509** da **Federação De Metadados XML**

    d. Clique **em Guardar** para completar a configuração SSO. As alterações fazem efeito imediatamente.

1. Neste momento, o SSO está ativado para o seu site.

### <a name="create-firmex-vdr-test-user"></a>Criar utilizador de teste VDR firmex

Nesta secção, cria-se um utilizador chamado B.Simon em Firmex. Trabalhe com a [Equipa de Suporte firmex](mailto:support@firmex.com) para adicionar os utilizadores na plataforma Firmex. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Firmex VDR no Painel de Acesso, deverá ser automaticamente inscrito no VDR Firmex para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o VDR firmex com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o Firmex com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)