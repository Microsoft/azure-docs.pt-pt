---
title: 'Tutorial: Integrar o Azure Ative Directory single sign-on (SSO) com o Maverics Identity Orchestrator SAML Connector / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Maverics Identity Orchestrator SAML Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: jeedes
ms.openlocfilehash: a4bfe2b87f3f2242189a78d9a31a89d82720fd37
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862076"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Tutorial: Integre o single sign-on do Azure AD com o Conector SAML, orquestrador de identidade Maverics

Strata fornece uma forma simples de integrar aplicações no local com O Diretório Ativo Azure (Azure AD) para autenticação e controlo de acessos.

Este artigo explica-lhe como configurar o Orquestrador de Identidade Maverics para:
* Migrar gradualmente os utilizadores de um sistema de identidade no local para o Azure AD durante o login para uma aplicação de legado no local.
* Encaminhar pedidos de login de um produto de gestão de acesso web legado, como CA SiteMinder ou Oracle Access Manager, para Azure AD.
* Autenticar os utilizadores para aplicações no local que estejam protegidas através da utilização de cabeçalhos HTTP ou cookies de sessão proprietárias após autenticar o utilizador contra a Azure AD.

Strata fornece software que você pode implementar no local ou na nuvem. Ajuda-o a descobrir, conectar e orquestrar fornecedores de identidade para criar uma gestão de identidade distribuída para empresas híbridas e multi-cloud.

Este tutorial demonstra como migrar uma aplicação web no local que está atualmente protegida por um produto de gestão de acesso web (CA SiteMinder) para usar a Azure AD para autenticação e controlo de acessos. Aqui estão os passos básicos:
1. Instale o Orquestrador de Identidade Maverics.
2. Registe a sua aplicação de empresa com Azure AD e configuure-a para utilizar o Conector de Código Zero Maverics AD SAML para um único sinal de SSO baseado em SAML.
3. Integre os Maverics com o SiteMinder e a loja de utilizadores do Protocolo de Acesso ao Diretório Leve (LDAP).
4. Crie um cofre de chaves Azure e configuure a Maverics para usá-lo como seu fornecedor de gestão de segredos.
5. Demonstre a migração do utilizador e a abstração da sessão utilizando maverics para fornecer acesso a uma aplicação web java no local.

