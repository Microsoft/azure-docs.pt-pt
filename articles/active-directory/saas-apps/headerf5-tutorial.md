---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com F5 | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59a87abb-1ec1-4438-be07-5b115676115f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ad6b7150a43a286a4bec39a0482e08f50d95c06
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048069"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com F5

Neste tutorial, você aprenderá a integrar o F5 ao Azure Active Directory (Azure AD). Ao integrar o F5 ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao F5.
* Habilite seus usuários a serem automaticamente conectados ao F5 com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com um único sign-on em Azure AD, consulte [o único sign-on para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

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

* Embora opcional, é altamente recomendado implantar os sistemas F5 num grupo de [dispositivos de sincronização/falha](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG), que inclui o par de espera ativo, com um endereço IP flutuante para alta disponibilidade (HA). A redundância de interface adicional pode ser obtida usando o LACP (protocolo de controle de agregação de link). O LACP gerencia as interfaces físicas conectadas como uma única interface virtual (grupo de agregação) e detecta quaisquer falhas de interface dentro do grupo.

* Para aplicativos Kerberos, uma conta de serviço do AD local para delegação restrita.  Consulte a [Documentação F5](https://support.f5.com/csp/article/K43063049) para a criação de uma conta de delegação da AD.

## <a name="access-guided-configuration"></a>Configuração guiada de acesso

* O acesso guiado à configuração ' tem suporte em F5 TMOS versão 13.1.0.8 e superior. Se o seu sistema BIG-IP estiver a executar uma versão abaixo de 13.1.0.8, consulte a secção de **configuração Avançada.**

* A configuração interativa de acesso apresenta uma experiência de usuário totalmente nova e simplificada. Essa arquitetura baseada em fluxo de trabalho fornece etapas de configuração reentrantes e intuitivas adaptadas para a topologia selecionada.

* Antes de proceder à configuração, atualize a configuração guiada, baixando o mais recente pacote de casos de utilização a partir de [downloads.f5.com](https://login.f5.com/resource/login.jsp?ctx=719748). Para atualizar, siga o procedimento abaixo.

    >[!NOTE]
    >As capturas de tela abaixo são para a versão mais recente lançada (BIG-IP 15,0 com AGC versão 5,0). As etapas de configuração a seguir são válidas para esse caso de uso em 13.1.0.8 para a versão BIG-IP mais recente.

1. No F5 BIG-IP Web UI, clique no **Access >> Configuração do Guia**.

1. Na página de **Configuração Guiada,** clique na **configuração guiada** de upgrade no canto superior esquerdo.

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure14.png) 

1. No ecrã pop de configuração do guia de atualização, **selecione Escolha o Ficheiro** para carregar o pacote de casos de utilização descarregado e clique no botão Upload e **Instalar.**

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure15.png) 

1. Quando a atualização estiver concluída, clique no botão **Continuar.**

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SSO do F5 pode ser configurado de três maneiras diferentes.

- [Configure o único sinal de F5 para a aplicação baseada em cabeçalho](#configure-f5-single-sign-on-for-header-based-application)

- [Configure f5 único sinal para candidatura kerberos](kerbf5-tutorial.md)

- [Configure F5 único sign-on para aplicação Kerberos Avançada](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Cenários de autenticação de chave

* Além do suporte à integração nativa Azure Active Directory para protocolos de autenticação modernos, como Open ID Connect, SAML e WS-reportado, F5 estende o acesso seguro para aplicativos de autenticação baseados em herdados para acesso interno e externo com o Azure AD, habilitando cenários modernos (por exemplo, acesso sem senha) a esses aplicativos. Isso inclui:

* Aplicativos de autenticação baseados em cabeçalho

* Aplicativos de autenticação Kerberos

* Autenticação anônima ou nenhum aplicativo de autenticação embutido

* Aplicativos de autenticação NTLM (proteção com prompts duplos para o usuário)

* Aplicativo baseado em formulários (proteção com prompts duplos para o usuário)

## <a name="adding-f5-from-the-gallery"></a>Adicionando F5 da Galeria

Para configurar a integração do F5 ao Azure AD, você precisa adicionar o F5 da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, **digite F5** na caixa de pesquisa.
1. Selecione **F5** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Configurar e testar o logon único do Azure AD para o F5

Configure e teste Azure AD SSO com F5 utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em F5.

Para configurar e testar o SSO do Azure AD com o F5, conclua os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure F5 SSO](#configure-f5-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. Criar o utilizador de **[teste F5](#create-f5-test-user)** - para ter uma contraparte de B.Simon em F5 que esteja ligada à representação azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **F5,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **identificador,** digite um URL utilizando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

    b. Na caixa de texto **URL de resposta,** escreva um URL utilizando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Contacte a equipa de apoio ao [cliente F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sessão com a página **SAML,** na secção certificado de **assinatura SAML,** encontre **metadados da Federação XML** e **Certificado (Base64)** e selecione **Download** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na secção **Deconfiguração de F5,** copie os URL(s) adequados com base no seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **de nome do utilizador,** introduza o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao F5.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **F5**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![O link Adicionar usuário](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**
1. Clique no **Acesso Condicional** .
1. Clique em **Nova Política**.
1. Agora você pode ver seu aplicativo F5 como um recurso para a política de CA e aplicar qualquer acesso condicional, incluindo autenticação multifator, controle de acesso baseado em dispositivo ou política de proteção de identidade.

## <a name="configure-f5-sso"></a>Configurar o SSO do F5

- [Configure f5 único sinal para candidatura kerberos](kerbf5-tutorial.md)

- [Configure F5 único sign-on para aplicação Kerberos Avançada](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Configure o único sinal de F5 para a aplicação baseada em cabeçalho

### <a name="guided-configuration"></a>Configuração guiada

1. Abra uma nova janela do navegador web e inscreva-se no site da empresa F5 (Header Based) como administrador e execute os seguintes passos:

1. Navegar para **System > Gestão de Certificados > Gestão de Certificados de Tráfego > Lista de Certificados SSL**. Selecione **Importar** a partir do canto direito. Especifique um nome de **certificado** (será referenciado mais tarde no config). Na Fonte de **Certificado,** selecione Upload File especifique o certificado descarregado do Azure enquanto configura o SAML Single Sign on. Clique em **Importar**.

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure12.png)
 
1. Além disso, irá requerer **o Certificado SSL para o nome de anfitrião da aplicação. Navegar para System > Gestão de Certificados > Gestão de Certificados de Tráfego > Lista de Certificados SSL**. Selecione **Importar** a partir do canto direito. **O Tipo de Importação** será **PKCS 12 (IIS)** . Especifique um **nome-chave** (será referenciado mais tarde no config) e especificar o ficheiro PFX. Especifique a **palavra-passe** para o PFX. Clique em **Importar**.

    >[!NOTE]
    >No exemplo, o nosso nome de aplicação é `Headerapp.superdemo.live`, estamos a usar um Certificado wild card o nosso nome-chave é `WildCard-SuperDemo.live`.

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure13.png)

1. Usaremos a experiência guiada para configurar a Federação do Azure AD e o acesso ao aplicativo. Vá a – F5 BIG-IP **Main** e selecione **Access > Configuração Guiada > Federação > Prestador de Serviços SAML**. Clique **em Seguinte** e clique em **Next** para iniciar a configuração.

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure01.png)

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure02.png)
 
1. Forneça um nome de **configuração**. Especifique o **ID da Entidade** (o mesmo que configurado na configuração da aplicação AD Azure). Especifique o **nome anfitrião**. Adicione uma **descrição** para referência. Aceite as entradas predefinidas restantes e selecione e, em seguida, clique em **Guardar e Seguir**.

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure03.png) 

1. Neste exemplo estamos a criar um novo Servidor Virtual como 192.168.30.20 com a porta 443. Especifique o endereço IP do servidor virtual no **endereço destino**. Selecione o **Perfil SSL**do Cliente, selecione Criar novo. Especifique o certificado de pedido previamente carregado (o certificado de cartão selvagem neste exemplo) e a chave associada, e, em seguida, clique em **Guardar e Seguir**.

    >[!NOTE]
    >neste exemplo, o nosso webserver interno está a funcionar na porta 888 e queremos publicá-lo com 443.

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure04.png) 

