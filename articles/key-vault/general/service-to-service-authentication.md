---
title: Autenticação serviço-a-serviço para cofre chave Azure usando .NET
description: Utilize a biblioteca Microsoft.Azure.Services.Services.AppAuthentication para autenticar o Cofre de Chaves Azure utilizando .NET.
keywords: credenciais locais de autenticação chave-cofre azul
author: msmbaldwin
manager: rkarlin
services: key-vault
ms.author: mbaldwin
ms.date: 08/28/2019
ms.topic: conceptual
ms.service: key-vault
ms.subservice: general
ms.openlocfilehash: cd630acfd65f0a79c186ba35bc15627bf7ccfdbe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81686199"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Autenticação serviço-a-serviço para cofre chave Azure usando .NET

Para autenticar o Cofre chave Azure, precisa de uma credencial azure Ative Directory (Azure AD), seja um segredo partilhado ou um certificado.

Gerir tais credenciais pode ser difícil. É tentador embalar credenciais numa aplicação, incluindo-as em ficheiros de origem ou configuração. A `Microsoft.Azure.Services.AppAuthentication` biblioteca para .NET simplifica este problema. Usa as credenciais do desenvolvedor para autenticar durante o desenvolvimento local. Quando a solução é posteriormente implantada para o Azure, a biblioteca muda automaticamente para credenciais de aplicação. Usar credenciais de desenvolvedordurante o desenvolvimento local é mais seguro porque você não precisa criar credenciais Azure AD ou partilhar credenciais entre desenvolvedores.

A `Microsoft.Azure.Services.AppAuthentication` biblioteca gere a autenticação automaticamente, o que por sua vez permite focar-se na sua solução, em vez das suas credenciais. Suporta o desenvolvimento local com o Microsoft Visual Studio, Azure CLI ou Azure AD Integrated Authentication. Quando implantada num recurso Azure que suporta uma identidade gerida, a biblioteca utiliza automaticamente [identidades geridas para recursos Azure.](../../active-directory/msi-overview.md) Não são necessárias alterações de código ou configuração. A biblioteca também suporta o uso direto das [credenciais](../../azure-resource-manager/resource-group-authenticate-service-principal.md) de cliente Azure AD quando uma identidade gerida não está disponível, ou quando o contexto de segurança do desenvolvedor não pode ser determinado durante o desenvolvimento local.

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) ou [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/).

- A extensão de autenticação de aplicações para o Estúdio Visual, disponível como uma extensão separada para visual studio 2017 Update 5 e agregada com o produto no Update 6 e posteriormente. Com o Update 6 ou mais tarde, pode verificar a instalação da extensão de autenticação da aplicação selecionando ferramentas de Desenvolvimento Azure a partir do instalador do Estúdio Visual.

## <a name="using-the-library"></a>Usando a biblioteca

Para aplicações .NET, a forma mais simples de trabalhar `Microsoft.Azure.Services.AppAuthentication` com uma identidade gerida é através do pacote. Eis como começar:

