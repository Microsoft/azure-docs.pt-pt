---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com f5 [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o F5.
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
ms.openlocfilehash: a1f06b0b5aa59328d2fe39d501cfdf3ad7524427
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75431463"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com F5

Neste tutorial, você vai aprender a integrar F5 com o Azure Ative Directory (Azure AD). Quando integrar F5 com AD Azure, pode:

* Controlo em Azure AD que tem acesso a F5.
* Permita que os seus utilizadores sejam automaticamente inscritos no F5 com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

* A subscrição ativada por um único sinal (SSO) f5.

* A implementação da solução conjunta requer a seguinte licença:
    * F5 BIG-IP® Melhor pacote (ou)

    * F5 BIG-IP Access Policy Manager™ (APM) licença autónoma

    * F5 BIG-IP Access Policy Manager™ (APM) licença de adição numa licença de big-ip f5 BIG-IP existente® Gerente de Tráfego Local™ (LTM).

    * Além da licença acima, o sistema F5 também pode ser licenciado com:

        * Uma subscrição de filtragem de URL para usar a base de dados da categoria URL

        * Uma subscrição da F5 IP Intelligence para detetar e bloquear atacantes conhecidos e tráfego malicioso

        * Um módulo de segurança de hardware de rede (HSM) para salvaguardar e gerir chaves digitais para autenticação forte

* O sistema F5 BIG-IP é aprovisionado com módulos APM (LTM é opcional)

* Embora opcional, é altamente recomendado implantar os sistemas F5 num grupo de [dispositivos de sincronização/falha](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG), que inclui o par de espera ativo, com um endereço IP flutuante para alta disponibilidade (HA). A redundância da interface adicional pode ser conseguida utilizando o Protocolo de Controlo de Agregação de Ligações (LACP). Lacp gere as interfaces físicas conectadas como uma única interface virtual (grupo agregado) e deteta quaisquer falhas de interface dentro do grupo.

* Para aplicações kerberos, uma conta de serviço ad no local para a delegação restrita.  Consulte a [Documentação F5](https://support.f5.com/csp/article/K43063049) para a criação de uma conta de delegação da AD.

## <a name="access-guided-configuration"></a>Acesso à configuração guiada

* A configuração guiada de acesso é suportada na versão F5 TMOS 13.1.0.8 e acima. Se o seu sistema BIG-IP estiver a executar uma versão abaixo de 13.1.0.8, consulte a secção de **configuração Avançada.**

* A configuração guiada de acesso apresenta uma experiência de utilizador completamente nova e simplificada. Esta arquitetura baseada em fluxo de trabalho fornece passos de configuração intuitivos e re-participantes adaptados à topologia selecionada.

* Antes de proceder à configuração, atualize a configuração guiada, baixando o mais recente pacote de casos de utilização a partir de [downloads.f5.com](https://login.f5.com/resource/login.jsp?ctx=719748). Para fazer upgrade, siga o procedimento abaixo.

    >[!NOTE]
    >As imagens abaixo são para a versão mais recente lançada (BIG-IP 15.0 com a versão AGC 5.0). Os passos de configuração abaixo são válidos para este caso de utilização de 13.1.0.8 para a versão MAIS recente big-IP.

1. No F5 BIG-IP Web UI, clique na **Configuração do Guia de Acesso >> **.

2. Na página de **Configuração Guiada,** clique na **configuração guiada** de upgrade no canto superior esquerdo.

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure14.png) 

3. No ecrã pop de configuração do guia de atualização, **selecione Escolha o Ficheiro** para carregar o pacote de casos de utilização descarregado e clique no botão Upload e **Instalar.**

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure15.png) 

4. Quando a atualização estiver concluída, clique no botão **Continuar.**

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* F5 suporta **SP e IDP** iniciadoS SSO
* F5 SSO pode ser configurado de três maneiras diferentes.

- [Configure f5 único sinal para candidatura kerberos](#configure-f5-single-sign-on-for-kerberos-application)

- [Configure o único sinal de F5 para a aplicação baseada em cabeçalho](headerf5-tutorial.md)

- [Configure F5 único sign-on para aplicação Kerberos Avançada](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Cenários chave de autenticação

Para além do suporte de integração nativa do Azure Ative Diretório para protocolos modernos de autenticação como Open ID Connect, SAML e WS-Fed, f5 alarga acesso seguro para aplicações de autenticação baseadas em legados para acesso interno e externo com AD Azure, permitindo cenários modernos (por exemplo, acesso sem palavra-passe) a estas aplicações. Isto inclui:

* Aplicativos de autenticação baseados em cabeçalho

* Apps de autenticação Kerberos

* Autenticação anónima ou sem aplicações de autenticação incorporadas

* Aplicações de autenticação NTLM (proteção com instruções duplas para o utilizador)

* Aplicação baseada em formulários (proteção com instruções duplas para o utilizador)

## <a name="adding-f5-from-the-gallery"></a>Adicionando F5 da galeria

Para configurar a integração de F5 em Azure AD, você precisa adicionar F5 da galeria à sua lista de aplicações SaaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, **digite F5** na caixa de pesquisa.
1. Selecione **F5** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Configure e teste Azure AD único signo para F5

Configure e teste Azure AD SSO com F5 utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em F5.

Para configurar e testar o Azure AD SSO com F5, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure F5 SSO](#configure-f5-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. Criar o utilizador de **[teste F5](#create-f5-test-user)** - para ter uma contraparte de B.Simon em F5 que esteja ligada à representação azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **F5,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<YourCustomFQDN>.f5.com/`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<YourCustomFQDN>.f5.com/`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta real e URL de sinalização. Contacte a equipa de apoio ao [cliente F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sessão com a página **SAML,** na secção certificado de **assinatura SAML,** encontre **metadados da Federação XML** e **Certificado (Base64)** e, em seguida, selecione **Download** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção **Deconfiguração de F5,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao F5.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **F5**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**
1. Clique no **Acesso Condicional** .
1. Clique em **Nova Política**.
1. Pode agora ver a sua App F5 como um recurso para a Política de CA e aplicar qualquer acesso condicional, incluindo Multifactor Auth, controlo de acesso baseado em dispositivos ou Política de Proteção de Identidade.

## <a name="configure-f5-sso"></a>Configure F5 SSO

- [Configure o único sinal de F5 para a aplicação baseada em cabeçalho](headerf5-tutorial.md)

- [Configure F5 único sign-on para aplicação Kerberos Avançada](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-kerberos-application"></a>Configure f5 único sinal para candidatura kerberos

### <a name="guided-configuration"></a>Configuração Guiada

1. Abra uma nova janela do navegador web e inscreva-se no site da sua empresa F5 (Kerberos) como administrador e execute os seguintes passos:

1. Terá de importar o Certificado de Metadados para o F5, que será utilizado mais tarde no processo de configuração.

1. Navegar para sistema > Gestão de Certificados > Gestão de Certificados de Tráfego > Lista de **Certificados SSL.** Selecione **Importar** a partir do canto direito. Especifique um nome de **certificado** (será referenciado mais tarde no config). Na Fonte de **Certificado,** selecione Upload File especifique o certificado descarregado do Azure enquanto configura o SAML Single Sign on. Clique **em Importar**.

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure01.png) 

1. Além disso, irá requerer **o Certificado SSL para o nome de anfitrião da aplicação. Navegar para sistema > Gestão de Certificados > Gestão de Certificados de Tráfego > Lista de Certificados SSL.** Selecione **Importar** a partir do canto direito. **O Tipo de Importação** será **PKCS 12 (IIS)**. Especifique um **nome-chave** (será referenciado mais tarde no config) e especificar o ficheiro PFX. Especifique a **palavra-passe** para o PFX. Clique **em Importar**.

    >[!NOTE]
    >No exemplo do nosso `Kerbapp.superdemo.live`nome de aplicativo é, estamos usando um Certificado wild card o nosso nome-chave é`WildCard-SuperDemo.live`

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure02.png) 
 
1. Usaremos a Experiência Guiada para configurar a Federação De AD Azure e o Acesso à Aplicação. Vá a – F5 BIG-IP **Main** e selecione **Access > Guided Configuração > Federação > Prestador de Serviços SAML**. Clique **em Seguinte** e clique em **Next** para iniciar a configuração.

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure03.png) 

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure04.png)

