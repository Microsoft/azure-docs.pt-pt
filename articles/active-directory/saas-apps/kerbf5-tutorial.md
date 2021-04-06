---
title: 'Tutorial: Azure AD integração única com | F5 Microsoft Docs'
description: Saiba como configurar um único sign-on (SSO) entre o Azure Ative Directory e o F5.
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
ms.openlocfilehash: 2fd96c47e98a6f8a66a5fda555304c4448d772e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646112"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Tutorial: Azure Ative Directory integração única (SSO) com F5

Neste tutorial, você vai aprender a integrar F5 com Azure Ative Directory (Azure AD). Quando integrar o F5 com AZure AD, pode:

* Controlo em Azure AD que tem acesso a F5.
* Ative os seus utilizadores a serem automaticamente inscritos no F5 com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

* F5 assinatura única ativada (SSO).

* A implementação da solução comum requer a seguinte licença:
    * F5 BIG-IP &reg; Melhor pacote (ou)

    * Licença autónoma do F5 BIG-IP Access Manager &trade; (APM)

    * Licença de complemento de informação de acesso BIG-IP &trade; (APM) de um gestor de tráfego local BIG-IP EXISTENTE BIG-IP F5 BIG-IP &reg; &trade; (LTM).

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

2. Na página **de Configuração Guiada,** clique na **Configuração Guiada de Atualização** no canto superior esquerdo.

    ![Screenshot que mostra a página "Configuração Guiada" com a ação "Atualização guiada" selecionada.](./media/kerbf5-tutorial/configure14.png) 

3. No ecrã pop da configuração do guia de atualização, **selecione Choose File** para carregar o pacote de casos de utilização descarregado e clique no botão **Upload e Install.**

    ![Screenshot que mostra o ecrã pop-up "Upgrade Guided Configuration" com "Choose File" e "Upload and Install" selecionados.](./media/kerbf5-tutorial/configure15.png) 

