---
title: Autenticação serviço a serviço para Azure Key Vault usando o .NET
description: Use a biblioteca Microsoft. Azure. Services. AppAuthentication para autenticar para Azure Key Vault usando o .NET.
keywords: credenciais locais de autenticação do cofre de chaves do Azure
author: msmbaldwin
manager: rkarlin
services: key-vault
ms.author: mbaldwin
ms.date: 08/28/2019
ms.topic: conceptual
ms.service: key-vault
ms.openlocfilehash: f98df297eceb0a85a214bc046d33bd4c08d20030
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70884803"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Autenticação serviço a serviço para Azure Key Vault usando o .NET

Para autenticar no Azure Key Vault, você precisa de uma credencial do Azure Active Directory (Azure AD), um segredo compartilhado ou um certificado.

O gerenciamento dessas credenciais pode ser difícil. É tentador agrupar credenciais em um aplicativo incluindo-as em arquivos de origem ou de configuração. A `Microsoft.Azure.Services.AppAuthentication` biblioteca do para .NET simplifica esse problema. Ele usa as credenciais do desenvolvedor para autenticar durante o desenvolvimento local. Quando a solução for implantada posteriormente no Azure, a biblioteca alternará automaticamente para as credenciais do aplicativo. O uso de credenciais de desenvolvedor durante o desenvolvimento local é mais seguro porque você não precisa criar credenciais do Azure AD nem compartilhar credenciais entre desenvolvedores.

A `Microsoft.Azure.Services.AppAuthentication` biblioteca gerencia a autenticação automaticamente, o que, por sua vez, permite que você se concentre em sua solução, em vez de suas credenciais. Ele dá suporte ao desenvolvimento local com Microsoft Visual Studio, CLI do Azure ou autenticação integrada do Azure AD. Quando implantado em um recurso do Azure que dá suporte a uma identidade gerenciada, a biblioteca usa automaticamente [identidades gerenciadas para recursos do Azure](../active-directory/msi-overview.md). Nenhuma alteração de código ou configuração é necessária. A biblioteca também dá suporte ao uso direto de [credenciais de cliente](../azure-resource-manager/resource-group-authenticate-service-principal.md) do Azure ad quando uma identidade gerenciada não está disponível ou quando o contexto de segurança do desenvolvedor não pode ser determinado durante o desenvolvimento local.

## <a name="prerequisites"></a>Pré-requisitos