1. Forneça um nome de **configuração**. Especifique o **ID da Entidade** (o mesmo que configurado na configuração da aplicação AD Azure). Especifique o **nome anfitrião**. Adicione uma **descrição** para referência. Aceite as entradas predefinidas restantes e selecione e, em seguida, clique em **Guardar & Seguinte**.

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure05.png) 

1. Neste exemplo estamos a criar um novo Servidor Virtual como 192.168.30.200 com porta 443. Especifique o endereço IP do servidor virtual no **endereço destino**. Selecione o **Perfil SSL**do Cliente, selecione Criar novo. Especifique o certificado de pedido previamente carregado (o certificado de cartão selvagem neste exemplo) e a chave associada, e, em seguida, clique em **Guardar & Seguinte**.

    >[!NOTE]
    >neste exemplo, o nosso webserver interno está a funcionar na porta 80 e queremos publicá-lo com 443.

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure06.png)

1. No **método Select para configurar o seu conector IDP,** especifique Metadados, clique no Ficheiro Escolha e carregue o ficheiro Metadata XML descarregado anteriormente a partir do Azure AD. Especifique um **nome** único para o conector IDP SAML. Escolha o Certificado de Assinatura de **Metadados** que foi carregado anteriormente. Clique em **Guardar & Seguinte**.

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure07.png)  