1. No **método Select para configurar o seu conector IDP,** especifique Metadados, clique no Ficheiro Escolha e carregue o ficheiro Metadata XML descarregado anteriormente a partir do Azure AD. Especifique um **nome** único para o conector IDP SAML. Escolha o Certificado de Assinatura de **Metadados** que foi carregado anteriormente. Clique em **Guardar e Seguir**.

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure05.png)
 
1. Em **Select a Pool,** especifique **Criar Novo** (em alternativa, selecione uma piscina que já existe). Deixe outro valor como padrão. Em servidores de pool, digite o endereço IP em nome ip **endereço/nó**. Especifique a **Porta**. Clique em **Guardar e Seguir**.

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure06.png)

1. No ecrã de definições de sinal único, **selecione Ativar um único sinal .** Em "Single Sign-On Type" selecionado, escolha **o cabeçalho http baseado em cabeçalho**. Substitua a **session.saml.last.Identity** por **session.saml.last.attr.name.name.Identity** under Username Source ( esta variável que definiu utilizando o mapeamento de sinistros no Azure AD ). Sob cabeçalhos SSO.

    * **Nome do cabeçalho : MyAuthorization**

    * **Valor do cabeçalho : %{session.saml.last.attr.name.Identity}**

    * Clique em **Guardar e Seguir**

    Consulte o Apêndice para lista completa de variáveis e valores. Pode adicionar mais cabeçalhos conforme necessário.

    >[!NOTE]
    >Nome da conta é a conta de delegação F5 criada (Ver Documentação F5).

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure07.png) 

