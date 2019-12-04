---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com F5 | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 39382eab-05fe-4dc2-8792-62d742dfb4e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1ec0dd844dea8cf98621130d6a19b415bda1cf0
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786491"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com F5

Neste tutorial, você aprenderá a integrar o F5 ao Azure Active Directory (Azure AD). Ao integrar o F5 ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao F5.
* Habilite seus usuários a serem automaticamente conectados ao F5 com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).

* F5 assinatura habilitada para SSO (logon único).

* A implantação da solução conjunta requer a seguinte licença:
    * F5 BIG-IP® melhor pacote (ou)

    * F5 BIG-IP Access Policy Manager™ (APM) licença autônoma

    * A licença de complemento do™ Gerenciador de políticas de acesso (APM) do F5 BIG-IP em um BIG-IP F5 existente,® o Gerenciador de tráfego local™ (LTM).

    * Além da licença acima, o sistema F5 também pode ser licenciado com:

        * Uma assinatura de filtragem de URL para usar o banco de dados de categoria de URL

        * Uma assinatura do F5 IP Intelligence para detectar e bloquear invasores conhecidos e tráfego mal-intencionado

        * Um HSM (módulo de segurança de hardware) de rede para proteger e gerenciar chaves digitais para autenticação forte

* O sistema de BIG-IP F5 é provisionado com módulos do APM (LTM é opcional)

* Embora seja opcional, é altamente recomendável implantar os sistemas F5 em um [grupo de dispositivos de sincronização/failover](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG), que inclui o par de espera ativo, com um endereço IP flutuante para ha (alta disponibilidade). A redundância de interface adicional pode ser obtida usando o LACP (protocolo de controle de agregação de link). O LACP gerencia as interfaces físicas conectadas como uma única interface virtual (grupo de agregação) e detecta quaisquer falhas de interface dentro do grupo.

* Para aplicativos Kerberos, uma conta de serviço do AD local para delegação restrita.  Consulte a [documentação do F5](https://support.f5.com/csp/article/K43063049) para criar uma conta de delegação do AD.

## <a name="access-guided-configuration"></a>Configuração guiada de acesso

* O acesso guiado à configuração ' tem suporte em F5 TMOS versão 13.1.0.8 e superior. Se o seu sistema BIG-IP estiver executando uma versão abaixo de 13.1.0.8, consulte a seção **Configuração avançada** .

* A configuração interativa de acesso apresenta uma experiência de usuário totalmente nova e simplificada. Essa arquitetura baseada em fluxo de trabalho fornece etapas de configuração reentrantes e intuitivas adaptadas para a topologia selecionada.

* Antes de prosseguir para a configuração, atualize a configuração guiada baixando o pacote de caso de uso mais recente de [downloads.F5.com](https://login.f5.com/resource/login.jsp?ctx=719748). Para atualizar, siga o procedimento abaixo.

    >[!NOTE]
    >As capturas de tela abaixo são para a versão mais recente lançada (BIG-IP 15,0 com AGC versão 5,0). As etapas de configuração a seguir são válidas para esse caso de uso em 13.1.0.8 para a versão BIG-IP mais recente.

1. Na interface do usuário da Web do F5 BIG-IP, clique em **acesso > > guia Configuração**.

2. Na página **configuração guiada** , clique em **Atualizar configuração guiada** no canto superior esquerdo.

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure14.png) 

3. Na tela pop de configuração do guia de atualização, selecione **escolher arquivo** para carregar o pacote de caso de uso baixado e clique no botão **carregar e instalar** .

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure15.png) 

4. Quando a atualização for concluída, clique no botão **continuar** .

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O F5 dá suporte a **SP** iniciado pelo
* O SSO do F5 pode ser configurado de três maneiras diferentes.