1. Em **Select a Pool,** especifique **Criar Novo** (em alternativa, selecione uma piscina que já existe). Deixe que outro valor seja predefinido. Em servidores de pool, digite o endereço IP em nome ip **endereço/nó**. Especifique a **Porta**. Clique em **Guardar & Seguinte**.
 
    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure08.png)

1. No ecrã de definições de sinal único, **selecione Ativar um único sinal .** Em **"Single Sign-On Type" selecionado,** escolha **Kerberos**. Substitua a **session.saml.last.Identity** por **session.saml.last.attr.name.name.Identity** under **Username Source** ( esta variável que definiu utilizando o mapeamento de sinistros no Azure AD ). Selecione **Mostrar Definição Avançada**. Em **Kerberos Realm** digite o Nome de Domínio. Em **nome da conta/ Palavra-passe da conta** Especifique a Conta de Delegação APM e a Palavra-passe. Especifique o IP do Controlador de Domínio no campo **KDC.** Clique em **Guardar & Seguinte**.

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure09.png)   

1. Para efeitos desta orientação, vamos ignorar as verificações de pontos finais.  Consulte a documentação de F5 para mais detalhes.  No ecrã, selecione **Save & Next**.

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure10.png) 

1. Aceite as predefinições e clique em **Guardar & Seguinte**. Consulte a documentação f5 para obter detalhes sobre as definições de gestão de sessões Da SAML.


    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure11.png) 
 
1. Reveja o ecrã resumo e selecione **Implementar** para configurar o BIG-IP.
 
    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure12.png)

1. Uma vez configurada a aplicação clique em **Terminar**.

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure13.png)

## <a name="advanced-configuration"></a>Configuração Avançada

