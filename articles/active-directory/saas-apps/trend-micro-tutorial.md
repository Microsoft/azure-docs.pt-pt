---
title: 'Tutorial: Integração AZURE AD SSO com Trend Micro Web Security (TMWS)'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Trend Micro Web Security (TMWS).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.openlocfilehash: b76c41787d7a35fb3024fa18c0122bc966243bbc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008400"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-security-tmws"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Trend Micro Web Security (TMWS)

Neste tutorial, você vai aprender a integrar a Trend Micro Web Security (TMWS) com o Azure Ative Directory (Azure AD). Quando integrar o TMWS com AZure AD, pode:

* Controlo em Azure AD que tem acesso à TMWS.
* Permitir que os seus utilizadores sejam automaticamente inscritos na TMWS com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte [o Single sign-on para aplicações no Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa do seguinte:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição TMWS que está ativada para SSO.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar Azure AD SSO em um ambiente de teste.

* A TMWS suporta sSO iniciado pelo SP.
* Depois de configurar o TMWS, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. Para aprender a impor o controlo da sessão utilizando a Microsoft Cloud App Security, consulte [a bordo e implemente o Controlo de Aplicações de Acesso Condicional para qualquer aplicação.](/cloud-app-security/proxy-deployment-any-app)

## <a name="add-tmws-from-the-gallery"></a>Adicione TMWS da galeria

Para configurar a integração da TMWS no AD Azure, é necessário adicionar TMWS da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione o serviço **Azure Ative Directory.**
1. Selecione **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** insira **trend Micro Web Security (TMWS)** na caixa de pesquisa.
1. Selecione **Trend Micro Web Security (TMWS)** nos resultados da pesquisa e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-tmws"></a>Configure e teste Azure AD SSO para TMWS

Irá configurar e testar O SSO Azure AD com TMWS utilizando um utilizador de teste chamado B.Simon. Para que o SSO funcione, é necessário estabelecer uma ligação entre um utilizador AZure AD e o utilizador relacionado em TMWS.

Você completará estes passos básicos para configurar e testar Azure AD SSO com TMWS:

1. [Configure Azure AD SSO](#configure-azure-ad-sso) para ativar a funcionalidade para os seus utilizadores.
    1. [Crie um utilizador AZure AD](#create-an-azure-ad-test-user) para testar o Azure AD com um único sinal de sessão.
    1. [Conceder ao utilizador de teste Azure AD](#grant-the-azure-ad-test-user-access-to-tmws) acesso à TMWS.
    1. [Configure as definições de sincronização do utilizador e do grupo em Azure AD](#configure-user-and-group-synchronization-settings-in-azure-ad).
1. [Configure o SSO TMWS](#configure-tmws-sso) no lado da aplicação.
1. [Teste SSO](#test-sso) para verificar a configuração.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Complete estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Trend Micro Web Security (TMWS),** na secção **Gerir,** selecione um único sinal de **sing.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. Na **configuração única Sign-On com** a página SAML, selecione o botão de caneta para **configuração SAML básica** para editar as definições:

   ![Editar as definições básicas de configuração SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** introduza valores nas seguintes caixas:

    a. Na caixa **identifier (Entity ID),** introduza um URL no seguinte padrão:

    `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. Na caixa **URL de resposta,** insira este URL:

    `https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > O valor do identificador no passo anterior não é o valor que deve introduzir. Tens de usar o identificador verdadeiro. Pode obter este valor nas **Definições do Fornecedor de Serviços para a secção Portal Admin Azure** na página Método de **Autenticação** para AD AZure a partir de Serviços de **Diretório de Administração >**.

1. A TMWS espera as afirmações DO SAML num formato específico, pelo que é necessário adicionar mapeamentos de atributos personalizados à configuração de atributos de token SAML. Esta imagem mostra os atributos predefinidos:

    ![Atributos padrão](common/default-attributes.png)

1. Além dos atributos na imagem anterior, a TMWS espera que mais dois atributos sejam repercutidos na resposta SAML. Estes atributos são apresentados na tabela seguinte. Os atributos são pré-povoados, mas pode alterá-los para satisfazer os seus requisitos.
    
    | Name | Atributo de origem|
    | --------------- | --------- |
    | sAMAccountName | user.onpremisessamaccountname |
    | uPN | user.userprincipalname |

1. Na **configuração de um único sinal com** a página SAML, na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)**. Selecione o link **descarregar** ao lado deste nome de certificado para descarregar o certificado e guardá-lo no seu computador:

    ![Link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar a Tecnologia Micro Segurança Web (TMWS),** copie os URL ou URLs apropriados, com base nos seus requisitos:

    ![Copiar os URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste chamado B.Simon no portal Azure.

1. No painel esquerdo do portal Azure, selecione **Azure Ative Directory**. Selecione **Utilizadores** e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. Na caixa **Nome,** insira `B.Simon` .  
   1. Na caixa de **nome de utilizador,** insira **@ nome de* utilizador dodomínio da empresa *.* extensão***. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione **Mostrar a palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa **palavra-passe.**
   1. Selecione **Criar**.

### <a name="grant-the-azure-ad-test-user-access-to-tmws"></a>Conceder ao utilizador de teste Azure AD acesso ao TMWS

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a TMWS.

1. No portal Azure, selecione **aplicações Enterprise** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Trend Micro Web Security (TMWS)**.
1. Na página geral da aplicação, na secção **Gerir,** selecione **Utilizadores e grupos**:

   ![Selecionar Utilizadores e grupos](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Adicionar Atribuição'.**

    ![Selecione Adicionar utilizador](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** na lista **de Utilizadores** e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se espera um valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Configurar as definições de sincronização do utilizador e do grupo em Azure AD

1. No painel esquerdo, selecione **Azure Active Directory**.

1. Em **Manage**, selecione **registos de Aplicações** e, em seguida, selecione a sua nova aplicação de empresa em **Todas as aplicações**.

1. Em **Gerir**, selecione **Certificados e segredos**.

1. Na área de **segredos** do Cliente, selecione **Novo segredo de cliente.**

1. No **ecrã secreto do Cliente,** adicione opcionalmente uma descrição e selecione um período de validade para o segredo do cliente e, em seguida, selecione **Add**. O novo segredo do cliente aparece na área dos segredos do **Cliente.**

1. Grave o valor secreto do cliente. Mais tarde, vais introduzi-lo na TMWS.

1. Em **Gestão**, selecione **permissões API**. 

1. Na janela de permissões da **API,** selecione **Adicionar uma permissão**.

1. No separador **APIs** da Microsoft da janela de **permissões API do Pedido,** selecione **o Microsoft Graph** e, em seguida, **permissões de aplicação**.

1. Localize e adicione estas permissões: 

    * Group.Read.All
    * User.Read.All

1. **Selecione Permissões de adicionar**. Uma mensagem parece confirmar que as suas definições foram guardadas. As novas permissões aparecem na janela de permissões da **API.**

1. Na área de consentimento do **Grant,** selecione **o consentimento de administração do Grant para *a sua conta de administrador* (Predefinitivo)** e, em seguida, selecione **Sim**. Uma mensagem parece confirmar que o consentimento administrativo para as permissões solicitadas foi concedido.

1. Selecione **Descrição geral**. 

1. Grave o **ID de Aplicação (cliente)** e **iD do Diretório (inquilino)** que você vê no painel certo. Mais tarde, introduzirá essa informação na TMWS. Também pode selecionar **nomes de domínio personalizados** no **Azure Ative Directory > Gerir** e registar o nome de domínio que vê no painel direito.

## <a name="configure-tmws-sso"></a>Configurar TMWS SSO

Complete estes passos para configurar o SSO TMWS no lado da aplicação.

1. Inscreva-se na consola de gestão TMWS e vá aos UTILIZADORES **da Administração**  >  & Serviços de **Diretório de Autenticação.**  >  

1. Selecione **aqui** na área superior do ecrã.

1. Na página Método de **Autenticação,** selecione **Azure AD**.

1. Selecione **On** ou **Off** para configurar se permite que os utilizadores de AZure AD na sua organização visitem websites através do TMWS se os seus dados não forem sincronizados com o TMWS.

    > [!NOTE]
    > Os utilizadores que não estejam sincronizados a partir do Azure AD só podem ser autenticados através de gateways TMWS conhecidos ou da porta dedicada à sua organização.

1. Na secção **Definições do Fornecedor de Identidade,** complete estes passos:

    a. Na caixa URL de **serviço,** introduza o valor **URL de login** que copiou a partir do portal Azure.

    b. Na caixa **de atributos de nome de logon,** insira o **nome de reclamação** do Utilizador com o atributo de origem do **utilizador.onpremisessamaccountname** a partir do portal Azure.

    c. Na caixa **de certificados SSL Público,** utilize o Certificado descarregado **(Base64)** a partir do portal Azure.

1. Na secção **Definições de Sincronização,** complete estes passos:

    a. Na caixa do **Inquilino,** insira o **ID do Diretório (inquilino)** ou o valor do nome de **domínio personalizado** a partir do portal Azure.

    b. Na caixa **de ID de aplicação,** insira o valor de **ID de Aplicação (cliente)** a partir do portal Azure.

    c. Na caixa secreta do **Cliente,** insira o segredo do **Cliente** a partir do portal Azure.

    d. Selecione **o calendário de sincronização** para sincronizar com o AD Azure manualmente ou de acordo com um horário. Se selecionar **manualmente**, sempre que houver alterações nas informações do utilizador do Ative Directory, lembre-se de voltar à página **de Serviços de Diretório** e realizar a sincronização manual para que a informação no TMWS permaneça atual.

    e. Selecione **a Ligação** de Teste para verificar se o serviço AZure AD pode ser ligado com sucesso.
    
    f. Selecione **Guardar**.
 
 > [!NOTE]
 > Para obter mais informações sobre como configurar o TMWS com AD AZure, consulte [configurações AD Azure configurantes em TMWS](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx).

## <a name="test-sso"></a>Teste SSO 

Depois de configurar o serviço AZure AD e especificar a Azure AD como o método de autenticação do utilizador, pode iniciar sação no servidor de procuração TMWS para verificar a sua configuração. Depois de o Azure AD iniciar a sua conta, pode visitar a internet.

> [!NOTE]
> A TMWS não suporta o teste de um único sismo a partir do portal AD Azure, sob **visão geral**  >  **Único sinal de**  >  **inscrição única com** teste SAML  >   da sua nova aplicação da empresa.

1. Limpe o navegador de todos os cookies e, em seguida, reinicie o navegador. 

1. Aponte o seu navegador para o servidor de procuração TMWS. Para mais informações, consulte [o encaminhamento de tráfego utilizando ficheiros PAC](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B).

1. Visite qualquer site da Internet. A TMWS irá direcioná-lo para o portal cativo TMWS.

1. Especifique uma conta de Diretório Ativo (formato: \\ *domínio sAMAccountName* ou *domínio sAMAccountName),* endereço de @ e-mail ou UPN, e, em seguida, selecione **Iniciar Sessão**. A TMWS envia-o para a janela de inscrição AD Azure.

1. Na janela de entrada AD Azure, insira as credenciais da sua conta Azure AD. Deve agora ser inscrito na TMWS.

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais sobre como integrar apps SaaS com Diretório Ativo Azure](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)

- [Experimente trend Micro Web Security com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger a Trend Micro Web Security com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)