1. Selecione **Ferramentas** > **NuGet Package Manager** > **Gere pacotes NuGet para solução** para adicionar referências aos pacotes [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet para o seu projeto.

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

A `AzureServiceTokenProvider` classe cacheo o símbolo na memória e o recupera de Azure AD pouco antes de expirar. Então, já não tens de verificar a `GetAccessTokenAsync` expiração antes de ligares para o método. Basta chamar o método quando quiser usar o símbolo.

O `GetAccessTokenAsync` método requer um identificador de recursos. Para saber mais sobre os serviços do Microsoft Azure, veja [o que é gerida identidades para os recursos Do Azure.](../../active-directory/msi-overview.md)

## <a name="local-development-authentication"></a>Autenticação do desenvolvimento local

Para o desenvolvimento local, existem dois cenários de autenticação primária: [autenticação aos serviços Azure,](#authenticating-to-azure-services)e [autenticação a serviços personalizados.](#authenticating-to-custom-services)

### <a name="authenticating-to-azure-services"></a>Autenticação aos Serviços Azure

As máquinas locais não suportam identidades geridas para os recursos do Azure. Como resultado, `Microsoft.Azure.Services.AppAuthentication` a biblioteca usa as suas credenciais de desenvolvimento para funcionar no seu ambiente de desenvolvimento local. Quando a solução é implantada para o Azure, a biblioteca usa uma identidade gerida para mudar para um fluxo de subvenção credencial de cliente OAuth 2.0. Esta abordagem significa que pode testar o mesmo código localmente e remotamente sem preocupações.

Para o `AzureServiceTokenProvider` desenvolvimento local, rebusca tokens utilizando **o Visual Studio,** a **interface de linha de comando Azure** (CLI) ou a **Autenticação Integrada Azure AD.** Cada opção é experimentada sequencialmente e a biblioteca usa a primeira opção que sucede. Se nenhuma opção `AzureServiceTokenProviderException` funcionar, uma exceção é lançada com informações detalhadas.

#### <a name="authenticating-with-visual-studio"></a>Autenticação com Estúdio Visual

Autenticar utilizando o Estúdio Visual:

1. Inscreva-se no Estúdio Visual e utilize**opções** de **ferramentas**&nbsp;>&nbsp;para abrir **opções.**

1. Selecione **Autenticação de Serviço Azure,** escolha uma conta para o desenvolvimento local e selecione **OK**.

Se tiver problemas com o Visual Studio, tais como erros que envolvam o ficheiro do fornecedor simbólico, reveja cuidadosamente os passos anteriores.

Pode ter de reautenticar o seu símbolo de desenvolvimento. Para isso, selecione **Tools**&nbsp;>&nbsp;**Options**, e, em seguida, selecione Autenticação de **&nbsp;&nbsp;Serviço Azure**. Procure um link **reautenticado** na conta selecionada. Selecione-o para autenticar.

#### <a name="authenticating-with-azure-cli"></a>Autenticação com Azure CLI

Para utilizar o Azure CLI para o desenvolvimento local, certifique-se de que tem versão [Azure CLI v2.0.12](/cli/azure/install-azure-cli) ou posterior.

Para utilizar o Azure CLI:

1. Procure o Azure CLI na barra de tarefas do Windows para abrir o Pedido de **Comando Microsoft Azure**.

1. Inicie sessão no portal Azure: *az login* para iniciar sessão no Azure.

1. Verifique o acesso ao introduzir a *conta az get-access-token --recurso https:\//vault.azure.net*. Se receber um erro, verifique se a versão certa do Azure CLI está corretamente instalada.

   Se o Azure CLI não estiver instalado no diretório predefinido, poderá receber um relatório de erro que `AzureServiceTokenProvider` não encontre o caminho para o Azure CLI. Utilize a variável ambiente **AzureCLIPath** para definir a pasta de instalação Azure CLI. `AzureServiceTokenProvider`adiciona o diretório especificado na variável ambiental **AzureCLIPath** à variável ambiente **Caminho,** quando necessário.

1. Se tiver assinado no Azure CLI usando várias contas ou a sua conta tiver acesso a várias subscrições, precisa especificar a subscrição a utilizar. Introduza o conjunto de conta az de comando *-- subscrição <>de subscrição-id *.

Este comando gera saída apenas por falha. Para verificar as definições da `az account list`conta corrente, introduza o comando .

#### <a name="authenticating-with-azure-ad-authentication"></a>Autenticação com autenticação AD Azure

Para utilizar a autenticação Azure AD, verifique se:

- O seu Diretório Ativo no local sincroniza com o Azure AD. Para mais informações, consulte O que é a [identidade híbrida com o Diretório Ativo Azure?](../../active-directory/connect/active-directory-aadconnect.md)

- O seu código está a funcionar num computador de domínio.

### <a name="authenticating-to-custom-services"></a>Autenticação a serviços personalizados

Quando um serviço chama os serviços Azure, as etapas anteriores funcionam porque os serviços do Azure permitem o acesso tanto aos utilizadores como às aplicações.

Ao criar um serviço que chame um serviço personalizado, utilize credenciais de cliente Azure AD para autenticação de desenvolvimento local. Existem duas opções:

- Utilize um diretor de serviço para assinar no Azure:

    1. Crie um diretor de serviço. Para mais informações, consulte Criar um diretor de [serviço Azure com o Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

    1. Utilize o Azure CLI para iniciar sessão com o seguinte comando:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Como o diretor de serviço pode não `--allow-no-subscriptions` ter acesso a uma subscrição, use o argumento.

- Utilize variáveis ambientais para especificar os principais detalhes do serviço. Para mais informações, consulte [Executar a aplicação utilizando um diretor de serviço](#running-the-application-using-a-service-principal).

Depois de ter assinado contrato `AzureServiceTokenProvider` com o Azure, usa o diretor de serviço para recuperar um símbolo para o desenvolvimento local.

Esta abordagem aplica-se apenas ao desenvolvimento local. Quando a sua solução é implantada para o Azure, a biblioteca muda para uma identidade gerida para autenticação.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Executar a aplicação utilizando identidade gerida ou identidade atribuída ao utilizador

Quando executa o seu código num Serviço de Aplicações Azure ou num Azure VM com uma identidade gerida, a biblioteca utiliza automaticamente a identidade gerida. Não são necessárias alterações de código, mas a identidade gerida deve *ter* permissões para o cofre chave. Você pode dar a identidade gerida *obter* permissões através das políticas de *acesso*do cofre chave .

Em alternativa, pode autenticar com uma identidade atribuída ao utilizador. Para obter mais informações sobre identidades atribuídas ao utilizador, consulte [identidades geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work). Para autenticar com uma identidade atribuída ao utilizador, é necessário especificar o ID do Cliente da identidade atribuída ao utilizador na cadeia de ligação. A cadeia de ligação é especificada no Suporte de [Cordas de Ligação](#connection-string-support).

## <a name="running-the-application-using-a-service-principal"></a>Executar a aplicação usando um Diretor de Serviço

Pode ser necessário criar uma credencial do Cliente Azure AD para autenticar. Esta situação pode acontecer nos seguintes exemplos:

- O seu código funciona num ambiente de desenvolvimento local, mas não sob a identidade do desenvolvedor. O Service Fabric, por exemplo, utiliza a [conta NetworkService](../../service-fabric/service-fabric-application-secret-management.md) para o desenvolvimento local.

- O seu código funciona num ambiente de desenvolvimento local e autentica-se num serviço personalizado, para que não possa usar a sua identidade de desenvolvedor.

- O seu código está a funcionar com um recurso de computação Azure que ainda não suporta identidades geridas para recursos Azure, como o Azure Batch.

Existem três métodos primários de utilização de um Diretor de Serviço para executar a sua aplicação. Para utilizar qualquer um deles, primeiro deve criar um diretor de serviço. Para mais informações, consulte Criar um diretor de [serviço Azure com o Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Use um certificado em keystore local para assinar em Azure AD

1. Crie um certificado principal de serviço utilizando o comando Azure CLI [ad sp create-for-rbac.](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Este comando cria um ficheiro .pem (chave privada) que está armazenado no seu diretório de casa. Implemente este certificado para a loja *LocalMachine* ou *CurrentUser.*

    > [!Important]
    > O comando CLI gera um ficheiro .pem, mas o Windows apenas fornece suporte nativo para certificados PFX. Para gerar um certificado PFX, utilize os comandos PowerShell aqui mostrados: [Crie o diretor de serviço com certificado auto-assinado](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate). Estes comandos também implantam automaticamente o certificado.

1. Definir uma variável ambiental chamada **AzureServicesAuthConnectionString** para o seguinte valor:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    Substitua *{AppId}*, *{TenantId}*, e *{Thumbprint}* com valores gerados no Passo 1. Substitua *{CertificateStore}* por *"LocalMachine"* ou *CurrentUser,* com base no seu plano de implementação.

1. Execute a aplicação.

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Use uma credencial secreta partilhada para assinar em Azure AD

1. Crie um certificado principal de serviço com uma palavra-passe utilizando o comando Azure CLI [ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) com o parâmetro --sdk-auth.

    ```azurecli
    az ad sp create-for-rbac --sdk-auth
    ```

1. Definir uma variável ambiental chamada **AzureServicesAuthConnectionString** para o seguinte valor:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    Substitua _{AppId}_, _{TenantId}_, e _{ClientSecret}_ com valores gerados no Passo 1.

1. Execute a aplicação.

Uma vez que tudo esteja configurado corretamente, não são necessárias mais alterações de código. `AzureServiceTokenProvider`utiliza a variável ambiental e o certificado para autenticar a AD Azure.

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Use um certificado no Cofre chave para assinar em Azure AD

Esta opção permite armazenar um certificado de cliente do diretor de serviço no Key Vault e usá-lo para autenticação principal de serviço. Pode utilizar esta opção para os seguintes cenários:

- Autenticação local, onde pretende autenticar usando um diretor de serviço explícito, e quer manter a credencial principal de serviço seguramente num cofre chave. A conta do construtor deve ter acesso ao cofre da chave.

- Autenticação a partir de Azure onde pretende utilizar credenciais explícitas e pretende manter a credencial principal de serviço seguramente num cofre chave. Você pode usar esta opção para um cenário de inquilino cruzado. A identidade gerida deve ter acesso ao cofre da chave.

A identidade gerida ou a sua identidade de desenvolvedor devem ter permissão para recuperar o certificado de cliente do Cofre chave. A biblioteca AppAuthentication utiliza o certificado recuperado como credencial de cliente do diretor de serviço.

Utilizar um certificado de cliente para autenticação principal de serviço:

1. Crie um certificado principal de serviço e guarde-o automaticamente no seu Cofre chave. Utilize o Azure CLI [ad sp create-for-rbac \<-keyvault keyvault \<name> --cert certificatename> --create-cert -- comando de atribuição de skip::](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    O identificador de certificado será um URL no formato`https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. Substitua `{KeyVaultCertificateSecretIdentifier}` nesta cadeia de ligação com o identificador de certificado:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Por exemplo, se o seu cofre de chaves se chamasse *myKeyVault* e criasse um certificado chamado *myCert,* o identificador de certificado seria:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>Suporte de cordas de ligação

Por padrão, `AzureServiceTokenProvider` utiliza vários métodos para recuperar um símbolo.

Para controlar o processo, utilize uma `AzureServiceTokenProvider` cadeia de ligação passada ao construtor ou especificada na variável ambiente *AzureServicesAuthConnectionString.*

As seguintes opções são suportadas:

| Opção de corda de ligação | Cenário | Comentários|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Desenvolvimento local | `AzureServiceTokenProvider`usa o AzureCli para obter ficha. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Desenvolvimento local | `AzureServiceTokenProvider`usa o Estúdio Visual para obter ficha. |
| `RunAs=CurrentUser` | Desenvolvimento local | `AzureServiceTokenProvider`usa autenticação integrada Azure AD para obter ficha. |
| `RunAs=App` | [Identidades geridas para os recursos do Azure](../../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider`usa uma identidade gerida para obter ficha. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Identidade atribuída ao utilizador para recursos Azure](../../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | `AzureServiceTokenProvider`usa uma identidade atribuída ao utilizador para obter ficha. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Autenticação de serviços personalizados | `KeyVaultCertificateSecretIdentifier`é o identificador secreto do certificado. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Service principal (Principal de serviço) | `AzureServiceTokenProvider`usa certificado para obter ficha da Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Service principal (Principal de serviço) | `AzureServiceTokenProvider`usa certificado para obter ficha da Azure AD|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Service principal (Principal de serviço) |`AzureServiceTokenProvider`usa o segredo para obter ficha da AD Azure. |

## <a name="samples"></a>Amostras

Para ver `Microsoft.Azure.Services.AppAuthentication` a biblioteca em ação, consulte as seguintes amostras de código.

- [Use uma identidade gerida para recuperar um segredo do Cofre chave Azure em tempo de funcionação](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [Implemente programáticamente um modelo de Gestor de Recursos Azure a partir de um VM Azure com uma identidade gerida](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

- [Utilize a amostra .NET Core e uma identidade gerida para ligar para os serviços Azure a partir de um VM Azure Linux](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).

## <a name="appauthentication-troubleshooting"></a>Resolução de problemas de autenticação de aplicações

### <a name="common-issues-during-local-development"></a>Questões comuns durante o desenvolvimento local

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>O Azure CLI não está instalado, não está saqueado, ou não tem a versão mais recente.

Gere a *conta az get-token* para ver se o Azure CLI mostra um símbolo para si. Se não for **encontrado tal programa,** instale a [versão mais recente do Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Ser-lhe-á pedido que inicie sessão.

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>AzureServiceTokenProvider não consegue encontrar o caminho para o Azure CLI

O AzureServiceTokenProvider procura o Azure CLI nas suas instalações predefinidas. Se não conseguir encontrar o Azure CLI, detete a variável ambiental **AzureCLIPath** para a pasta de instalação Azure CLI. O AzureServiceTokenProvider adicionará a variável ambiental à variável ambiente Path.

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>Você está registrado no Azure CLI usando várias contas, a mesma conta tem acesso a subscrições em vários inquilinos, ou você obtém um erro de Access Negado ao tentar fazer chamadas durante o desenvolvimento local

Utilizando o Azure CLI, detete a subscrição predefinida para uma que tenha a conta que pretende utilizar. A subscrição deve estar no mesmo inquilino que o recurso a que pretende aceder: **az account set -- subscrição [subscrição-id]**. Se não se vê nenhuma saída, conseguiu. Verifique se a conta certa é agora o padrão usando a lista de **conta az**.

### <a name="common-issues-across-environments"></a>Questões comuns em todos os ambientes

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>Acesso não autorizado, acesso negado, proibido ou erro semelhante

O principal utilizado não tem acesso ao recurso que está a tentar aceder. Conceda o acesso à conta de utilizador ou ao MSI "Contributor" do Serviço de Aplicações a um recurso. Qual depende se está a executar a amostra no seu computador local ou implantada em Azure para o seu Serviço de Aplicações. Alguns recursos, como os cofres-chave, também têm as suas próprias políticas de [acesso](https://docs.microsoft.com/azure/key-vault/secure-your-key-vault#data-plane-and-access-policies) que utiliza para conceder acesso a diretores, como utilizadores, apps e grupos.

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Questões comuns quando implantados no Serviço de Aplicações Azure

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>Identidade gerida não está configurada no Serviço de Aplicações

Verifique as variáveis ambientais MSI_ENDPOINT e MSI_SECRET existem usando [a consola Kudu debug](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/). Se estas variáveis ambientais não existirem, a Identidade Gerida não está ativada no Serviço de Aplicações.

### <a name="common-issues-when-deployed-locally-with-iis"></a>Questões comuns quando implantadas localmente com o IIS

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>Não se consegue recuperar fichas ao depurar app no IIS

Por padrão, o AppAuth funciona num contexto de utilizador diferente no IIS. É por isso que não tem acesso a usar a sua identidade de desenvolvedor para obter fichas de acesso. Pode configurar o IIS para executar com o contexto do utilizador com os seguintes dois passos:
- Configure o Pool de Aplicações para que a aplicação web corra como a sua conta de utilizador corrente. Veja mais informações [aqui](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities)
- Configure "setProfileEnvironment" para "True". Veja mais informações [aqui.](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) 

    - Vá a %windir%\System32\inetsrv\config\applicationHost.config
    - Procure por "setProfileEnvironment". Se for definido como "Falso", mude-o para "True". Se não estiver presente, adicione-o como um atributo/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironmentao elemento processModel ( ), e coloque-o em "True".

- Saiba mais sobre [identidades geridas para os recursos Azure.](../../active-directory/managed-identities-azure-resources/index.yml)
- Saiba mais sobre cenários de [autenticação da AD Azure.](../../active-directory/develop/active-directory-authentication-scenarios.md)
