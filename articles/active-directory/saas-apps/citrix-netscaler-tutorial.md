---
title: 'Tutorial: Azure Ative Directory integração individual de assinatura com citrix NetScaler (autenticação baseada em Kerberos) [ Microsoft Docs'
description: Saiba como configurar um único sign-on (SSO) entre o Azure Ative Directory e o Citrix NetScaler utilizando a autenticação baseada em Kerberos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 785242a2cf51571a6d13b2b4691d33e46369bf94
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75977916"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-netscaler-kerberos-based-authentication"></a>Tutorial: Azure Ative Directory integração individual de assinatura com citrix NetScaler (autenticação baseada em Kerberos)

Neste tutorial, você vai aprender a integrar citrix NetScaler com o Azure Ative Directory (Azure AD). Quando integrar o Citrix NetScaler com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a Citrix NetScaler.
* Ative que os seus utilizadores sejam automaticamente inscritos no Citrix NetScaler com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre software como uma integração de aplicações de serviço (SaaS) com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Citrix NetScaler única subscrição ativada por Citrix NetScaler (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. O tutorial inclui estes cenários:

* **SP iniciado** SSO para Citrix NetScaler

* **Mesmo a tempo,** o fornecimento de utilizadores para citrix NetScaler

* [Autenticação baseada em Kerberos para Citrix NetScaler](#publish-the-web-server)

* [Autenticação baseada em cabeçalho para Citrix NetScaler](header-citrix-netscaler-tutorial.md#publish-the-web-server)

## <a name="add-citrix-netscaler-from-the-gallery"></a>Adicione Citrix NetScaler da galeria

Para integrar o Citrix NetScaler com o Azure AD, adicione primeiro o Citrix NetScaler à sua lista de aplicações saaS geridas a partir da galeria:

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.

1. No menu esquerdo, selecione **Azure Ative Directory**.

1. Vá a **Aplicações Empresariais**e, em seguida, selecione **Todas as Aplicações**.

1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.

1. No **Add da** secção galeria, introduza **citrix NetScaler** na caixa de pesquisa.

1. Nos resultados, selecione **Citrix NetScaler,** e adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Configure e teste Azure AD único sign-on para Citrix NetScaler

Configure e teste Azure AD SSO com Citrix NetScaler utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Citrix NetScaler.

Para configurar e testar o Azure AD SSO com o Citrix NetScaler, complete os seguintes blocos de construção:

1. [Configure Azure AD SSO](#configure-azure-ad-sso) - para permitir que os seus utilizadores utilizem esta funcionalidade.

    1. [Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user) - para testar o Azure AD SSO com B.Simon.

    1. Atribuir o utilizador de [teste Azure AD](#assign-the-azure-ad-test-user) - para permitir que b.Simon utilize O SSO Azure AD.

1. [Configure Citrix NetScaler SSO](#configure-citrix-netscaler-sso) - para configurar as definições SSO no lado da aplicação.

    * [Crie um utilizador de teste Citrix NetScaler](#create-a-citrix-netscaler-test-user) - para ter uma contraparte de B.Simon no Citrix NetScaler que esteja ligada à representação da AD Azure do utilizador.

1. [Teste SSO](#test-sso) - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Para ativar o Azure AD SSO utilizando o portal Azure, complete estes passos:

1. No [portal Azure](https://portal.azure.com/), no painel de integração de aplicações **Citrix NetScaler,** em **Manage**, selecione **single sign-on**.

1. No select um único painel de método de **sinalização,** selecione **SAML**.

1. No **set up Single Sign-On com painel SAML,** selecione o ícone **de edição** de canetas para **configuração SAML básica** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **de Configuração SAML Básica,** para configurar a aplicação no modo **iniciado pelo IDP:**

    1. Na caixa de texto **identificador,** introduza um URL que tenha o seguinte padrão:`https://<Your FQDN>`

    1. Na caixa de texto **URL resposta,** introduza um URL que tenha o seguinte padrão:`https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

1. Para configurar a aplicação no modo **iniciado por SP,** selecione **Definir URLs adicionais** e completar o seguinte passo:

    * Na caixa de texto **de URL sign-on,** introduza um URL que tenha o seguinte padrão:`https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * Os URLs que são usados nesta secção não são valores reais. Atualize estes valores com os valores reais para Identifier, URL de resposta e URL de sign-on. Contacte a equipa de suporte ao [cliente Citrix NetScaler](https://www.citrix.com/contact/technical-support.html) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.
    > * Para configurar o SSO, os URLs devem estar acessíveis a partir de sites públicos. Tem de ativar a firewall ou outras definições de segurança do lado Citrix NetScaler para envolver o Azure AD para colocar o símbolo no URL configurado.

1. No **set set Single Sign-On com painel SAML,** na secção certificado de **assinatura SAML,** para Url de Metadados da Federação de **Aplicações,** copie o URL e guarde-o no Bloco de Notas.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **Configurar Citrix NetScaler,** copie os URLs relevantes com base nos seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, cria-se um utilizador de teste no portal Azure chamado B.Simon.

1. No menu esquerdo do portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

1. Selecione **Novo utilizador** na parte superior do painel.

1. Nas propriedades **do Utilizador,** preencha estes passos:

   1. Para **Nome,** insira `B.Simon`.  

   1. Para o nome _username@companydomain.extension_do **utilizador,** insira . Por exemplo, `B.Simon@contoso.com`.

   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva ou copie o valor que está apresentado na **Palavra-passe**.

   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite ao utilizador B.Simon utilizar o Azure SSO, concedendo ao utilizador acesso ao Citrix NetScaler.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.

1. Na lista de aplicações, selecione **Citrix NetScaler**.

1. Na visão geral da aplicação, em **'Gerir',** selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Em seguida, na caixa de diálogo **adicionar atribuição,** selecione **Utilizadores e grupos**.

    ![O link adicionar do utilizador](common/add-assign-user.png)

1. Na caixa de diálogo **De Utilizadores e grupos,** selecione **B.Simon** da lista **de Utilizadores.** Escolha **Selecionar**.

1. Se estiver à espera de algum valor de papel na afirmação do SAML, na caixa de diálogo **Select Role,** selecione a função relevante para o utilizador da lista e, em seguida, escolha **Select**.

1. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

## <a name="configure-citrix-netscaler-sso"></a>Configure Citrix NetScaler SSO

Selecione um link para passos para o tipo de autenticação que pretende configurar:

- [Configure Citrix NetScaler SSO para autenticação baseada em Kerberos](#publish-the-web-server)

- [Configure Citrix NetScaler SSO para autenticação baseada em cabeçalho](header-citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>Publicar o servidor web 

Para criar um servidor virtual:

1. Selecione**Serviços**de**Equilíbrio** > de Carga **de Gestão** > de Tráfego .
    
1. Selecione **Adicionar**.

    ![Configuração Citrix NetScaler - Painel de serviços](./media/citrix-netscaler-tutorial/web01.png)

1. Detete os seguintes valores para o servidor web que está a executar as aplicações:

   * **Nome do Serviço**
   * **Servidor IP/ Servidor Existente**
   * **Protocolo**
   * **Porto**

### <a name="configure-the-load-balancer"></a>Configure o equilibrador de carga

Para configurar o equilibrador de carga:

1. Vá para a carga de **gestão** > de tráfego**equilibrando** > **servidores virtuais**.

1. Selecione **Adicionar**.

1. Desdefinir os seguintes valores descritos na seguinte imagem:

    * **Nome**
    * **Protocolo**
    * **Endereço IP**
    * **Porto**

1. Selecione **OK**.

    ![Configuração Citrix NetScaler - Painel de Definições Básicas](./media/citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>Ligue o servidor virtual

Para ligar o equilibrador de carga ao servidor virtual:

1. No painel **de Serviços e Grupos de Serviços,** selecione **No Load Balanceing Virtual Server Service Binding**.

   ![Configuração Citrix NetScaler - Painel de ligação de serviço de servidor virtual de equilíbrio de carga](./media/citrix-netscaler-tutorial/bind01.png)

1. Verifique as definições como mostrado na seguinte imagem e, em seguida, selecione **Fechar**.

   ![Configuração Citrix NetScaler - Verifique a ligação dos serviços de servidores virtuais](./media/citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Ligue o certificado

Para publicar este serviço como SSL, ligue o certificado do servidor e, em seguida, teste a sua aplicação:

1. Em **Certificado,** selecione **No Server Certificate**.

   ![Configuração Citrix NetScaler - Painel de Certificado de Servidor](./media/citrix-netscaler-tutorial/bind03.png)

1. Verifique as definições como mostrado na seguinte imagem e, em seguida, selecione **Fechar**.

   ![Configuração Citrix NetScaler - Verifique o certificado](./media/citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Perfil Citrix ADC SAML

Para configurar o perfil Citrix ADC SAML, complete as seguintes secções.

### <a name="create-an-authentication-policy"></a>Criar uma política de autenticação

Para criar uma política de autenticação:

1. Ir para **Segurança** > **AAA –** > Políticas de**Autenticação** > **Authentication Policies**de**Políticas** > de Tráfego de Aplicação.

1. Selecione **Adicionar**.

1. No painel **"Criar Política de Autenticação",** introduza ou selecione os seguintes valores:

    * **Nome**: Introduza um nome para a sua política de autenticação.
    * **Ação**: Introduza **o SAML**e, em seguida, selecione **Adicionar**.
    * **Expressão**: Enter **true**.     
    
    ![Configuração Citrix NetScaler - Criar painel de política de autenticação](./media/citrix-netscaler-tutorial/policy01.png)

1. Selecione **Criar**.

### <a name="create-an-authentication-saml-server"></a>Criar um servidor SAML de autenticação

Para criar um servidor SAML de autenticação, vá ao painel **do Servidor Create Authentication SAML** e, em seguida, complete os seguintes passos:

1. Para **Nome**, introduza um nome para o servidor SAML de autenticação.

1. Em **dados SAML exportados:**

   1. Selecione a caixa de verificação **de metadados de importação.**

   1. Introduza o URL de metadados da federação do Azure SAML UI que copiou anteriormente.
    
1. Para **nome emitente,** introduza o URL relevante.

1. Selecione **Criar**.

![Configuração Citrix NetScaler - Crie a autenticação do painel do servidor SAML](./media/citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Criar um servidor virtual de autenticação

Para criar um servidor virtual de autenticação:

1.  Ir para **Segurança** > **AAA - Aplicação** > **Políticas** > de Tráfego**Autenticação** > **Servidores Virtuais**.

1.  Selecione **Adicionar**e, em seguida, completar os seguintes passos:

    1. Para **Nome**, introduza um nome para o servidor virtual de autenticação.

    1. Selecione a caixa de verificação **não endereçada.**

    1. Para **protocolo,** selecione **SSL**.

    1. Selecione **OK**.
    
1. Selecione **Continuar**.

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Configure o servidor virtual de autenticação para utilizar o Azure AD

Modificar duas secções para o servidor virtual de autenticação:

1.  No painel de políticas de **autenticação avançada,** selecione **Nenhuma Política de Autenticação**.

    ![Configuração Citrix NetScaler - Painel de Políticas de Autenticação Avançada](./media/citrix-netscaler-tutorial/virtual01.png)

1. No painel de **ligação política,** selecione a política de autenticação e, em seguida, selecione **Bind**.

    ![Configuração Citrix NetScaler - Painel de Ligação de Políticas](./media/citrix-netscaler-tutorial/virtual02.png)

1. No painel **de servidores virtuais baseados em formulários,** selecione **Sem servidor virtual de equilíbrio de carga**.

    ![Configuração Citrix NetScaler - Painel de servidores virtuais baseados em formulários](./media/citrix-netscaler-tutorial/virtual03.png)

1. Para **autenticação FQDN,** introduza um nome de domínio totalmente qualificado (FQDN) (obrigatório).

1. Selecione o servidor virtual de equilíbrio de carga que pretende proteger com a autenticação Azure AD.

1. Selecione **Bind**.

    ![Configuração Citrix NetScaler - Painel de ligação do servidor virtual de equilíbrio de carga](./media/citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Certifique-se de selecionar **Feito** no painel de configuração do **servidor virtual de autenticação.**

1. Para verificar as suas alterações, num browser, vá ao URL da aplicação. Você deve ver a sua página de inscrição do seu inquilino em vez do acesso não autenticado que você teria visto anteriormente.

    ![Configuração Citrix NetScaler - Uma página de inscrição num navegador web](./media/citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-sso-for-kerberos-based-authentication"></a>Configure Citrix NetScaler SSO para autenticação baseada em Kerberos

### <a name="create-a-kerberos-delegation-account-for-citrix-adc"></a>Criar uma conta de delegação kerberos para a Citrix ADC

1. Criar uma conta de utilizador (neste exemplo, utilizamos _a AppDelegation)._

    ![Configuração Citrix NetScaler - Painel de propriedades](./media/citrix-netscaler-tutorial/kerberos01.png)

1. Criar um HOST SPN para esta conta. 

    Exemplo: `setspn -S HOST/AppDelegation.IDENTT.WORK identt\appdelegation`
    
    Neste exemplo:

    * `IDENTT.WORK`é o domínio FQDN.
    * `identt`é o nome de domínio NetBIOS.
    * `appdelegation`é o nome da conta de utilizador da delegação.

1. Configure a delegação para o servidor web, como mostrado na seguinte imagem:
 
    ![Configuração Citrix NetScaler - Delegação no painel Properties](./media/citrix-netscaler-tutorial/kerberos02.png)

    > [!NOTE]
    > No exemplo da imagem, o nome do servidor interno que executa o site de autenticação integrada do Windows (WIA) é _CWEB2_.

### <a name="citrix-netscaler-aaa-kcd-kerberos-delegation-accounts"></a>Citrix NetScaler AAA KCD (contas da delegação kerberos)

Para configurar a conta Citrix NetScaler AAA KCD:

1.  Vá a Contas **Citrix Gateway** > **AAA KCD (Delegação Limitada Kerberos)** contas .

1.  Selecione **Adicionar**e, em seguida, introduzir ou selecionar os seguintes valores:

    * **Nome**: Insira um nome para a conta KCD.

    * **Reino**: Introduza o domínio e a extensão em maiúsculas.

    * **Serviço SPN:** `http/<host/fqdn>@<DOMAIN.COM>`.
    
        > [!NOTE]
        > `@DOMAIN.COM`é necessário e deve ser maiúsculo. Exemplo: `http/cweb2@IDENTT.WORK`.

    * **Utilizador delegado**: Introduza o nome de utilizador delegado.

    * Selecione a **palavra-passe para** a caixa de verificação de utilizador delegado e introduza e confirme uma palavra-passe.

1. Selecione **OK**.
 
    ![Configuração Citrix NetScaler - Painel de conta KCD configure](./media/citrix-netscaler-tutorial/kerberos03.png)

### <a name="citrix-traffic-policy-and-traffic-profile"></a>Política de tráfego citrix e perfil de tráfego

Para configurar a política de tráfego citrix e o perfil de tráfego:

1.  Ir para **Segurança** > **AAA - Políticas** > **Policies** > de tráfego de aplicação**Políticas de tráfego, perfis e formulário SSO Perfis Políticas**de tráfego .

1.  Selecione **Perfis de Tráfego**.

1.  Selecione **Adicionar**.

1.  Para configurar um perfil de tráfego, introduza ou selecione os seguintes valores.

    * **Nome**: Introduza um nome para o perfil de tráfego.

    * **Sinal único**: Selecione **ON**.

    * **Conta KCD**: Selecione a conta KCD criada na secção anterior.

1. Selecione **OK**.

    ![Configuração Citrix NetScaler - Configure Painel de perfil de tráfego](./media/citrix-netscaler-tutorial/kerberos04.png)
 
1.  Selecione **Política de Tráfego**.

1.  Selecione **Adicionar**.

1.  Para configurar uma política de tráfego, insira ou selecione os seguintes valores:

    * **Nome**: Introduza um nome para a política de tráfego.

    * **Perfil**: Selecione o perfil de tráfego que criou na secção anterior.

    * **Expressão**: Enter **true**.

1. Selecione **OK**.

    ![Configuração Citrix NetScaler - Configure Traffic Policy pane](./media/citrix-netscaler-tutorial/kerberos05.png)

### <a name="bind-a-traffic-policy-to-a-virtual-server-in-citrix"></a>Ligue uma política de tráfego a um servidor virtual em Citrix

Para ligar uma política de tráfego a um servidor virtual utilizando o GUI:

1. Vá para a carga de **gestão** > de tráfego**equilibrando** > **servidores virtuais**.

1. Na lista de servidores virtuais, selecione o servidor virtual ao qual pretende ligar a política de reescrita e, em seguida, selecione **Open**.

1. No painel do **servidor virtual de equilíbrio de carga,** em **Definições Avançadas,** selecione **Políticas**. Todas as políticas configuradas para a sua instância NetScaler aparecem na lista.
 
    ![Configuração Citrix NetScaler - Painel de servidor virtual de equilíbrio de carga](./media/citrix-netscaler-tutorial/kerberos06.png)

    ![Configuração Citrix NetScaler - Caixa de diálogo de políticas](./media/citrix-netscaler-tutorial/kerberos07.png)

1.  Selecione a caixa de verificação ao lado do nome da apólice que pretende ligar a este servidor virtual.
 
    ![Configuração Citrix NetScaler - Painel de ligação da política de tráfego virtual do servidor de equilíbrio de carga](./media/citrix-netscaler-tutorial/kerberos09.png)

1. Na caixa de diálogo **Tipo Escolha:**

    1. Para **escolher a política,** selecione **Tráfego**.

    1. Para **escolher O Tipo,** selecione **Pedido**.

    ![Configuração Citrix NetScaler - Escolha o painel tipo](./media/citrix-netscaler-tutorial/kerberos08.png)

1. Quando a apólice estiver vinculada, selecione **Done**.
 
    ![Configuração Citrix NetScaler - Painel de políticas](./media/citrix-netscaler-tutorial/kerberos10.png)

1. Teste a encadernação utilizando o site da WIA.

    ![Configuração Citrix NetScaler - Uma página de teste num navegador web](./media/citrix-netscaler-tutorial/kerberos11.png)    

### <a name="create-a-citrix-netscaler-test-user"></a>Criar um utilizador de teste Citrix NetScaler

Nesta secção, um utilizador chamado B.Simon é criado no Citrix NetScaler. A Citrix NetScaler suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há ação que tome nesta secção. Se um utilizador ainda não existir no Citrix NetScaler, um novo é criado após a autenticação.

> [!NOTE]
> Se precisar de criar um utilizador manualmente, contacte a equipa de suporte ao [cliente Citrix NetScaler](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração Azure AD SSO utilizando o Painel de Acesso.

Quando selecionar o azulejo Citrix NetScaler no Painel de Acesso, deve ser automaticamente inscrito no Citrix NetScaler para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações do SaaS com diretório ativo azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente citrix NetScaler com Azure AD](https://aad.portal.azure.com/)

- [Configure Citrix NetScaler único sinal para autenticação baseada em cabeçalho](header-citrix-netscaler-tutorial.md)
