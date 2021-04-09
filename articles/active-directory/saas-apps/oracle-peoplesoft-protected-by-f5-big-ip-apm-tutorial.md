---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a Oracle PeopleSoft - Protegida por F5 BIG-IP APM | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Oracle PeopleSoft - Protegido por F5 BIG-IP APM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: jeedes
ms.openlocfilehash: 3b7c8e024ac8361c08cc41195531a114bb12fcb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522296"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a Oracle PeopleSoft - Protegida por F5 BIG-IP APM

Neste tutorial, você aprenderá a integrar o Oracle PeopleSoft - Protegido por F5 BIG-IP APM com Azure Ative Directory (Azure AD). Quando integrar a Oracle PeopleSoft - Protegida por F5 BIG-IP APM com AZure AD, pode:

* Control em Azure AD que tem acesso à Oracle PeopleSoft - Protegida por F5 BIG-IP APM.
* Permita que os seus utilizadores sejam automaticamente inscritos na Oracle PeopleSoft - Protegida por F5 BIG-IP APM com as suas contas AD AZure.
* Gerencie as suas contas numa localização central - o portal Azure.
* Este tutorial abrange instruções para o Oracle PeopleSoft ELM.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

1. Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
1. Oracle PeopleSoft - Protegido por F5 BIG-IP APM única assinatura ativada (SSO).

1. A implementação da solução comum requer a seguinte licença:

    1. F5 BIG-IP® Melhor pacote (ou) 
    2. F5 BIG-IP Access Policy Manager™ (APM) licença autónoma 
    3. F5 BIG-IP Access Policy Manager™ (APM) licença de complemento em um BIG-IP F5 BIG-IP existente® Gestor de Tráfego Local™ (LTM).
    4. Além da licença acima, o sistema F5 também pode ser licenciado com: 
        * Uma subscrição de filtragem de URL para utilizar a base de dados da categoria URL 
        * Uma subscrição de Inteligência IP F5 para detetar e bloquear agressores conhecidos e tráfego malicioso 
        * Um módulo de segurança de hardware de rede (HSM) para salvaguardar e gerir chaves digitais para autenticação forte
1. O sistema F5 BIG-IP é a provisionado com módulos APM (LTM é opcional) 
1. Embora opcional, é altamente recomendado implementar os sistemas F5 num grupo de [dispositivos de sincronização/failover](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG), que inclui o par de espera ativo, com um endereço IP flutuante para alta disponibilidade (HA). Uma maior redundância de interface pode ser alcançada utilizando o Protocolo de Controlo de Agregação de Ligações (LACP). LACP gere as interfaces físicas conectadas como uma única interface virtual (grupo agregado) e deteta quaisquer falhas de interface dentro do grupo.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Oracle PeopleSoft - Protegido por F5 BIG-IP APM suporta **SP e IDP** iniciado SSO

## <a name="adding-oracle-peoplesoft---protected-by-f5-big-ip-apm-from-the-gallery"></a>Adicionar Oracle PeopleSoft - Protegido por F5 BIG-IP APM da galeria

Para configurar a integração da Oracle PeopleSoft - Protegida por F5 BIG-IP APM em Azure AD, precisa de adicionar o Oracle PeopleSoft - Protegido por F5 BIG-IP APM da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Oracle PeopleSoft - Protegido por F5 BIG-IP APM** na caixa de pesquisa.
1. Selecione **Oracle PeopleSoft - Protegido por F5 BIG-IP APM** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>Configurar e testar Azure AD SSO para o Oracle PeopleSoft - Protegido por F5 BIG-IP APM

Configure e teste Azure AD SSO com Oracle PeopleSoft - Protegido por F5 BIG-IP APM usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Oracle PeopleSoft - Protegida por F5 BIG-IP APM.

