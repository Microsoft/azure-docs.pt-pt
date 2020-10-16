---
title: Autenticação de serviço-a-serviço para Azure Key Vault usando .NET
description: Utilize a biblioteca Microsoft.Azure.Services.AppAuthentication para autenticar o Cofre da Chave Azure utilizando .NET.
keywords: credenciais locais de autenticação azure-cofre
author: msmbaldwin
services: key-vault
ms.author: mbaldwin
ms.date: 09/04/2020
ms.topic: how-to
ms.service: key-vault
ms.openlocfilehash: 1a6ec20d860a409bbe7d3114c54e1e46a75968a0
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91970117"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Autenticação de serviço-a-serviço para Azure Key Vault usando .NET

> [!NOTE]
> **Microsoft.Azure.Services.AppAuthentication** já não é recomendado para usar com o novo Key Vault SDK. É substituído por uma nova **biblioteca de clientes Azure Identity** disponível para .NET, Java, TypeScript e Python e deve ser utilizado para todo o novo desenvolvimento. Mais informações podem ser encontradas aqui: [Autenticação para Key Vault em código](https://docs.microsoft.com/azure/key-vault/general/developers-guide#azure-identity-client-libraries).

Para autenticar o Azure Key Vault, precisa de uma credencial Azure Ative Directory (Azure AD), seja um segredo partilhado ou um certificado.

Gerir tais credenciais pode ser difícil. É tentador agregar credenciais numa aplicação, incluindo-as em ficheiros de origem ou configuração. A `Microsoft.Azure.Services.AppAuthentication` biblioteca para .NET simplifica este problema. Utiliza as credenciais do desenvolvedor para autenticar durante o desenvolvimento local. Quando a solução é posteriormente implantada no Azure, a biblioteca muda automaticamente para credenciais de aplicação. A utilização de credenciais de desenvolvedores durante o desenvolvimento local é mais segura porque não precisa de criar credenciais AZure AD ou partilhar credenciais entre desenvolvedores.

A `Microsoft.Azure.Services.AppAuthentication` biblioteca gere a autenticação automaticamente, o que por sua vez permite focar-se na sua solução, em vez das suas credenciais. Suporta o desenvolvimento local com Microsoft Visual Studio, Azure CLI ou Azure AD Integrated Authentication. Quando implantada num recurso Azure que suporta uma identidade gerida, a biblioteca utiliza automaticamente [identidades geridas para recursos Azure](../../active-directory/msi-overview.md). Não são necessárias alterações de código ou de configuração. A biblioteca também suporta o uso direto de [credenciais](../../azure-resource-manager/resource-group-authenticate-service-principal.md) de cliente Azure AD quando uma identidade gerida não está disponível, ou quando o contexto de segurança do desenvolvedor não pode ser determinado durante o desenvolvimento local.

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) ou [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/).

- A extensão de autenticação da app para o Visual Studio, disponível como uma extensão separada para Visual Studio 2017 Update 5 e agregada com o produto no Update 6 e posteriormente. Com o Update 6 ou posteriormente, pode verificar a instalação da extensão de Autenticação de Aplicação selecionando ferramentas de Desenvolvimento Azure a partir do instalador do Estúdio Visual.

## <a name="using-the-library"></a>Usando a biblioteca

Para aplicações .NET, a forma mais simples de trabalhar com uma identidade gerida é através do `Microsoft.Azure.Services.AppAuthentication` pacote. Eis como começar:

