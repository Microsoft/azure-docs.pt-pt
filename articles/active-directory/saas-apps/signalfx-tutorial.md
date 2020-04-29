---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com signalFx [ integração de diretório ativo azure) com signalFx [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SignalFx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/24/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 149718dcd325ef6bd6a6754ba100ffdc34be0a07
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79136419"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com a SignalFx

Neste tutorial, você aprenderá a integrar o SignalFx com o Azure Ative Directory (Azure AD). Quando integrar o SignalFx com o Azure AD, pode:

* Controlo da AD Azure que tem acesso ao SignalFx;
* Permitir que os seus utilizadores sejam automaticamente inscritos no SignalFx com as suas contas Azure AD; e
* Gerencie as suas contas num local (portal Azure).

Para saber mais sobre a integração de aplicações saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, vai precisar:

* Uma subscrição do Azure
    * Se não tiver uma subscrição, pode obter uma [conta gratuita aqui](https://azure.microsoft.com/free/).
* Assinatura de sinal único SignalFx (SSO) ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, irá configurar e testar o Azure AD SSO num ambiente de teste.

* SignalFx suporta **IDP** iniciado SSO
* SignalFx suporta o provisionamento de utilizadores **justo no tempo**
* Assim que configurar o SignalFx, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="step-1-add-the-signalfx-application-in-azure"></a>Passo 1: Adicionar a aplicação SignalFx em Azure

Utilize estas instruções para adicionar a aplicação SignalFx à sua lista de aplicações SaaS geridas.

1. Inicie sessão no [portal Azure.](https://portal.azure.com)
1. Na janela de navegação do lado esquerdo, selecione **Azure Ative Directory**.
1. Selecione **aplicações Enterprise**e, em seguida, selecione **Todas as aplicações**.
1. Selecione **Nova aplicação**.
1. No Add da secção **galeria,** na caixa de pesquisa, introduza e selecione **SignalFx**.
     * Poderá ter de esperar alguns minutos para que o pedido seja adicionado ao seu inquilino.
1. Deixe o portal Azure aberto e, em seguida, abra um novo separador web.    

## <a name="step-2-begin-signalfx-sso-configuration"></a>Passo 2: Iniciar a configuração SignalFx SSO

Utilize estas instruções para iniciar o processo de configuração para o SSO SignalFx.

1. No separador recém-aberto, aceda e entre no SignalFx UI. 
1. No menu superior, clique em **Integrações.** 
1. No campo de pesquisa, insira e selecione **Azure Ative Directory**.
1. Clique **em criar nova integração.**
1. Em **Nome,** introduza um nome facilmente reconhecível que os seus utilizadores compreenderão.
1. Mark Show na página de **login**.
    * Esta funcionalidade apresentará um botão personalizado na página de login em que os seus utilizadores podem clicar. 
    * A informação que inseriu em **Nome** aparecerá no botão. Como resultado, introduza um **Nome** que os seus utilizadores reconhecerão. 
    * Esta opção só funcionará se utilizar um subdomínio personalizado para a aplicação SignalFx, como **yourcompanyname.signalfx.com**. Para obter um subdomínio personalizado, contacte o suporte SignalFx. 
1. Copiar o ID de **Integração**. Precisará desta informação num passo posterior. 
1. Deixe o SignalFx UI aberto. 

## <a name="step-3-configure-azure-ad-sso"></a>Passo 3: Configure Azure AD SSO

Utilize estas instruções para ativar o Azure AD SSO no portal Azure.

1. Volte ao [portal Azure](https://portal.azure.com/), e na página de integração de aplicações **SignalFx,** localize a secção **Gerir** e, em seguida, selecione o único **sinal .**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone caneta (editar) para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. No **set up single sign-on com** a página SAML, complete os seguintes campos: 

    a. No **Identificador,** introduza `https://api.<realm>.signalfx.com/v1/saml/metadata` o `<realm>` URL seguinte e substitua-o pelo seu reino SignalFx. 

    b. No **URL de Resposta,** introduza o URL seguinte `https://api.<realm>.signalfx.com/v1/saml/acs/<integration ID>` e substitua-o `<realm>` pelo seu reino SignalFx, bem como `<integration ID>` com o ID de **Integração** copiado anteriormente do SignalFx UI.

1. A aplicação SignalFx espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. 
    
1. Reveja e verifique se as seguintes reclamações mapeiam os atributos de origem que são povoados no Diretório Ativo. 

    | Nome |  Atributo fonte|
    | ------------------- | -------------------- |
    | Utilizador.Primeiro Nome  | user.givenname |
    | Utilizador.email  | utilizador.mail |
    | Id personimmutável       | user.userprincipalname    |
    | Utilizador.Apelido       | utilizador.sobrenome    |

    > [!NOTE]
    > Este processo requer que o seu Diretório Ativo esteja configurado com pelo menos um domínio personalizado verificado, bem como tem acesso às contas de e-mail neste domínio. Se não tiver a certeza ou precisar de assistência com esta configuração, contacte o suporte SignalFx.  

1. No **set set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** encontre **certificado (Base64)** e, em seguida, selecione **Download**. Faça o download do certificado e guarde-o no seu computador. Em seguida, copiar o valor de url de **metadados da Federação de Aplicações;** necessitará desta informação num passo posterior no SignalFx UI. 

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **'Set-Up SignalFx',** copie o valor do **identificador AD Azure.** Você precisará desta informação em um passo posterior no SignalFx UI. 

## <a name="step-4-create-an-azure-ad-test-user"></a>Passo 4: Criar um utilizador de teste Azure AD

Utilize estas instruções para criar um utilizador de teste no portal Azure chamado **B.Simon**.

1. No portal Azure, na janela de navegação do lado esquerdo, selecione **O Diretório Ativo Azure**, depois selecione **Utilizadores,** e depois selecione **Todos os utilizadores**.
1. No topo da página, selecione **Novo utilizador**.
1. Nas propriedades do **Utilizador:**
   1. No **nome do utilizador,** introduza, `username@companydomain.extension`como `b.simon@contoso.com`.
   1. Em **Nome,** insira `B.Simon`.
   1. **Mark Mostrar palavra-passe**, e depois copiar o valor visualizado em **Password**. Necessitará desta informação mais tarde para testar esta integração. 
   1. Clique em **Criar**.

## <a name="step-5-assign-the-azure-ad-test-user"></a>Passo 5: Atribuir o utilizador de teste Azure AD

Utilize estas instruções para permitir ao utilizador do teste utilizar um único sinal de entrada azure para o SignalFx.

1. No portal Azure, selecione **aplicações Enterprise**, e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **SignalFx**.
1. Na página geral da aplicação, encontre a secção **Gerir** e, em seguida, selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**, e, em seguida, na caixa de diálogo **Adicionar Assignment,** selecione Utilizadores e **grupos**.

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. Na caixa de diálogo **Utilizadores e grupos,** a partir da lista **de Utilizadores,** selecione **B.Simon**, e depois na parte inferior da página, clique em **Select**.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, então na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique em **Select** na parte inferior da página.
1. Na caixa de diálogo **Adicionar Atribuição,** clique na **placa de design**.

## <a name="step-6-complete-the-signalfx-sso-configuration"></a>Passo 6: Complete a configuração SignalFx SSO 

1. Abra o separador anterior e volte ao SignalFx UI para ver a página de integração do Diretório Ativo Azure atual. 
1. Ao lado do **Certificado (Base64)** clique no **Upload File**e, em seguida, localize o ficheiro de **certificado codificado Base64** que já descarregou do portal Azure.
1. Junto ao **Identificador Azure AD,** cola o valor do **Identificador AD Azure** que copiou anteriormente do portal Azure. 
1. Ao lado do **URL de Metadados da Federação,** cola o valor url de **metadados da Federação** de Aplicações que copiou anteriormente do portal Azure. 
1. Clique em **Guardar**.

## <a name="step-7-test-sso"></a>Passo 7: Teste SSO

Reveja as seguintes informações sobre como testar o SSO, bem como as expectativas de iniciar sessão no SignalFx pela primeira vez. 

### <a name="test-logins"></a>Logins de teste

* Para testar o login, deve utilizar uma janela privada/incógnita, ou pode sair do portal Azure. Caso contrário, os cookies para o utilizador que configuraram a aplicação interferirão e impedirão um login bem sucedido com o utilizador do teste.

* Quando um novo utilizador de teste entrar pela primeira vez, o Azure forçará uma alteração de senha. Quando isto ocorrer, o processo de login SSO não será concluído; o utilizador do teste será direcionado para o portal Azure. Para resolver problemas, o utilizador do teste deve alterar a sua palavra-passe e navegar para a página de login SignalFx ou para o Painel de Acesso e tentar novamente.
    * Quando clicar no azulejo SignalFx no Painel de Acesso, deve ser automaticamente iniciado no SignalFx. 
        * Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

* A aplicação SignalFx pode ser acedida a partir do Painel de Acesso ou através de uma página de login personalizada atribuída à organização. O utilizador do teste deve testar a integração a partir de qualquer um destes locais.
    * O utilizador do teste pode utilizar as **b.simon@contoso.com**credenciais criadas anteriormente neste processo para .

### <a name="first-time-logins"></a>Logins pela primeira vez

* Quando um utilizador inicia sessão no SignalFx a partir do SSO SAML pela primeira vez, o utilizador receberá um e-mail SignalFx com um link. O utilizador deve clicar no link para fins de autenticação. Esta validação de e-mail só será efetuada para utilizadores pela primeira vez. 

* O SignalFx suporta a criação do utilizador **Just In Time,** o que significa que se um utilizador não existir no SignalFx, então a conta do utilizador será criada após a primeira tentativa de login.

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Experimente signalFx com Azure AD](https://aad.portal.azure.com/)