Para configurar e testar Azure AD SSO com Oracle PeopleSoft - Protegido por F5 BIG-IP APM, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Oracle PeopleSoft-Protected por F5 BIG-IP APM SSO](#configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Oracle PeopleSoft-Protected pelo utilizador de teste APM APM F5 BIG-IP](#create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user)** - para ter uma contraparte de B.Simon na Oracle PeopleSoft - Protegida por F5 BIG-IP APM que está ligada à representação AD Ad Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações Oracle **PeopleSoft - Protegida por F5 BIG-IP APM,** encontre a secção **Gestão** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<FQDN>.peoplesoft.f5.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/post/acs`

    c. Na caixa de texto **URL logout,** digite um URL utilizando o seguinte padrão: `https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/redirect/slr`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<FQDN>.peoplesoft.f5.com/`

    > [!NOTE]
    >Estes valores não são reais. Atualize estes valores com o URL de Sign-On real, identificador, URL de resposta e URL de logo. Contacte [a Oracle PeopleSoft - Protegida pela equipa de suporte do cliente F5 BIG-IP APM](https://support.f5.com) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Oracle PeopleSoft - Protegido por F5 BIG-IP APM aplicação espera as afirmações SAML em um formato específico, o que requer que você adicione mapeamentos de atributos personalizados à configuração de atributos DEKEN SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a Oracle PeopleSoft - Protegida pela aplicação F5 BIG-IP APM espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Name |  Atributo de origem|
    | ------------------ | --------- |
    | EMPLID | utilizador.employeeid |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** descarregue os **metadados da Federação XML** e o **Certificado (Base64)** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/both-certificate.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Oracle PeopleSoft - Protegida por F5 BIG-IP APM.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Oracle PeopleSoft - Protected by F5 BIG-IP APM**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso"></a>Configurar o Oráculo PeopleSoft-Protected por F5 BIG-IP APM SSO

### <a name="f5-saml-sp-configuration"></a>Configuração F5 SAML SP

Importe o Certificado de Metadados no F5 que será utilizado mais tarde no processo de configuração. Navegue para **a Gestão de Certificados > Sistema > Gestão de Certificados de Tráfego > Lista de Certificados SSL**. **Selecione Importar** do canto direito.

![Configuração F5 SAML SP](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sp-configuration.png)

#### <a name="setup-the-saml-idp-connector"></a>Configurar o Conector IDP SAML 

1. Navegar para **o Access > Federation > SAML: Fornecedor de serviços > Conectores Externos de Idp** e clique em Criar > A partir de **Metadados**.

    ![Conector IDP F5 SAML](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/saml-idp-connector.png)

1. Na página seguinte, clique em **Procurar** para carregar o ficheiro xml.

1. Dê um nome válido na caixa de texto **do fornecedor de identidade** e, em seguida, clique em **OK**.

    ![novo Conector IDP SAML](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-idp.png)

1. Execute os passos necessários no separador **Definições de Segurança** e, em seguida, clique em **OK**.

    ![editar O Conector IDP SAML](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp.png)

#### <a name="setup-the-saml-sp"></a>Configurar o SAML SP

1. Navegue para **access > Federation > Prestador de Serviços SAML > Serviços SP Locais** e clique em **Criar**. Complete as seguintes informações e clique **em OK**.

    * Nome: `<Name>`
    * ID de entidade: `https://<FQDN>.peoplesoft.f5.com`
    * Definições de nomes sp
        * Regime: `https`
        * Anfitrião: `<FQDN>.peoplesoft.f5.com`
        * Descrição: `<Description>`

    ![novos serviços SAML SP](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-sp-service.png)

1. Selecione a Configuração SP, PeopleSoftAppSSO e **Clique em Conectores IdP Bind/UnBind**.
Clique em **Adicionar Nova Linha** e selecione o **conector IdP externo** criado no passo anterior, clique em **Update** e, em seguida, clique em **OK**.

    ![criar serviços SAML SP](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp-use-sp.png)

## <a name="configuring-application"></a>Aplicação de configuração

### <a name="create-a-new-pool"></a>Criar uma nova Piscina
1. Navegue para **o tráfego local > Piscinas > Pool List,** clique em **Criar,** complete as seguintes informações e clique em **Terminado**.

    * Nome: `<Name>`
    * Descrição: `<Description>`
    * Monitores de Saúde: `http`
    * Endereço: `<Address>`
    * Porta de serviço: `<Service Port>`

    ![nova criação de piscina](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/create-pool.png)


### <a name="create-a-new-client-ssl-profile"></a>Criar um novo perfil SSL do Cliente

Navegue para **perfis de > de tráfego local > SSL > cliente > +**, complete as seguintes informações e clique em **Terminado.**

* Nome: `<Name>`
* Certificado: `<Certificate>`
* Chave: `<Key>`

![novo perfil SSL do cliente](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/client-ssl-profile.png)

### <a name="create-a-new-virtual-server"></a>Criar um novo Servidor Virtual

1. Navegue para **o tráfego local > servidores virtuais > Lista de Servidores Virtuais > +**, complete as seguintes informações e clique em **Terminado**.
    * Nome: `<Name>`
    * Endereço de destino/máscara: `<Address>`
    * Porto de serviço: Porto 443 HTTPS
    * Perfil HTTP (Cliente): http

    ![Criar um novo Servidor Virtual](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-list.png)

1. Preencha os seguintes valores na página abaixo:

    * Perfil SSL (Cliente): `<SSL Profile>`
    * Tradução do endereço de origem: Mapa automático
    * Perfil de acesso: `<Access Profile>`
    * Piscina padrão: `<Pool>`


    ![Criar uma nova piscina de pessoas do Servidor Virtual](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-people-soft.png)

## <a name="setting-up-peoplesoft-application-to-support-f5-big-ip-apm-as-the-single-sign-on-solution"></a>Criação da aplicação PeopleSoft para apoiar f5 Big-IP APM como a solução de inscrição única

>[!Note]
> Referência https://docs.oracle.com/cd/E12530_01/oam.1014/e10356/people.htm

1. Logon para Peoplesoft Consola `https://<FQDN>.peoplesoft.f5.com/:8000/psp/ps/?cmd=start` usando credenciais de administrador (Exemplo: PS/PS)

    ![Self services do gestor](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-console.png)

1. Na aplicação PeopleSoft, crie **o OAMPSFT** como um novo perfil de utilizador e associe um papel de baixa segurança, como **o PeopleSoft User**.
Navegue para **o Peopletools > Security > Os perfis de utilizador > perfis de utilizador** para criar um novo perfil de utilizador, por exemplo: **OAMPSFT** e Add **Peoplesoft User**.

    ![Utilizador Peoplesoft](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/user-profile.png)

1. Aceda ao perfil web e **introduza o OAMPSFT** como o **ID** do utilizador de acesso público.

    ![Perfis de Utilizador](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/web-profile-configuration.png)

1. Do **PeopleTools Application Designer,** abra o **disco FUNCLIB_LDAP.**

    ![configuração de perfil web](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/funclib.png)

1. Atualize o cabeçalho do utilizador com **PS_SSO_UID** para **OAMSSO_AUTHENTICATION** função.
Na função **getWWWAuthConfig(),** substitua o valor que é atribuído ao **&defaultUserId** pelo **OAMPSFT** que definimos no perfil Web. Guarde a definição de registo.

    ![OAMSSO_AUTHENTICATION](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/record.png)

1. Aceda à página **Signon PeopleCode** (PeopleTools, Security, Security Objects, Signon PeopleCode) e ative a função **OAMSSO_AUTHENTICATION** — o Signon PeopleCode for Oracle Access Manager single signon.

    ![OAMSSO_AUTHENTICATION ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-on-people-soft.png)

## <a name="setting-up-f5-big-ip-apm-to-populate-ps_sso_uid-http-header-with-the-peoplesoft-user-id"></a>Configuração F5 Big-IP APM para preencher "PS_SSO_UID" HTTP Header com o ID do utilizador PeopleSoft

### <a name="configuring-per-request-policy"></a>Política de Per-Request configuração
1. Navegar para aceder a **> perfil/políticas > Per-Request Políticas,** clique em **Criar,** complete as seguintes informações e clique em **Terminado**.

    * Nome: `<Name>`
    * Tipo de perfil: Todos
    * Idiomas: `<Language>`

    ![Política de Per-Request configuração ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/per-request.png)

1. Clique **em Editar** Per-Request edição de política `<Name>` ![ Per-Request Política PeopleSoftSSO ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso.png)

    `Header Name: <Header Name>`   
    `Header Value: <Header Value>`

### <a name="assign-per-request-policy-to-the-virtual-server"></a>Atribuir a Política de Per-Request ao Servidor Virtual

Navegue para servidores virtuais > de **tráfego local > lista de servidores virtuais > PeopleSoftApp** Specify como Per-Request `<Name>` Política

![PeopleSoftSSO como Política Per-Request ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso-1.png)

## <a name="setting-up-f5-big-ip-apm-to-support-single-logout-from-peoplesoft-application"></a>Criação de F5 Big-IP APM para apoiar o Logotipout Único da aplicação PeopleSoft

Para adicionar um suporte único de logout para todos os utilizadores da PeopleSoft, siga estes passos:

1. Determine o URL de logout correto para o portal PeopleSoft
    * Para determinar o endereço que a aplicação PeopleSoft utiliza para terminar uma sessão de utilizador, é necessário abrir o portal utilizando qualquer navegador web e ativar ferramentas de depurar o navegador, como mostra o exemplo abaixo:

        ![URL logout para portal PeopleSoft ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-out.png)

    * Encontre o elemento com o `PT_LOGOUT_MENU` id e guarde o caminho URL com os parâmetros de consulta. No nosso exemplo, obtivemos o seguinte valor: `/psp/ps/?cmd=logout`

1. Crie ltm iRule que redirecionará o utilizador para o URL de logout APM: `/my.logout.php3`

    * Navegar para **o tráfego local > iRule,** clique em **Criar,** complete as seguintes informações e clique em **Terminado.**

        Nome: `<Name>`  
        Definição:  
                    _quando HTTP_REQUEST { switch -glob -- [HTTP:uri] `/psp/ps/?cmd=logout` { { HTTP::redirect `/my.logout.php3` } } }_

1. Atribuir o iRule criado ao Servidor Virtual

    * Navegue para **o tráfego local > servidores virtuais > lista de servidores virtuais > recursos > Do PeopleSoftApp**. Clique no **Manage...** botão:   

    * Especifique `<Name>` como iRule ativado e clique **em Terminado**

        ![_iRule_PeopleSoftApp ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/irule-people-soft.png)

    * Dê o valor da caixa de texto **Nome** como `<Name>` 

        ![_iRule_PeopleSoftApp terminado](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/common-irule.png)


### <a name="create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user"></a>Criar PeopleSoft-Protected Oracle por F5 BIG-IP Utilizador de teste APM

Nesta secção, cria-se um utilizador chamado B.Simon in Oracle PeopleSoft-Protected por F5 BIG-IP APM. Trabalhe com [a Oracle PeopleSoft-Protected pela equipa de suporte APM da F5 BIG-IP](https://support.f5.com) para adicionar os utilizadores no PeopleSoft-Protected Oracle pela plataforma F5 BIG-IP APM. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o Oracle PeopleSoft-Protected por F5 BIG-IP APM Assinar no URL onde pode iniciar o fluxo de login.  

* Vá diretamente à Oracle PeopleSoft-Protected por F5 BIG-IP APM E inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Oráculo PeopleSoft-Protected por F5 BIG-IP APM para o qual configura o SSO 

Também pode utilizar o Microsoft Access Panel para testar a aplicação em qualquer modo. Quando clicar no PeopleSoft-Protected Oráculo por F5 BIG-IP APM no Painel de Acesso, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Oracle PeopleSoft-Protected por F5 BIG-IP APM para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Oracle PeopleSoft - Protegido por F5 BIG-IP APM, pode impor o controlo de sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).