1. Selecione **Ferramentas**  >  **NuGet Package Manager**Gere  >  **pacotes NuGet para solução** para adicionar referências aos pacotes [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet ao seu projeto.

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

A classe de segurança de linha `AzureServiceTokenProvider` caches o símbolo na memória e o recupera do Azure AD pouco antes de expirar. Isso significa que nunca precisa de verificar a expiração do token antes de ligar para o `GetAccessTokenAsync` método. 

O `GetAccessTokenAsync` método requer um identificador de recursos. Para saber mais sobre os serviços microsoft Azure, consulte [o que é gerido para os recursos da Azure.](../../active-directory/msi-overview.md)

## <a name="local-development-authentication"></a>Autenticação do desenvolvimento local

Para o desenvolvimento local, existem dois cenários de autenticação primária: [autenticação nos serviços Azure,](#authenticating-to-azure-services)e [autenticação em serviços personalizados.](#authenticating-to-custom-services)

### <a name="authenticating-to-azure-services"></a>Autenticação para serviços Azure

Máquinas locais não suportam identidades geridas para recursos Azure. Como resultado, a `Microsoft.Azure.Services.AppAuthentication` biblioteca usa as suas credenciais de desenvolvimento para funcionar no seu ambiente de desenvolvimento local. Quando a solução é implantada para a Azure, a biblioteca usa uma identidade gerida para mudar para um fluxo de credencial de cliente OAuth 2.0. Esta abordagem significa que pode testar o mesmo código localmente e remotamente sem se preocupar.

Para o desenvolvimento local, `AzureServiceTokenProvider` procure fichas utilizando o **Visual Studio,** **Azure command-line interface** (CLI) ou **Azure AD Integrated Authentication**. Cada opção é tentada sequencialmente e a biblioteca usa a primeira opção que tem sucesso. Se nenhuma opção funcionar, uma `AzureServiceTokenProviderException` exceção é lançada com informações detalhadas.

#### <a name="authenticating-with-visual-studio"></a>Autenticação com Estúdio Visual

Para autenticar utilizando o Visual Studio:

1. Inscreva-se no Estúdio **Tools**Visual e utilize &nbsp; > &nbsp; **opções de ferramentas** para abrir **opções.**

1. Selecione **autenticação do serviço Azure,** escolha uma conta para o desenvolvimento local e selecione **OK**.

Se tiver problemas com o Visual Studio, como erros que envolvem o ficheiro do fornecedor de token, reveja cuidadosamente os passos anteriores.

Pode ter de reautorar o seu símbolo de desenvolvimento. Para tal, **Tools**selecione &nbsp; > &nbsp; **Opções de Ferramentas**e, em seguida, selecione ** &nbsp; &nbsp; Autenticação de Serviço Azure**. Procure um link **de autenticação autenticado** na conta selecionada. Selecione-o para autenticar.

#### <a name="authenticating-with-azure-cli"></a>Autenticação com Azure CLI

Para utilizar o Azure CLI para o desenvolvimento local, certifique-se de que tem a versão [Azure CLI v2.0.12](/cli/azure/install-azure-cli) ou posterior.

Para utilizar o Azure CLI:

1. Procure por Azure CLI na barra de tarefas do Windows para abrir o **Pedido de Comando Microsoft Azure**.

1. Inscreva-se no portal Azure: *az login* para iniciar sessão no Azure.

1. Verifique o acesso entrando na *conta az get-token --resource https: \/ /vault.azure.net*. Se receber um erro, verifique se a versão correta do Azure CLI está corretamente instalada.

   Se o Azure CLI não estiver instalado no diretório predefinido, poderá receber um relatório de erro que `AzureServiceTokenProvider` não encontre o caminho para o Azure CLI. Utilize a variável ambiente **AzureCLIPath** para definir a pasta de instalação Azure CLI. `AzureServiceTokenProvider` adiciona o diretório especificado na variável ambiente **AzureCLIPath** à variável **ambiente Path** quando necessário.

1. Se estiver inscrito no Azure CLI usando várias contas ou a sua conta tiver acesso a várias subscrições, tem de especificar a subscrição a utilizar. Introduza o conjunto de conta de comando *az --subscrição <>de subscrição . *

Este comando gera saída apenas por falha. Para verificar as definições da conta corrente, insira o comando `az account list` .

#### <a name="authenticating-with-azure-ad-authentication"></a>Autenticação com autenticação AD AZure

Para utilizar a autenticação AZure AD, verifique se:

- O seu Ative Directory no local sincroniza com a Azure AD. Para mais informações, veja [o que é a identidade híbrida com o Azure Ative Directory?](../../active-directory/connect/active-directory-aadconnect.md)

- O seu código está a ser colocado num computador ligado a domínios.

### <a name="authenticating-to-custom-services"></a>Autenticação para serviços personalizados

Quando um serviço chama os serviços Azure, os passos anteriores funcionam porque os serviços Azure permitem o acesso tanto aos utilizadores como às aplicações.

Ao criar um serviço que chame um serviço personalizado, use credenciais de cliente Azure AD para autenticação de desenvolvimento local. Existem duas opções:

- Utilize um principal de serviço para assinar no Azure:

    1. Criar um diretor de serviço. Para mais informações, consulte [Criar um diretor de serviço Azure com a Azure CLI.](/cli/azure/create-an-azure-service-principal-azure-cli)

    1. Utilize o Azure CLI para iniciar sação com o seguinte comando:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Como o diretor de serviço pode não ter acesso a uma subscrição, use o `--allow-no-subscriptions` argumento.

- Use variáveis ambientais para especificar os principais detalhes do serviço. Para obter mais informações, consulte [executar a aplicação utilizando um principal serviço.](#running-the-application-using-a-service-principal)

Depois de se inscrever no Azure, `AzureServiceTokenProvider` usa o diretor de serviço para recuperar um símbolo para o desenvolvimento local.

Esta abordagem aplica-se apenas ao desenvolvimento local. Quando a sua solução é implantada no Azure, a biblioteca muda para uma identidade gerida para autenticação.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Executar a aplicação utilizando identidade gerida ou identidade atribuída ao utilizador

Quando executa o seu código num Serviço de Aplicações Azure ou num VM Azure com uma identidade gerida ativada, a biblioteca utiliza automaticamente a identidade gerida. Não são necessárias alterações de código, mas a identidade gerida deve ter permissões *GET* para o cofre de chaves. Pode dar permissões *de* acesso à identidade gerida através das Políticas de *Acesso*do cofre da chave.

Em alternativa, pode autenticar com uma identidade atribuída ao utilizador. Para obter mais informações sobre identidades atribuídas ao utilizador, consulte [Sobre Identidades Geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types). Para autenticar com uma identidade atribuída ao utilizador, é necessário especificar o ID do Cliente da identidade atribuída ao utilizador na cadeia de ligação. A cadeia de ligação é especificada no [Suporte de Cordas de Ligação](#connection-string-support).

## <a name="running-the-application-using-a-service-principal"></a>Executar a aplicação usando um diretor de serviço

Pode ser necessário criar uma credencial do Cliente AD Azure para autenticar. Esta situação pode acontecer nos seguintes exemplos:

- O seu código funciona num ambiente de desenvolvimento local, mas não sob a identidade do desenvolvedor. O Service Fabric, por exemplo, utiliza a [conta NetworkService](../../service-fabric/service-fabric-application-secret-management.md) para o desenvolvimento local.

- O seu código funciona num ambiente de desenvolvimento local e autentica-se num serviço personalizado, para que não possa utilizar a sua identidade de desenvolvedor.

- O seu código está a ser executada num recurso de computação Azure que ainda não suporta identidades geridas para recursos Azure, como o Azure Batch.

Existem três métodos primários de utilização de um Diretor de Serviço para executar a sua aplicação. Para utilizar qualquer um deles, primeiro tem de criar um diretor de serviço. Para mais informações, consulte [Criar um diretor de serviço Azure com a Azure CLI.](/cli/azure/create-an-azure-service-principal-azure-cli)

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Use um certificado na loja local para assinar no Azure AD

1. Crie um certificado principal de serviço utilizando o comando Azure CLI [ad sp create-for-rbac.](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Este comando cria um ficheiro .pem (chave privada) que está armazenado no seu diretório de casa. Defina este certificado na loja *LocalMachine* ou *CurrentUser.*

    > [!Important]
    > O comando CLI gera um ficheiro .pem, mas o Windows apenas fornece suporte nativo para certificados PFX. Para gerar um certificado PFX, utilize os comandos PowerShell mostrados aqui: [Crie o principal de serviço com certificado auto-assinado](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate). Estes comandos também implantam automaticamente o certificado.

1. Desaprova uma variável ambiental chamada **AzureServicesAuthConnectionString** para o seguinte valor:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    Substitua *{AppId}*, *{TenantId}* e *{Thumbprint}* por valores gerados no Passo 1. Substitua *{CertificateStore}* por *LocalMachine*' ou *CurrentUser*, com base no seu plano de implantação.

1. Execute a aplicação.

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Use uma credencial secreta partilhada para assinar no Azure AD

1. Crie um certificado principal de serviço com uma palavra-passe utilizando o comando Azure CLI [ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) com o parâmetro --sdk-auth.

    ```azurecli
    az ad sp create-for-rbac --sdk-auth
    ```

1. Desaprova uma variável ambiental chamada **AzureServicesAuthConnectionString** para o seguinte valor:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    Substitua _{AppId}_, _{TenantId}_ e _{ClientSecret}_ por valores gerados no Passo 1.

1. Execute a aplicação.

Uma vez que tudo esteja configurado corretamente, não são necessárias mais alterações de código. `AzureServiceTokenProvider` utiliza a variável ambiente e o certificado para autenticar a Azure AD.

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Use um certificado em Key Vault para assinar no Azure AD

Esta opção permite armazenar o certificado de cliente de um principiante de serviço no Key Vault e usá-lo para autenticação principal de serviço. Pode utilizar esta opção para os seguintes cenários:

- Autenticação local, onde pretende autenticar usando um principal de serviço explícito, e quer manter a credencial principal de serviço seguramente num cofre de chaves. A conta do construtor deve ter acesso ao cofre da chave.

- Autenticação a partir de Azure onde pretende utilizar credenciais explícitas e quer manter a credencial principal de serviço seguramente num cofre de chaves. Você pode usar esta opção para um cenário de inquilino cruzado. A identidade gerida deve ter acesso ao cofre da chave.

A identidade gerida ou a sua identidade de desenvolvedor devem ter permissão para recuperar o certificado de cliente do Cofre-Chave. A biblioteca appAuthentication usa o certificado recuperado como credencial de cliente do principiante do serviço.

Para utilizar um certificado de cliente para autenticação principal de serviço:

1. Crie um certificado principal de serviço e armazene-o automaticamente no seu Cofre de Chaves. Utilize o comando Azure CLI [az ad sp create-for-rbac --keyvault \<keyvaultname> --cert \<certificatename> --create-cert --skip-assignment](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) command:

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    O identificador de certificado será um URL no formato `https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. Substitua `{KeyVaultCertificateSecretIdentifier}` nesta cadeia de ligação o identificador de certificado:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Por exemplo, se o seu cofre-chave fosse chamado *myKeyVault* e criasse um certificado chamado *myCert,* o identificador de certificado seria:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>Suporte de cadeia de ligação

Por predefinição, `AzureServiceTokenProvider` tenta os seguintes métodos de autenticação, a fim de recuperar um símbolo:

- [Uma identidade gerida para os recursos da Azure](../..//active-directory/managed-identities-azure-resources/overview.md)
- Autenticação do Estúdio Visual
- [Autenticação do Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)
- [Autenticação integrada do Windows](/aspnet/web-api/overview/security/integrated-windows-authentication)

Para controlar o processo, utilize uma cadeia de ligação passada ao `AzureServiceTokenProvider` construtor ou especificada na variável ambiente *AzureServicesAuthConnectionString.*  As seguintes opções são suportadas:

| Opção de cadeia de ligação | Cenário | Comentários|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Desenvolvimento local | `AzureServiceTokenProvider` usa AzureCli para obter ficha. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Desenvolvimento local | `AzureServiceTokenProvider` usa o Visual Studio para obter o token. |
| `RunAs=CurrentUser` | Desenvolvimento local | Não suportado em .NET Core. `AzureServiceTokenProvider` utiliza autenticação integrada Azure AD para obter ficha. |
| `RunAs=App` | [Identidades geridas para os recursos do Azure](../../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider` usa uma identidade gerida para obter ficha. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Identidade atribuída pelo utilizador para recursos Azure](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) | `AzureServiceTokenProvider` usa uma identidade atribuída ao utilizador para obter ficha. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Autenticação de serviços personalizados | `KeyVaultCertificateSecretIdentifier` é o identificador secreto do certificado. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Service principal (Principal de serviço) | `AzureServiceTokenProvider` usa certificado para obter ficha da Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Service principal (Principal de serviço) | `AzureServiceTokenProvider` usa certificado para obter token de Azure AD|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Service principal (Principal de serviço) |`AzureServiceTokenProvider` usa segredo para obter ficha de Azure AD. |

## <a name="samples"></a>Amostras

Para ver a `Microsoft.Azure.Services.AppAuthentication` biblioteca em ação, consulte as seguintes amostras de código.

- [Use uma identidade gerida para recuperar um segredo do Azure Key Vault no tempo de execução](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [Implementar programáticamente um modelo de Gestor de Recursos Azure a partir de um VM Azure com uma identidade gerida](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

- [Utilize uma amostra .NET Core e uma identidade gerida para ligar para os serviços Azure de um Azure Linux VM](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).

## <a name="appauthentication-troubleshooting"></a>AppAuthentication Resolução de problemas

### <a name="common-issues-during-local-development"></a>Questões comuns durante o desenvolvimento local

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>O Azure CLI não está instalado, não está a iniciar sessão, ou não tem a versão mais recente

Gere *a conta az obter acesso-token para* ver se o Azure CLI mostra um símbolo para si. Se diz que **não foi encontrado tal programa,** instale a versão mais recente do [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Ser-lhe-á pedido que inicie sessão.

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>AzureServiceTokenProvider não consegue encontrar o caminho para o Azure CLI

A AzureServiceTokenProvider procura o Azure CLI nas suas localizações de instalação predefinidas. Se não conseguir encontrar o Azure CLI, deslobre a variável ambiente **AzureCLIPath** para a pasta de instalação Azure CLI. O AzureServiceTokenProvider irá adicionar a variável ambiental à variável ambiente Path.

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>Você está registrado no Azure CLI usando várias contas, a mesma conta tem acesso a subscrições em vários inquilinos, ou você obtém um erro de Acesso Negado ao tentar fazer chamadas durante o desenvolvimento local

Utilizando o Azure CLI, desapedaça a subscrição padrão para uma que tenha a conta que pretende utilizar. A subscrição deve estar no mesmo inquilino que o recurso a que pretende aceder: **az conjunto de conta --subscrição [subscrição-id]**. Se nenhuma saída for vista, conseguiu. Verifique se a conta certa é agora o padrão utilizando **a lista de conta az**.

### <a name="common-issues-across-environments"></a>Questões comuns em todos os ambientes

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>Acesso não autorizado, acesso negado, proibido ou erro semelhante

O principal utilizado não tem acesso ao recurso que está a tentar aceder. Conceda a sua conta de utilizador ou o acesso "Contribuinte" do MSI do Serviço de Aplicações a um recurso. Qual depende se está a executar a amostra no seu computador local ou implementada em Azure para o seu Serviço de Aplicações. Alguns recursos, como cofres-chave, também têm as suas próprias políticas de [acesso](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#data-plane-and-access-policies) que você usa conceder acesso a diretores, como utilizadores, apps e grupos.

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Questões comuns quando implementadas no Azure App Service

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>Identidade gerida não está criada no Serviço de Aplicações

Verifique as variáveis ambientais MSI_ENDPOINT e MSI_SECRET existem usando a [consola Kudu depurg](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/). Se estas variáveis ambientais não existirem, a Identidade Gerida não está ativada no Serviço de Aplicações.

### <a name="common-issues-when-deployed-locally-with-iis"></a>Questões comuns quando implementadas localmente com iIS

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>Não é possível recuperar fichas ao depurar app no IIS

Por padrão, o AppAuth funciona num contexto de utilizador diferente no IIS. É por isso que não tem acesso a usar a sua identidade de desenvolvedor para recuperar fichas de acesso. Pode configurar o IIS para correr com o seu contexto de utilizador com os seguintes dois passos:
- Configure o Conjunto de Aplicações para que a aplicação web possa funcionar como conta de utilizador corrente. Veja mais informações [aqui](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities)
- Configure "setProfileEnvironment" para "True". Veja mais [aqui.](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) 

    - Ir para %windir%\System32\inetsrv\config\applicationHost.config
    - Procure por "setProfileEnvironment". Se estiver definido para "Falso", mude-o para "Verdadeiro". Se não estiver presente, adicione-o como um atributo ao elemento processModel ( /configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironment ) e desfiá-lo para "Verdadeiro".

- Saiba mais sobre [identidades geridas para recursos Azure.](../../active-directory/managed-identities-azure-resources/index.yml)
- Saiba mais sobre [os cenários de autenticação AD Azure](../../active-directory/develop/active-directory-authentication-scenarios.md).
