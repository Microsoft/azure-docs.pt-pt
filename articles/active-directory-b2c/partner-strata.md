---
title: Tutorial para configurar Azure Ative Directy B2C com Strata
titleSuffix: Azure AD B2C
description: Saiba como integrar a autenticação Azure AD B2C com o whoIam para verificação do utilizador
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: bddc4c64feb31f78bed482bbd729ab1c4b8e676e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96171420"
---
# <a name="tutorial-for-extending-azure-ad-b2c-to-protect-on-premises-applications-using-strata"></a>Tutorial para alargar a Azure AD B2C para proteger aplicações no local utilizando estratos

Neste tutorial de amostra, aprenda a integrar o Azure Ative Directory (AD) B2C com o Orquestrador de [Identidade Maverics](https://www.strata.io/maverics-identity-orchestrator/)de Strata.
O Orquestrador de Identidade Maverics estende o Azure AD B2C para proteger as aplicações no local. Liga-se a qualquer sistema de identidade, migra transparentemente utilizadores e credenciais, sincroniza políticas e configurações, e abstratas autenticação e gestão de sessão. A utilização de empresas strata pode rapidamente transitar do legado para Azure AD B2C sem reescrever aplicações. A solução possui os benefícios seguintes:

- **Sign-On single do cliente (SSO) para aplicações híbridas no local**: Azure AD B2C suporta o cliente SSO com orquestrador de identidade Maverics. Os utilizadores insinuam-se com as suas contas que estão hospedadas no Azure AD B2C ou no Social Identity provider (IdP). A Maverics estende o SSO a apps historicamente protegidas por sistemas de identidade legados como o Symantec SiteMinder.

- **Estender sSO baseado em padrões a aplicações sem reescrita-las**: Use Azure AD B2C para gerir o acesso ao utilizador e habilite sSO com Maverics Identity Orchestrator SAML ou OIDC Connectors.

- **Configuração fácil**: Azure AD B2C fornece uma interface de utilizador passo a passo simples para ligar conectores Maverics Identity Orchestrator SAML ou OIDC a Azure AD B2C.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de:

- Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

- Um [inquilino Azure AD B2C](./tutorial-create-tenant.md) que está ligado à sua assinatura Azure.

- Um caso de [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para armazenar segredos que são usados pelo Orquestrador de Identidade Maverics. É usado para ligar ao Azure AD B2C ou a outros fornecedores de atributos, como um diretório ou base de dados do Protocolo de Acesso ao Diretório Leve (LDAP).

- Um exemplo de [Maverics Identity Orchestrator](https://www.strata.io/maverics-identity-orchestrator/) que está instalado e a funcionar numa máquina virtual Azure ou no servidor no local à sua escolha. Para obter informações sobre como obter o software e acesso à documentação de instalação e configuração, contacte [a Strata](https://www.strata.io/contact/)

- Uma aplicação no local que irá transitar de um sistema de identidade legado para Azure AD B2C.

## <a name="scenario-description"></a>Descrição do cenário

A integração Maverics da Strata inclui os seguintes componentes:

- **Azure AD B2C**: O servidor de autorização responsável pela verificação das credenciais do utilizador. Os utilizadores autenticados podem aceder a aplicações no local utilizando uma conta local armazenada no diretório Azure AD B2C.

- **Um IdP social ou empresarial externo**: Pode ser qualquer fornecedor openID connect, Facebook, Google ou GitHub. Consulte informações sobre a utilização [de IDPs externos](./technical-overview.md#external-identity-providers) com Azure AD B2C.  

- **Strata's Maverics Identity Orchestrator**: O serviço que orquestra o sign-on do utilizador e passa a identidade para apps através de cabeçalhos HTTP.

O seguinte diagrama de arquitetura mostra a implementação.

![A imagem mostra a arquitetura de uma integração AD B2C Azure com Strata Maverics para permitir o acesso a aplicações híbridas](./media/partner-strata/strata-architecture-diagram.png)

| Passos | Description |
|:-------|:---------------|
| 1. | O utilizador faz um pedido de acesso à aplicação hospedada no local. O Orquestrador de Identidade Maverics solicita o pedido feito pelo utilizador à aplicação.|
| 2. | O Orquestrador verifica o estado de autenticação do utilizador. Se não receber um token de sessão, ou o token da sessão fornecido for inválido, então envia o utilizador para Azure AD B2C para autenticação.|
| 3. | A Azure AD B2C envia o pedido de autenticação para o IdP social configurado.|
| 4. | O IdP desafia o utilizador para obter credenciais. Dependendo do IdP, o utilizador poderá necessitar de autenticação multi-factor (MFA).|
| 5. | O IdP remete a resposta de autenticação para Azure AD B2C. Opcionalmente, o utilizador pode criar uma conta local no diretório Azure AD B2C durante este passo.|
| 6. | A Azure AD B2C envia o pedido do utilizador para o ponto final especificado durante o registo da app Orchestrator no inquilino Azure AD B2C.|
| 7. | O Orquestrador avalia as políticas de acesso e calcula valores de atributos a incluir nos cabeçalhos HTTP reencaminhados para a app. Durante este passo, o Orquestrador pode chamar os fornecedores de atributos adicionais para recuperar as informações necessárias para definir corretamente os valores do cabeçalho. O Orquestrador define os valores do cabeçalho e envia o pedido para a aplicação.|
| 8. | O utilizador está agora autenticado e tem acesso à aplicação.|

## <a name="get-maverics-identity-orchestrator"></a>Obter Maverics Identity Orchestrator
Para obter o software que utilizará para integrar a sua aplicação de legado no local com a Azure AD B2C, contacte [a Strata](https://www.strata.io/contact/). Depois de obter o software, siga os passos abaixo para determinar os pré-requisitos específicos do Orquestrador e execute os passos de instalação e configuração necessários.

## <a name="configure-your-azure-ad-b2c-tenant"></a>Configure o seu inquilino Azure AD B2C

1. **Registar a aplicação**

   a. [Registe o Orquestrador como um requerimento](./tutorial-register-applications.md?tabs=app-reg-ga) no inquilino Azure AD B2C.
   >[!Note]
   >Você vai precisar do nome do inquilino e identificador, identificação do cliente, segredo de cliente, reclamações configuradas, e redirecionar uri mais tarde quando configurar o seu exemplo de Orquestrador.

   b. Conceda permissões de API do Microsoft MS Graph às suas aplicações. A sua aplicação necessitará das seguintes permissões: `offline_access` `openid` . .

   c. Adicione um URI de redirecionamento para a sua aplicação. Este URI corresponderá ao `oauthRedirectURL` parâmetro da configuração do conector Azure AD B2C do seu Orquestrador, por exemplo, `https://example.com/oidc-endpoint` .

2. **Criar um fluxo de utilizador**: Crie um sinal e assine no fluxo do [utilizador](./tutorial-create-user-flows.md).

3. **Adicione um IdP**: Escolha inscrever-se no seu utilizador com uma conta local ou com um [IdP](./tutorial-add-identity-providers.md)social ou empresarial.

4. **Definir atributos do utilizador**: Defina os atributos a recolher durante a inscrição.

5. **Especificar pedidos** de pedido : Especifique os atributos a devolver à aplicação através da sua instância Orquestradora. O Orquestrador consome atributos de reclamações devolvidas pelo Azure AD B2C e pode obter atributos adicionais de outros sistemas de identidade conectados, tais como diretórios LDAP e bases de dados. Estes atributos são definidos em cabeçalhos HTTP e enviados para a aplicação a montante no local.

## <a name="configure-maverics-identity-orchestrator"></a>Configure Orquestrador de Identidade Maverics

Nas secções seguintes, vamos acompanhá-lo através dos passos necessários para configurar o seu exemplo de Orquestrador. Para obter apoio e documentação adicionais, contacte [a Strata.](https://www.strata.io/contact/)

### <a name="maverics-identity-orchestrator-server-requirements"></a>Requisitos do servidor do orquestrador de identidade Maverics

Pode executar o seu exemplo de Orquestrador em qualquer servidor, seja no local ou numa infraestrutura de nuvem pública por fornecedores como Azure, AWS ou GCP.

- OS: REHL 7.7 ou superior, CentOS 7+

- Disco: 10 GB (pequeno)

- Memória: 16 GB

- Portas: 22 (SSH/SCP), 443, 80

- Acesso à raiz para tarefas de instalação/administrativa

- Maverics Identity Orchestrator funciona como utilizador `maverics` sob `systemd`

- A saída de rede do servidor que acolhe o Maverics Identity Orchestrator com a capacidade de chegar ao seu inquilino AZure AD.

### <a name="install-maverics-identity-orchestrator"></a>Instalar Orquestrador de Identidade Maverics

1. Obtenha o mais recente pacote RPM da Maverics. Coloque a embalagem no sistema em que pretende instalar maverics. Se estiver a copiar o ficheiro para um anfitrião remoto, [o SCP](https://www.ssh.com/ssh/scp/) é uma ferramenta útil.

2. Para instalar o pacote Maverics, executar o seguinte comando substituindo o seu nome de ficheiro no lugar de `maverics.rpm` .

   `sudo rpm -Uvf maverics.rpm`

   Por predefinição, a Maverics é instalada no `/usr/local/bin` diretório.

3. Após a instalação da Maverics, funcionará como um serviço sob `systemd` .  Para verificar se o serviço Maverics está em execução, execute o seguinte comando:

   `sudo service maverics status`

  Se a instalação do Orquestrador tiver sido bem sucedida, deve ver uma mensagem semelhante a esta:

```
Redirecting to /bin/systemctl status maverics.service
  maverics.service - Maverics
  Loaded: loaded (/etc/systemd/system/maverics.service; enabled; vendor preset: disabled)
  Active: active (running) since Thu 2020-08-13 16:48:01 UTC; 24h ago
  Main PID: 330772 (maverics)
  Tasks: 5 (limit: 11389)
  Memory: 14.0M
  CGroup: /system.slice/maverics.service
          └─330772 /usr/local/bin/maverics --config /etc/maverics/maverics.yaml
  ```

4. Se o serviço Maverics não começar, execute o seguinte comando para investigar o problema:

   `journalctl --unit=maverics.service --reverse`

   A entrada de registo mais recente aparecerá no início da saída.

Após a instalação de Maverics, o ficheiro predefinido `maverics.yaml` é criado no `/etc/maverics` diretório.

Configuure o seu Orquestrador para proteger a aplicação. Integre com Azure AD B2C, armazene e recupere segredos do [Azure Key Vault](https://azure.microsoft.com/services/key-vault/?OCID=AID2100131_SEM_bf7bdd52c7b91367064882c1ce4d83a9:G:s&ef_id=bf7bdd52c7b91367064882c1ce4d83a9:G:s&msclkid=bf7bdd52c7b91367064882c1ce4d83a9). Defina o local onde o Orquestrador deve ler a sua configuração.

### <a name="supply-configuration-using-environment-variables"></a>Configuração de fornecimento usando variáveis ambientais

Forneça config aos seus exemplos de Orquestrador através de variáveis ambientais.

`MAVERICS_CONFIG`

Esta variável ambiente diz à instância orquestradora que os ficheiros de configuração YAML devem ser utilizados e onde encontrá-los durante o arranque ou reinicialização. Coloque a variável ambiente em `/etc/maverics/maverics.env` .

### <a name="create-the-orchestrators-tls-configuration"></a>Criar a configuração TLS do Orquestrador

O `tls` campo no seu declara as `maverics.yaml` configurações de segurança da camada de transporte que a sua instância orquestradora irá utilizar. Os conectores podem utilizar objetos TLS e o servidor Do Orquestrador.

A `maverics` chave está reservada para o servidor do Orquestrador. Todas as outras teclas estão disponíveis e podem ser usadas para injetar um objeto TLS num determinado conector.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```

### <a name="configure-the-azure-ad-b2c-connector"></a>Configure o Conector AD B2C Azure

Os orquestradores usam Conectores para integrar-se com fornecedores de autenticação e atributos. Neste caso, esta App Gateway de Orquestradores utiliza o conector Azure AD B2C como um fornecedor de autenticação e atributo. Azure AD B2C utiliza o IdP social para autenticação e, em seguida, atua como um provedor de atributos ao Orquestrador, passando atributos em reclamações definidas em cabeçalhos HTTP.  

A configuração deste Conector corresponde à aplicação registada no inquilino Azure AD B2C.

1. Copie a identificação do cliente, segredo, e redirecione a URI da sua app config no seu inquilino.

2. Dê ao seu Conector um nome, mostrado aqui como `azureADB2C` , e coloque o conector como `type` `azure` . Tome nota do nome do Conector, uma vez que este valor é utilizado em outros parâmetros de configuração abaixo.

3. Para esta integração, `authType` o deve ser definido para `oidc` .

4. Desa estava o ID do cliente copiado no passo 1 como o valor para o `oauthClientID` parâmetro.

5. Desateia o segredo do cliente que copiou no passo 1 como o valor para o `oauthClientSecret` parâmetro.

6. Desa redoja o URI de redireccionamento copiado no passo 1 como o valor para o `oauthRedirectURL` parâmetro.

7. O Conector Azure AD B2C OIDC utiliza o conhecido ponto final OIDC para descobrir metadados, incluindo URLs e chaves de assinatura. Desaponte o valor do `oidcWellKnownURL` ponto final do seu inquilino.

```yaml
connectors:
  name: azureADB2C
  type: azure
  oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
  oauthRedirectURL: https://example.com/oidc-endpoint
  oauthClientID: <azureADB2CClientID>
  oauthClientSecret: <azureADB2CClientSecret>
  authType: oidc
```

### <a name="define-azure-ad-b2c-as-your-authentication-provider"></a>Defina o Azure AD B2C como seu fornecedor de autenticação

Um fornecedor de autenticação determina como fazer a autenticação para um utilizador que não apresentou uma sessão válida como parte do pedido de recursos da aplicação. A configuração no seu inquilino Azure AD B2C determina como desafiar um utilizador para obter credenciais e aplicar políticas de autenticação adicionais. Por exemplo, exigir um segundo fator para completar o processo de autenticação e decidir quais as reclamações que devem ser devolvidas ao Portal da Aplicação do Orquestrador após a autenticação ter sucesso.

O valor do must corresponde ao `authProvider` valor do seu `name` Conector.

```yaml
authProvider: azureADB2C
```

### <a name="protect-your-on-premises-app-with-an-orchestrator-app-gateway"></a>Proteja a sua app no local com uma App Gateway de Orquestrador

A configuração do Portal de Aplicações do Orquestrador declara como o Azure AD B2C deve proteger a sua aplicação e como os utilizadores devem aceder à aplicação.

1. Crie um nome para o seu gateway de aplicações. Pode usar um nome amigável ou um nome de anfitrião totalmente qualificado como identificador para a sua aplicação.

2. Desemote o `location` . O exemplo aqui utiliza a raiz da aplicação `/` , no entanto, pode ser qualquer caminho URL da sua aplicação.

3. Defina a aplicação protegida `upstream` utilizando a convenção host:port: `https://example.com:8080` .

4. Desa ajuste os valores de erro e páginas não autorizadas.

5. Defina os nomes e valores de atributos HTTP que devem ser fornecidos à aplicação para estabelecer a autenticação e controlar o acesso à app. Os nomes dos cabeçalhos são arbitrários e normalmente correspondem à configuração da aplicação. Os valores de atributos são atribuídos pelo Conector que os fornece. No exemplo abaixo, os valores devolvidos do Azure AD B2C estão prefixados com o nome do Conector `azureADB2C` onde o sufixo é o nome do atributo que contém o valor exigido, por `given_name` exemplo.

6. Definir as políticas a avaliar e aplicar. Três ações são definidas: `allowUnauthenticated` `allowAnyAuthenticated` , e `allowIfAny` . Cada ação está associada a uma `resource` e a política é avaliada para `resource` isso.

>[!NOTE]
>Tanto e utilize extensões de `headers` serviço `policies` JavaScript ou GoLang para implementar uma lógica arbitrária que melhore significativamente as capacidades padrão.

```yaml
appgateways:
  - name: Sonar
    location: /
    upstream: https://example.com:8080
    errorPage: https://example.com:8080/sonar/error
    unauthorizedPage: https://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

### <a name="use-azure-key-vault-as-your-secrets-provider"></a>Use o Azure Key Vault como seu fornecedor de segredos

É importante guardar os segredos que o seu Orquestrador usa para se ligar ao Azure AD B2C e a qualquer outro sistema de identidade. Os Maverics não irão por defeito para carregar segredos em texto `maverics.yaml` simples, no entanto, neste tutorial, você usará Azure Key Vault como fornecedor de segredos.

Siga as instruções para [criar um novo Key Vault](../key-vault/secrets/quick-create-portal.md) que a sua instância orquestradora usará como fornecedor de segredos. Adicione os seus segredos ao seu cofre e tome nota do `SECRET NAME` dado a cada segredo. Por exemplo, `AzureADB2CClientSecret`.

Para declarar um valor como segredo num `maverics.yaml` ficheiro config, embrulhe o segredo com suportes angulares:

```yaml
connectors:
  - name: AzureADB2C
    type: azure
    oauthClientID: <AzureADB2CClientID>
    oauthClientSecret: <AzureADB2CClientSecret>
```

O valor especificado dentro dos suportes angulares deve corresponder `SECRET NAME` ao dado ao segredo no seu Cofre de Chave Azure.

Para carregar segredos do Azure Key Vault, desconfiem da variável ambiental `MAVERICS_SECRET_PROVIDER` no `/etc/maverics/maverics.env` ficheiro, com as credenciais encontradas no azure-credentials.jsno ficheiro, utilizando o seguinte padrão:

`MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

### <a name="put-everything-together"></a>Juntar tudo

Eis como aparecerá a configuração do Orquestrador quando completar as configurações descritas acima.

```yaml
version: 0.4.2
listenAddress: ":443"
tls:
  maverics:
    certFile: certs/maverics.crt
    keyFile: certs/maverics.key

authProvider: azureADB2C

connectors:
  - name: azureADB2C
    type: azure
    oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
    oauthRedirectURL: https://example.com/oidc-endpoint
    oauthClientID: <azureADB2CClientID>
    oauthClientSecret: <azureADB2CClientSecret>
    authType: oidc

appgateways:
  - name: Sonar
    location: /
    upstream: http://example.com:8080
    errorPage: http://example.com:8080/sonar/accessdenied
    unauthorizedPage: http://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

## <a name="test-the-flow"></a>Testar o fluxo

1. Navegue para o url de aplicação no local, `https://example.com/sonar/dashboard` .

2. O Orquestrador deve redirecionar para a página configurada no fluxo do seu utilizador.

3. Selecione o IdP da lista na página.

4. Assim que for redirecionado para o IdP, forneça as suas credenciais conforme solicitado, incluindo um token MFA, se necessário por esse IdP.

5. Após autenticação com sucesso, deverá ser redirecionado para Azure AD B2C, que remete o pedido da app para o URI do Orquestrador.

6. O Orquestrador avalia as políticas, calcula os cabeçalhos e envia o utilizador para a aplicação a montante.  

7. Devia ver o pedido solicitado.

## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais, reveja os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Começar com políticas personalizadas em Azure AD B2C](./custom-policy-get-started.md?tabs=applications)