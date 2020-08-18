---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com o Maverics Identity Orchestrator SAML Connector / Microsoft Docs'
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
ms.openlocfilehash: ec5368427f50f548be965bb883683c859759bbf3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88518935"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maverics-identity-orchestrator-saml-connector"></a>Tutorial: Azure Ative Directory single sign-on (SSO) integração com Maverics Identity Orchestrator SAML Connector

## <a name="introduction"></a>Introdução

Strata fornece uma forma simples de integrar aplicações no local com Azure AD para autenticação e controlo de acessos.

Este guia percorre como configurar o Orquestrador de Identidade Maverics &trade; para:
* Migrar gradualmente os utilizadores de um sistema de identidade no local para o Azure AD durante o login para uma aplicação de legado no local.
* Encaminhar pedidos de login de um produto de gestão de acesso web legado, como CA SiteMinder ou Oracle Access Manager para Azure AD.
* Autenticar os utilizadores para aplicações no local que estejam protegidas utilizando cabeçalhos http ou cookies de sessão proprietários após autenticar o utilizador contra a Azure AD.

Strata fornece software que implementa no local ou na nuvem para descobrir, conectar e orquestrar através de fornecedores de identidade para criar uma gestão de identidade distribuída para empresas híbridas e multi-cloud.

Este tutorial demonstrará como migrar uma aplicação web no local atualmente protegida por um produto de gestão de acesso web (CA SiteMinder) para utilizar a Azure AD para autenticação e controlo de acessos.
1. Instalar Orquestrador de Identidade Maverics&trade;
2. Registe a sua aplicação de empresa com a Azure AD e configuure-a para utilizar o Conector de Código Zero Maverics AD SAML zero &trade; para SSO baseado em SAML.
3. Integre os Maverics com o SiteMinder e a loja de utilizadores LDAP.
4. Crie o Azure Key Vault e configuure a Maverics para usá-lo como seu fornecedor de gestão de segredos.
5. Demonstre a migração do utilizador e a abstração da sessão usando Maverics para fornecer acesso a uma aplicação web Java no local.

Para instruções adicionais de instalação e configuração, visite https://strata.io/docs

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
- Maverics Identity Orchestrator SAML Connector única assinatura ativada (SSO) ativada. Para obter o software Maverics contacte sales@strata.io

## <a name="install-maverics-identity-orchestratortrade"></a>Instalar Orquestrador de Identidade Maverics&trade;

Para começar com a instalação do Orquestrador de Identidade Maverics, consulte as instruções de instalação em https://strata.io/docs

## <a name="system-requirements"></a>Requisitos de sistema
### <a name="supported-operating-systems"></a>Sistemas Operativos Suportados
* RHEL 7 e superior
* CentOS 7 e superior

### <a name="dependencies"></a>Dependências
* sistemas

## <a name="installation"></a>Instalação

1. Obtenha o mais recente pacote RPM da Maverics. Copie a embalagem para o sistema no qual pretende instalar o software Maverics.

2. Instale o pacote Maverics, substituindo o seu nome de ficheiro no lugar de `maverics.rpm` .

    `sudo rpm -Uvf maverics.rpm`

3. Após a instalação da Maverics, funcionará como um serviço sob `systemd` . Para verificar se o serviço está em execução, execute o seguinte comando.

    `sudo systemctl status maverics`

Por predefinição, a Maverics é instalada no `/usr/local/bin` diretório.

Após a instalação de Maverics, o ficheiro predefinido `maverics.yaml` é criado no `/etc/maverics` diretório. Antes de editar a sua configuração para incluir `workflows` `connectors` e, o seu ficheiro de configuração será assim:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="config-options"></a>Opções Config
### <a name="version"></a>Versão
O `version` campo declara qual a versão do ficheiro de configuração que está a ser utilizada. Se não for especificada, a versão config mais recente será utilizada.

```yaml
version: 0.1
```
### <a name="listen-address"></a>Endereço de escuta
`listenAddress` declara que endereço o Orquestrador vai ouvir. Se a secção de anfitrião do endereço estiver em branco, o Orquestrador ouvirá todos os endereços IP disponíveis e independentes do sistema local. Se a secção porta do endereço estiver em branco, um número de porta é automaticamente escolhido.

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

O `tls` campo declara um mapa de objetos de segurança da camada de transporte. Os objetos TLS podem ser utilizados por conectores, bem como pelo servidor Orchestrator. Para todas as opções TLS disponíveis, consulte a `transport` documentação do pacote.

