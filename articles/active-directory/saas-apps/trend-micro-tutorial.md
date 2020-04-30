---
title: 'Tutorial: Integração Azure AD SSO com trend micro web security (TMWS)'
description: Saiba como configurar um único sign-on entre o Diretório Ativo Azure e a Trend Micro Web Security (TMWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 827285d3-8e65-43cd-8453-baeda32ef174
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d09af492439beb931b4ab1cd08ccb9e1d82fc3f
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583145"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-security-tmws"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com trend micro web security (TMWS)

Neste tutorial, você aprenderá a integrar a Trend Micro Web Security (TMWS) com o Azure Ative Directory (Azure AD). Quando integrar o TMWS com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a TMWS.
* Ative que os seus utilizadores sejam automaticamente inscritos na TMWS com as suas contas Azure AD.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre a integração de aplicações saaS com a Azure AD, consulte [o single sign-on para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa do seguinte:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição TMWS que está ativada para sSO.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar Azure AD SSO em um ambiente de teste.

* A TMWS suporta o SSO iniciado por SP.
* Depois de configurar o TMWS, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. Para aprender a impor o controlo da sessão utilizando o Microsoft Cloud App Security, consulte [o Onboard e implemente](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)o Controlo de Aplicações de Acesso Condicional para qualquer aplicação .

## <a name="add-tmws-from-the-gallery"></a>Adicione TMWS da galeria

Para configurar a integração do TMWS em Azure AD, precisa adicionar TMWS da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione o serviço **de Diretório Ativo Azure.**
1. Selecione **aplicações Enterprise** e, em seguida, selecione **Todas as aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** introduza a **Trend Micro Web Security (TMWS)** na caixa de pesquisa.
1. Selecione **Trend Micro Web Security (TMWS)** nos resultados da pesquisa e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-tmws"></a>Configure e teste Azure AD SSO para TMWS

Irá configurar e testar O SSO Azure AD com TMWS utilizando um utilizador de teste chamado B.Simon. Para que o SSO funcione, é necessário estabelecer uma ligação entre um utilizador da AD Azure e o utilizador relacionado em TMWS.

Completará estes passos básicos para configurar e testar O SSO Azure AD com TMWS:

1. [Configure O SSO AD Azure](#configure-azure-ad-sso) para ativar a funcionalidade para os seus utilizadores.
    1. [Crie um utilizador da AD Azure](#create-an-azure-ad-test-user) para testar o único sinal de Entrada de Azure AD.
    1. [Conceda ao utilizador do teste Azure AD acesso](#grant-the-azure-ad-test-user-access-to-tmws) ao TMWS.
    1. [Configure as definições de sincronização do utilizador e do grupo em Azure AD](#configure-user-and-group-synchronization-settings-in-azure-ad).
1. [Configure o SSO TMWS](#configure-tmws-sso) no lado da aplicação.
1. [Teste SSO](#test-sso) para verificar a configuração.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Complete estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Trend Micro Web Security (TMWS),** na secção **Gerir,** selecione **um único sinal .**
1. Na página **de método de inscrição, selecione** **SAML**.
1. Na **configuração do "Single Sign-On" com** a página SAML, selecione o botão de caneta para **configuração SAML básica** para editar as definições:

   ![Editar as definições básicas de configuração do SAML](common/edit-urls.png)

1. Na secção **De Configuração SAML Básica,** introduza valores nas seguintes caixas:

    a. Na caixa **Identifier (Id** da Entidade), introduza um URL no seguinte padrão:

    `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. Na caixa **DEURL resposta,** introduza este URL:

    `https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > O valor do identificador no passo anterior não é o valor que deve introduzir. Tens de usar o identificador real. Pode obter este valor nas Definições do Fornecedor de Serviços para a secção **Portal De Administração Azure** na página método de **autenticação** para AD Azure da **Administração > Serviços de Diretório.**

1. A TMWS espera as afirmações do SAML num formato específico, pelo que precisa adicionar mapeamentos personalizados de atributos à configuração de atributos de token SAML. Esta imagem mostra os atributos padrão:

    ![Atributos padrão](common/default-attributes.png)

1. Além dos atributos na imagem anterior, a TMWS espera que mais dois atributos sejam retransmitidos na resposta SAML. Estes atributos são mostrados na tabela seguinte. Os atributos são pré-povoados, mas pode mudá-los para satisfazer os seus requisitos.
    
    | Nome | Atributo de origem|
    | --------------- | --------- |
    | sAMAccountName | user.onpremisessamaccountname |
    | UPN | user.userprincipalname |

1. No **set set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** encontre **certificado (Base64)**. Selecione o link **Descarregamento** ao lado deste nome de certificado para descarregar o certificado e guardá-lo no seu computador:

    ![Link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar a Trend Micro Web Security (TMWS),** copie os URL ou URLs apropriados, com base nos seus requisitos:

    ![Copiar os URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste chamado B.Simon no portal Azure.

1. No painel esquerdo do portal Azure, selecione **Azure Ative Directory**. Selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. Na caixa **nome,** introduza `B.Simon`.  
   1. Na caixa de **nome do utilizador,** introduza o domínio da ***empresa username*@**.* extensão***. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione **Mostrar palavra-passe**e, em seguida, anote o valor que está apresentado na caixa **password.**
   1. Selecione **Criar**.

### <a name="grant-the-azure-ad-test-user-access-to-tmws"></a>Conceder ao utilizador de teste Azure AD acesso ao TMWS

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso à TMWS.

1. No portal Azure, selecione **aplicações Enterprise**, e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Trend Micro Web Security (TMWS)**.
1. Na página geral da aplicação, na secção **Gerir,** selecione **Utilizadores e grupos:**

   ![Selecionar Utilizadores e grupos](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Atribuição adicionar'.**

    ![Selecione Adicionar utilizador](common/add-assign-user.png)

1. Na caixa de diálogo **De utilizadores e grupos,** selecione **B.Simon** na lista **de Utilizadores** e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se esperar um valor de função na afirmação do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Configure as definições de sincronização do utilizador e do grupo em Azure AD

1. No painel esquerdo, selecione **Azure Active Directory**.

1. Em **'Gerir ',** selecione **registos de Aplicações'** e, em seguida, selecione a sua nova aplicação empresarial em **todas as aplicações**.

1. Under **Manage**, selecione **Certificados & segredos.**

1. Na área dos **segredos** do Cliente, selecione **novo segredo de cliente.**

1. No **ecrã secreto adicionar um cliente,** adicione opcionalmente uma descrição e selecione um período de validade para o segredo do cliente e, em seguida, selecione **Adicionar**. O novo segredo do cliente aparece na área dos segredos do **Cliente.**

1. Grave o valor secreto do cliente. Mais tarde, vai sintetrá-lo na TMWS.

1. Em **Gerir,** selecione **permissões API**. 

1. Na janela de **permissões DaPI,** selecione **Adicionar uma permissão**.

1. No separador **APIs** da Microsoft da janela de **permissões Request API,** selecione **Microsoft Graph** e, em seguida, aplicações de **aplicação**.

1. Localize e adicione estas permissões: 

    * Group.Read.All
    * User.Read.All

1. **Selecione Adicionar permissões**. Uma mensagem parece confirmar que as suas definições foram guardadas. As novas permissões aparecem na janela de **permissões da API.**

1. Na área de consentimento do **Grant,** selecione o consentimento do administrador grant para a **sua conta de *administrador* (Diretório padrão)** e, em seguida, selecione **Sim**. Uma mensagem parece confirmar que o consentimento do administrador para as permissões solicitadas foi concedido.

1. Selecione **Descrição geral**. 

1. Grave o ID de **Aplicação (cliente)** e **o ID de Diretório (inquilino)** que vê no painel certo. Mais tarde, vai introduzir essa informação na TMWS. Também pode selecionar nomes de **domínio personalizados** sob **o Diretório Ativo do Azure > Gerir** e gravar o nome de domínio que vê no painel certo.

## <a name="configure-tmws-sso"></a>Configure TMWS SSO

Complete estes passos para configurar o SSO TMWS no lado da aplicação.

1. Inscreva-se na consola de gestão TMWS e vá aos UTILIZADORES **da Administração** > & Serviços de**Diretório****de AUTENTICAÇÃO.** > 

1. Selecione **aqui** na área superior do ecrã.

1. Na página método de **autenticação,** **selecione Azure AD**.

1. Selecione **On** ou **Off** para configurar se permite que utilizadores de AD Azure na sua organização visitem websites através da TMWS se os seus dados não estiverem sincronizados com tMWS.

    > [!NOTE]
    > Os utilizadores que não estejam sincronizados a partir do Azure AD só podem ser autenticados através de gateways Conhecidos da TMWS ou da porta dedicada à sua organização.

1. Na secção Definições do Fornecedor de **Identidade,** preencha estes passos:

    a. Na caixa URL de **serviço,** introduza o valor URL de **Login** que copiou do portal Azure.

    b. Na caixa de atribuição de **nome Logon,** introduza o nome de **reclamação** do Utilizador com o atributo fonte de origem de conta do **utilizador** do portal Azure.

    c. Na caixa de **certificados SSL Pública,** utilize o Certificado descarregado **(Base64)** do portal Azure.

1. Na secção Definições de **Sincronização,** preencha estes passos:

    a. Na caixa **do Arrendatário,** insira o **Id de Diretório (inquilino)** ou o valor de nome de **domínio personalizado** do portal Azure.

    b. Na caixa de ID de **aplicação,** insira o valor de ID da **Aplicação (cliente)** do portal Azure.

    c. Na caixa **secreta do Cliente,** insira o segredo do **Cliente** a partir do portal Azure.

    d. Selecione **o calendário de sincronização** para sincronizar manualmente com a AD Azure ou de acordo com um horário. Se selecionar **Manualmente,** sempre que existam alterações nas informações do utilizador do Diretório Ativo, lembre-se de voltar à página de Serviços de **Diretório** e efetuar sincronização manual para que a informação em TMWS permaneça atual.

    e. Selecione **Ligação** de Teste para verificar se o serviço Azure AD pode ser ligado com sucesso.
    
    f. Selecione **Guardar**.
 
 > [!NOTE]
 > Para obter mais informações sobre como configurar o TMWS com a AD Azure, consulte [configurar as definições de AD Azure em TMWS](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx).

## <a name="test-sso"></a>Teste SSO 

Depois de configurar o serviço Azure AD e especificar o Azure AD como método de autenticação do utilizador, pode iniciar sessão no servidor proxy TMWS para verificar a sua configuração. Depois do sign-in Azure AD verificar a sua conta, pode visitar a internet.

> [!NOTE]
> A TMWS não suporta testar um único sinal do portal Azure AD, no âmbito do**Up-on** > set Single Set-on **de Visão Geral** > com o**Teste** **SAML** > da sua nova aplicação empresarial.

1. Limpe o navegador de todos os cookies e, em seguida, reinicie o navegador. 

1. Aponte o seu navegador para o servidor proxy TMWS. Para mais detalhes, consulte [o Reencaminhamento de Tráfego utilizando ficheiros PAC](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B).

1. Visite qualquer site da Internet. A TMWS irá direcioná-lo para o portal cativo TMWS.

1. Especifique uma conta de Diretório Ativo (formato: *domínio*\\*sAMAccountName* ou*domínio* *sAMAccountName*@), endereço de e-mail ou UPN, e, em seguida, selecione **Iniciar sessão**. A TMWS envia-o para a janela de entrada da AD Azure.

1. Na janela de entrada de adato Azure, introduza as credenciais da sua conta Azure AD. Agora devias ser inscrito na TMWS.

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais sobre como integrar aplicações saaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a Trend Micro Web Security com a Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a Trend Micro Web Security com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

