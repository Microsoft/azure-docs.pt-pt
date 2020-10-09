---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com a SignalFx Microsoft Docs'
description: Saiba como configurar um único sinal de entrada entre o Azure Ative Directory e o SignalFx.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/24/2020
ms.author: jeedes
ms.openlocfilehash: 989b41d4fc55241a5fa75a0eed9ea8f4ebaeee67
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856828"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Tutorial: Azure Ative Directory integração única (SSO) com SignalFx

Neste tutorial, você aprenderá a integrar o SignalFx com o Azure Ative Directory (Azure AD). Quando integrar o SignalFx com AZure AD, pode:

* Controlo da Azure AD que tem acesso ao SignalFx;
* Permitir que os seus utilizadores sejam automaticamente inscritos no SignalFx com as suas contas AD Azure; e
* Gerencie as suas contas num local (o portal Azure).

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único acesso com o Azure Ative Directory](/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, vai precisar de:

* Uma subscrição do Azure
    * Se não tiver uma subscrição, pode obter uma [conta gratuita aqui](https://azure.microsoft.com/free/).
* Sign-on único SignalFx (SSO) ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, irá configurar e testar o Azure AD SSO num ambiente de teste.

* SignalFx suporta **IDP** iniciado SSO
* SignalFx suporta **provisão do** utilizador Just In Time
* Uma vez configurado o SignalFx, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="step-1-add-the-signalfx-application-in-azure"></a>Passo 1: Adicione a aplicação SignalFx em Azure

Utilize estas instruções para adicionar a aplicação SignalFx à sua lista de aplicações geridas para o SaaS.

1. Inicie sessão no [portal Azure](https://portal.azure.com).
1. Na janela de navegação do lado esquerdo, selecione **Azure Ative Directory**.
1. Selecione **aplicações Enterprise**e, em seguida, selecione **Todas as aplicações**.
1. Selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** na caixa de pesquisa, insira e selecione **SignalFx**.
     * Poderá ter de esperar alguns minutos para que o pedido seja adicionado ao seu inquilino.
1. Deixe o portal Azure aberto e, em seguida, abra um novo separador web.    

## <a name="step-2-begin-signalfx-sso-configuration"></a>Passo 2: Iniciar a configuração do SignalFx SSO

Utilize estas instruções para iniciar o processo de configuração do SignalFx SSO.

1. No separador recém-aberto, aceda e inicie sessão no SignalFx UI. 
1. No menu superior, clique em **Integrações.** 
1. No campo de pesquisa, insira e selecione **O Diretório Ativo Azure**.
1. Clique **em Criar Nova Integração.**
1. Em **Nome,** insira um nome facilmente reconhecível que os seus utilizadores irão entender.
1. **Mark Show na página de login**.
    * Esta funcionalidade apresentará um botão personalizado na página de login em que os seus utilizadores podem clicar. 
    * As informações que inseriu no **Nome** aparecerão no botão. Como resultado, insira um **Nome** que os seus utilizadores reconhecerão. 
    * Esta opção só funcionará se utilizar um subdomínio personalizado para a aplicação SignalFx, como **yourcompanyname.signalfx.com**. Para obter um subdomínio personalizado, contacte o suporte signalFx. 
1. Copiar o **ID de integração.** Vai precisar desta informação mais tarde. 
1. Deixe o SignalFx UI aberto. 

## <a name="step-3-configure-azure-ad-sso"></a>Passo 3: Configurar a ad sso Azure

Utilize estas instruções para ativar o Azure AD SSO no portal Azure.

1. Volte ao [portal Azure](https://portal.azure.com/), e na página de integração da aplicação **SignalFx,** localize a secção **'Gerir'** e, em seguida, selecione **'Único sinal de entrada'**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set up single sign-on com** a página SAML, clique no ícone caneta (editar) para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. No **set-on único com** a página SAML, complete os seguintes campos: 

    a. No **Identifier,** insira o seguinte URL `https://api.<realm>.signalfx.com/v1/saml/metadata` e `<realm>` substitua-o pelo seu reino SignalFx. 

    b. Em **URL de resposta**, introduza o seguinte URL e `https://api.<realm>.signalfx.com/v1/saml/acs/<integration ID>` substitua-o pelo seu reino `<realm>` SignalFx, bem como `<integration ID>` pelo ID de **Integração** que copiou anteriormente a partir do SignalFx UI.

1. A aplicação SignalFx espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. 
    
1. Reveja e verifique se o seguinte mapa de reclamações aos atributos de origem que são povoados no Diretório Ativo. 

    | Nome |  Atributo de origem|
    | ------------------- | -------------------- |
    | User.FirstName  | user.givenname |
    | User.email  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | utilizador.sobrenome    |

    > [!NOTE]
    > Este processo requer que o seu Ative Directory esteja configurado com pelo menos um domínio personalizado verificado, bem como tenha acesso às contas de e-mail deste domínio. Se não tiver a certeza ou precisar de assistência com esta configuração, contacte o suporte do SignalFx.  

1. Na **configuração de um único sinal com** a página SAML, na secção certificado de assinatura **SAML,** encontre **o Certificado (Base64)** e, em seguida, selecione **Download**. Faça o download do certificado e guarde-o no seu computador. Em seguida, copiar o valor do **url de metadados da Federação de Aplicações;** necessitará desta informação num passo posterior na UI SignalFx. 

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **SignalFx configurar,** copie o valor do **identificador Azure AD.** Você precisará desta informação em um passo posterior na UI SignalFx. 

## <a name="step-4-create-an-azure-ad-test-user"></a>Passo 4: Criar um utilizador de teste AZure AD

Utilize estas instruções para criar um utilizador de teste no portal Azure chamado **B.Simon**.

1. No portal Azure, na janela de navegação do lado esquerdo, selecione **O Diretório Ativo Azure,** selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. No topo da página, selecione **Novo utilizador**.
1. Nas propriedades do **Utilizador:**
   1. No **nome do utilizador,** insira, `username@companydomain.extension` tal como `b.simon@contoso.com` .
   1. Em **Nome,** insira `B.Simon` .
   1. Marque **a palavra-passe**e, em seguida, copie o valor apresentado na **Palavra-passe**. Necessitará desta informação mais tarde para testar esta integração. 
   1. Clique em **Criar**.

## <a name="step-5-assign-the-azure-ad-test-user"></a>Passo 5: Atribuir o utilizador de teste Azure AD

Utilize estas instruções para permitir que o utilizador de teste utilize o Azure um único sinal de entrada para o SignalFx.

1. No portal Azure, selecione **aplicações Enterprise**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **SignalFx**.
1. Na página geral da aplicação, encontre a secção **Gerir** e, em seguida, selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador**e, em seguida, na caixa de diálogo de atribuição de **adicionar,** selecione **Utilizadores e grupos**.

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** a partir da lista **de Utilizadores,** selecione **B.Simon,** e, em seguida, na parte inferior da página, clique em **Select**.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, então na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique em **Selecionar** na parte inferior da página.
1. Na caixa de diálogo **'Adicionar Atribuição',** clique na **Atribuição**.

## <a name="step-6-complete-the-signalfx-sso-configuration"></a>Passo 6: Complete a configuração SignalFx SSO 

1. Abra o separador anterior e volte ao SignalFx UI para ver a atual página de integração do Azure Ative Directory. 
1. Ao lado **do Certificado (Base64),** clique em **Upload File**e, em seguida, localize o ficheiro de **certificado codificado Base64** que descarregou anteriormente a partir do portal Azure.
1. Junto ao **Identificador AD AD Azure,** cole o valor do **identificador Azure AD** que copiou anteriormente do portal Azure. 
1. Ao lado **do URL de metadados da Federação,** cole o valor **url de metadados da Federação** de Aplicações que copiou anteriormente do portal Azure. 
1. Clique em **Guardar**.

## <a name="step-7-test-sso"></a>Passo 7: Teste SSO

Reveja as seguintes informações sobre como testar sSO, bem como as expectativas de iniciar sessão no SignalFx pela primeira vez. 

### <a name="test-logins"></a>Teste de logins

* Para testar o login, deve utilizar uma janela privada/incógnita, ou pode sair do portal Azure. Caso contrário, os cookies para o utilizador que configurar a aplicação irão interferir e impedir um login bem sucedido com o utilizador do teste.

* Quando um novo utilizador de teste iniciar sessão pela primeira vez, o Azure forçará uma mudança de palavra-passe. Quando isto ocorrer, o processo de login SSO não será concluído; o utilizador de teste será direcionado para o portal Azure. Para resolver problemas, o utilizador de teste deve alterar a sua palavra-passe e navegar para a página de login do SignalFx ou para o Painel de Acesso e tentar novamente.
    * Quando clicar no azulejo SignalFx no Painel de Acesso, deverá iniciar sessão automaticamente no SignalFx. 
        * Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

* A aplicação SignalFx pode ser acedida a partir do Painel de Acesso ou através de uma página de login personalizada atribuída à organização. O utilizador de teste deve testar a integração a partir de qualquer um destes locais.
    * O utilizador de teste pode utilizar as credenciais criadas anteriormente neste processo para **b.simon \@ contoso.com**.

### <a name="first-time-logins"></a>Logins pela primeira vez

* Quando um utilizador iniciar sessão no SignalFx do SSO SAML pela primeira vez, o utilizador receberá um e-mail SignalFx com um link. O utilizador deve clicar no link para efeitos de autenticação. Esta validação de e-mail só será para os utilizadores da primeira vez. 

* O SignalFx suporta a criação do utilizador **Just In Time,** o que significa que se um utilizador não existir no SignalFx, então a conta do utilizador será criada após a primeira tentativa de login.

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Experimente o SignalFx com Azure AD](https://aad.portal.azure.com/)