- [Visual studio 2019](https://www.visualstudio.com/downloads/) ou [Visual Studio 2017 v 15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/).

- A [extensão de autenticação de aplicativo para o Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354), disponível como uma extensão separada para a atualização 5 do visual Studio 2017 e agrupada com o produto na atualização 6 e posterior. Com a atualização 6 ou posterior, você pode verificar a instalação da extensão de autenticação do aplicativo selecionando Ferramentas de desenvolvimento do Azure no instalador do Visual Studio.

## <a name="using-the-library"></a>Usando a biblioteca

Para aplicativos .net, a maneira mais simples de trabalhar com uma identidade gerenciada é por `Microsoft.Azure.Services.AppAuthentication` meio do pacote. Veja como começar:

1. Selecione **ferramentas** > **Gerenciador** [](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) [](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) de pacotes NuGet gerenciar pacotes NuGet para solução para adicionar referências ao Microsoft. Azure. Services. AppAuthentication e ao Microsoft. Azure. keyvault >  Pacotes NuGet para seu projeto.

1. Adicione o seguinte código:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

A `AzureServiceTokenProvider` classe armazena em cache o token na memória e o recupera do Azure ad logo antes da expiração. Portanto, você não precisa mais verificar a expiração antes de chamar `GetAccessTokenAsync` o método. Basta chamar o método quando desejar usar o token.

O `GetAccessTokenAsync` método requer um identificador de recurso. Para saber mais sobre os serviços de Microsoft Azure, confira [o que são identidades gerenciadas para recursos do Azure](../active-directory/msi-overview.md).

## <a name="local-development-authentication"></a>Autenticação de desenvolvimento local

Para o desenvolvimento local, há dois cenários de autenticação principais: [autenticação nos serviços do Azure](#authenticating-to-azure-services)e [autenticação para serviços personalizados](#authenticating-to-custom-services).

### <a name="authenticating-to-azure-services"></a>Autenticando nos serviços do Azure

Os computadores locais não dão suporte a identidades gerenciadas para recursos do Azure. Como resultado, a `Microsoft.Azure.Services.AppAuthentication` biblioteca usa suas credenciais de desenvolvedor para executar em seu ambiente de desenvolvimento local. Quando a solução é implantada no Azure, a biblioteca usa uma identidade gerenciada para alternar para um fluxo de concessão de credencial de cliente OAuth 2,0. Essa abordagem significa que você pode testar o mesmo código localmente e remotamente sem se preocupar.

Para o desenvolvimento local `AzureServiceTokenProvider` , o busca tokens usando o **Visual Studio**, a CLI ( **interface de linha de comando) do Azure ou a** **autenticação integrada do Azure ad**. Cada opção é tentada sequencialmente e a biblioteca usa a primeira opção que é realizada com sucesso. Se nenhuma opção funcionar, uma `AzureServiceTokenProviderException` exceção será lançada com informações detalhadas.

#### <a name="authenticating-with-visual-studio"></a>Autenticando com o Visual Studio

Para autenticar usando o Visual Studio:

1. Entre no Visual Studio e use**as opções** de **ferramentas**&nbsp;>&nbsp;para abrir **Opções**.

1. Selecione **autenticação de serviço do Azure**, escolha uma conta para o desenvolvimento local e selecione **OK**.

Se você encontrar problemas ao usar o Visual Studio, como erros que envolvem o arquivo do provedor de token, examine atentamente as etapas anteriores.

Talvez seja necessário autenticar novamente o token do desenvolvedor. Para fazer isso, selecione**Opções**de **ferramentas**&nbsp;>&nbsp;e, em seguida, selecione **autenticação de&nbsp;serviço&nbsp;do Azure**. Procure um link **autenticar novamente** na conta selecionada. Selecione-o para autenticar.

#### <a name="authenticating-with-azure-cli"></a>Autenticando com CLI do Azure

Para usar CLI do Azure para o desenvolvimento local, verifique se você tem a versão [CLI do Azure v 2.0.12](/cli/azure/install-azure-cli) ou posterior.

Para usar CLI do Azure:

1. Procure CLI do Azure na barra de tarefas do Windows para abrir o **prompt de comando Microsoft Azure**.

1. Entre no portal do Azure: *AZ login* para entrar no Azure.

1. Verifique o acesso digitando *AZ Account Get-Access-token*. Se você receber um erro, verifique se a versão correta do CLI do Azure está instalada corretamente.

   Se CLI do Azure não estiver instalado no diretório padrão, você poderá receber um relatório de erros `AzureServiceTokenProvider` que não pode localizar o caminho para CLI do Azure. Use a variável de ambiente **AzureCLIPath** para definir a pasta de instalação do CLI do Azure. `AzureServiceTokenProvider`Adiciona o diretório especificado na variável de ambiente **AzureCLIPath** à variável de ambiente **Path** quando necessário.

1. Se você tiver entrado no CLI do Azure usando várias contas ou sua conta tiver acesso a várias assinaturas, será necessário especificar a assinatura a ser usada. Insira o comando *AZ Account Set--subscription < subscription-id >* .

Esse comando gera a saída somente em caso de falha. Para verificar as configurações atuais da conta, digite o `az account list`comando.

#### <a name="authenticating-with-azure-ad-authentication"></a>Autenticando com a autenticação do Azure AD

Para usar a autenticação do Azure AD, verifique se:

- Seu Active Directory local é sincronizado com o Azure AD. Para obter mais informações, consulte [o que é identidade híbrida com Azure Active Directory?](../active-directory/connect/active-directory-aadconnect.md).

- Seu código está sendo executado em um computador ingressado no domínio.

### <a name="authenticating-to-custom-services"></a>Autenticando para serviços personalizados

Quando um serviço chama os serviços do Azure, as etapas anteriores funcionam porque os serviços do Azure permitem o acesso a usuários e aplicativos.

Ao criar um serviço que chama um serviço personalizado, use as credenciais de cliente do Azure AD para a autenticação de desenvolvimento local. Há duas opções:

- Use uma entidade de serviço para entrar no Azure:

    1. Crie uma entidade de serviço. Para obter mais informações, consulte [criar uma entidade de serviço do Azure com CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

    1. Use CLI do Azure para entrar com o seguinte comando:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Como a entidade de serviço pode não ter acesso a uma assinatura, use `--allow-no-subscriptions` o argumento.

- Use variáveis de ambiente para especificar detalhes da entidade de serviço. Para obter mais informações, consulte [executando o aplicativo usando uma entidade de serviço](#running-the-application-using-a-service-principal).

Depois de entrar no Azure, `AzureServiceTokenProvider` o usa a entidade de serviço para recuperar um token para o desenvolvimento local.

Essa abordagem se aplica somente ao desenvolvimento local. Quando sua solução é implantada no Azure, a biblioteca alterna para uma identidade gerenciada para autenticação.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Executando o aplicativo usando identidade gerenciada ou identidade atribuída pelo usuário

Quando você executa o código em um serviço de Azure App ou em uma VM do Azure com uma identidade gerenciada habilitada, a biblioteca usa automaticamente a identidade gerenciada. Nenhuma alteração de código é necessária, mas a identidade gerenciada deve ter permissões *Get* para o cofre de chaves. Você pode conceder permissões de *obtenção* de identidade gerenciada por meio das *políticas de acesso*do Key Vault.

Como alternativa, você pode autenticar com uma identidade atribuída pelo usuário. Para obter mais informações sobre identidades atribuídas ao usuário, consulte [sobre identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work). Para autenticar com uma identidade atribuída pelo usuário, você precisa especificar a ID do cliente da identidade atribuída pelo usuário na cadeia de conexão. A cadeia de conexão é especificada no [suporte à cadeia de conexão](#connection-string-support).

## <a name="running-the-application-using-a-service-principal"></a>Executando o aplicativo usando uma entidade de serviço

Pode ser necessário criar uma credencial de cliente do Azure AD para autenticação. Essa situação pode ocorrer nos seguintes exemplos:

- Seu código é executado em um ambiente de desenvolvimento local, mas não sob a identidade do desenvolvedor. Service Fabric, por exemplo, usa a [conta NetworkService](../service-fabric/service-fabric-application-secret-management.md) para o desenvolvimento local.

- Seu código é executado em um ambiente de desenvolvimento local e você autentica em um serviço personalizado, portanto, você não pode usar sua identidade de desenvolvedor.

- Seu código está sendo executado em um recurso de computação do Azure que ainda não dá suporte a identidades gerenciadas para recursos do Azure, como o lote do Azure.

Há três métodos principais de usar uma entidade de serviço para executar seu aplicativo. Para usar qualquer um deles, você deve primeiro criar uma entidade de serviço. Para obter mais informações, consulte [criar uma entidade de serviço do Azure com CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Usar um certificado no repositório de chaves local para entrar no Azure AD

1. Crie um certificado de entidade de serviço usando o comando CLI do Azure [AZ ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) .

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Esse comando cria um arquivo. PEM (chave privada) que é armazenado em seu diretório base. Implante esse certificado no repositório *LocalMachine* ou *CurrentUser* .

    > [!Important]
    > O comando da CLI gera um arquivo. PEM, mas o Windows só fornece suporte nativo para certificados PFX. Para gerar um certificado PFX, use os comandos do PowerShell mostrados aqui: [Crie uma entidade de serviço com um certificado autoassinado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate). Esses comandos também implantam o certificado automaticamente.

1. Defina uma variável de ambiente chamada **AzureServicesAuthConnectionString** com o seguinte valor:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    Substitua *{AppID}* , *{tenantid}* e *{Thumbprint}* pelos valores gerados na etapa 1. Substitua *{CertificateStore}* por *LocalMachine*' ou *CurrentUser*, com base no seu plano de implantação.

1. Execute a aplicação.

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Usar uma credencial de segredo compartilhado para entrar no Azure AD

1. Crie um certificado de entidade de serviço com uma senha usando [AZ ad SP Create-for-RBAC--password](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac).

1. Defina uma variável de ambiente chamada **AzureServicesAuthConnectionString** com o seguinte valor:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    Substitua _{AppID}_ , _{tenantid}_ e _{ClientSecret}_ pelos valores gerados na etapa 1.

1. Execute a aplicação.

Depois que tudo estiver configurado corretamente, nenhuma alteração de código adicional será necessária. `AzureServiceTokenProvider`usa a variável de ambiente e o certificado para autenticar no Azure AD.

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Usar um certificado no Key Vault para entrar no Azure AD

Essa opção permite armazenar o certificado de cliente de uma entidade de serviço em Key Vault e usá-lo para autenticação de entidade de serviço. Você pode usar essa opção para os seguintes cenários:

- Autenticação local, onde você deseja autenticar usando uma entidade de serviço explícita e deseja manter as credenciais da entidade de serviço com segurança em um cofre de chaves. A conta de desenvolvedor deve ter acesso ao cofre de chaves.

- Autenticação do Azure em que você deseja usar credencial explícita e deseja manter as credenciais da entidade de serviço com segurança em um cofre de chaves. Você pode usar essa opção para um cenário de locatário cruzado. A identidade gerenciada deve ter acesso ao key Vault.

A identidade gerenciada ou a identidade do desenvolvedor deve ter permissão para recuperar o certificado do cliente do Key Vault. A biblioteca AppAuthentication usa o certificado recuperado como a credencial do cliente da entidade de serviço.

Para usar um certificado de cliente para autenticação de entidade de serviço:

1. Crie um certificado de entidade de serviço e armazene-o automaticamente em seu Key Vault. Use o CLI do Azure [AZ ad SP Create-for-RBAC--keyvault \<keyvaultname >--certificado \<CertName >--Create-CERT--ignore-Assignment](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) comando:

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    O identificador de certificado será uma URL no formato`https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. Substituir `{KeyVaultCertificateSecretIdentifier}` nesta cadeia de conexão pelo identificador de certificado:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Por exemplo, se o cofre de chaves foi chamado de *myKeyVault* e você criou um certificado chamado *MyCert*, o identificador de certificado seria:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>Suporte à cadeia de conexão

Por padrão, `AzureServiceTokenProvider` o usa vários métodos para recuperar um token.

Para controlar o processo, use uma cadeia de conexão passada para `AzureServiceTokenProvider` o construtor ou especificada na variável de ambiente *AzureServicesAuthConnectionString* .

Há suporte para as seguintes opções:

| Opção de cadeia de conexão | Cenário | Comentários|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Desenvolvimento local | `AzureServiceTokenProvider`usa AzureCli para obter o token. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Desenvolvimento local | `AzureServiceTokenProvider`usa o Visual Studio para obter o token. |
| `RunAs=CurrentUser` | Desenvolvimento local | `AzureServiceTokenProvider`usa a autenticação integrada do Azure AD para obter o token. |
| `RunAs=App` | [Identidades geridas para os recursos do Azure](../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider`usa uma identidade gerenciada para obter o token. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Identidade atribuída pelo usuário para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | `AzureServiceTokenProvider`usa uma identidade atribuída pelo usuário para obter o token. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Autenticação de serviços personalizados | `KeyVaultCertificateSecretIdentifier`é o identificador secreto do certificado. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Principal de serviço | `AzureServiceTokenProvider`usa o certificado para obter o token do Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Principal de serviço | `AzureServiceTokenProvider`usa o certificado para obter o token do Azure AD|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Principal de serviço |`AzureServiceTokenProvider`usa o segredo para obter o token do Azure AD. |

## <a name="samples"></a>Amostras

Para ver a `Microsoft.Azure.Services.AppAuthentication` biblioteca em ação, consulte os exemplos de código a seguir.

- [Usar uma identidade gerenciada para recuperar um segredo de Azure Key Vault em tempo de execução](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [Implante programaticamente um modelo de Azure Resource Manager de uma VM do Azure com uma identidade gerenciada](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

- [Use o exemplo do .NET Core e uma identidade gerenciada para chamar os serviços do Azure de uma VM Linux do Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).

## <a name="appauthentication-troubleshooting"></a>Solução de problemas do AppAuthentication

### <a name="common-issues-during-local-development"></a>Problemas comuns durante o desenvolvimento local

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>O CLI do Azure não está instalado, você não está conectado ou não tem a versão mais recente

Execute *AZ Account Get-Access-token* para ver se CLI do Azure mostra um token para você. Se esse **programa não for encontrado**, instale a [versão mais recente do CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Ser-lhe-á pedido que inicie sessão.

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>O azureservicetokenprovider não pode localizar o caminho para CLI do Azure

O azureservicetokenprovider procura CLI do Azure em seus locais de instalação padrão. Se não for possível localizar CLI do Azure, defina a variável de ambiente **AzureCLIPath** para a pasta de instalação CLI do Azure. O azureservicetokenprovider adicionará a variável de ambiente à variável de ambiente Path.

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>Você está conectado em CLI do Azure usando várias contas, a mesma conta tem acesso a assinaturas em vários locatários ou você recebe um erro de acesso negado ao tentar fazer chamadas durante o desenvolvimento local

Usando CLI do Azure, defina a assinatura padrão como uma que tenha a conta que você deseja usar. A assinatura deve estar no mesmo locatário que o recurso que você deseja acessar: **AZ Account Set--Subscription [Subscription-ID]** . Se nenhuma saída for vista, ela foi bem-sucedida. Verifique se a conta certa agora é a padrão usando a **lista de contas AZ**.

### <a name="common-issues-across-environments"></a>Problemas comuns entre ambientes

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>Acesso não autorizado, acesso negado, proibido ou erro semelhante

A entidade de segurança usada não tem acesso ao recurso que está tentando acessar. Conceda à sua conta de usuário ou ao MSI "colaborador" do serviço de aplicativo o acesso a um recurso. Qual delas depende se você está executando o exemplo em seu computador local ou implantado no Azure em seu serviço de aplicativo. Alguns recursos, como cofres de chaves, também têm suas próprias [políticas de acesso](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) que você usa para conceder acesso a entidades de segurança, como usuários, aplicativos e grupos.

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Problemas comuns quando implantados no serviço Azure App

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>A identidade gerenciada não está configurada no serviço de aplicativo

Verifique as variáveis de ambiente MSI_ENDPOINT e MSI_SECRET existam usando o [console de depuração do kudu](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/). Se essas variáveis de ambiente não existirem, a identidade gerenciada não será habilitada no serviço de aplicativo.

### <a name="common-issues-when-deployed-locally-with-iis"></a>Problemas comuns quando implantados localmente com o IIS

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>Não é possível recuperar Tokens ao depurar o aplicativo no IIS

Por padrão, o AppAuth é executado em um contexto de usuário diferente no IIS. É por isso que ele não tem acesso para usar sua identidade de desenvolvedor para recuperar Tokens de acesso. Você pode configurar o IIS para ser executado com o seu contexto de usuário com as duas etapas a seguir:
- Configure o pool de aplicativos para que o aplicativo Web seja executado como sua conta de usuário atual. Veja mais informações [aqui](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities)
- Configure "setProfileEnvironment" como "true". Veja mais informações [aqui](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration). 

    - Vá para%windir%\System32\inetsrv\config\applicationHost.config
    - Pesquise por "setProfileEnvironment". Se estiver definido como "false", altere-o para "true". Se não estiver presente, adicione-o como um atributo ao elemento processModel (/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironment) e defina-o como "true".

- Saiba mais sobre [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/index.yml).
- Saiba mais sobre os [cenários de autenticação do Azure ad](../active-directory/develop/active-directory-authentication-scenarios.md).