- [Configurar o logon único F5 para o aplicativo Kerberos](#configure-f5-single-sign-on-for-kerberos-application)

- [Configurar o logon único F5 para o aplicativo baseado em cabeçalho](headerf5-tutorial.md)

- [Configurar o logon único F5 para o aplicativo Kerberos avançado](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Cenários de autenticação de chave

Além do suporte à integração nativa Azure Active Directory para protocolos de autenticação modernos, como Open ID Connect, SAML e WS-reportado, F5 estende o acesso seguro para aplicativos de autenticação baseados em herdados para acesso interno e externo com o Azure AD, habilitando cenários modernos (por exemplo, acesso sem senha) a esses aplicativos. Isso inclui:

* Aplicativos de autenticação baseados em cabeçalho

* Aplicativos de autenticação Kerberos

* Autenticação anônima ou nenhum aplicativo de autenticação embutido

* Aplicativos de autenticação NTLM (proteção com prompts duplos para o usuário)

* Aplicativo baseado em formulários (proteção com prompts duplos para o usuário)

## <a name="adding-f5-from-the-gallery"></a>Adicionando F5 da Galeria

Para configurar a integração do F5 ao Azure AD, você precisa adicionar o F5 da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **F5** na caixa de pesquisa.
1. Selecione **F5** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Configurar e testar o logon único do Azure AD para o F5

Configure e teste o SSO do Azure AD com F5 usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em F5.

Para configurar e testar o SSO do Azure AD com o F5, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do F5](#configure-f5-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Create F5 Test User](#create-f5-test-user)** – para ter um equivalente de B. Simon em F5 que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos **F5** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Contate a [equipe de suporte ao cliente do F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize XML de metadados de **Federação** e **certificado (Base64)** e, em seguida, selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/metadataxml.png)

1. Na seção **Configurar F5** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo usuário** na parte superior da tela.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao F5.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **F5**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .
1. Clique em **acesso condicional** .
1. Clique em **nova política**.
1. Agora você pode ver seu aplicativo F5 como um recurso para a política de CA e aplicar qualquer acesso condicional, incluindo autenticação multifator, controle de acesso baseado em dispositivo ou política de proteção de identidade.

## <a name="configure-f5-sso"></a>Configurar o SSO do F5

- [Configurar o logon único F5 para o aplicativo baseado em cabeçalho](headerf5-tutorial.md)

- [Configurar o logon único F5 para o aplicativo Kerberos avançado](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-kerberos-application"></a>Configurar o logon único F5 para o aplicativo Kerberos

### <a name="guided-configuration"></a>Configuração guiada

1. Abra uma nova janela do navegador da Web e entre no site da sua empresa do F5 (Kerberos) como administrador e execute as seguintes etapas:

1. Será necessário importar o certificado de metadados para o F5, que será usado posteriormente no processo de instalação.

1. Navegue até **sistema > gerenciamento de certificados > gerenciamento de certificado de tráfego > lista de certificados SSL**. Selecione **importar** no canto direito. Especifique um **nome de certificado** (será referenciado mais tarde na configuração). Na **origem do certificado**, selecione carregar arquivo especifique o certificado baixado do Azure ao configurar o logon único do SAML. Clique em **Importar**.

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure01.png) 

1. Além disso, você precisará **de um certificado SSL para o nome de host do aplicativo. Navegue até sistema > gerenciamento de certificados > gerenciamento de certificado de tráfego > lista de certificados SSL**. Selecione **importar** no canto direito. O **tipo de importação** será **PKCS 12 (IIS)** . Especifique um **nome de chave** (que será referenciado mais tarde na configuração) e especifique o arquivo PFX. Especifique a **senha** para o PFX. Clique em **Importar**.

    >[!NOTE]
    >No exemplo, o nome do aplicativo é `Kerbapp.superdemo.live`, estamos usando um certificado curinga nosso KeyName é `WildCard-SuperDemo.live`

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure02.png) 
 
1. Usaremos a experiência guiada para configurar a Federação do Azure AD e o acesso ao aplicativo. Vá para – F5 BIG-IP **principal** e selecione **acessar > configuração guiada > Federação > provedor de serviços SAML**. Clique em **Avançar** e em **Avançar** para iniciar a configuração.

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure03.png) 

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure04.png)

1. Forneça um **nome de configuração**. Especifique a **ID da entidade** (o mesmo que você configurou na configuração de aplicativo do Azure AD). Especifique o **nome do host**. Adicione uma **Descrição** para referência. Aceite as entradas padrão restantes e selecione e, em seguida, clique em **salvar & avançar**.

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure05.png) 

