---
title: 'Tutorial: Azure Ative Directory integração única de sign-on com Citrix ADC (autenticação baseada em Kerberos) | Microsoft Docs'
description: Saiba como configurar um único sign-on (SSO) entre o Azure Ative Directory e o Citrix ADC utilizando a autenticação baseada em Kerberos.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/15/2020
ms.author: jeedes
ms.openlocfilehash: 75d46edb332fb28132592e414e78bad64e75fef5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736444"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-adc-kerberos-based-authentication"></a>Tutorial: Azure Ative Directory integração única de sign-on com Citrix ADC (autenticação baseada em Kerberos)

Neste tutorial, você vai aprender a integrar citrix ADC com Azure Ative Direy (Azure AD). Quando integrar o Citrix ADC com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à Citrix ADC.
* Permita que os seus utilizadores sejam automaticamente inscritos na Citrix ADC com as suas contas AD AZure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Citrix ADC única assinatura ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. O tutorial inclui estes cenários:

* **Iniciado pela SP** SSO para Citrix ADC

* **Mesmo a tempo,** o fornecimento de utilizadores para a Citrix ADC

* [Autenticação baseada em Kerberos para Citrix ADC](#publish-the-web-server)

* [Autenticação baseada em cabeçalho para Citrix ADC](header-citrix-netscaler-tutorial.md#publish-the-web-server)


## <a name="add-citrix-adc-from-the-gallery"></a>Adicione Citrix ADC da galeria

Para integrar o Citrix ADC com a Azure AD, adicione primeiro a Citrix ADC à sua lista de aplicações geridas do SaaS da galeria:

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.

1. No menu esquerdo, selecione **Azure Ative Directory**.

1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.

1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.

1. Na secção Adicionar a partir da secção **da galeria,** **insira o Citrix ADC** na caixa de pesquisa.

1. Nos resultados, selecione **Citrix ADC** e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-citrix-adc"></a>Configurar e testar Azure AD SSO para citrix ADC

Configure e teste Azure AD SSO com Citrix ADC utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Citrix ADC.

Para configurar e testar o Azure AD SSO com a Citrix ADC, execute os seguintes passos:

1. [Configure Azure AD SSO](#configure-azure-ad-sso) - para permitir que os seus utilizadores utilizem esta funcionalidade.

    1. Crie um utilizador de [teste AD Azure](#create-an-azure-ad-test-user) - para testar Azure AD SSO com B.Simon.

    1. [Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user) - para permitir que a B.Simon utilize o Azure AD SSO.

1. [Configure citrix ADC SSO](#configure-citrix-adc-sso) - para configurar as definições SSO no lado da aplicação.

    * [Crie um utilizador de teste Citrix ADC](#create-a-citrix-adc-test-user) - para ter uma contraparte de B.Simon na Citrix ADC que está ligada à representação AD AZure do utilizador.

1. [Teste SSO](#test-sso) - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Para ativar o Azure AD SSO utilizando o portal Azure, complete estes passos:

1. No portal Azure, no painel de integração de aplicações **Citrix ADC,** em **Gestão**, selecione **Single sign-on**.

1. No painel **de método de inscrição,** selecione **SAML**.

1. Na **configuração single Sign-On com painel SAML,** selecione o ícone de **edição** de caneta para **configuração SAML básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** para configurar a aplicação no modo **iniciado pelo IDP:**

    1. Na caixa de texto **identifier,** introduza um URL com o seguinte padrão: `https://<Your FQDN>`

    1. Na caixa de texto **URL de resposta,** introduza um URL com o seguinte padrão: `http(s)://<Your FQDN>.of.vserver/cgi/samlauth`

1. Para configurar a aplicação no modo **iniciado pelo SP,** selecione **Definir URLs adicionais** e completar o seguinte passo:

    * Na caixa de texto **URL de entrada,** introduza um URL com o seguinte padrão: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * Os URLs que são usados nesta secção não são valores reais. Atualize estes valores com os valores reais para Identifier, URL de resposta e URL de inscrição. Contacte a equipa de apoio ao cliente da [Citrix ADC](https://www.citrix.com/contact/technical-support.html) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.
    > * Para criar SSO, os URLs devem estar acessíveis a partir de sites públicos. Deve ativar a firewall ou outras definições de segurança do lado Citrix ADC para escorrê-lo AD para colocar o token no URL configurado.

1. Na **configuração single Sign-On com painel SAML,** na secção certificado de assinatura **SAML,** para **url de metadados da Federação de Aplicações,** copie o URL e guarde-o no Bloco de Notas.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configuração Citrix ADC,** copie os URLs relevantes com base nos seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, cria-se um utilizador de teste no portal Azure chamado B.Simon.

1. No menu esquerdo do portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

1. Selecione **Novo utilizador** na parte superior do painel.

1. Nas propriedades **do Utilizador,** complete estes passos:

   1. Para **nome,** insira `B.Simon` .  

   1. Para **o nome de utilizador,** insira _username@companydomain.extension_ . Por exemplo, `B.Simon@contoso.com`.

   1. Selecione a caixa **de verificação de passwords Show** e, em seguida, escreva ou copie o valor que é apresentado na **Palavra-passe**.

   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, permite ao utilizador B.Simon utilizar o Azure SSO, concedendo ao utilizador acesso à Citrix ADC.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.

1. Na lista de candidaturas, selecione **Citrix ADC**.

1. Na visão geral da aplicação, em **Gestão,** selecione **Utilizadores e grupos**.
1. Selecione **Adicionar utilizador**. Em seguida, na caixa de diálogo **de atribuição de adicionar,** selecione **Utilizadores e grupos**.
1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista **de Utilizadores.** Escolha **Selecionar**.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

## <a name="configure-citrix-adc-sso"></a>Configuração Citrix ADC SSO

Selecione um link para etapas para o tipo de autenticação que pretende configurar:

- [Citrix ADC SSO de configuração para a autenticação baseada em Kerberos](#publish-the-web-server)

- [Citrix Citrix SSO para autenticação baseada em cabeçalho](header-citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>Publique o servidor web 

Para criar um servidor virtual:

1. Selecione   >  serviços de equilíbrio de carga **de gestão**  >  **de tráfego**.
    
1. Selecione **Adicionar**.

    ![Configuração Citrix ADC - Painel de serviços](./media/citrix-netscaler-tutorial/web01.png)

1. Desacorda os seguintes valores para o servidor web que está a executar as aplicações:

   * **Nome de serviço**
   * **Servidor IP/ Servidor Existente**
   * **Protocolo**
   * **Porta**

### <a name="configure-the-load-balancer"></a>Configure o balançador de carga

Para configurar o equilibrador de carga:

1. Vá para **a carga de gestão de tráfego**  >  **equilibrando**  >  **servidores virtuais**.

1. Selecione **Adicionar**.

1. Decreva os seguintes valores descritos na seguinte imagem:

    * **Nome**
    * **Protocolo**
    * **Endereço IP**
    * **Porta**

1. Selecione **OK**.

    ![Configuração Citrix ADC - Painel de Definições Básicas](./media/citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>Ligue o servidor virtual

Para ligar o balançador de carga ao servidor virtual:

1. No painel **de serviços e grupos de serviço,** selecione **No Load BalanceIng Virtual Server Service Binding**.

   ![Configuração Citrix ADC - Painel de ligação ao serviço do servidor virtual de equilíbrio de carga](./media/citrix-netscaler-tutorial/bind01.png)

1. Verifique as definições como mostrado na imagem a seguir e, em seguida, selecione **Fechar**.

   ![Configuração Citrix ADC - Verifique a ligação de serviços de servidor virtual](./media/citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Vincular o certificado

Para publicar este serviço como TLS, ligue o certificado do servidor e, em seguida, teste a sua aplicação:

1. Sob **o Certificado**, selecione No Server **Certificate**.

   ![Configuração Citrix ADC - Painel de certificado de servidor](./media/citrix-netscaler-tutorial/bind03.png)

1. Verifique as definições como mostrado na imagem a seguir e, em seguida, selecione **Fechar**.

   ![Configuração Citrix ADC - Verifique o certificado](./media/citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Perfil Citrix ADC SAML

Para configurar o perfil Citrix ADC SAML, complete as seguintes secções.

### <a name="create-an-authentication-policy"></a>Criar uma política de autenticação

Para criar uma política de autenticação:

1. Ir para **a Segurança** AAA – Políticas de  >    >  Autenticação de Políticas de  >  **Autenticação de Aplicações**  >  de Tráfego.

1. Selecione **Adicionar**.

1. No painel **'Criar Política de Autenticação',** introduza ou selecione os seguintes valores:

    * **Nome**: Introduza um nome para a sua política de autenticação.
    * **Ação**: Introduza **o SAML** e, em seguida, selecione **Adicionar**.
    * **Expressão**: Entrar **verdadeiro**.     
    
    ![Configuração Citrix ADC - Criar painel de política de autenticação](./media/citrix-netscaler-tutorial/policy01.png)

1. Selecione **Criar**.

### <a name="create-an-authentication-saml-server"></a>Criar um servidor SAML de autenticação

Para criar um servidor SAML de autenticação, aceda ao painel **do Servidor SAML de Autenticação** e, em seguida, complete os seguintes passos:

1. Para **nome**, insira um nome para o servidor SAML de autenticação.

1. Nos **metadados SAML de exportação:**

   1. Selecione a caixa **de verificação de metadados de importação.**

   1. Introduza o URL de metadados da federação a partir do UI Azure SAML que copiou anteriormente.
    
1. Para **nome do emitente,** insira o URL relevante.

1. Selecione **Criar**.

![Configuração Citrix ADC - Criar painel de servidor SAML de autenticação](./media/citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Criar um servidor virtual de autenticação

Para criar um servidor virtual de autenticação:

1.  Ir para **Security**  >  **AAA -**  >  **Aplicação Políticas de**  >  **Autenticação**  >  **Autenticação Servidores Virtuais**.

1.  **Selecione Adicionar** e, em seguida, complete os seguintes passos:

    1. Para **nome**, insira um nome para o servidor virtual de autenticação.

    1. Selecione a caixa de verificação **não endereçada.**

    1. Para **protocolo**, selecione **SSL**.

    1. Selecione **OK**.
    
1. Selecione **Continuar**.

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Configurar o servidor virtual de autenticação para usar o Azure AD

Modificar duas secções para o servidor virtual de autenticação:

1.  No painel **de Políticas de Autenticação Avançada,** selecione **No Authentication Policy**.

    ![Configuração Citrix ADC - Painel de Políticas avançadas de autenticação](./media/citrix-netscaler-tutorial/virtual01.png)

1. No painel **de ligação política,** selecione a política de autenticação e, em seguida, selecione **Bind**.

    ![Configuração Citrix ADC - Painel de ligação à política](./media/citrix-netscaler-tutorial/virtual02.png)

1. No painel de **servidores virtuais baseados em formulários,** selecione **No Load BalanceIng Virtual Server**.

    ![Configuração Citrix ADC - Painel de Servidores Virtuais Baseados em Formulários](./media/citrix-netscaler-tutorial/virtual03.png)

1. Para **autenticação FQDN,** introduza um nome de domínio totalmente qualificado (FQDN) (obrigatório).

1. Selecione o servidor virtual de equilíbrio de carga que pretende proteger com a autenticação AD do Azure.

1. Selecione **Bind**.

    ![Configuração Citrix ADC - Painel de ligação do servidor virtual de equilíbrio de carga](./media/citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Certifique-se de que **seleciona Feito** no painel de configuração do servidor virtual de **autenticação.**

1. Para verificar as suas alterações, num browser, aceda ao URL da aplicação. Você deveria ver a página de inscrição do seu inquilino em vez do acesso não autenticado que você teria visto anteriormente.

    ![Configuração Citrix ADC - Uma página de sent-in num navegador web](./media/citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-adc-sso-for-kerberos-based-authentication"></a>Citrix ADC SSO de configuração para a autenticação baseada em Kerberos

### <a name="create-a-kerberos-delegation-account-for-citrix-adc"></a>Criar uma conta de delegação Kerberos para a Citrix ADC

1. Crie uma conta de utilizador (neste exemplo, utilizamos _a AppDelegation)._

    ![Configuração Citrix ADC - Painel de propriedades](./media/citrix-netscaler-tutorial/kerberos01.png)

1. Crie um HOST SPN para esta conta. 

    Exemplo: `setspn -S HOST/AppDelegation.IDENTT.WORK identt\appdelegation`
    
    Neste exemplo:

    * `IDENTT.WORK` é o domínio FQDN.
    * `identt` é o nome netbios de domínio.
    * `appdelegation` é o nome da conta de utilizador da delegação.

1. Configure a delegação para o servidor web, tal como mostrado na seguinte imagem:
 
    ![Configuração Citrix ADC - Delegação sob painel de propriedades](./media/citrix-netscaler-tutorial/kerberos02.png)

    > [!NOTE]
    > No exemplo da imagem, o nome do servidor interno da web que executa o site de autenticação integrada do Windows (WIA) é _CWEB2_.

### <a name="citrix-adc-aaa-kcd-kerberos-delegation-accounts"></a>Citrix ADC AAA KCD (contas da delegação Kerberos)

Para configurar a conta Citrix ADC AAA KCD:

1.  Vá às contas **Citrix Gateway**  >  **AAA KCD (Delegação Restrita Kerberos).**

1.  **Selecione Adicionar** e, em seguida, insira ou selecione os seguintes valores:

    * **Nome**: Introduza um nome para a conta KCD.

    * **Reino**: Introduza o domínio e a extensão em maiúsculas.

    * **Serviço SPN**: `http/<host/fqdn>@<DOMAIN.COM>` .
    
        > [!NOTE]
        > `@DOMAIN.COM` é necessário e deve ser maiúscula. Exemplo: `http/cweb2@IDENTT.WORK`.

    * **Utilizador delegado**: Introduza o nome de utilizador delegado.

    * Selecione a **palavra-passe para** caixa de verificação do utilizador delegada e introduza e confirme uma palavra-passe.

1. Selecione **OK**.
 
    ![Configuração Citrix ADC - Painel de Conta KCD configurado](./media/citrix-netscaler-tutorial/kerberos03.png)

### <a name="citrix-traffic-policy-and-traffic-profile"></a>Política de tráfego citrix e perfil de tráfego

Para configurar a política de tráfego da Citrix e o perfil de tráfego:

1.  Ir para **a Segurança** AAA - Políticas de Tráfego  >  **de**  >  **Aplicações**  >  **Políticas de Tráfego, Perfis e Perfis SSO Políticas de Investigação**.

1.  Selecione **Perfis de Tráfego**.

1.  Selecione **Adicionar**.

1.  Para configurar um perfil de tráfego, insira ou selecione os seguintes valores.

    * **Nome**: Introduza um nome para o perfil de tráfego.

    * **Único Sinal de inscrição**: Selecione **ON**.

    * **Conta KCD**: Selecione a conta KCD que criou na secção anterior.

1. Selecione **OK**.

    ![Configuração Citrix ADC - Painel de Perfil de Tráfego Configurar](./media/citrix-netscaler-tutorial/kerberos04.png)
 
1.  Selecione **a Política de Tráfego**.

1.  Selecione **Adicionar**.

1.  Para configurar uma política de tráfego, insira ou selecione os seguintes valores:

    * **Nome**: Introduza um nome para a política de tráfego.

    * **Perfil**: Selecione o perfil de tráfego que criou na secção anterior.

    * **Expressão**: Entrar **verdadeiro**.

1. Selecione **OK**.

    ![Configuração Citrix ADC - Painel de Política de Tráfego Configurar](./media/citrix-netscaler-tutorial/kerberos05.png)

### <a name="bind-a-traffic-policy-to-a-virtual-server-in-citrix"></a>Ligue uma política de tráfego a um servidor virtual em Citrix

Para ligar uma política de tráfego a um servidor virtual utilizando o GUI:

1. Vá para **a carga de gestão de tráfego**  >  **equilibrando**  >  **servidores virtuais**.

1. Na lista de servidores virtuais, selecione o servidor virtual ao qual pretende vincular a política de reescrita e, em seguida, selecione **Open**.

1. No painel de **servidor virtual de equilíbrio de carga,** em **Definições Avançadas,** selecione **Políticas**. Todas as políticas configuradas para a sua instância NetScaler aparecem na lista.
 
    ![Configuração Citrix ADC - Painel de servidor virtual de equilíbrio de carga](./media/citrix-netscaler-tutorial/kerberos06.png)

    ![Configuração Citrix ADC - Caixa de diálogo de políticas](./media/citrix-netscaler-tutorial/kerberos07.png)

1.  Selecione a caixa de verificação ao lado do nome da apólice que pretende ligar a este servidor virtual.
 
    ![Configuração Citrix ADC - Painel de ligação à política de tráfego do servidor virtual de equilíbrio de balanço](./media/citrix-netscaler-tutorial/kerberos09.png)

1. Na caixa de diálogo **Escolha Tipo:**

    1. Para **Escolha Política**, selecione **Traffic**.

    1. Para **Escolher Tipo,** selecione **Pedido.**

    ![Configuração Citrix ADC - Escolha o painel tipo](./media/citrix-netscaler-tutorial/kerberos08.png)

1. Quando a apólice estiver vinculada, selecione **'Fazer' ( 'Fazer').**
 
    ![Configuração Citrix ADC - Painel de políticas](./media/citrix-netscaler-tutorial/kerberos10.png)

1. Teste a ligação utilizando o site da WIA.

    ![Configuração Citrix ADC - Uma página de teste em um navegador web](./media/citrix-netscaler-tutorial/kerberos11.png)    

### <a name="create-a-citrix-adc-test-user"></a>Criar um utilizador de teste Citrix ADC

Nesta secção, um utilizador chamado B.Simon é criado na Citrix ADC. A Citrix ADC suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nenhuma ação para tomar nesta secção. Se um utilizador já não existir no Citrix ADC, um novo é criado após a autenticação.

> [!NOTE]
> Se precisar de criar um utilizador manualmente, contacte a equipa de apoio ao [cliente da Citrix ADC.](https://www.citrix.com/contact/technical-support.html)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para citrix ADC Url de entrada de sinal onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição do Citrix ADC e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Citrix ADC nas Minhas Apps, este será redirecionado para o URL de assinatura do Citrix ADC. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado citrix ADC, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).