1. Para fins deste guia, ignoraremos as verificações de ponto de extremidade.  Consulte a documentação do F5 para obter detalhes. Selecione **Guardar e Seguir**.

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure08.png)

1. Aceite as predefinições e clique em **Guardar e Seguir**. Consulte a documentação F5 para obter detalhes sobre as definições de gestão da sessão SAML.

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure09.png)

1. Reveja o ecrã resumo e selecione **Implementar** para configurar o BIG-IP. clique em **Terminar**.

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure10.png)

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure11.png)

## <a name="advanced-configuration"></a>Configuração Avançada

Esta secção destina-se a ser utilizada se não puder utilizar a configuração guiada ou se quiser adicionar/modificar parâmetros adicionais. Necessitará de Certificado SSL para o nome de anfitrião da aplicação.

1. Navegar para **System > Gestão de Certificados > Gestão de Certificados de Tráfego > Lista de Certificados SSL**. Selecione **Importar** a partir do canto direito. **O Tipo de Importação** será **PKCS 12 (IIS)** . Especifique um **nome-chave** (será referenciado mais tarde no config) e especificar o ficheiro PFX. Especifique a **palavra-passe** para o PFX. Clique em **Importar**.

    >[!NOTE]
    >No exemplo, o nosso nome de aplicação é `Headerapp.superdemo.live`, estamos a usar um Certificado wild card o nosso nome-chave é `WildCard-SuperDemo.live`.
  
    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure17.png)

### <a name="adding-a-new-web-server-to-bigip-f5"></a>Adicionar um novo Servidor Web ao BigIP-F5

1. Clique em **Main > IApps > Serviços de Aplicação > Aplicação > Criar**.

1. Forneça o **nome** e em **modelo** escolha **f5.http**.
 
    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure18.png)

1. Publicaremos o nosso HeaderApp2 externamente como HTTPS neste caso, **como deve o sistema BIG-IP lidar com o Tráfego SSL**? especificamos **o Terminate SSL do Cliente, O Texto Simples para servidores (SSL Offload)** . Especificar o seu Certificado e Chave em que certificado SSL pretende utilizar? e **qual a chave privada SSL que quer usar?** Especificar o IP do Servidor Virtual em **que endereço IP pretende utilizar para o Servidor Virtual?** 

    * **Especificar outros detalhes**

        * FQDN  

        * Especifique a saída do pool de aplicações ou crie uma nova.

        * Se criar um novo Servidor de Aplicações, especifique o **endereço IP interno** e o número da **porta**.

        ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure19.png) 

1. Clique em **Terminar**.

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure20.png) 

1. Certifique-se de que as Propriedades da Aplicação podem ser modificadas. Clique em **Main > IApps > Serviços de Aplicação: Aplicações >> HeaderApp2**. Desfaça **atualizações rigorosas** (modificaremos alguma definição fora do GUI). Clique no botão **'Actualizar'.**

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure21.png) 

1. Neste momento deverá ser capaz de navegar no Servidor virtual.

### <a name="configuring-f5-as-sp-and-azure-as-idp"></a>Configurar F5 como SP e Azure como IDP

1.  Clique em **Access > Federation> SAML Service Provider > Local SP Service > clique em criar ou + sinal**.

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure22.png)

1. Especifique detalhes para o Serviço de Prestador de Serviços. Especificar **nome** que represente a configuração F5 SP. Especificar **ID da entidade** (geralmente o mesmo que url de aplicação).

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure23.png)

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure24.png)

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure25.png)

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure26.png)

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure27.png)

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure28.png)

### <a name="create-idp-connector"></a>Criar conector IDP