>[!NOTE]
>Para clicar em referência [aqui](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

### <a name="configuring-an-active-directory-aaa-server"></a>Configurar um servidor AAA de Diretório Ativo

Configura um servidor AAA do Diretório Ativo no Access Policy Manager (APM) para especificar controladores de domínio e credenciais para a APM utilizar para autenticar utilizadores.

1.  No separador Principal, clique na **Política de Acesso > Servidores AAA > Diretório Ativo**. O ecrã da lista de servidores de diretórios ativos abre.

2.  Clique em **Criar**. O ecrã de propriedades do Novo Servidor abre.

3.  No campo **Nome,** digite um nome único para o servidor de autenticação.

4.  No campo Nome do **Domínio,** digite o nome do domínio Windows.

5.  Para a definição de Ligação ao **Servidor,** selecione uma destas opções:

    * Selecione **Use Pool** para configurar uma alta disponibilidade para o servidor AAA.

    * Selecione **Direct** para configurar o servidor AAA para funcionalidade autónoma.

6.  Se selecionar **Direct,** escreva um nome no campo controlador de **domínio.**

7.  Se selecionou Use **Pool,** configure a piscina:

    * Digite um nome no campo nome do controlador de **domínio.**

    * Especifique os **Controladores** de Domínio na piscina digitando o endereço IP e o nome do anfitrião para cada um, e clicando no botão **Adicionar.**

    * Para monitorizar a saúde do servidor AAA, tem a opção de selecionar um monitor de saúde: apenas o monitor **gateway_icmp** é apropriado neste caso; pode selecioná-lo a partir da lista de Monitor de Piscina do **Servidor.**

8.  No campo **Nome do Administrador,** o tipo A é um nome sensível ao caso para um administrador que tenha permissões administrativas de Diretório Ativo. A APM utiliza a informação nos campos **de nome de administrador** e de **palavra-passe de administrador** para consulta de anúncios. Se o Diretório Ativo estiver configurado para consultas anónimas, não precisa de fornecer um Nome De Administrador. Caso contrário, a APM necessita de uma conta com privilégios suficientes para se ligar a um servidor de Diretório Ativo, buscar informações do grupo de utilizadores e obter políticas de palavra-passe do Ative Directory para suportar a funcionalidade relacionada com a palavra-passe. (APM deve obter políticas de senha, por exemplo, se selecionar o utilizador Do Prompt para alterar a palavra-passe antes de expirar a opção numa ação de Consulta AD.) Se não fornecer informações sobre a conta Admin nesta configuração, a APM utiliza a conta de utilizador para obter informações. Isto funciona se a conta de utilizador tiver privilégios suficientes.

9.  No campo **'Palavra-passe' do Administrador,** digite a palavra-passe do administrador associada ao Nome de Domínio.

10. No campo **'Ver' 'Password',** reescreva a palavra-passe do administrador associada à definição **de Nome de Domínio.**

11. No campo De Vida do **Grupo Cache,** digite o número de dias. A vida padrão é de 30 dias.

12. No campo Cache Lifetime do **Objeto de Segurança de Password,** digite o número de dias. A vida padrão é de 30 dias.

13. A partir da lista de tipo de **encriptação pré-autenticação Kerberos,** selecione um tipo de encriptação. O padrão é **Nenhum**. Se especificar um tipo de encriptação, o sistema BIG-IP inclui dados de pré-autenticação Kerberos no pacote do primeiro pedido de serviço de autenticação (AS-REQ).

14. No campo **Timeout,** digite um intervalo de tempo (em segundos) para o servidor AAA. (Esta definição é opcional.)

15. Clique em **Terminar**. O novo servidor aparece na lista. Isto adiciona o novo servidor de Diretório Ativo à lista de Servidores de Directórioactivo Ativo.

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure17.png)

### <a name="saml-configuration"></a>Configuração SAML

1. Terá de importar o Certificado de Metadados para o F5, que será utilizado mais tarde no processo de configuração. Navegar para sistema > Gestão de Certificados > Gestão de Certificados de Tráfego > Lista de **Certificados SSL.** Selecione **Importar** a partir do canto direito.

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure18.png)

2. Para configurar o IDP SAML, **navegue para aceder ao Access > Federation > SAML: Prestador de serviços > Conectores externos de IDP,** e clique em **Criar > a partir de metadados**.

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure19.png)

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure20.png)

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure21.png)

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure22.png)

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure23.png)

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure24.png)

1. Para configurar o SAML SP, navegue para **aceder à Federação > de Acesso > prestador de serviços SAML > Serviços SP Locais** e clique em **Criar**. Complete as seguintes informações e clique **em OK**.

    * Nome do tipo: KerbApp200SAML
    * Id da entidade*:https://kerbapp200.superdemo.live
    * Definições de nome SP
    * Esquema: https
    * Anfitrião: kerbapp200.superdemo.live
    * Descrição: kerbapp200.superdemo.live

     ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure25.png)

     b. Selecione a configuração SP, KerbApp200SAML e clique em **ligar/unBind IdP Conectores**.

     ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure26.png)

     ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure27.png)

     c. Clique em **Adicionar Nova Linha** e selecione o **conector IdP externo** criado em passo anterior, clique em **'Actualizar'** e, em seguida, clique **em OK**.

     ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure28.png)

1. Para configurar kerberos SSO, navegue para **aceder > signon single > Kerberos,** complete informações e clique **em Terminado**.

    >[!Note]
    > Necessitará da Conta de Delegação Kerberos para ser criada e especificada. Consulte a secção KCD (Consulte o apêndice para referências variáveis)

    * **Nome de utilizador Fonte**: session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname

    * **Fonte do Reino do Utilizador**: session.logon.last.domain

        ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure29.png)