1. Neste exemplo, estamos criando um novo servidor virtual como 192.168.30.200 com a porta 443. Especifique o endereço IP do servidor virtual no **endereço de destino**. Selecione o **perfil cliente SSL**, selecione criar novo. Especifique o certificado de aplicativo carregado anteriormente, (o certificado curinga neste exemplo) e a chave associada e, em seguida, clique em **salvar & avançar**.

    >[!NOTE]
    >Neste exemplo, nosso servidor webinterno está em execução na porta 80 e queremos publicá-lo com 443.

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure06.png)

1. Em **selecionar método para configurar o conector IDP**, especifique metadados, clique em escolher arquivo e carregue o arquivo XML de metadados baixado anteriormente do Azure AD. Especifique um **nome** exclusivo para o conector IDP do SAML. Escolha o **certificado de assinatura de metadados** que foi carregado anteriormente. Clique em **salvar & avançar**.

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure07.png)  

1. Em **selecionar um pool**, especifique **criar novo** (como alternativa, selecione um pool que já existe). Deixe outro valor como padrão. Em servidores de pool, digite o endereço IP em **endereço IP/nome do nó**. Especifique a **porta**. Clique em **salvar & avançar**.
 
    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure08.png)

1. Na tela Configurações de logon único, selecione **habilitar logon único**. Em **tipo de logon único selecionado,** escolha **Kerberos**. Substitua **Session. SAML. Last. Identity** por **Session. SAML. Last. attr. Name. Identity** sob a **origem do nome de usuário** (essa variável foi definida usando o mapeamento de declarações no Azure AD). Selecione **Mostrar configuração avançada**. Em **realm do Kerberos** , digite o nome de domínio. Em **nome da conta/senha da conta** , especifique a conta e a senha de delegação do APM. Especifique o IP do controlador de domínio no campo **KDC** . Clique em **salvar & avançar**.

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure09.png)   

1. Para fins deste guia, ignoraremos as verificações de ponto de extremidade.  Consulte a documentação do F5 para obter detalhes.  Na tela, selecione **salvar & avançar**.

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure10.png) 

1. Aceite os padrões e clique em **salvar & avançar**. Consulte a documentação do F5 para obter detalhes sobre as configurações de gerenciamento de sessão SAML.


    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure11.png) 
 
1. Examine a tela Resumo e selecione **implantar** para configurar o Big-IP.
 
    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure12.png)

1. Depois que o aplicativo tiver sido configurado, clique em **concluir**.

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure13.png)

## <a name="advanced-configuration"></a>Configuração Avançada

