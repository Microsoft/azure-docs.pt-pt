---
title: Proteger o Azure Remote Rendering e o armazenamento de modelos
description: Endurecimento de uma aplicação de renderização remota para garantir conteúdo
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 07374debf8d660d8f1c32788db3d218da611d539
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650481"
---
# <a name="tutorial-securing-azure-remote-rendering-and-model-storage"></a>Tutorial: Assegurar a renderização remota do Azure e o armazenamento do modelo

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Armazenamento Secure Azure Blob contendo modelos de renderização remota Azure
> * Autenticar com Azure AD para aceder à sua instância de renderização remota Azure
> * Utilize credenciais Azure para autenticação de renderização remota Azure

## <a name="prerequisites"></a>Pré-requisitos

* Este tutorial baseia-se em [Tutorial: Materiais de refinação, iluminação e efeitos.](..\materials-lighting-effects\materials-lighting-effects.md)

## <a name="why-additional-security-is-needed"></a>Por que a segurança adicional é necessária

O estado atual da aplicação e o seu acesso aos seus recursos Azure são assim:

![Segurança inicial](./media/security-one.png)

Tanto o "AccountID + AccountKey" como o "URL + SAS Token" estão essencialmente a armazenar um nome de utilizador e uma palavra-passe em conjunto. Por exemplo, se o "AccountID + AccountKey" fosse exposto, seria trivial para um intruso utilizar os seus recursos ARR sem a sua permissão às suas custas.

## <a name="securing-your-content-in-azure-blob-storage"></a>Segurança do seu conteúdo no Azure Blob Storage