1. Para configurar o Perfil de Acesso, navegue para aceder ao **> Perfil/Políticas > Perfil de Acesso (por política de sessão)** clique em **Criar,** complete as seguintes informações e clique **em Terminar**.

    * Nome: KerbApp200
    * Tipo de perfil: Todos
    * Âmbito de perfil: Perfil
    * Línguas: Inglês

        ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure30.png)

1. Clique no nome, KerbApp200, complete as seguintes informações e clique em **Atualizar**.

    * Cookie de domínio: superdemo.live
    * Configuração SSO: KerAppSSO_sso

        ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure31.png)

1. Clique na Política de **Acesso** e clique em **Editar a Política** de Acesso para Perfil "KerbApp200".

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure32.png)

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure33.png)

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure34.png)

    * **session.logon.last.usernameUPN expr {[mcget {session.saml.last.identity}}}}**

    * **session.ad.lastrealdomínio TEXT superdemo.live**

        ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure35.png)

    * **(userPrincipalName=%{session.logon.last.usernameUPN})**

        ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure36.png)

        ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure37.png)

    * **session.logon.last.username expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }**

        ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure38.png)

    * **mcget {session.logon.last.username}**
    * **mcget {session.logon.last.password**

1. Para adicionar novo nó, navegue para os **nódosos de tráfego local > > lista de nó, clique em Criar,** complete as seguintes informações e, em seguida, clique em **Terminado**.

    * Nome: KerbApp200
    * Descrição: KerbApp200
    * Endereço: 192.168.200

        ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure39.png)

1. Para criar um novo Pool, navegue para **piscinas locais > piscinas > Pool List, clique em Criar,** complete as seguintes informações e clique **em Terminar**.

    * Nome: KerbApp200-Pool
    * Descrição: KerbApp200-Pool
    * Monitores de Saúde: http
    * Endereço: 192.168.200
    * Porta de serviço: 81

        ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure40.png)

1. Para criar o Servidor Virtual, navegue para os **servidores virtuais > De tráfego local > Lista de Servidores Virtuais > +**, complete as seguintes informações e clique **em Terminado**.

    * Nome: KerbApp200
    * Endereço/Máscara de Destino: Anfitrião 192.168.30.200
    * Porta de serviço: Porta 443 HTTPS
    * Perfil de acesso: KerbApp200
    * Especificar o perfil de acesso criado em passo anterior

        ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure41.png)

        ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure42.png)

### <a name="setting-up-kerberos-delegation"></a>Criação da Delegação Kerberos 

>[!NOTE]
>Para clicar em referência [aqui](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

*  **Passo 1:** Criar uma Conta de Delegação

    **Exemplo:**
    * Nome de domínio: **superdemo.live**

    * Nome da conta Sam: **big-ipuser**

    * New-ADUser -Name "APM Delegation Account" host/big-ipuser.superdemo.live@superdemo.live -UserPrincipalName -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")

* **Passo 2:** Definir SPN (na Conta de Delegação APM)

    **Exemplo:**
    * setspn - Um **anfitrião/big-ipuser.superdemo.live** big-ipuser

* **Passo 3:** A Delegação SPN (para a conta de serviço de aplicações) criou a delegação adequada para a conta de delegação f5.
    No exemplo abaixo, a conta de delegação da APM está a ser configurada para o KCD para FRP-App1.superdemo. aplicativo ao vivo.

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure43.png)

* Forneça os detalhes mencionados no documento de referência acima referido [seletivo](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html).

### <a name="create-f5-test-user"></a>Criar o utilizador de teste F5

Nesta secção, cria-se um utilizador chamado B.Simon em F5. Trabalhe com a equipa de suporte ao [Cliente F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para adicionar os utilizadores na plataforma F5. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal. 

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo F5 no Painel de Acesso, deve ser automaticamente inscrito no F5 para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente F5 com Azure AD](https://aad.portal.azure.com/)

- [Configure o único sinal de F5 para a aplicação baseada em cabeçalho](headerf5-tutorial.md)

- [Configure F5 único sign-on para aplicação Kerberos Avançada](advance-kerbf5-tutorial.md)