1. Clique no botão **de ligação idp do botão de ligação,** selecione **Criar novo conector IDP** e escolha a opção **Metadata** e, em seguida, execute os seguintes passos:
 
    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure29.png)

    a. Navegue para o ficheiro metadata.xml descarregado a partir de Azure AD e especifique um **Nome de Fornecedor**de Identidade .

    b. Clique **bem**.

    c. O conector é criado e o certificado está pronto automaticamente a partir do ficheiro metadados xml.
    
    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure30.png)

    d. Configure f5BIG-IP para enviar todos os pedidos para a Azure AD.

    e. Clique em **Adicionar Nova Linha,** escolha **AzureIDP** (conforme criado em passos anteriores, especifique 

    f. **Fonte correspondente = %{session.server.landinguri}** 

    g. **Valor correspondente = /** *

    h. Clique na **atualização**

    i. Clique em **OK**

    j. **A configuração do IDP SAML está concluída**
    
    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure31.png)

### <a name="configure-f5-policy-to-redirect-users-to-azure-saml-idp"></a>Configure a Política F5 para redirecionar os utilizadores para o IDP Azure SAML

1. Para configurar a Política F5 para redirecionar os utilizadores para o IDP Azure SAML, execute os seguintes passos:

    a. Clique em **Main > Access > Profile/Policies > Access Profiles**.

    b. Clique no botão **Criar.**

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure32.png)
 
    c. Especificar **Nome** (HeaderAppAzureSAMLPolicy no exemplo).

    d. Pode personalizar outras definições, consulte a Documentação F5.

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure33.png)

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure34.png) 

    e. Clique em **Terminar**.

    f. Uma vez concluída a criação da Política, clique na Política e vá ao Separador política de **acesso.**

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure35.png)
 
    g. Clique no editor de **Política Visual,** edite a Política de Acesso para o link **Profile.**

    h. Clique no + Assine no editor de Política Visual e escolha **O SAML Auth**.

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure36.png)

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure37.png)
 
    i. Clique **em Adicionar Item**.

    j. Em **propriedades** especifiquem **nome** e sob o **Servidor AAA** selecione o SP previamente configurado, clique em **SAVE**.
 
    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure38.png)

    k. A Política Básica está pronta para personalizar a política para incorporar fontes/lojas de atributos adicionais.

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure39.png)
 
    l. Certifique-se de clicar no link Política de **Acesso aplicável** no topo.

### <a name="apply-access-profile-to-the-virtual-server"></a>Aplicar perfil de acesso ao Servidor Virtual

1. Atribuir o perfil de acesso ao Servidor Virtual para que f5 BIG-IP APM aplique as definições de perfil ao tráfego de entrada e execute a política de acesso previamente definida.

    a. Clique em **servidores virtuais** > de > **tráfego local** **principal** .

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure40.png)
 
    b. Clique na secção de Política de Acesso, percorra a secção Política de **Acesso,** na queda do Perfil de **Acesso** e selecione a Política SAML criada (no exemplo HeaderAppAzureSAMLPolicy)

    c. Clique na **atualização**
 
    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure41.png)

    d. criar um F5 BIG-IP iRule® extrair os atributos SAML personalizados da afirmação de entrada e passá-los como cabeçalhos HTTP para a aplicação de teste de backend. Clique em **Main > Tráfego Local > iRules > iRule List > clique em criar**

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure42.png)
 
    e. Colá-lo o texto f5 BIG-IP iRule abaixo na janela Definição.

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure43.png)
 
    quando RULE_INIT { definir estático::debug 0 } quando ACCESS_ACL_ALLOWED {

    definir AZUREAD_USERNAME [ACCESS::dados da sessão obter "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"] se { $static::debug } - log local0. "AZUREAD_USERNAME = $AZUREAD_USERNAME" } se { !. [HTTP::header existe "AZUREAD_USERNAME"]) { HTTP::header insert "AZUREAD_USERNAME" $AZUREAD_USERNAME }

    definir AZUREAD_DISPLAYNAME [ACCESS::dados da sessão obter "session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/displayname"] se { $static::debug } - log local0. "AZUREAD_DISPLAYNAME = $AZUREAD_DISPLAYNAME" } se { !. [HTTP::cabeçalho existe "AZUREAD_DISPLAYNAME"]) { HTTP::header insert "AZUREAD_DISPLAYNAME" $AZUREAD_DISPLAYNAME }

    definir AZUREAD_EMAILADDRESS [ACCESS::dados da sessão obter "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress"] se { $static::debug } - log local0. "AZUREAD_EMAILADDRESS = $AZUREAD_EMAILADDRESS" } se { !. [HTTP::header existe "AZUREAD_EMAILADDRESS"]) { HTTP::header insert "AZUREAD_EMAILADDRESS" $AZUREAD_EMAILADDRESS }}

    **Saída de amostra abaixo**

    ![Configuração F5 (Baseada em Cabeçalho)](./media/headerf5-tutorial/configure44.png)
 
### <a name="create-f5-test-user"></a>Criar usuário de teste do F5

Nesta seção, você criará um usuário chamado B. Simon em F5. Trabalhe com a equipa de suporte ao [Cliente F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para adicionar os utilizadores na plataforma F5. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco F5 no painel de acesso, você deverá ser conectado automaticamente ao F5 para o qual você configurou o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente F5 com Azure AD](https://aad.portal.azure.com/)

- [Configure f5 único sinal para candidatura kerberos](kerbf5-tutorial.md)

- [Configure F5 único sign-on para aplicação Kerberos Avançada](advance-kerbf5-tutorial.md)