A renderização remota Azure pode aceder de forma segura ao conteúdo do seu Azure Blob Storage com a configuração correta. Ver [Como fazer: Ligue as contas de armazenamento](../../../how-tos/create-an-account.md#link-storage-accounts) para configurar a sua instância de renderização remota Azure com as suas contas de armazenamento blob.

Ao utilizar um armazenamento de bolhas ligado, utilizará métodos ligeiramente diferentes para carregar modelos:

```csharp
var loadModelParams = new LoadModelFromSASParams(modelPath, modelEntity);
var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
```

As linhas acima utilizam a `FromSAS` versão dos params e a ação de sessão. Devem ser convertidos para as versões não SAS:

```csharp
var loadModelParams = new LoadModelParams(storageAccountPath, blobContainerName, modelPath, modelEntity);
var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelAsync(loadModelParams);
```

Vamos modificar **o RemoteRenderingCoordinator** para carregar um modelo personalizado, a partir de uma conta de armazenamento de bolhas ligada.

1. Se ainda não o fez, preencha as [contas de armazenamento de Ligação](../../../how-tos/create-an-account.md#link-storage-accounts) para conceder a sua permissão de exemplo ARR para aceder à sua instância de Armazenamento Blob.
1. Adicione o seguinte método **loadmodel** modificado ao **RemoteRenderingCoordinator** logo abaixo do método atual **do LoadModel:**

    ```csharp
    /// <summary>
    /// Loads a model from blob storage that has been linked to the ARR instance
    /// </summary>
    /// <param name="storageAccountName">The storage account name, this contains the blob containers </param>
    /// <param name="blobContainerName">The blob container name, i.e. arroutput</param>
    /// <param name="modelPath">The relative path inside the container to the model, i.e. test/MyCustomModel.arrAsset</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns></returns>
    public async Task<Entity> LoadModel(string storageAccountName, string blobContainerName, string modelPath, Transform parent = null, ProgressHandler progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Actions.CreateEntity();

        //Get the game object representation of this entity
        var modelGameObject = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        //Ensure the entity will sync its transform with the server
        var sync = modelGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        //Parent the new object under the defined parent
        if (parent != null)
        {
            modelGameObject.transform.SetParent(parent, false);
            modelGameObject.name = parent.name + "_Entity";
        }

    #if UNITY_WSA
        //Anchor the model in the world, prefer anchoring parent if there is one
        if (parent != null)
        {
            parent.gameObject.AddComponent<WorldAnchor>();
        }
        else
        {
            modelGameObject.AddComponent<WorldAnchor>();
        }
    #endif

        //Load a model that will be parented to the entity
        var loadModelParams = new LoadModelParams($"{storageAccountName}.blob.core.windows.net", blobContainerName, modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelAsync(loadModelParams);
        if (progress != null)
            loadModelAsync.ProgressUpdated += progress;
        var result = await loadModelAsync.AsTask();
        return modelEntity;
    }
    ```

    Na maior parte das vezes, este código é idêntico ao `LoadModel` método original, no entanto substituímos a versão SAS das chamadas de método pelas versões não-SAS.

    Os inputs adicionais `storageAccountName` e `blobContainerName` também foram adicionados aos argumentos. Vamos chamar este novo método **LoadModel** de outro método semelhante ao primeiro método **LoadTestModel** que criamos no primeiro tutorial.

1. Adicione o seguinte método ao **RemoteRenderingCoordinator** logo após **o LoadTestModel**

    ```csharp
    private bool loadingLinkedCustomModel = false;

    [SerializeField]
    private string storageAccountName;
    public string StorageAccountName {
        get => storageAccountName.Trim();
        set => storageAccountName = value;
    }

    [SerializeField]
    private string blobContainerName;
    public string BlobContainerName {
        get => blobContainerName.Trim();
        set => blobContainerName = value;
    }

    [SerializeField]
    private string modelPath;
    public string ModelPath {
        get => modelPath.Trim();
        set => modelPath = value;
    }

    [ContextMenu("Load Linked Custom Model")]
    public async void LoadLinkedCustomModel()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if (loadingLinkedCustomModel)
        {
            Debug.Log("Linked Test model already loading or loaded!");
            return;
        }
        loadingLinkedCustomModel = true;

        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("LinkedCustomModel");
        testParent.transform.position = new Vector3(0f, 0f, 3f);

        await LoadModel(StorageAccountName, BlobContainerName, ModelPath, testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```

    Este código adiciona três variáveis de corda adicionais ao seu componente **RemoteRenderingCoordinator.**
    ![Screenshot que realça o nome da conta de armazenamento, nome do recipiente blob e caminho do modelo do componente RemoteRenderingCoordinator.](./media/storage-account-linked-model.png)

1. Adicione os seus valores ao componente **RemoteRenderingCoordinator.** Depois de ter seguido o [Quickstart para conversão de modelos,](../../../quickstarts/convert-model.md)os seus valores devem ser:

    * **Nome da conta de armazenamento**: O nome da sua conta de armazenamento, o nome globalmente único que escolhe para a sua conta de armazenamento. No início rápido isto foi *arrtutorialstorage,* o seu valor será diferente.
    * **Nome do recipiente blob**: arroutput, o recipiente de armazenamento blob
    * **Caminho do Modelo**: A combinação do "outputFolderPath" e do "outputAssetFileName" definido no *arrconfig.jsem* ficheiro. No arranque rápido, este foi "outputFolderPath":"convertido/robô", "outputAssetFileName": "robot.arrAsset". O que resultaria num valor de Model Path de "convertido/robô/robot.arrAsset", o seu valor será diferente.

    >[!TIP]
    > Se [executar **Conversion.ps1** o](../../../quickstarts/convert-model.md#run-the-conversion)Conversion.ps1script, sem o argumento "UseContainerSas", o script irá descoduar todos os valores acima para o seu em vez do token SAS. ![Modelo ligado](./media/converted-output.png)
1. Por enquanto, remova ou desative o GameObject **TestModel,** para abrir espaço para o seu modelo personalizado carregar.
1. Toque a cena e ligue-se a uma sessão remota.
1. Clique no seu **RemoteRenderingCoordinator** e selecione **Load Linked Custom Model**.
    ![Modelo ligado à carga](./media/load-linked-model.png)

Estas medidas aumentaram a segurança do pedido removendo o token SAS da aplicação local.

Agora o estado atual da aplicação e o seu acesso aos seus recursos Azure são assim:

![Melhor segurança](./media/security-two.png)

Temos mais uma "senha", a AccountKey, para remover da aplicação local. Isto pode ser feito através da autenticação do Azure Ative Directory (AAD).

## <a name="azure-active-directory-azure-ad-authentication"></a>Autenticação do Azure Ative Directory (Azure AD)

A autenticação AAD permitir-lhe-á determinar quais indivíduos ou grupos estão a utilizar o ARR de uma forma mais controlada. A ARR construiu suporte para aceitar [Tokens de acesso](https://docs.microsoft.com/azure/active-directory/develop/access-tokens) em vez de usar uma Chave de Conta. Pode pensar no Access Tokens como uma chave limitada no tempo, específica para o utilizador, que apenas desbloqueia certas partes do recurso específico que foi solicitado.

O script **RemoteRenderingCoordinator** tem um delegado chamado **ARRCredentialGetter,** que detém um método que devolve um objeto **AzureFrontendAccountInfo,** que é usado para configurar a gestão de sessão remota. Podemos atribuir um método diferente ao **ARRCredentialGetter,** permitindo-nos usar um sinal Azure em fluxo, gerando um objeto **AzureFrontendAccountInfo** que contém um Azure Access Token. Este Token de Acesso será específico para o utilizador que está a iniciar sessão.

1. Siga a [autenticação como: Configurar a autenticação - Autenticação para aplicações implementadas](../../../how-tos/authentication.md#authentication-for-deployed-applications), especificamente seguirá as instruções enumeradas na documentação Azure Spatial Anchors [Azure AD autenticação](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication)do utilizador . O que envolve registar uma nova aplicação do Azure Ative Directory e configurar o acesso à sua instância ARR.
1. Depois de configurar a nova aplicação AAD, verifique se a sua aplicação AAD se parece com as seguintes imagens:

    **AAD Aplicação -Autenticação** ![ > Autenticação de aplicativos](./media/app-authentication-public.png)

    Permissões AAD **-> API** ![ APIs de aplicações](./media/request-api-permissions-step-five.png)

1. Depois de configurar a sua conta de renderização remota, verifique se a sua configuração se parece com a seguinte imagem:

    **AAR -> AccessControl (IAM)** ![ Papel ARR](./media/azure-remote-rendering-role-assignment-complete.png)

    >[!NOTE]
    > Uma função *de Proprietário* não é suficiente para gerir sessões através da aplicação do cliente. Para cada utilizador pretende conceder a capacidade de gerir sessões deve fornecer a função **Cliente de Renderização Remota**. Para cada utilizador que pretende gerir sessões e converter modelos, tem de fornecer o papel **administrador de renderização remota**.

Com o lado Azure das coisas no lugar, precisamos agora modificar a forma como o seu código se liga ao serviço AAR. Fazemos isso implementando uma instância de **BaseARRAuthentication**, que devolverá um novo objeto **AzureFrontendAccountInfo.** Neste caso, a informação da conta será configurada com o Azure Access Token.

1. Crie um novo script chamado **AADAuthentication** e substitua o seu código pelo seguinte:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Identity.Client;
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using UnityEngine;

    public class AADAuthentication : BaseARRAuthentication
    {
        [SerializeField]
        private string accountDomain;
        public string AccountDomain
        {
            get => accountDomain.Trim();
            set => accountDomain = value;
        }

        [SerializeField]
        private string activeDirectoryApplicationClientID;
        public string ActiveDirectoryApplicationClientID
        {
            get => activeDirectoryApplicationClientID.Trim();
            set => activeDirectoryApplicationClientID = value;
        }

        [SerializeField]
        private string azureTenantID;
        public string AzureTenantID
        {
            get => azureTenantID.Trim();
            set => azureTenantID = value;
        }

        [SerializeField]
        private string azureRemoteRenderingAccountID;
        public string AzureRemoteRenderingAccountID
        {
            get => azureRemoteRenderingAccountID.Trim();
            set => azureRemoteRenderingAccountID = value;
        }

        public override event Action<string> AuthenticationInstructions;

        string authority => "https://login.microsoftonline.com/" + AzureTenantID;

        string redirect_uri = "https://login.microsoftonline.com/common/oauth2/nativeclient";

        string[] scopes => new string[] { "https://sts.mixedreality.azure.com/mixedreality.signin" };

        public void OnEnable()
        {
            RemoteRenderingCoordinator.ARRCredentialGetter = GetAARCredentials;
            this.gameObject.AddComponent<ExecuteOnUnityThread>();
        }

        public async override Task<AzureFrontendAccountInfo> GetAARCredentials()
        {
            var result = await TryLogin();
            if (result != null)
            {
                Debug.Log("Account signin successful " + result.Account.Username);

                var AD_Token = result.AccessToken;

                return await Task.FromResult(new AzureFrontendAccountInfo(AccountDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
            }
            else
            {
                Debug.LogError("Error logging in");
            }
            return default;
        }

        private Task DeviceCodeReturned(DeviceCodeResult deviceCodeDetails)
        {
            //Since everything in this task can happen on a different thread, invoke responses on the main Unity thread
            ExecuteOnUnityThread.Enqueue(() =>
            {
                // Display instructions to the user for how to authenticate in the browser
                Debug.Log(deviceCodeDetails.Message);
                AuthenticationInstructions?.Invoke(deviceCodeDetails.Message);
            });

            return Task.FromResult(0);
        }

        public override async Task<AuthenticationResult> TryLogin()
        {
            var clientApplication = PublicClientApplicationBuilder.Create(ActiveDirectoryApplicationClientID).WithAuthority(authority).WithRedirectUri(redirect_uri).Build();
            AuthenticationResult result = null;
            try
            {
                var accounts = await clientApplication.GetAccountsAsync();

                if (accounts.Any())
                {
                    result = await clientApplication.AcquireTokenSilent(scopes, accounts.First()).ExecuteAsync();

                    return result;
                }
                else
                {
                    try
                    {
                        result = await clientApplication.AcquireTokenWithDeviceCode(scopes, DeviceCodeReturned).ExecuteAsync(CancellationToken.None);
                        return result;
                    }
                    catch (MsalUiRequiredException ex)
                    {
                        Debug.LogError("MsalUiRequiredException");
                        Debug.LogException(ex);
                    }
                    catch (MsalServiceException ex)
                    {
                        Debug.LogError("MsalServiceException");
                        Debug.LogException(ex);
                    }
                    catch (MsalClientException ex)
                    {
                        Debug.LogError("MsalClientException");
                        Debug.LogException(ex);
                        // Mitigation: Use interactive authentication
                    }
                    catch (Exception ex)
                    {
                        Debug.LogError("Exception");
                        Debug.LogException(ex);
                    }
                }
            }
            catch (Exception ex)
            {
                Debug.LogError("GetAccountsAsync");
                Debug.LogException(ex);
            }

            return null;
        }
    }
    ```

>[!NOTE]
> Este código não está de forma alguma completo e não está pronto para uma aplicação comercial. Por exemplo, no mínimo, provavelmente irá querer adicionar a capacidade de assinar também. Isto pode ser feito usando o `Task RemoveAsync(IAccount account)` método fornecido pela aplicação do cliente. Este código destina-se apenas a uso tutorial, a sua implementação será específica da sua aplicação.

O código tenta primeiro obter o símbolo silenciosamente usando **AquireTokenSilent**. Isto será bem sucedido se o utilizador tiver autenticado previamente esta aplicação. Se não for bem sucedido, passe para uma estratégia mais empresarial.

Para este código, estamos a usar o fluxo de código do [dispositivo](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code) para obter um Token de Acesso. Este fluxo permite ao utilizador iniciar seduca na sua conta Azure num computador ou dispositivo móvel e enviar o símbolo resultante para a aplicação HoloLens.

A parte mais importante desta classe do ponto de vista ARR é esta linha:

```csharp
return await Task.FromResult(new AzureFrontendAccountInfo(AccountDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
```

Aqui, criamos um novo objeto **AzureFrontendAccountInfo** usando o domínio da conta, iD de conta e token de acesso. Este token é então utilizado pelo serviço ARR para consultar, criar e aderir a sessões de renderização remotas desde que o utilizador seja autorizado com base nas permissões baseadas em funções configuradas anteriormente.

Com esta mudança, o estado atual da aplicação e o seu acesso aos seus recursos Azure são assim:

![Ainda melhor segurança](./media/security-three.png)

Uma vez que as Credenciais de Utilizador não são armazenadas no dispositivo (ou neste caso mesmo introduzidos no dispositivo), o seu risco de exposição é muito baixo. Agora, o dispositivo está a utilizar um Token de Acesso limitado por tempo do utilizador para aceder ao ARR, que utiliza o controlo de acesso (IAM) para aceder ao Blob Storage. Estes dois passos removeram completamente as "passwords" do código fonte e aumentaram consideravelmente a segurança. No entanto, esta não é a maior segurança disponível, mover o modelo e a gestão de sessão para um serviço web irá melhorar ainda mais a segurança. Considerações de segurança adicionais são discutidas no capítulo [de Prontidão Comercial.](../commercial-ready/commercial-ready.md)

### <a name="testing-aad-auth"></a>Teste AAD Auth

No Editor de Unidade, quando a AAD Auth estiver ativa, terá de autenticar sempre que lançar a aplicação. No dispositivo, o passo de autenticação acontecerá pela primeira vez e só será necessário novamente quando o token expirar ou for invalidado.

1. Adicione o componente **AADAuthentication** ao **RemoteRenderingCoordinator** GameObject.

    ![Componente auth AAD](./media/azure-active-directory-auth-component.png)

1. Preencha os seus valores para o ID do Cliente e para o ID do Inquilino. Estes valores podem ser encontrados na página geral do registo da sua aplicação:

    * **O Domínio da Conta** é o mesmo domínio que tem usado no Domínio de Conta do **RemoteRenderingCoordinator.**
    * **ID do Cliente de Aplicação de Diretório Ativo** é o *ID da Aplicação (cliente)* encontrado no registo da sua aplicação AAD (ver imagem abaixo).
    * **Azure Tenant ID** é o ID do *Diretório (inquilino)* encontrado no seu registo de aplicações AAD (ver imagem abaixo).
    * **O ID da conta de renderização remota Azure** é o mesmo **ID** de conta que tem usado para **RemoteRenderingCoordinator**.

    ![Screenshot que destaca o ID de Aplicação (cliente) e Diretório (inquilino) ID.](./media/app-overview-data.png)

1. Press Play no Editor de Unidade e consentimento para executar uma sessão.
    Uma vez que o componente **AADAuthentication** tem um controlador de visualização, o seu automaticamente ligado para apresentar um pedido após o painel modal de autorização de sessão.
1. Siga as instruções encontradas no painel à direita do **AppMenu**.
    Deve ver algo semelhante a isto: ![ Ilustração que mostra o painel de instruções que aparece à direita do AppMenu.](./media/device-flow-instructions.png)
    Depois de introduzir o fornecido codificado no seu dispositivo secundário (ou navegador no mesmo dispositivo) e iniciar sessão com as suas credenciais, um Access Token será devolvido à aplicação de solicitação, neste caso, o Editor de Unidade.
1. Depois deste ponto, tudo o que está no pedido deve proceder normalmente. Verifique se a Consola de Unidade se não está a progredir nas fases como esperado.

## <a name="build-to-device"></a>Construir para dispositivo

Se estiver a construir uma aplicação utilizando o MSAL para o dispositivo, terá de incluir um ficheiro na pasta **Ativos** do seu projeto. Isto ajudará o compilador a construir a aplicação corretamente utilizando o *Microsoft.Identity.Client.dll* incluído nos **Ativos Tutoriais**.

1. Adicione um novo ficheiro em **Ativos** ** nomeadoslink.xml**
1. Adicione o seguinte para o ficheiro:

    ```xml
    <linker>
        <assembly fullname="Microsoft.Identity.Client" preserve="all"/>
        <assembly fullname="System.Runtime.Serialization" preserve="all"/>
        <assembly fullname="System.Core">
            <type fullname="System.Linq.Expressions.Interpreter.LightLambda" preserve="all" />
        </assembly>
    </linker>
    ```

1. Guardar as alterações

Siga os passos encontrados em [Quickstart: Deploy Unitity sample to HoloLens - Build the sample Project](../../../quickstarts/deploy-to-hololens.md#build-the-sample-project), to build to HoloLens.

## <a name="next-steps"></a>Passos seguintes

O restante deste conjunto tutorial contém tópicos conceptuais para a criação de uma aplicação pronta à produção que utiliza renderização remota do Azure.

> [!div class="nextstepaction"]
> [Seguinte: Prontidão Comercial](../commercial-ready/commercial-ready.md)