>[!NOTE]
>Para referência, clique [aqui](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

### <a name="configuring-an-active-directory-aaa-server"></a>Configurando um servidor Active Directory AAA

Você configura um servidor Active Directory AAA no Gerenciador de políticas de acesso (APM) para especificar controladores de domínio e credenciais para o APM usar para autenticar usuários.

1.  Na guia principal, clique em **política de acesso > servidores AAA > Active Directory**. A tela de lista de servidores de Active Directory é aberta.

2.  Clique em **Criar**. A tela Propriedades do novo servidor é aberta.

3.  No campo **nome** , digite um nome exclusivo para o servidor de autenticação.

4.  No campo **nome de domínio** , digite o nome do domínio do Windows.

5.  Para a configuração de **conexão do servidor** , selecione uma destas opções:

    * Selecione **usar pool** para configurar a alta disponibilidade para o servidor AAA.

    * Selecione **direto** para configurar o servidor AAA para funcionalidade autônoma.

6.  Se você selecionou **direta**, digite um nome no campo **controlador de domínio** .

7.  Se você selecionou usar **pool**, configure o pool:

    * Digite um nome no campo **nome do pool do controlador de domínio** .

    * Especifique os **controladores de domínio** no pool digitando o endereço IP e o nome do host para cada um e clicando no botão **Adicionar** .

    * Para monitorar a integridade do servidor AAA, você tem a opção de selecionar um monitor de integridade: somente o monitor de **gateway_icmp** é apropriado nesse caso; Você pode selecioná-lo na lista **Monitor do pool de servidores** .

8.  No campo **nome do administrador** , digite um nome que diferencia maiúsculas de minúsculas para um administrador que tenha Active Directory permissões administrativas. O APM usa as informações nos campos **nome do administrador** e senha do **administrador** para consulta do AD. Se Active Directory estiver configurado para consultas anônimas, você não precisará fornecer um nome de administrador. Caso contrário, o APM precisa de uma conta com privilégios suficientes para associar a um Active Directory Server, obter informações do grupo de usuários e buscar Active Directory políticas de senha para dar suporte à funcionalidade relacionada a senhas. (O APM deve buscar políticas de senha, por exemplo, se você selecionar a opção solicitar que o usuário altere a senha antes da expiração em uma ação de consulta do AD.) Se você não fornecer informações de conta de administrador nessa configuração, o APM usará a conta de usuário para buscar informações. Isso funcionará se a conta de usuário tiver privilégios suficientes.

9.  No campo **senha de administrador** , digite a senha de administrador associada ao nome de domínio.

10. No campo **verificar senha de administrador** , digite novamente a senha de administrador associada à configuração de **nome de domínio** .

11. No campo **tempo de vida do cache do grupo** , digite o número de dias. O tempo de vida padrão é de 30 dias.

12. No campo **tempo de vida do cache de objeto de segurança de senha** , digite o número de dias. O tempo de vida padrão é de 30 dias.

13. Na lista **tipo de criptografia de pré-autenticação Kerberos** , selecione um tipo de criptografia. O padrão é **nenhum**. Se você especificar um tipo de criptografia, o sistema BIG-IP incluirá dados de pré-autenticação do Kerberos dentro do primeiro pacote de solicitação de serviço de autenticação (AS-REQ).

14. No campo **tempo limite** , digite um intervalo de tempo limite (em segundos) para o servidor AAA. (Essa configuração é opcional.)

15. Clique em **concluído**. O novo servidor é exibido na lista. Isso adiciona o novo servidor de Active Directory à lista de servidores de Active Directory.

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure17.png)

### <a name="saml-configuration"></a>Configuração do SAML

1. Será necessário importar o certificado de metadados para o F5, que será usado posteriormente no processo de instalação. Navegue até **sistema > gerenciamento de certificados > gerenciamento de certificado de tráfego > lista de certificados SSL**. Selecione **importar** no canto direito.

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure18.png)

2. Para configurar o IDP do SAML, **navegue até acessar > federação > SAML: provedor de serviços > conectores IDP externos**e clique em **criar > de metadados**.

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure19.png)

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure20.png)

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure21.png)

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure22.png)

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure23.png)

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure24.png)

1. Para configurar o SP do SAML, navegue até **acessar > federação > provedor de serviços SAML > serviços do SP local** e clique em **criar**. Preencha as informações a seguir e clique em **OK**.

    * Nome do tipo: KerbApp200SAML
    * ID da entidade *: https://kerbapp200.superdemo.live
    * Configurações de nome do SP
    * Esquema: https
    * Host: kerbapp200. superdemo. Live
    * Descrição: kerbapp200. superdemo. Live

     ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure25.png)

     b. Selecione a configuração do SP, KerbApp200SAML, e clique em **associar/desassociar conectores IDP**.

     ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure26.png)

     ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure27.png)

     c. Clique em **Adicionar nova linha** e selecione o **conector IDP externo** criado na etapa anterior, clique em **Atualizar**e, em seguida, clique em **OK**.

     ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure28.png)

1. Para configurar o SSO do Kerberos, navegue até **acesso > logon único > Kerberos**, preencha as informações e clique em **concluído**.

    >[!Note]
    > Você precisará da conta de delegação Kerberos a ser criada e especificada. Consulte a seção KCD (consulte o apêndice para referências de variáveis)

    * **Origem do nome de usuário**: Session.SAML.Last.attr.Name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname

    * **Origem do realm do usuário**: sessão. logon. último. domínio

        ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure29.png)

1. Para configurar o perfil de acesso, navegue até **acessar > perfil/políticas > perfil de acesso (por políticas de sessão)** , clique em **criar**, preencha as informações a seguir e clique em **concluído**.

    * Nome: KerbApp200
    * Tipo de perfil: todos
    * Escopo do perfil: perfil
    * Idiomas: Inglês

        ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure30.png)

