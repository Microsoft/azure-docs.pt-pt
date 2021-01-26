---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com F5 | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o F5.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.openlocfilehash: ba58af8d3d28efcc7f97be01a96202a2203a3459
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98796932"
---
# <a name="tutorial-configure-single-sign-on-sso-between-azure-active-directory-and-f5"></a>Tutorial: Configurar um único sign-on (SSO) entre o Azure Ative Directory e o F5

Neste tutorial, você vai aprender a integrar F5 com Azure Ative Directory (Azure AD). Quando integrar o F5 com AZure AD, pode:

* Controlo em Azure AD que tem acesso a F5.
* Ative os seus utilizadores a serem automaticamente inscritos no F5 com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com um único sign-on em Azure AD, consulte [o single sign-on para aplicações no Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

* F5 assinatura única ativada (SSO).

* A implementação da solução comum requer a seguinte licença:

    * F5 BIG-IP® Melhor pacote (ou) 

    * F5 BIG-IP Access Policy Manager™ (APM) licença autónoma 

    * F5 BIG-IP Access Policy Manager™ (APM) licença de complemento em um BIG-IP F5 BIG-IP existente® Gestor de Tráfego Local™ (LTM).

    * Além da licença acima, o sistema F5 também pode ser licenciado com: 

        * Uma subscrição de filtragem de URL para utilizar a base de dados da categoria URL

        * Uma subscrição de Inteligência IP F5 para detetar e bloquear agressores conhecidos e tráfego malicioso
     
        * Um módulo de segurança de hardware de rede (HSM) para salvaguardar e gerir chaves digitais para autenticação forte

* O sistema F5 BIG-IP é a provisionado com módulos APM (LTM é opcional)

* Embora opcional, é altamente recomendado implementar os sistemas F5 num grupo de [dispositivos de sincronização/failover](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG), que inclui o par de espera ativo, com um endereço IP flutuante para alta disponibilidade (HA). Uma maior redundância de interface pode ser alcançada utilizando o Protocolo de Controlo de Agregação de Ligações (LACP). LACP gere as interfaces físicas conectadas como uma única interface virtual (grupo agregado) e deteta quaisquer falhas de interface dentro do grupo.

* Para os pedidos da Kerberos, um serviço de AD no local conta para a delegação restrita.  Consulte a [Documentação F5](https://support.f5.com/csp/article/K43063049) para a criação de uma conta de delegação de AD.

## <a name="access-guided-configuration"></a>Aceda à configuração guiada

* A configuração guiada de acesso» é suportada na versão 13.1.0.8 ou superior do F5 TMOS. Se o seu sistema BIG-IP estiver a executar uma versão abaixo de 13.1.0.8, consulte a secção **de configuração Avançada.**

* A configuração guiada de acesso apresenta uma experiência de utilizador completamente nova e simplificada. Esta arquitetura baseada em fluxo de trabalho fornece passos de configuração intuitivos e re-entrantes adaptados à topologia selecionada.

* Antes de prosseguir com a configuração, atualize a configuração guiada descarregando o pacote de casos de utilização mais recente a partir de [downloads.f5.com](https://login.f5.com/resource/login.jsp?ctx=719748). Para atualizar, siga o procedimento abaixo.

    >[!NOTE]
    >As imagens abaixo são para a versão mais recente (BIG-IP 15.0 com a versão AGC 5.0). Os passos de configuração abaixo são válidos para este caso de utilização em 13.1.0.8 até à versão MAIS recente BIG-IP.

1. No UI Web F5 BIG-IP, clique na **Configuração do Guia de >> de Acesso**.

1. Na página **de Configuração Guiada,** clique na **Configuração Guiada de Atualização** no canto superior esquerdo.

    ![A screenshot mostra a página de configuração guiada com o link de configuração guiada de atualização.](./media/headerf5-tutorial/configure14.png) 

1. No ecrã pop da configuração do guia de atualização, **selecione Choose File** para carregar o pacote de casos de utilização descarregado e clique no botão **Upload e Install.**

    ![A screenshot mostra a caixa de diálogo de configuração guiada de atualização com o Choose File selecionado.](./media/headerf5-tutorial/configure15.png) 

1. Quando a atualização estiver concluída, clique no botão **Continuar.**

    ![A screenshot mostra a caixa de diálogo de configuração guiada de atualização com uma mensagem de conclusão.](./media/headerf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* F5 SSO pode ser configurado de três maneiras diferentes.

- [Configurar f5 único sinal para aplicação baseada em cabeçalho](#configure-f5-single-sign-on-for-header-based-application)

- [Configurar f5 único sinal para a aplicação Kerberos](kerbf5-tutorial.md)

- [Configurar f5 único sinal para aplicação Kerberos Avançada](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Principais cenários de autenticação

* Para além do suporte de integração nativa do Azure Ative Directory para protocolos modernos de autenticação como Open ID Connect, SAML e WS-Fed, o F5 alarga o acesso seguro a aplicações de autenticação baseadas em legados para acesso interno e externo com Azure AD, permitindo cenários modernos (por exemplo, acesso sem palavra-passe) a estas aplicações. Isto inclui:

* Aplicativos de autenticação baseados em cabeçalho

* Aplicativos de autenticação Kerberos

* Autenticação anónima ou sem aplicações de autenticação incorporadas

* Aplicativos de autenticação NTLM (proteção com solicitações duplas para o utilizador)

* Aplicação baseada em formulários (proteção com solicitações duplas para o utilizador)

## <a name="adding-f5-from-the-gallery"></a>Adicionar F5 da galeria

Para configurar a integração do F5 no AD Azure, é necessário adicionar F5 da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** escreva **F5** na caixa de pesquisa.
1. Selecione **F5** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Configurar e testar Azure AD único sinal para F5

Configure e teste Azure AD SSO com F5 usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em F5.

Para configurar e testar o Azure AD SSO com F5, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure F5 SSO](#configure-f5-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create F5 test user](#create-f5-test-user)** - para ter uma contraparte de B.Simon em F5 que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **F5,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de suporte do Cliente F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML** e Certificado da Federação **(Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar F5,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a F5.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **F5**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**
1. Clique em **Acesso Condicional.**
1. Clique em **Nova Política.**
1. Pode agora ver a sua App F5 como um recurso para a política de acesso condicional e aplicar qualquer acesso condicional, incluindo Multifactor Auth, controlo de acesso baseado no dispositivo ou Política de Proteção de Identidade.

## <a name="configure-f5-sso"></a>Configurar F5 SSO

- [Configurar f5 único sinal para a aplicação Kerberos](kerbf5-tutorial.md)

- [Configurar f5 único sinal para aplicação Kerberos Avançada](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Configurar f5 único sinal para aplicação baseada em cabeçalho

### <a name="guided-configuration"></a>Configuração guiada

1. Abra uma nova janela do navegador web e inscreva-se no seu site da empresa F5 (Header Based) como administrador e execute os seguintes passos:

1. Navegue para **a Gestão de Certificados > Sistema > Gestão de Certificados de Tráfego > Lista de Certificados SSL**. **Selecione Importar** do canto direito. Especifique um **Nome de Certificado** (será referenciado mais tarde no config). Na **Fonte** de Certificado , selecione Upload File especificar o certificado descarregado do Azure enquanto configura o Sinal Único DE SAML. Clique **em Importar.**

    ![A screenshot mostra a Lista de Certificados S S L onde seleciona o nome do certificado e a fonte de certificado.](./media/headerf5-tutorial/configure12.png)
 
1. Além disso, irá necessitar de **Certificado SSL para o nome de anfitrião da aplicação. Navegue para a Gestão de Certificados > sistema > Gestão de Certificados de Tráfego > Lista de Certificados SSL**. **Selecione Importar** do canto direito. **O Tipo de Importação** será **PKCS 12(IIS)**. Especifique um **Nome chave** (será referenciado mais tarde no config) e o especifique o ficheiro PFX. Especifique a **palavra-passe** para o PFX. Clique **em Importar.**

    >[!NOTE]
    >No exemplo, o nome da nossa aplicação `Headerapp.superdemo.live` é, estamos a usar um Certificado wild card que o nosso nome de chave é `WildCard-SuperDemo.live` .

    ![A screenshot mostra a página S S L Certificate/Key Source.](./media/headerf5-tutorial/configure13.png)

1. Utilizaremos a Experiência Guiada para configurar a Federação AD Azure e o Acesso à Aplicação. Vá a – F5 BIG-IP **Main** e selecione **Access > Configuração Guiada > Federação > Prestador de Serviços SAML**. Clique **em Seguinte** e clique em **Seguinte** para iniciar a configuração.

    ![A screenshot mostra a página de Configuração Guiada com a Federação selecionada.](./media/headerf5-tutorial/configure01.png)

    ![A screenshot mostra a página do Fornecedor de Serviços SAML.](./media/headerf5-tutorial/configure02.png)
 
1. Fornecer um **nome de configuração**. Especifique o **ID da Entidade** (o mesmo que configurado na Configuração da Aplicação AD Azure). Especificar o **nome hospedeiro**. Adicione uma **descrição** para referência. Aceite as entradas predefinitivas restantes e selecione e, em seguida, clique em **Guardar & Seguinte**.

    ![A screenshot mostra a página De Propriedades do Fornecedor de Serviços.](./media/headerf5-tutorial/configure03.png) 

1. Neste exemplo estamos a criar um novo Servidor Virtual como 192.168.30.20 com a porta 443. Especifique o endereço IP do Servidor Virtual no **Endereço destino**. Selecione o **Perfil SSL do** Cliente, selecione Criar novo. Especifique o certificado de inscrição previamente carregado (o certificado wild card neste exemplo) e a chave associada, e, em seguida, clique em **Guardar & Next**.

    >[!NOTE]
    >neste exemplo, o nosso webserver Interno está a funcionar na porta 888 e queremos publicá-lo com 443.

    ![A screenshot mostra a página Propriedades do Servidor Virtual.](./media/headerf5-tutorial/configure04.png) 

1. No **método Select para configurar o seu conector IdP**, especifique metadados, clique no Choose File e carregufique o ficheiro Metadados XML descarregado anteriormente a partir de Azure AD. Especifique um **nome** único para conector IDP SAML. Escolha o **Certificado de Assinatura de Metadados** que foi carregado anteriormente. Clique **em Guardar & Seguinte**.

    ![A screenshot mostra a página de Definições do Conector de Identidade Externa.](./media/headerf5-tutorial/configure05.png)
 
1. Em **Select a Pool**, especifique Criar **Novo** (em alternativa selecione uma piscina que já existe). Que outro valor seja padrão. Nos Servidores de Pool, digite o endereço IP no **endereço IP/nome do nó**. Especificar o **Porto.** Clique **em Guardar & Seguinte**.

    ![A screenshot mostra a página Pool Properties.](./media/headerf5-tutorial/configure06.png)

1. No ecrã de definições de Sign-On único, selecione **Ative O Sign-On único**. Em Tipo único Sign-On selecionado escolha **http baseado em cabeçalho**. Substitua **session.saml.last.Identity** por **session.saml.last.attr.name.Name** under Username Source (esta variável que definiu usando mapeamento de reclamações no AD Azure). Sob os cabeçalhos SSO.

    * **HeaderName : MyAuthorization**

    * **Valor do cabeçalho : %{session.saml.last.attr.name.Identity}**

    * Clique **em Guardar & Seguinte**

    Consulte o Apêndice para obter uma lista completa de variáveis e valores. Pode adicionar mais cabeçalhos conforme necessário.

    >[!NOTE]
    >O nome da conta é a conta da delegação F5 criada (Ver Documentação F5).

    ![A screenshot mostra a página 'Definições Sign-On Únicas'.](./media/headerf5-tutorial/configure07.png) 

1. Para efeitos desta orientação, saltaremos as verificações de pontos finais.  Consulte a documentação F5 para mais detalhes. **Selecione Guardar & Seguinte**.

    ![A screenshot mostra a página Endpoint Checks Properties.](./media/headerf5-tutorial/configure08.png)

1. Aceite as predefinições e clique em **Guardar & Seguinte**. Consulte a documentação F5 para obter detalhes sobre as definições de gestão da sessão SAML.

    ![A screenshot mostra a página Definições de Tempo de Saída.](./media/headerf5-tutorial/configure09.png)

1. Reveja o ecrã de resumo e **selecione Implementar** para configurar o BIG-IP. clique em **Terminar**.

    ![A screenshot mostra que a sua aplicação está pronta para ser implantada página.](./media/headerf5-tutorial/configure10.png)

    ![A screenshot mostra que a sua aplicação está implantada.](./media/headerf5-tutorial/configure11.png)

## <a name="advanced-configuration"></a>Configuração Avançada

Esta secção destina-se a ser utilizada se não puder utilizar a configuração guiada ou pretender adicionar/modificar parâmetros adicionais. Necessitará de um certificado TLS/SSL para o nome anfitrião da aplicação.

1. Navegue para **a Gestão de Certificados > Sistema > Gestão de Certificados de Tráfego > Lista de Certificados SSL**. **Selecione Importar** do canto direito. **O Tipo de Importação** será **PKCS 12(IIS)**. Especifique um **Nome chave** (será referenciado mais tarde no config) e o especifique o ficheiro PFX. Especifique a **palavra-passe** para o PFX. Clique **em Importar.**

    >[!NOTE]
    >No exemplo, o nome da nossa aplicação `Headerapp.superdemo.live` é, estamos a usar um Certificado wild card que o nosso nome de chave é `WildCard-SuperDemo.live` .
  
    ![A screenshot mostra a página S S L Certificate/Key Source para configuração avançada.](./media/headerf5-tutorial/configure17.png)

### <a name="adding-a-new-web-server-to-bigip-f5"></a>Adicionar um novo Servidor Web ao BigIP-F5

1. Clique em **IApps > > Serviços de aplicação > Principais > > Criar**.

1. Forneça o **Nome** e em **Modelo** escolha **f5.http**.
 
    ![A screenshot mostra a página de Serviços de Aplicação com a Seleção de Modelos.](./media/headerf5-tutorial/configure18.png)

1. Publicaremos o nosso HeaderApp2 externamente como HTTPS neste caso, **como deve o sistema BIG-IP lidar com o Tráfego SSL**? especificamos **Terminate SSL de Cliente, Texto Simples para servidores (SSL Offload)**. Especificar o certificado e a chave sob **qual certificado SSL pretende utilizar e** qual a chave privada **SSL que pretende utilizar?** Especificar o IP do Servidor Virtual em **Que Endereço IP pretende utilizar para o Servidor Virtual?** 

    * **Especificar outros detalhes**

        * FQDN  

        * Especifique sair do pool de aplicativos ou criar um novo.

        * Se criar um novo Servidor de Aplicações especifique **o endereço IP interno** e o número de **porta.**

        ![A screenshot mostra o painel onde pode especificar estes detalhes.](./media/headerf5-tutorial/configure19.png) 

1. Clique **em Terminado**.

    ![A screenshot mostra a página após a conclusão.](./media/headerf5-tutorial/configure20.png) 

1. Certifique-se de que as Propriedades da Aplicação podem ser modificadas. Clique **em Principais > IApps > Serviços de Aplicação: Aplicações >> HeaderApp2**. Desmarque **atualizações estritas** (modificaremos alguma definição fora do GUI). Clique no botão **'Actualizar'.**

    ![A screenshot mostra a página serviços de aplicação com o separador Propriedades selecionado.](./media/headerf5-tutorial/configure21.png) 

1. Neste momento deverá ser capaz de navegar no Servidor Virtual.

### <a name="configuring-f5-as-sp-and-azure-as-idp"></a>Configurar f5 como SP e Azure como IDP

1.  Clique **em Access > Federation> O Fornecedor de Serviços SAML > Serviço SP Local > clique para criar ou + assinar**.

    ![A screenshot mostra a página sobre o BIG I P. ](./media/headerf5-tutorial/configure22.png)

1. Especifique detalhes para o Serviço de Prestadores de Serviços. Especifique **o nome** que representa a configuração F5 SP. Especificar **o ID da entidade** (geralmente o mesmo que o URL da aplicação).

    ![A screenshot mostra a página do Fornecedor de Serviços SAML com a caixa de diálogo de serviço Create New SAML S P.](./media/headerf5-tutorial/configure23.png)

    ![A screenshot mostra a caixa de diálogo de serviço Create New SAML S P com definições de ponto final selecionadas.](./media/headerf5-tutorial/configure24.png)

    ![A screenshot mostra a caixa de diálogo de serviço Create New SAML S P com definições de segurança selecionadas.](./media/headerf5-tutorial/configure25.png)

    ![A screenshot mostra a caixa de diálogo de serviço Create New SAML S P com o Contexto de Autenticação selecionado.](./media/headerf5-tutorial/configure26.png)

    ![A screenshot mostra a caixa de diálogo de serviço Create New SAML S P com atributos solicitados selecionados.](./media/headerf5-tutorial/configure27.png)

    ![A screenshot mostra a caixa de diálogo de serviço Edit SAML S P com Definições Avançadas selecionadas.](./media/headerf5-tutorial/configure28.png)

### <a name="create-idp-connector"></a>Criar Conector Idp

1. Clique no botão **Conectores IdP Bind/Unbind,** selecione **Criar novo conector IdP** e escolha A partir da opção **Metadados** e, em seguida, execute os seguintes passos:
 
    ![A screenshot mostra o Edit SAML I d Ps que utiliza esta caixa de diálogo S P com o Create New I d P Connector selecionado.](./media/headerf5-tutorial/configure29.png)

    a. Navegue para metadata.xml ficheiro descarregado a partir de Azure AD e especifique um **Nome de Fornecedor de Identidade**.

    b. Clique **ok**.

    c. O conector é criado e o certificado está pronto automaticamente a partir do ficheiro xml de metadados.
    
    ![A screenshot mostra a caixa de diálogo Create New SAML I d P Connector.](./media/headerf5-tutorial/configure30.png)

    d. Configure f5BIG-IP para enviar todo o pedido para Azure AD.

    e. Clique **em Adicionar Nova Linha,** escolha **AzureIDP** (como criado em passos anteriores, especifique 

    f. **Fonte de correspondência = %{session.server.landinguri}** 

    exemplo, **Valor correspondente = /** _

    h. Clique em _ *atualização**

    i. Clique **em OK**

    j. **A configuração do IDP SAML está concluída**
    
    ![A screenshot mostra o Edit SAML I d Ps que utilizador desta caixa de diálogo S P.](./media/headerf5-tutorial/configure31.png)

### <a name="configure-f5-policy-to-redirect-users-to-azure-saml-idp"></a>Configure a Política F5 para redirecionar os utilizadores para o IDP Azure SAML

1. Para configurar a Política F5 para redirecionar os utilizadores para o IDP Azure SAML, execute os seguintes passos:

    a. Clique **em > Principais De acesso > perfil/políticas > perfis de acesso**.

    b. Clique no botão **Criar.**

    ![A screenshot mostra a página de Perfis de Acesso.](./media/headerf5-tutorial/configure32.png)
 
    c. Especifique **o nome** (HeaderAppAzureSAMLPolicy no exemplo).

    d. Pode personalizar outras definições por favor consulte a Documentação F5.

    ![A screenshot mostra a página Propriedades Gerais.](./media/headerf5-tutorial/configure33.png)

    ![A screenshot mostra que a página "Propriedades Gerais" continuou.](./media/headerf5-tutorial/configure34.png) 

    e. Clique **em Terminado**.

    f. Assim que a criação da Política estiver concluída, clique na Política e vá ao Separador política de **acesso.**

    ![A screenshot mostra o separador Política de Acesso com Propriedades Gerais.](./media/headerf5-tutorial/configure35.png)
 
    exemplo, Clique no **editor de Política Visual,** edite a Política de Acesso para link **profile.**

    h. Clique no + Iniciar no editor de Política Visual e escolha **SAML Auth**.

    ![A screenshot mostra uma política de acesso.](./media/headerf5-tutorial/configure36.png)

    ![A screenshot mostra uma caixa de diálogo de pesquisa com SAML Auth selecionada.](./media/headerf5-tutorial/configure37.png)
 
    i. Clique **em Adicionar Item**.

    j. Em **Propriedades** especifiquem **nome** e em **servidor AAA** selecione o SP previamente configurado, clique em **SAVE**.
 
    ![A screenshot mostra as propriedades do item, incluindo o seu servidor A A A.](./media/headerf5-tutorial/configure38.png)

    k. A Política Básica está pronta para personalizar a política para incorporar fontes/lojas de atributos adicionais.

    ![A screenshot mostra a política personalizada.](./media/headerf5-tutorial/configure39.png)
 
    l. Certifique-se de clicar no link **'Aplicar a Política de Acesso'** no topo.

### <a name="apply-access-profile-to-the-virtual-server"></a>Aplicar o Perfil de Acesso ao Servidor Virtual

1. Atribua o perfil de acesso ao Servidor Virtual para que f5 BIG-IP APM aplique as definições de perfil no tráfego de entrada e execute a política de acesso previamente definida.

    a. Clique **nos**  >  **principais**  >  **servidores virtuais de** tráfego local.

    ![A screenshot mostra a página lista de servidores virtuais.](./media/headerf5-tutorial/configure40.png)
 
    b. Clique no servidor virtual, desloque-se para a secção **Política de Acesso,** na queda do **perfil de acesso** e selecione a Política SAML criada (no exemplo HeaderAppAzureSAMLPolicy)

    c. Clique **na atualização**
 
    ![A screenshot mostra o painel de Política de Acesso.](./media/headerf5-tutorial/configure41.png)

    d. criar um F5 BIG-IP iRule® extrair os atributos SAML personalizados da afirmação de entrada e passá-los como cabeçalhos HTTP para a aplicação de teste de backend. Clique **em > Principal > iRules > lista iRule > clique para criar**

    ![A screenshot mostra a lista local de iRule de tráfego.](./media/headerf5-tutorial/configure42.png)
 
    e. Cole o texto F5 BIG-IP iRule abaixo na janela Definição.

    ![A screenshot mostra a página new iRule.](./media/headerf5-tutorial/configure43.png)
 
    quando RULE_INIT { definir estática::d ebug 0 } quando ACCESS_ACL_ALLOWED {

    definir AZUREAD_USERNAME [ACCESS::s session data get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name "] if { $static::d ebug} { log local0. "AZUREAD_USERNAME = $AZUREAD_USERNAME" } se { . [HTTP::header existe "AZUREAD_USERNAME"]] } HTTP:inserção de cabeçalho "AZUREAD_USERNAME" $AZUREAD_USERNAME }

    definir AZUREAD_DISPLAYNAME [ACCESS::s session data get "session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/displayname "] if { $static::d ebug } { log local0. "AZUREAD_DISPLAYNAME = $AZUREAD_DISPLAYNAME" } se { . [HTTP::header existe "AZUREAD_DISPLAYNAME"]] } HTTP:inserção de cabeçalho "AZUREAD_DISPLAYNAME" $AZUREAD_DISPLAYNAME }

    definir AZUREAD_EMAILADDRESS [ACCESS::s session data get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress "] if { $static::d ebug} { log local0. "AZUREAD_EMAILADDRESS = $AZUREAD_EMAILADDRESS" } se { . [HTTP::header existe "AZUREAD_EMAILADDRESS"]] } HTTP:INSerção de cabeçalho "AZUREAD_EMAILADDRESS" $AZUREAD_EMAILADDRESS }}

    **Produção de amostra abaixo**

    ![A imagem mostra a saída da amostra.](./media/headerf5-tutorial/configure44.png)
 
### <a name="create-f5-test-user"></a>Criar utilizador de teste F5

Nesta secção, cria-se um utilizador chamado B.Simon em F5. Trabalhe com [a equipa de suporte do Cliente F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para adicionar os utilizadores na plataforma F5. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação. 

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo F5 no Painel de Acesso, deverá ser automaticamente inscrito no F5 para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente F5 com Azure AD](https://aad.portal.azure.com/)

- [Configurar f5 único sinal para a aplicação Kerberos](kerbf5-tutorial.md)

- [Configurar f5 único sinal para aplicação Kerberos Avançada](advance-kerbf5-tutorial.md)

- [F5 BIG-IP APM e Ad AD Azure para acesso híbrido seguro](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-integration)

- [Tutorial para implementar F5 BIG-IP Virtual Edition VM em Azure IaaS para acesso híbrido seguro](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-bigip-deployment-guide)

- [Tutorial para integração única de sign-on do Azure Ative Directory com F5 BIG-IP para VPN sem palavra-passe](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-password-less-vpn) 