Para obter instruções adicionais de instalação e configuração, aceda ao [site strata](https://www.strata.io).

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
- Uma assinatura ativada pelo Maverics Identity Orchestrator SAML Connector SSO. Para obter o software Maverics, contacte [as vendas da Strata](mailto:sales@strata.io).

## <a name="install-maverics-identity-orchestrator"></a>Instalar Orquestrador de Identidade Maverics

Para começar com a instalação do Orquestrador de Identidade Maverics, consulte as [instruções de instalação](https://www.strata.io).

### <a name="system-requirements"></a>Requisitos de sistema
* Sistemas operativos suportados
  * RHEL 7 e superior
  * CentOS 7 e superior

* Dependências
  * sistemas

### <a name="installation"></a>Instalação

1. Obtenha o mais recente pacote Maverics Redhat Package Manager (RPM). Copie a embalagem para o sistema em que pretende instalar o software Maverics.

2. Instale o pacote Maverics, substituindo o nome do seu ficheiro no lugar de `maverics.rpm` .

    `sudo rpm -Uvf maverics.rpm`

3. Depois de instalar maverics, funcionará como um serviço sob `systemd` . Para verificar se o serviço está em execução, execute o seguinte comando:

    `sudo systemctl status maverics`

Por predefinição, a Maverics é instalada no diretório */usr/local/bin.*

Depois de instalar maverics, o ficheiro *maverics.yaml* predefinido é criado no diretório */etc/maverics.* Antes de editar a sua configuração para incluir `workflows` `connectors` e, o seu ficheiro de configuração será assim:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="configuration-options"></a>Opções de configuração
### <a name="version"></a>Versão
O `version` campo declara qual a versão do ficheiro de configuração que está a ser utilizada. Se a versão não for especificada, a versão de configuração mais recente será utilizada.

```yaml
version: 0.1
```
### <a name="listenaddress"></a>ouvirAddress
`listenAddress` declara qual o endereço que o Orquestrador vai ouvir. Se a secção de anfitrião do endereço estiver em branco, o Orquestrador ouvirá todos os endereços IP disponíveis e independentes do sistema local. Se a secção porta do endereço estiver em branco, um número de porta é escolhido automaticamente.

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

O `tls` campo declara um mapa de objetos de Segurança da Camada de Transporte (TLS). Os objetos TLS podem ser utilizados por conectores e pelo servidor Do Orquestrador. Para todas as opções TLS disponíveis, consulte a documentação do `transport` pacote.

O Microsoft Azure requer comunicação através do TLS quando estiver a utilizar SSO baseado em SAML. Para obter informações sobre a geração de certificados, aceda ao [site Let's Encrypt](https://letsencrypt.org/getting-started/).

A `maverics` chave está reservada para o servidor do Orquestrador. Todas as outras teclas estão disponíveis e podem ser usadas para injetar um objeto TLS num determinado conector.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>Ficheiros de inclusão

Pode definir `connectors` `workflows` e, nos seus próprios ficheiros de configuração, separar os ficheiros e faz referência no ficheiro *maverics.yaml,* `includeFiles` utilizando, pelo seguinte exemplo:

```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

Este tutorial utiliza um único ficheiro de configuração *maverics.yaml.*

## <a name="use-azure-key-vault-as-your-secrets-provider"></a>Use o Azure Key Vault como seu fornecedor de segredos

### <a name="manage-secrets"></a>Gerir segredos

Para carregar segredos, a Maverics pode integrar-se com várias soluções de gestão secreta. As integrações atuais incluem um ficheiro, Hashicorp Vault, e Azure Key Vault. Se não for especificada nenhuma solução de gestão secreta, a Maverics não consegue carregar segredos em texto simples do ficheiro *maverics.yaml.*

Para declarar um valor como segredo num ficheiro *maverics.yaml* config, encerra o segredo em suportes angulares:

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="load-secrets-from-a-file"></a>Carregar segredos de um arquivo

1. Para carregar segredos de um ficheiro, adicione a variável ambiente `MAVERICS_SECRET_PROVIDER` no ficheiro */etc/maverics/maverics.env* utilizando:

   `MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

2. Reinicie o serviço Maverics executando:

   `sudo systemctl restart maverics`

O conteúdo do ficheiro *secrets.yaml* pode ser preenchido com qualquer número de `secrets` .

```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="set-up-an-azure-key-vault"></a>Crie um cofre de chaves Azure

Pode configurar um cofre de chaves Azure utilizando o portal Azure ou o Azure CLI.

**Utilizar o portal do Azure**
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. [Criar um novo cofre de chaves.](../../key-vault/general/quick-create-portal.md)
1. [Adicione os segredos ao cofre das chaves.](../../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault)
1. [Registe uma candidatura com Azure AD](../develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal).
1. [Autorizar uma aplicação a usar um segredo.](../../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault)

**Utilizar a CLI do Azure**

1. Abra o [Azure CLI](/cli/azure/install-azure-cli)e, em seguida, insira o seguinte comando:

    ```shell
    az login
    ```

1. Criar um novo cofre de chaves executando o seguinte comando:
    ```shell
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

1. Adicione os segredos ao cofre da chave executando o seguinte comando:
    ```shell
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

1. Registar uma candidatura com Azure AD executando o seguinte comando:
    ```shell
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

1. Autorizar uma aplicação a utilizar um segredo executando o seguinte comando:
    ```shell
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

1. Para carregar segredos do seu cofre de chave Azure, desagregue a variável ambiente `MAVERICS_SECRET_PROVIDER` no ficheiro */etc/maverics/maverics.env* utilizando as credenciais encontradas no *azure-credentials.jsno* ficheiro, no seguinte formato:
 
   `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

1. Reiniciar o serviço Maverics: `sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>Configure a sua aplicação em AD Azure para SSO baseado em SAML

1. No seu inquilino Azure AD, vá às **aplicações da Enterprise,** procure o **Maverics Identity Orchestrator SAML Connector,** e depois selecione-o.

1. No painel Maverics Identity Orchestrator SAML Connector **Properties,** de **No** definição **de atribuição do utilizador necessária?**

1. No painel de **visão geral** do conector de identidade Maverics, selecione **Configurar um único sinal de inscrição** e, em seguida, selecione **SAML**.

1. No sinal baseado no painel de cores saml do Maverics Identity Orchestrator **SAML,** edite a **Configuração Básica SAML** selecionando o botão **Edit** (ícone de lápis).

   ![Screenshot do botão de edição "Configuração BÁSICA SAML".](common/edit-urls.png)

1. Introduza o **ID da Entidade** digitando um URL no seguinte formato: `https://<SUBDOMAIN>.maverics.org` . O ID da Entidade deve ser único em todas as aplicações do arrendatário. Guarde o valor introduzido aqui para ser incluído na configuração de Maverics.

1. Introduza o **URL de resposta** no seguinte formato: `https://<AZURECOMPANY.COM>/<MY_APP>/` . 

1. Introduza o **Sinal no URL** no seguinte formato: `https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>` . 

1. Selecione **Guardar**.

1. Na secção **Certificado de Assinatura SAML,** selecione o botão **Copiar** para copiar o URL **de metadados da Federação de Aplicações** e guarde-o para o seu computador.

    ![Screenshot do botão de cópia "Certificado de assinatura SAML".](common/copy-metadataurl.png)

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector"></a>Configure Maverics Identity Orchestrator Azure AD SAML Connector

O Maverics Identity Orchestrator Azure AD Connector suporta OpenID Connect e SAML Connect. Para configurar o conector, faça o seguinte: 

1. Para ativar o SSO baseado em SAML, coloque em conjunto `authType: saml` .

1. Crie o valor para `samlMetadataURL` no seguinte formato: `samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>` .

1. Defina o URL para o qual o Azure será redirecionado de volta para a sua aplicação depois de os utilizadores terem iniciado sessão com as suas credenciais Azure. Utilize o seguinte formato: `samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>` .

1. Copiar o valor da entidadeID previamente configurada: `samlEntityID: https://<SUBDOMAIN>.maverics.org` .

1. Copie o valor do URL de resposta que a Azure AD utilizará para registar a resposta SAML: `samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>` .

1. Gere uma chave de assinatura JSON Web Token (JWT), que é usada para proteger as informações da sessão do Orquestrador de Identidade Maverics, utilizando a [ferramenta OpenSSL](https://www.openssl.org/source/):

    ```shell 
    openssl rand 64 | base64
    ```
1. Copie a resposta à `jwtSigningKey` propriedade config: `jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==` .

## <a name="attributes-and-attribute-mapping"></a>Atributos e mapeamento de atributos
O mapeamento de atributos é usado para definir o mapeamento dos atributos do utilizador de um diretório de utilizador de fonte no local para um inquilino AZure AD após a configuração do utilizador.

Os atributos determinam quais os dados do utilizador que podem ser devolvidos a uma aplicação numa reclamação, transmitidos em cookies de sessão ou transmitidos à aplicação em variáveis de cabeçalho HTTP.

## <a name="configure-the-maverics-identity-orchestrator-azure-ad-saml-connector-yaml-file"></a>Configure o ficheiro Maverics Identity Orchestrator Azure AD SAML Connector YAML

A configuração do seu orquestrador de identidade Maverics Azure AD Connector será assim:

```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: <SIGNING KEY>
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="migrate-users-to-an-azure-ad-tenant"></a>Migrar utilizadores para um inquilino AZure AD

Siga esta configuração para migrar gradualmente os utilizadores de um produto de gestão de acesso web, como CA SiteMinder, Oracle Access Manager ou IBM Tivoli. Também pode migrar para eles a partir de um diretório de protocolo de diretório leve (LDAP) ou de uma base de dados SQL.

### <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>Configure as permissões da sua aplicação em Azure AD para criar utilizadores

1. No seu inquilino Azure AD, vá `App registrations` e selecione a aplicação **do Maverics Identity Orchestrator SAML Connector.**

1. No **Maverics Identity Orchestrator SAML Connector Os certificados & o painel de segredos,** selecione `New client secret` e, em seguida, selecione na opção de expiração. Selecione o botão **Copiar** para copiar o segredo e guarde-o para o seu computador.

1. No **Maverics Identity Orchestrator SAML Connector Permissões API** painel, **selecione Adicionar permissão** e, em seguida, no painel de **permissões API do Pedido,** selecione permissões **de Gráfico** e **Aplicação do** Microsoft . 

1. No ecrã seguinte, selecione **User.ReadWrite.All** e, em seguida, **selecione Adicionar permissões**. 

1. De volta ao painel de permissões da **API,** selecione **Grant consentimento administrativo**.

### <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-file-for-user-migration"></a>Configure o ficheiro Maverics Identity Orchestrator SAML Connector YAML para migração de utilizadores

Para ativar o fluxo de trabalho de migração do utilizador, adicione estas propriedades adicionais ao ficheiro de configuração:
1. Introduza o URL do **Gráfico Azure** no seguinte formato: `graphURL: https://graph.microsoft.com` .
1. Introduza o **URL OAuth Token** no seguinte formato: `oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>` .
1. Insira o segredo do cliente anteriormente gerado no seguinte formato: `oauthClientSecret: <CLIENT SECRET>` .


O seu último ficheiro de configuração do Orquestrador de Identidade Maverics Azure Ad Connector será assim:

```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  graphURL: https://graph.microsoft.com
  oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/oauth2/v2.0/token
  oauthClientID: <APP ID>
  oauthClientSecret: <NEW CLIENT SECRET>
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

### <a name="configure-maverics-zero-code-connector-for-siteminder"></a>Configure o conector de código zero Maverics para o SiteMinder

Utilize o conector SiteMinder para migrar os utilizadores para um inquilino AZure AD. Inicie sessão com os utilizadores em aplicações antigas no local que são protegidas pelo SiteMinder utilizando as identidades e credenciais AZure AD recém-criadas.

Para este tutorial, o SiteMinder foi configurado para proteger a aplicação do legado utilizando a autenticação baseada em formulários e o `SMSESSION` cookie. Para se integrar com uma aplicação que consome informações de autenticação e de sessão através de cabeçalhos HTTP, é necessário adicionar a configuração de emulação do cabeçalho ao conector.

Este exemplo `username` mapeia o atributo ao `SM_USER` cabeçalho HTTP:

```yaml
  headers:
    SM_USER: username
```

Definir `proxyPass` para o local a que os pedidos são proxiemente. Normalmente, este local é o anfitrião da aplicação protegida.

`loginPage` deve corresponder ao URL do formulário de login que é atualmente utilizado pelo SiteMinder quando redireciona os utilizadores para a autenticação.

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

### <a name="configure-maverics-zero-code-connector-for-ldap"></a>Configure Maverics Zero Code Connector para LDAP

Quando as aplicações são protegidas por um produto de gestão de acesso web (WAM), como o SiteMinder, as identidades e atributos dos utilizadores são normalmente armazenados num diretório LDAP.

Esta configuração do conector demonstra como ligar-se ao diretório LDAP. O conector é configurado como a loja de utilizador para o SiteMinder para que as informações corretas do perfil do utilizador possam ser recolhidas durante o fluxo de trabalho da migração e um utilizador correspondente pode ser criado em Azure AD.

* `baseDN` especifica a localização no diretório contra a qual efetuar a pesquisa LDAP.

* `url` é o endereço e a porta do servidor LDAP a que se deve ligar.

* `serviceAccountUsername` é o nome de utilizador que é usado para ligar ao servidor LDAP, normalmente expresso como um dN vinculado (por exemplo, `CN=Directory Manager` ).

* `serviceAccountPassword` é a palavra-passe que é usada para ligar ao servidor LDAP. Este valor é armazenado na instância de cofre de chave Azure previamente configurada.  

* `userAttributes` define a lista de atributos relacionados com o utilizador à consulta. Estes atributos são posteriormente mapeados em atributos AD Azure correspondentes.

```yaml
- name: company-ldap
  type: ldap
  url: "ldap://ldap.company.com:389"
  baseDN: ou=People,o=company,c=US
  serviceAccountUsername: uid=admin,ou=Admins,o=company,c=US
  serviceAccountPassword: <vaulted-password>
  userAttributes:
    - uid
    - cn
    - givenName
    - sn
    - mail
    - mobile
```

### <a name="configure-the-migration-workflow"></a>Configure o fluxo de trabalho da migração

A configuração do fluxo de trabalho de migração determina como a Maverics migra os utilizadores do SiteMinder ou LDAP para a Azure AD.

Este fluxo de trabalho:
- Utiliza o conector SiteMinder para proxy o login siteMinder. As credenciais do utilizador são validadas através da autenticação SiteMinder e depois passadas para os passos subsequentes do fluxo de trabalho.
- Recupera os atributos do perfil do utilizador na loja de utilizadores do SiteMinder.
- Faz um pedido à Microsoft Graph API para criar o utilizador no seu inquilino AZure AD.

Para configurar o fluxo de trabalho da migração, faça o seguinte:

1. Dê ao fluxo de trabalho um nome (por exemplo, **SiteMinder para Azure AD Migration).**
1. Especificar o `endpoint` , que é um caminho HTTP em que o fluxo de trabalho está exposto, desencadeando o `actions` fluxo de trabalho em resposta aos pedidos. O `endpoint` normalmente corresponde à aplicação que é proxied (por exemplo, `/my_app` ). O valor deve incluir tanto os cortes de liderança como os que estão a seguir.
1. Adicione o adequado `actions` ao fluxo de trabalho.

   a. Defina o `login` método para o conector SiteMinder. O valor do conector deve corresponder ao valor do nome na configuração do conector.

   b. Defina o `getprofile` método para o conector LDAP.

   c.  Defina o `createuser` método para o conector AzureAD.

    ```yaml
      workflows:
      - name: SiteMinder to Azure AD Migration
        endpoint: /my_app/
        actions:
        - connector: siteminder-login-form
          method: login
        - connector: company-ldap
          method: getprofile
        - connector: AzureAD
          method: createuser
    ```
### <a name="verify-the-migration-workflow"></a>Verifique o fluxo de trabalho da migração

1. Se o serviço Maverics ainda não estiver em funcionamento, inicie-o executando o seguinte comando: 

   `sudo systemctl start maverics`

1. Aceda ao URL de login, `http://host.company.com/my_app` .
1. Forneça as credenciais de utilizador que são usadas para iniciar sessão na aplicação enquanto esta é protegida pelo SiteMinder.
4. Ir **Home** para  >  **utilizadores domésticos ! Todos os Utilizadores** para verificar se o utilizador é criado no inquilino AZURE AD.  

### <a name="configure-the-session-abstraction-workflow"></a>Configure o fluxo de trabalho de abstração da sessão

O fluxo de trabalho de abstração da sessão move a autenticação e o controlo de acesso para a aplicação web do legado no local para o inquilino AZure AD.

O conector Azure utiliza o `login` método para redirecionar o utilizador para o URL de login, assumindo que não existe nenhuma sessão.

Depois de autenticado, o token da sessão que é criado como resultado é passado para Maverics. O método do conector SiteMinder `emulate` é utilizado para imitar a sessão baseada em cookies ou a sessão baseada em cabeçalhos e, em seguida, decorar o pedido com quaisquer atributos adicionais exigidos pela aplicação.

1. Dê ao fluxo de trabalho um nome (por exemplo, **Resumo da Sessão SiteMinder).**
1. Especifique o `endpoint` , que corresponde à aplicação que está a ser proxiimente. O valor deve incluir cortes de liderança e de fuga (por exemplo, `/my_app/` ).
1. Adicione o adequado `actions` ao fluxo de trabalho.

   a. Defina o `login` método para o conector Azure. O `connector` valor deve corresponder ao valor na `name` configuração do conector.

   b. Defina o `emulate` método para o conector SiteMinder.

     ```yaml
      - name: SiteMinder Session Abstraction
        endpoint: /my_app/
        actions:
      - connector: azure
        method: login
      - connector: siteminder-login-form
        method: emulate
     ```
### <a name="verify-the-session-abstraction-workflow"></a>Verifique o fluxo de trabalho de abstração da sessão

1. Aceda ao URL de aplicação proxied, `https://<AZURECOMPANY.COM>/<MY_APP>` . 
    
    É redirecionado para a página de login.

1. Insira as credenciais de utilizador da AZure.

   Deve ser redirecionado para a aplicação como se tivesse sido autenticado diretamente pelo SiteMinder.