4. Quando a atualização estiver concluída, clique no botão **Continuar.**

    ![Screenshot que mostra o diálogo "Atualização de Configuração Guiada está completo" e o botão "Continue" selecionado.](./media/kerbf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* F5 apoia **SP e IDP** iniciado SSO
* F5 SSO pode ser configurado de três maneiras diferentes.

- [Configurar f5 único sinal para a aplicação Kerberos](#configure-f5-single-sign-on-for-kerberos-application)

- [Configurar f5 único sinal para aplicação baseada em cabeçalho](headerf5-tutorial.md)

- [Configurar f5 único sinal para aplicação Kerberos Avançada](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Principais cenários de autenticação

Para além do suporte de integração nativa do Azure Ative Directory para protocolos modernos de autenticação como Open ID Connect, SAML e WS-Fed, o F5 alarga o acesso seguro a aplicações de autenticação baseadas em legados para acesso interno e externo com Azure AD, permitindo cenários modernos (por exemplo, acesso sem palavra-passe) a estas aplicações. Isto inclui:

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

1. Na **configuração de um único sinal com** a página SAML, na secção Certificado de Assinatura **SAML,** encontre o **Metadados XML** e o Certificado da Federação **(Base64)** e, em seguida, selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

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

- [Configurar f5 único sinal para aplicação baseada em cabeçalho](headerf5-tutorial.md)

- [Configurar f5 único sinal para aplicação Kerberos Avançada](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-kerberos-application"></a>Configurar f5 único sinal para a aplicação Kerberos

### <a name="guided-configuration"></a>Configuração guiada

1. Abra uma nova janela do navegador web e inscreva-se no seu site da empresa F5 (Kerberos) como administrador e execute os seguintes passos:

1. Terá de importar o Certificado de Metadados para o F5 que será utilizado mais tarde no processo de configuração.

1. Navegue para **a Gestão de Certificados > Sistema > Gestão de Certificados de Tráfego > Lista de Certificados SSL**. **Selecione Importar** do canto direito. Especifique um **Nome de Certificado** (será referenciado mais tarde no config). Na **Fonte** de Certificado , selecione Upload File especificar o certificado descarregado do Azure enquanto configura o Sinal Único DE SAML. Clique **em Importar.**

    ![Screenshot que mostra a página "S S L Certificate/Key Source" com o "Certificate Name" realçado, "Upload File" e o botão "Import" selecionado.](./media/kerbf5-tutorial/configure01.png) 

1. Além disso, irá necessitar de **Certificado SSL para o nome de anfitrião da aplicação. Navegue para a Gestão de Certificados > sistema > Gestão de Certificados de Tráfego > Lista de Certificados SSL**. **Selecione Importar** do canto direito. **O Tipo de Importação** será **PKCS 12(IIS)**. Especifique um **Nome chave** (será referenciado mais tarde no config) e o especifique o ficheiro PFX. Especifique a **palavra-passe** para o PFX. Clique **em Importar.**

    >[!NOTE]
    >No exemplo, o nosso nome de aplicativo `Kerbapp.superdemo.live` é, estamos usando um Certificado wild card o nosso nome de chave é `WildCard-SuperDemo.live`

    ![Screenshot que mostra a página "S S L Certificate/Key Source" com os valores introduzidos e o botão "Importar" selecionado.](./media/kerbf5-tutorial/configure02.png) 
 
1. Utilizaremos a Experiência Guiada para configurar a Federação AD Azure e o Acesso à Aplicação. Vá a – F5 BIG-IP **Main** e selecione **Access > Configuração Guiada > Federação > Prestador de Serviços SAML**. Clique **em Seguinte** e clique em **Seguinte** para iniciar a configuração.

    ![Screenshot que mostra a página "Configuração Guiada" com o ícone "Federação" em destaque e "S A M L Service Provider" selecionado.](./media/kerbf5-tutorial/configure03.png) 

    ![Screenshot que mostra a página "Configuração Guiada - S A M L Service Provider" com o botão "Seguinte" selecionado.](./media/kerbf5-tutorial/configure04.png)

1. Fornecer um **nome de configuração**. Especifique o **ID da Entidade** (o mesmo que configurado na Configuração da Aplicação AD Azure). Especificar o **nome hospedeiro**. Adicione uma **descrição** para referência. Aceite as entradas predefinitivas restantes e selecione e, em seguida, clique em **Guardar & Seguinte**.

    ![Screenshot que mostra as "Propriedades do Fornecedor de Serviço" com caixas de texto "Nome de anfitrião" e "Descrição" realçadas e o botão "Save & Next" selecionado.](./media/kerbf5-tutorial/configure05.png) 

1. Neste exemplo estamos a criar um novo Servidor Virtual como 192.168.30.200 com a porta 443. Especifique o endereço IP do Servidor Virtual no **Endereço destino**. Selecione o **Perfil SSL do** Cliente, selecione Criar novo. Especifique o certificado de inscrição previamente carregado (o certificado wild card neste exemplo) e a chave associada, e, em seguida, clique em **Guardar & Next**.

    >[!NOTE]
    >neste exemplo, o nosso webserver Interno está a funcionar na porta 80 e queremos publicá-lo com 443.

    ![Screenshot que mostra a página "Propriedades do servidor virtual" com a caixa de texto "Destino Address" realçada e o botão "Save & Next" selecionado.](./media/kerbf5-tutorial/configure06.png)

1. No **método Select para configurar o seu conector IdP**, especifique metadados, clique no Choose File e carregufique o ficheiro Metadados XML descarregado anteriormente a partir de Azure AD. Especifique um **nome** único para conector IDP SAML. Escolha o **Certificado de Assinatura de Metadados** que foi carregado anteriormente. Clique **em Guardar & Seguinte**.

    ![Screenshot que mostra a página "Definições de conector de fornecedor de identidade externa" com a caixa de texto "Nome" realçada e o botão "Save & Next" selecionado.](./media/kerbf5-tutorial/configure07.png)  

1. Em **Select a Pool**, especifique Criar **Novo** (em alternativa selecione uma piscina que já existe). Que outro valor seja padrão.    Nos Servidores de Pool, digite o endereço IP no **endereço IP/nome do nó**. Especificar o **Porto.** Clique **em Guardar & Seguinte**.
 
    ![Screenshot que mostra a página "Propriedades da Piscina" com as caixas de texto "IP Address/Node Name" e "Port" destacadas e o botão "Save & Next" selecionado.](./media/kerbf5-tutorial/configure08.png)

1. No ecrã de definições de Sign-On único, selecione **Ative O Sign-On único**. Em **Tipo de Sign-On Único Selecionado** escolha **Kerberos**. Substitua **session.saml.last.Identity**  por **session.saml.last.attr.name.Name** under **Username Source** (esta variável que definiu usando mapeamento de reclamações no AD Azure). Selecione **mostrar definição avançada**. Sob **o reino de Kerberos digite** o Nome de Domínio. No **Nome da Conta/Palavra-Passe de Conta** Especifique a conta e a senha da delegação APM. Especificar o IP do controlador de domínio no campo **KDC.** Clique **em Guardar & Seguinte**.

    ![Screenshot que mostra as "Definições de Sign-On únicas" com caixas de texto realçadas e o botão "Save & Next" selecionado.](./media/kerbf5-tutorial/configure09.png)   

1. Para efeitos desta orientação, saltaremos as verificações de pontos finais.  Consulte a documentação F5 para mais detalhes.  No ecrã **selecione Save & Next**.

    ![Screenshot que mostra a página "Endpoint Checks Properties" e o botão "Save & Next" selecionados.](./media/kerbf5-tutorial/configure10.png) 

1. Aceite as predefinições e clique em **Guardar & Seguinte**. Consulte a documentação F5 para obter detalhes sobre as definições de gestão de sessão SAML.


    ![Screenshot que mostra a página "Definições de tempo" com o botão "Save & Next" selecionado.](./media/kerbf5-tutorial/configure11.png) 
 
1. Reveja o ecrã de resumo e **selecione Implementar** para configurar o BIG-IP.
 
    ![Screenshot que mostra a página "A sua aplicação está pronta para ser implantada" com a secção "Resumo" realçada e o botão "Implementar" selecionado.](./media/kerbf5-tutorial/configure12.png)

1. Uma vez configurada a aplicação clique em **Terminar**.

    ![Screenshot que mostra a página "A sua aplicação está implantada" com o botão "Finish" selecionado.](./media/kerbf5-tutorial/configure13.png)

## <a name="advanced-configuration"></a>Configuração Avançada

>[!NOTE]
>Para referência clique [aqui](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

### <a name="configuring-an-active-directory-aaa-server"></a>Configurar um servidor AAA do Ative Directory

Configura um servidor AAA do Diretório Ativo no Access Policy Manager (APM) para especificar controladores de domínio e credenciais para APM para utilizar para autenticar utilizadores.

1. No separador Principal, clique em **Política de Acesso > servidores AAA > Ative Directory**. O ecrã da lista de servidores de diretório ativo abre.

2. Clique em **Criar**. O ecrã de propriedades do Novo Servidor abre.

3. No campo **Nome,** escreva um nome único para o servidor de autenticação.

4. No campo **Nome de Domínio,** digite o nome do domínio Windows.

5. Para a definição **de Ligação do Servidor,** selecione uma destas opções:

   * Selecione **Use Pool** para configurar uma alta disponibilidade para o servidor AAA.

   * Selecione **Diretamente** para configurar o servidor AAA para uma funcionalidade autónoma.

6. Se selecionar **Diretamente,** digite um nome no campo **Controlador de Domínio.**

7. Se selecionou Use **Pool,** configuure a piscina:

   * Digite um nome no campo **Nome do Conjunto do Controlador de Domínio.**

   * Especifique os **controladores de domínio** na piscina digitando o endereço IP e o nome de anfitrião para cada um, e clicando no botão **Adicionar.**

   * Para monitorizar a saúde do servidor AAA, tem a opção de selecionar um monitor de saúde: apenas o monitor **gateway_icmp** é adequado neste caso; pode selecioná-lo na lista do **Monitor do Pool do Servidor.**

8. No campo **Nome Admin,** o tipo a é nome sensível a casos para um administrador que tenha permissões administrativas do Diretório Ativo. A APM utiliza as informações nos campos **nome de administração** e **palavra-passe** de administrador para consulta de AD. Se o Ative Directory estiver configurado para consultas anónimas, não precisa de fornecer um Nome Dedmin. Caso contrário, a APM necessita de uma conta com privilégio suficiente para se ligar a um servidor ative Directory, recolher informações do grupo de utilizador e obter políticas de palavra-passe do Ative Directory para suportar funcionalidades relacionadas com palavras-passe. (APM deve obter políticas de palavra-passe, por exemplo, se selecionar o utilizador Prompt para alterar a palavra-passe antes da opção de expiração numa ação de consulta de AD.) Se não fornecer informações sobre a conta de Administra nesta configuração, a APM utiliza a conta de utilizador para obter informações. Isto funciona se a conta de utilizador tiver privilégios suficientes.

9. No campo **palavra-passe** de administrador, digite a palavra-passe do administrador associada ao Nome do Domínio.

10. No campo **Palavra-Passe De Administração Verificar,** retipe a palavra-passe do administrador associada à definição **de Nome de Domínio.**

11. No campo **Grupo Cache Lifetime,** digite o número de dias. A vida útil padrão é de 30 dias.

12. No campo **Cache Lifetime do Objeto de Segurança palavra-passe,** digite o número de dias. A vida útil padrão é de 30 dias.

13. A partir da lista **do Tipo de Encriptação pré-autorização de Kerberos,** selecione um tipo de encriptação. O padrão é **Nenhum**. Se especificar um tipo de encriptação, o sistema BIG-IP inclui dados de pré-autorização kerberos dentro do primeiro pacote de pedido de serviço de autenticação (AS-REQ).

14. No campo **Timeout,** digite um intervalo de tempo (em segundos) para o servidor AAA. (Esta definição é opcional.)

15. Clique **em Terminado**. O novo servidor aparece na lista. Isto adiciona o novo servidor Ative Directory à lista de Servidores de Diretório Ativo.

    ![Screenshot que mostra as secções "Propriedades Gerais" e "Configuração".](./media/kerbf5-tutorial/configure17.png)

### <a name="saml-configuration"></a>Configuração SAML

1. Terá de importar o Certificado de Metadados para o F5 que será utilizado mais tarde no processo de configuração. Navegue para **a Gestão de Certificados > Sistema > Gestão de Certificados de Tráfego > Lista de Certificados SSL**. **Selecione Importar** do canto direito.

    ![Screenshot que mostra a página "Import S S L Certificate/Key Source" com o botão "Import" selecionado.](./media/kerbf5-tutorial/configure18.png)

2. Para configurar o IDP SAML, **navegue para Access > Federation > SAML: Service Provider > External Idp Connectors**, e clique em **Criar > A partir de Metadados**.

    ![Screenshot que mostra a página "S A M L Service Provider" com "From Metadata" selecionada a partir do drop-down "Create".](./media/kerbf5-tutorial/configure19.png)

    ![Screenshot que mostra o diálogo "Create New S A M L L I d P Connector".](./media/kerbf5-tutorial/configure20.png)

    ![Screenshot que mostra a janela "Editar S A M L I d P Connector" com "Definições Gerais" selecionada.](./media/kerbf5-tutorial/configure21.png)

    ![Screenshot que mostra a janela "Editar S A M L I d P Connector" com "Single Sign On Service Settings" selecionada.](./media/kerbf5-tutorial/configure22.png)

    ![Screenshot que mostra a janela "Editar S A M L I d P Connector" com "Definições de Segurança" selecionada.](./media/kerbf5-tutorial/configure23.png)

    ![Screenshot que mostra a janela "Editar S A M L I d P Connector" com "S L O Service Settings" selecionada.](./media/kerbf5-tutorial/configure24.png)

1. Para configurar o SAML SP, navegue para **Access > Federation > PRESTADOR de Serviços SAML > Serviços SP Locais** e clique em **Criar**. Complete as seguintes informações e clique **em OK**.

    * Nome do tipo: KerbApp200SAML
    * ID de entidade*: https://kerb-app.com.cutestat.com
    * Definições de nomes sp
    * Regime: https
    * Anfitrião: kerbapp200.superdemo.live
    * Descrição: kerbapp200.superdemo.live

     ![Screenshot que mostra a janela "Editar S A M L S P Service" com "Definições Gerais" selecionadas.](./media/kerbf5-tutorial/configure25.png)

     b. Selecione a Configuração SP, KerbApp200SAML e Clique **em Conectores IdP Bind/UnBind**.

     ![Screenshot que mostra a página "S A M L Service Provider - Local S P Services" com "KerbAPP200 S A M L" selecionada.](./media/kerbf5-tutorial/configure26.png)

     ![Screenshot que mostra o botão "Bind/Unbind I d P Connectors" selecionado.](./media/kerbf5-tutorial/configure27.png)

     c. Clique em **Adicionar Nova Linha** e selecione o **conector IdP externo** criado no passo anterior, clique em **Update** e, em seguida, clique em **OK**.

     ![Screenshot que mostra o "Editar S A M L I d Ps que usa esta janela S P" com o botão "Adicionar Nova Fila" selecionado.](./media/kerbf5-tutorial/configure28.png)

1. Para configurar kerberos SSO, navegue para **aceder > único sign-on > Kerberos,** informações completas e clique em **Terminado**.

    >[!Note]
    > Necessitará da Conta delegação de Kerberos para ser criada e especificada. Consulte a secção KCD (consulte o apêndice para referências variáveis)

    * **Nome de utilizador Fonte**: session.saml.last.attr.name.http: \/ /schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname

    * **Fonte do User Realm**: session.logon.last.domain

        ![Screenshot que mostra a página "Single Sign-On - Properties" com as caixas de texto "Username Source" e "User Realm Source" em destaque.](./media/kerbf5-tutorial/configure29.png)

1. Para configurar o perfil de acesso, navegue para aceder ao **perfil de >/políticas > perfil de acesso (por políticas de sessão)**, clique em **Criar,** complete as seguintes informações e clique em **Terminado**.

    * Nome: KerbApp200
    * Tipo de perfil: Todos
    * Perfil: Perfil
    * Línguas: Inglês

        ![Screenshot que mostra a página "Perfis/Políticas - Propriedades" com as caixas de texto "Nome", "Tipo de Perfil" e "Langauges" em destaque.](./media/kerbf5-tutorial/configure30.png)

1. Clique no nome, KerbApp200, complete as seguintes informações e clique em **Update**.

    * Cookie de domínio: superdemo.live
    * Configuração SSO: KerAppSSO_sso

        ![Screenshot que mostra a página "S S D/Auth Domains" com a caixa de texto "Domain Cookie" e "S S O Configuration" em destaque, e o botão "Update" selecionado.](./media/kerbf5-tutorial/configure31.png)

1. Clique **na Política de Acesso** e, em seguida, clique em **Editar Política de Acesso** para Perfil "KerbApp200".

    ![Screenshot que mostra a página "Política de Acesso" com a ação "Editar Política de Acesso para Perfil KerbApp200" selecionada.](./media/kerbf5-tutorial/configure32.png)

    ![Screenshot que mostra a página "Política de Acesso" e o diálogo "S A M L Authentication S P".](./media/kerbf5-tutorial/configure33.png)

    ![Screenshot que mostra a página "Política de Acesso" e o diálogo "Atributo Variável" com as caixas de texto "Atribuição" realçadas.](./media/kerbf5-tutorial/configure34.png)

    * **session.logon.last.usernameUPN expr {[mcget {session.saml.last.identity}}}**

    * **session.ad.lastactualdomain TEXT superdemo.live**

        ![Screenshot que mostra a página "Política de Acesso" e o diálogo "Ative Directory" com a caixa de texto "SearchFilter" realçada.](./media/kerbf5-tutorial/configure35.png)

    * **(userPrincipalName=%{session.logon.last.usernameUPN})**

        ![Screenshot que mostra a página "Política de Acesso" com diálogo "A D Query - Branch Rules".](./media/kerbf5-tutorial/configure36.png)

        ![Screenshot que mostra as caixas de texto "Custom Variable" e "Custom Expression" destacadas.](./media/kerbf5-tutorial/configure37.png)

    * **session.logon.last.username expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }**

        ![Screenshot que mostra o nome de utilizador da página de início de página" realçado.](./media/kerbf5-tutorial/configure38.png)

    * **mcget {session.logon.last.username}**
    * **mcget {session.logon.last.password**

1. Para adicionar Novo Nó, navegue para **o tráfego local > nós > Lista de Nós, clique em Criar,** complete as seguintes informações e, em seguida, clique em **Terminar**.

    * Nome: KerbApp200
    * Descrição: KerbApp200
    * Endereço: 192.168.20.200

        ![Screenshot que mostra a página "Novo Nó" com as caixas de texto "Nome", "Descrição" e "Endereço" realçadas, e o botão "Terminado" selecionado.](./media/kerbf5-tutorial/configure39.png)

1. Para criar uma nova Piscina, navegue para **o Tráfego Local > Pools > Pool List, clique em Criar,** complete as seguintes informações e clique em **Terminado.**

    * Nome: KerbApp200-Pool
    * Descrição: KerbApp200-Pool
    * Monitores de Saúde: http
    * Endereço: 192.168.20.200
    * Porto de Serviço: 81

        ![Screenshot que mostra a página "New Pool" com valores introduzidos e o botão "Terminado" selecionado.](./media/kerbf5-tutorial/configure40.png)

1. Para criar servidor virtual, navegue para **o Tráfego Local > Servidores Virtuais > Lista de ServidorEs Virtuais > +**, complete as seguintes informações e clique em **Terminado**.

    * Nome: KerbApp200
    * Endereço de destino/máscara: Anfitrião 192.168.30.200
    * Porto de serviço: Porto 443 HTTPS
    * Perfil de acesso: KerbApp200
    * Especificar o perfil de acesso criado no passo anterior

        ![Screenshot que mostra a página "Lista de Servidor Virtual" com as caixas de texto "Name", "Destination Address/Mask" e "Service Port" em destaque.](./media/kerbf5-tutorial/configure41.png)

        ![Screenshot que mostra a página "Lista de Servidor Virtual" com o drop-down "Access Profile" realçado.](./media/kerbf5-tutorial/configure42.png)

### <a name="setting-up-kerberos-delegation"></a>Criação da Delegação Kerberos 

>[!NOTE]
>Para referência clique [aqui](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

*  **Passo 1:** Criar uma Conta delegação

    **Exemplo:**
    * Nome do domínio: **superdemo.live**

    * Nome da conta de Sam: **big-ipuser**

    * New-ADUser -Nome "Conta delegação APM" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")

* **Passo 2:** Definir SPN (na Conta delegação APM)

    **Exemplo:**
    * setspn -Um **hospedeiro/big-ipuser.superdemo.live** grande ipuser

* **Passo 3:** Delegação SPN (para a Conta de Serviço de Aplicações) Configurar a delegação adequada para a Conta delegação F5.
    No exemplo a seguir, a conta da delegação APM está a ser configurada para o KCD para FRP-App1.superdemo. aplicativo ao vivo.

    ![Configuração F5 (Kerberos)](./media/kerbf5-tutorial/configure43.png)

* Fornecer os detalhes mencionados no documento de referência acima referido ao abrigo [deste](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html).

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

- [Configurar f5 único sinal para aplicação baseada em cabeçalho](headerf5-tutorial.md)

- [Configurar f5 único sinal para aplicação Kerberos Avançada](advance-kerbf5-tutorial.md)

- [F5 BIG-IP APM e Ad AD Azure para acesso híbrido seguro](../manage-apps/f5-aad-integration.md)

- [Tutorial para implementar F5 BIG-IP Virtual Edition VM em Azure IaaS para acesso híbrido seguro](../manage-apps/f5-bigip-deployment-guide.md)

- [Tutorial para integração única de sign-on do Azure Ative Directory com F5 BIG-IP para VPN sem palavra-passe](../manage-apps/f5-aad-password-less-vpn.md)