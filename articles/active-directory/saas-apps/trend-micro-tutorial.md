---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com trend micro web security (TMWS) [ Microsoft Docs'
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
ms.openlocfilehash: 5f4dc7223d64fd299da70375329260f7b4f8b322
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82083432"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-securitytmws"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com trend micro web security (TMWS)

Neste tutorial, você aprenderá a integrar a Trend Micro Web Security (TMWS) com o Azure Ative Directory (Azure AD). Quando integrar a Trend Micro Web Security (TMWS) com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à Trend Micro Web Security (TMWS).
* Ative que os seus utilizadores sejam automaticamente inscritos na Trend Micro Web Security (TMWS) com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada pela Trend Micro Web Security (TMWS) ativada por subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Trend Micro Web Security (TMWS) suporta **SP** iniciado SSO
* Assim que configurar a Trend Micro Web Security (TMWS), pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-trend-micro-web-securitytmws-from-the-gallery"></a>Adicionar tendência Micro Web Security (TMWS) da galeria

Para configurar a integração da Trend Micro Web Security (TMWS) em Azure AD, é necessário adicionar a Trend Micro Web Security (TMWS) da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **trend micro web security (TMWS)** na caixa de pesquisa.
1. Selecione **Trend Micro Web Security (TMWS)** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trend-micro-web-securitytmws"></a>Configurar e testar o único sinal de Acesso da Web da Trend Micro Web Security (TMWS)

Configure e teste Azure AD SSO com Trend Micro Web Security (TMWS) utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Trend Micro Web Security (TMWS).

Para configurar e testar o Azure AD SSO com a Trend Micro Web Security (TMWS), complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
    1. **[Configure as definições](#configure-user-and-group-synchronization-settings-in-azure-ad)** de sincronização do utilizador e do grupo em Definições de sincronização do utilizador e do grupo em Azure AD
1. **[Configurar a Tendência Micro Web Security (TMWS) SSO](#configure-trend-micro-web-security-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Trend Micro Web Security (TMWS),** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. Na caixa de texto **URL resposta,** escreva um URL:`https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > O valor do identificador não é real. Atualize este valor com o identificador real. Pode obter estes valores ao abrigo das Definições do Fornecedor de Serviços para a área do **Portal do Administrador Azure** no ecrã do método de **autenticação** para o Azure AD da **Administração > Serviços de Diretórios.**

1. A aplicação Trend Micro Web Security (TMWS) espera que as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Trend Micro Web Security (TMWS) espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Nome | Atributo fonte|
    | --------------- | --------- |
    | sAMAccountName | user.onpremisessamaccountname |
    | UPN | user.userprincipalname |

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **Configurar A Configuração Micro Web Security (TMWS),** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso à Trend Micro Web Security (TMWS).

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Trend Micro Web Security (TMWS)**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Configure as definições de sincronização do utilizador e do grupo em Azure AD

1. A partir da navegação à esquerda, clique no **Diretório Ativo Azure**.

1. Em **'Gerir',** clique nos **registos da App** e clique na sua nova aplicação empresarial na área de Todas as **aplicações.**

1. Under **Manage,** clique em **Certificados & segredos.**

1. Na área de segredos do Cliente que aparece, clique em **novo segredo de cliente.**

1. No Add um ecrã secreto do cliente que aparece, adicione opcionalmente uma descrição e selecione um período de validade para este segredo de cliente e, em seguida, clique em **Adicionar**. O segredo do cliente recentemente adicionado aparece sob a área de segredos do Cliente.

1. Grave o valor. Mais tarde, irá digitar a informação em TMWS.

1. Em **'Gerir',** clique em **permissões API**. 

1. No ecrã de permissões DaPi que aparece, clique **em Adicionar uma permissão**.

1. No separador APIs da Microsoft do ecrã de permissões Request API que aparece, clique no **Microsoft Graph** e, em seguida, **nas permissões**de Aplicação .

1. Localizar e adicionar as seguintes permissões: 

    * Group.Read.All
    * User.Read.All

1. Clique em **Adicionar permissões**. Uma mensagem parece confirmar que as suas definições foram guardadas com sucesso. As permissões recentemente adicionadas aparecem no ecrã de permissões DaPI.

1. Na área de consentimento do Grant, clique **no consentimento do Administrador Grant para < sua conta de administrador > (Diretório Padrão)** e, em **seguida, Sim**. Uma mensagem parece confirmar que o consentimento do administrador para as permissões solicitadas foi concedido com sucesso.

1. Clique em **Descrição geral**. 

1. No painel certo que aparece, grave o ID de Aplicação (cliente) e o ID do Diretório (inquilino). Mais tarde, irá digitar a informação em TMWS. Também pode clicar em nomes de **domínio personalizados** em **Diretório Ativo do** Azure > Gerir e gravar o nome de domínio no painel certo.

## <a name="configure-trend-micro-web-security-sso"></a>Configurar tendência Micro Web Security SSO

1. Assine na consola de gestão TMWS e vá aos utilizadores **da Administração** > & Serviços de**Diretório****de AUTENTICAÇÃO.** > 

1. Clique aqui na área superior do ecrã.

1. No ecrã método de autenticação que aparece, clique em **Azure AD**.

1. Clique **em On** ou **Off** para decidir se permite que os utilizadores de AD da sua organização visitem websites através da TMWS se os seus dados não estiverem sincronizados com tMWS.

    > [!NOTE]
    > Os utilizadores não sincronizados a partir do Azure AD só podem ser autenticados através de gateways TMWS conhecidos ou da porta dedicada para a sua organização.

1. Na secção Definições do Fornecedor de **Identidade,** execute os seguintes passos:

    a. No campo URL de **serviço,** colhe o valor **URL login,** que copiou do portal Azure

    b. No campo de atribuição de **nome Logon,** colhe o nome de reclamação do Utilizador com o **atributo** fonte de origem de conta do utilizador do portal Azure.

    c. No campo de **certificados SSL Público,** utilize o Certificado descarregado **(Base64)** do portal Azure.

1. Na secção Definições de **Sincronização,** execute os seguintes passos:

    a. No campo **do Arrendatário,** utilize o Id de **Diretório (inquilino)** ou o valor de nome de **domínio personalizado** do portal Azure.

    b. No campo de ID de **aplicação,** o valor de ID de **aplicação (cliente)** do portal Azure.

    c. No campo **secreto cliente,** use o segredo do **Cliente** do portal Azure.

    d. No campo de **sincronização,** selecione sincronizar manualmente com a AD Azure ou de acordo com um horário. Se escolher Manualmente, sempre que existam alterações nas informações do utilizador do Diretório Ativo, lembre-se de voltar ao ecrã dos Serviços de Diretório e efetuar sincronização manual para que a informação no TMWS permaneça atual.

    e. Clique em **Ligação** de Teste para verificar se o serviço Azure AD pode ser ligado com sucesso. 
    
    f. Clique em **Guardar**.
 
 > [!NOTE]
 > Para mais informações sobre como configurar a Trend Micro Web Security com o Azure AD, consulte [este](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx) documento.

## <a name="test-sso"></a>Teste SSO 

Uma vez configurado com sucesso o serviço Azure AD e especificado O AD Azure como o método de autenticação do utilizador, pode iniciar sessão no servidor proxy TMWS para verificar a sua configuração. Depois do logon da AD Azure verificar a sua conta, pode visitar a Internet.

> [!NOTE]
> A TMWS não suporta testar um único sinal do portal Azure AD, sob a visão geral > > set single sign-on com a SAML > Teste da sua nova aplicação empresarial.

1. Limpe o navegador de todos os cookies e, em seguida, reinicie o navegador. 

1. Aponte o seu navegador para o servidor proxy TMWS. Para mais detalhes, consulte [o Reencaminhamento de Tráfego utilizando ficheiros PAC](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B).

1. Visite qualquer site da Internet. A TMWS irá direcioná-lo para o portal cativo TMWS.

1. Especifique uma conta de Diretório Ativo sAMAccountName@domain(formato: domínio\sAMAccountName ou ), ou endereço de e-mail, ou UPN, e, em seguida, clique **em Iniciar Sessão**. A TMWS envia-o para o logon Azure AD.

1. No logon Azure AD, digite as credenciais da sua conta AD. Deverá iniciar sessão com sucesso na TMWS.

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a Trend Micro Web Security (TMWS) com a Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a Trend Micro Web Security (TMWS) com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