O Microsoft Azure requer comunicação sobre o TLS ao utilizar o SSO baseado em SAML, pode encontrar mais informações [aqui](https://letsencrypt.org/getting-started/) para gerar os seus certificados.

A `maverics` chave está reservada para o servidor do Orquestrador. Todas as outras teclas estão disponíveis e podem ser usadas para injetar um objeto TLS num determinado conector.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>Incluir ficheiros

`connectors` e `workflows` pode ser definido nos seus próprios ficheiros de configuração separados e referenciados `maverics.yaml` usando pelo seguinte `includeFiles` exemplo.
```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

Este tutorial utiliza um único `maverics.yaml` ficheiro de configuração.

## <a name="using-azure-key-vault-as-your-secrets-provider"></a>Usando o Azure Key Vault como fornecedor de segredos

### <a name="secret-management"></a>Gestão secreta

A Maverics é capaz de se integrar com várias soluções de gestão secreta para carregar segredos. As integrações atuais incluem um ficheiro, Hashicorp Vault e Azure Key Vault. Se não for especificada nenhuma solução de gestão secreta, a Maverics não irá por defeito para carregar segredos em texto simples de `maverics.yaml` .
Para declarar um valor como segredo num `maverics.yaml` ficheiro config, embrulhe o segredo com suportes angulares:

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="file"></a>Ficheiro

Para carregar segredos de um ficheiro, adicione a variável ambiental `MAVERICS_SECRET_PROVIDER` no  `/etc/maverics/maverics.env` ficheiro, com:

`MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

Em seguida, reinicie o serviço maverics: `sudo systemctl restart maverics`

O `secrets.yaml` conteúdo do ficheiro pode ser preenchido com qualquer número de `secrets` .
```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="azure-key-vault"></a>Azure Key Vault

Os seguintes passos mostram como configurar um Cofre de Chaves Azure, utilizando o [portal Azure](https://portal.azure.com) ou utilizando o [Azure CLI:](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

1. [Faça login](https://portal.azure.com) utilizando o portal Azure ou utilizando o comando CLI:
    ```shell
    az login
    ```

2. [Criar um novo Cofre,](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault)ou utilizando o comando CLI:
    ```shell
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

3. [Adicione os Segredos ao Cofre de Chaves,](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault)ou utilizando um comando CLI:
    ```shell
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

4. [Registe uma aplicação com o Azure Ative Directory,](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)ou utilizando um comando CLI:
    ```shell
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

5. [Autorizar uma aplicação a utilizar um segredo,](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault)ou a utilizar um comando CLI:
    ```shell
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

Para carregar segredos da Azure KeyVault, desagregue a variável ambiental `MAVERICS_SECRET_PROVIDER` no `/etc/maverics/maverics.env` ficheiro, com as credenciais encontradas no azure-credentials.jsem arquivo, utilizando o seguinte padrão: `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

Em seguida, reinicie o serviço maverics: `sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>Configure a sua aplicação em AD Azure para SSO baseado em SAML

1. No seu inquilino Azure Ative Directory, navegue `Enterprise applications` para, procure `Maverics Identity Orchestrator SAML Connector` e selecione.

2. No 'Maverics Identity Orchestrator SAML Connector' Página de `User assignment required?` propriedades, definida para Não para permitir que a aplicação funcione para utilizadores recém-migrados.

3. No 'Maverics Identity Orchestrator SAML Connector' Página geral, selecione `Setup single sign-on` e, em seguida, selecione `SAML` .

4. No 'Maverics Identity Orchestrator SAML Connector' Sinal baseado em SAML, edite a Configuração Básica SAML.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

5. definir a `Entity ID` dactilografia de um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.maverics.org` . O `Entity ID` deve ser único em todas as aplicações no inquilino. Guarde o valor introduzido aqui para ser incluído na configuração de Maverics.

6. Definir URL de resposta, utilizando o seguinte padrão: `https://<AZURECOMPANY.COM>/<MY_APP>/` . 

7. Descreva o sinal no URL, utilizando o seguinte padrão: `https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>` e clique em Guardar.

8. Aceda à secção de Certificado de Assinatura SAML e clique no botão de cópia para copiar o Url de metadados da Federação de Aplicações e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

## <a name="maverics-identity-orchestrator-azure-ad-saml-connector-configuration"></a>Configuração do conector de identidade Maverics Azure AD SAML

O Maverics Identity Orchestrator Azure AD Connector suporta: 
- OpenID Connect
- Ligação SAML 

1. Para ativar o SSO baseado em SAML, coloque em conjunto `authType: saml` .

1. Crie o valor `samlMetadataURL` para: `samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`

1. Agora, defina o URL que o Azure irá redirecionar de volta para a sua aplicação depois de terem iniciado sessão com as suas credenciais Azure.
`samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>`

1. Copiar o valor da EntidadeID configurada acima: `samlEntityID: https://<SUBDOMAIN>.maverics.org`

1. Copie o valor a partir do URL de resposta que a Azure AD utilizará para registar a resposta SAML.
`samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>`

1. Gere uma chave de assinatura JWT, usada para proteger a informação da sessão do Orquestrador de Identidade Maverics, &trade; utilizando a [ferramenta OpenSSL](https://www.openssl.org/source/):

    ```shell 
    openssl rand 64 | base64
    ```
1. Copiar a resposta à `jwtSigningKey` propriedade config: `jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==`

## <a name="attributes-and-attribute-mapping"></a>Atributos e mapeamento de atributos
O Attribute Mapping é utilizado para definir o mapeamento dos atributos do utilizador a partir de um diretório de utilizador de fonte no local para Azure AD quando os utilizadores são a provisionados.

Os atributos determinam os dados do utilizador que podem ser devolvidos a uma aplicação numa reclamação, transmitidos em cookies de sessão ou transmitidos à aplicação em variáveis de cabeçalho http.

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector-yaml"></a>Configure Maverics Identity Orchestrator Azure AD SAML Connector yaml

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

## <a name="migrate-users-to-azure-ad"></a>Migrar utilizadores para Azure AD

Siga esta configuração para migrar gradualmente os utilizadores de um produto de gestão de acesso web como CA SiteMinder, Oracle Access Manager ou IBM Tivoli; um diretório LDAP; ou uma base de dados SQL.

## <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>Configure as permissões da sua aplicação em Azure AD para criar utilizadores

1. No seu inquilino Azure Ative Directory, navegue `App registrations` para e selecione a aplicação 'Maverics Identity Orchestrator SAML Connector'.

2. No 'Maverics Identity Orchestrator SAML Connector' Os certificados & segredos, selecione `New client secret` e, em seguida, selecione na opção de expiração. Clique no botão de cópia para copiar o segredo e guarde-o no seu computador.

3. No 'Maverics Identity Orchestrator SAML Connector' Permissões API, selecione `Add permission` e, em seguida, no Request API permissões selecionam `Microsoft Graph` e depois `Application permissions` . No ecrã seguinte selecione o `User.ReadWrite.All` e, em seguida, selecione `Add permissions` . Isto irá levá-lo de volta às permissões da API, lá selecione `Grant admin consent` .


## <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-for-user-migration"></a>Configure o Maverics Identity Orchestrator SAML Connector yaml para a migração do utilizador

Para permitir o fluxo de trabalho de migração do utilizador, adicione estas propriedades adicionais no ficheiro config:
1. Deite o URL do Gráfico Azure: `graphURL: https://graph.microsoft.com`
1. Desacorra o URL do OAuth Token, seguindo o padrão: `oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`
1. Desa estação o Segredo do Cliente gerado acima: `oauthClientSecret: <CLIENT SECRET>`


A sua última configuração do Orquestrador de Identidade Maverics, Azure AD Connector, será assim:
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

## <a name="configure-the-maverics-zero-code-connectortrade-for-siteminder"></a>Configure o Conector de Código Zero Maverics &trade; para o SiteMinder

O conector SiteMinder é utilizado para migrar os utilizadores para a Azure AD e para iniciar sessão de utilizadores para aplicações antigas no local protegidas pelo SiteMinder utilizando as identidades e credenciais AD do Azure recém-criados.

Para este tutorial, o SiteMinder foi configurado para proteger a aplicação do legado com autenticação baseada em formulários e usar o `SMSESSION` cookie. Para se integrar com uma aplicação que consome autenticação e sessão através de cabeçalhos http, terá de adicionar a configuração de emulação do cabeçalho ao conector.

Este exemplo `username` mapeia o atributo ao `SM_USER` cabeçalho http:
```yaml
  headers:
    SM_USER: username
```

Desa parte `proxyPass` para o local a que os pedidos são proxiemente. Normalmente, este é o anfitrião da aplicação protegida.

`loginPage` deve coincidir com o URL do formulário de login atualmente utilizado pelo SiteMinder ao redirecionar os utilizadores para a autenticação.

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

## <a name="configure-the-maverics-zero-code-connectortrade-for-ldap"></a>Configure o Conector de Código Zero Maverics &trade; para LDAP

Quando as aplicações são protegidas por um produto WAM, como o SiteMinder, as identidades e atributos do utilizador são normalmente armazenados num diretório LDAP.

Esta configuração do conector demonstra como ligar-se ao diretório LDAP configurado como a loja de utilizador do SiteMinder para que as informações corretas do perfil do utilizador possam ser recolhidas durante o fluxo de trabalho da migração e um utilizador correspondente possa ser criado em Azure AD.

* `baseDN` especifica a localização no diretório contra a qual efetuar a pesquisa LDAP.

* `url` é o endereço e a porta do servidor LDAP a que se deve ligar.

* `serviceAccountUsername` é o nome de utilizador utilizado para ligar ao servidor LDAP, normalmente expresso como um Bind DN, por exemplo `CN=Directory Manager` .

* `serviceAccountPassword` a palavra-passe utilizada para ligar ao servidor LDAP. Este valor é armazenado na instância Azure Key Vault configurada anteriormente.  

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

## <a name="configure-the-migration-workflow"></a>Configure o fluxo de trabalho migratório

A configuração do fluxo de trabalho de migração determina como os Maverics migrarão os utilizadores do SiteMinder/LDAP para a Azure AD.

Este fluxo de trabalho:
- Utiliza o conector SiteMinder para proxy o login siteMinder. As credenciais do utilizador são validadas através da autenticação SiteMinder e depois passadas para os passos subsequentes do fluxo de trabalho.
- Recupera os atributos do perfil do utilizador na loja de utilizadores do SiteMinder.
- Faz um pedido à Microsoft Graph API para criar o utilizador no seu inquilino AZure AD.

Passos:
1. Dê ao fluxo de trabalho um nome, por exemplo, SiteMinder para Azure AD Migration.
2. Especificar o `endpoint` , que é um caminho HTTP em que o fluxo de trabalho está exposto, o que desencadeia o `actions` fluxo de trabalho em resposta aos pedidos. A `endpoint` aplicação normalmente corresponde à aplicação que está a ser proxied, por `/my_app` exemplo. O valor deve incluir tanto os cortes de liderança como os que estão a seguir.
3. Adicione o adequado `actions` ao fluxo de trabalho.
    - Defina o `login` método para o conector SiteMinder. O valor do conector deve corresponder ao valor do nome na configuração do conector.
     - Defina o `getprofile` método para o conector LDAP.
     - Defina o `createuser` para o conector AzureAD.

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

1. Se o serviço Maverics ainda não estiver em funcionamento, inicie-o executando o seguinte comando: `sudo systemctl start maverics`

2. Navegue para o url de login proxied: `http://host.company.com/my_app` .
3. Forneça credenciais de utilizador utilizadas para iniciar sessão na aplicação enquanto protegida pelo SiteMinder.
4. Navegar para utilizadores de > em casa [ Todos os Utilizadores verifiquem se o seu utilizador foi criado no inquilino AZURE AD.  

## <a name="configure-the-session-abstraction-workflow"></a>Configure o fluxo de trabalho de abstração de sessão

O fluxo de trabalho de abstração da sessão move a autenticação e o controlo de acesso para a aplicação web legacy on-in para Azure AD.

O conector Azure utiliza o `login` método para redirecionar o utilizador para o URL de login, assumindo que não existe sessão.

Uma vez autenticado, o token de sessão criado como resultado é passado para Maverics e o método do conector SiteMinder `emulate` é usado para imitar a sessão baseada em cookies e/ou a sessão baseada no cabeçalho e, em seguida, decorar o pedido com quaisquer atributos adicionais exigidos pela aplicação.

1. Dê ao fluxo de trabalho um nome, por exemplo, Resumo da Sessão SiteMinder.
2. Especifique o `endpoint` , que corresponde à aplicação que está sendo proxied. O valor deve incluir cortes de liderança e de fuga, por `/my_app/` exemplo.
3. Adicione o adequado `actions` ao fluxo de trabalho.
    - Defina o `login` método para o conector Azure. O `connector` valor deve corresponder ao valor na `name` configuração do conector.
    - Defina o `emulate` método para o conector SiteMinder.

     ```yaml
      - name: SiteMinder Session Abstraction
        endpoint: /my_app/
        actions:
      - connector: azure
        method: login
      - connector: siteminder-login-form
        method: emulate
     ```
### <a name="verify-the-session-abstraction-workflow"></a>Verifique o fluxo de trabalho de Abstração de Sessão

1. Navegue para o URL de aplicação proxied: `https://<AZURECOMPANY.COM>/<MY_APP>` . O utilizador será redirecionado para a página de login.
2. Insira as credenciais de utilizador da AZure.
3. O utilizador deve ser redirecionado para a aplicação como se fosse autenticado diretamente pelo SiteMinder.