1. Clique no nome, KerbApp200, preencha as informações a seguir e clique em **Atualizar**.

    * Cookie de domínio: superdemo. Live
    * Configuração de SSO: KerAppSSO_sso

        ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure31.png)

1. Clique em **política de acesso** e, em seguida, clique em **Editar política de acesso** para o perfil "KerbApp200".

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure32.png)

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure33.png)

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure34.png)

    * **sessão. logon. Last. usernameUPN expr {[mcget {Session. SAML. Last. Identity}]}**

    * **sessão. ad. lastactualdomain de texto superdemo. Live**

        ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure35.png)

    * **(userPrincipalName =% {Session. logon. Last. usernameUPN})**

        ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure36.png)

        ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure37.png)

    * **Session. logon. Last. username expr {"[mcget {Session. ad. Last. attr. sAMAccountName}]"}**

        ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure38.png)

    * **mcget {Session. logon. Last. username}**
    * **mcget {sessão. logon. última. senha**

1. Para adicionar um novo nó, navegue até o **tráfego Local > nós > lista de nós, clique em criar**, preencha as informações a seguir e clique em **concluído**.

    * Nome: KerbApp200
    * Descrição: KerbApp200
    * Endereço: 192.168.20.200

        ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure39.png)

1. Para criar um novo pool, navegue até o **tráfego Local > pools > lista de pools, clique em criar**, preencha as informações a seguir e clique em **concluído**.

    * Nome: KerbApp200-pool
    * Descrição: KerbApp200-pool
    * Monitores de integridade: http
    * Endereço: 192.168.20.200
    * Porta do serviço: 81

        ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure40.png)

1. Para criar o servidor virtual, navegue até **tráfego Local > servidores virtuais > lista de servidores virtuais > +** , preencha as informações a seguir e clique em **concluído**.

    * Nome: KerbApp200
    * Endereço/máscara de destino: host 192.168.30.200
    * Porta de serviço: porta 443 HTTPS
    * Perfil de acesso: KerbApp200
    * Especificar o perfil de acesso criado na etapa anterior

        ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure41.png)

        ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure42.png)

### <a name="setting-up-kerberos-delegation"></a>Configurando a delegação Kerberos 

>[!NOTE]
>Para referência, clique [aqui](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

*  **Etapa 1:** Criar uma conta de delegação

    **Exemplo:**
    * Nome de domínio: **superdemo. Live**

    * Nome da conta Sam: **Big-ipuser**

    * New-ADUser-Name "conta de delegação do APM"-UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live-SamAccountName "Big-ipuser"-PasswordNeverExpires $true-habilitado $true-AccountPassword (Read-Host-assegurastring "senha! 1234")

* **Etapa 2:** Definir o SPN (na conta de delegação do APM)

    **Exemplo:**
    * SetSPN – A **host/Big-ipuser. superdemo. Live** -ipuser

* **Etapa 3:** Delegação de SPN (para a conta de serviço de aplicativo) Configure a delegação apropriada para a conta de delegação F5.
    No exemplo a seguir, a conta de delegação do APM está sendo configurada para KCD para FRP-App1. superdemo. aplicativo ao vivo.

    ![Configuração de F5 (Kerberos)](./media/kerbf5-tutorial/configure43.png)

* Forneça os detalhes conforme mencionado no documento de referência acima abaixo [disso](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html).

### <a name="create-f5-test-user"></a>Criar usuário de teste do F5

Nesta seção, você criará um usuário chamado B. Simon em F5. Trabalhe com a [equipe de suporte ao cliente F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para adicionar os usuários à plataforma F5. Os usuários devem ser criados e ativados antes de usar o logon único. 

## <a name="test-sso"></a>Testar SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco F5 no painel de acesso, você deverá ser conectado automaticamente ao F5 para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o F5 com o Azure AD](https://aad.portal.azure.com/)

- [Configurar o logon único F5 para o aplicativo baseado em cabeçalho](headerf5-tutorial.md)

- [Configurar o logon único F5 para o aplicativo Kerberos avançado](advance-kerbf5-tutorial.md)

