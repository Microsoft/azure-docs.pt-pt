---
title: Configurar Azure Key Vault com a rotação de chaves e a auditoria de ponta a ponta | Microsoft Docs
description: Use este guia de instruções para ajudá-lo a configurar a rotação de chaves e monitorar os logs do cofre de chaves.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 1f60ce3a23882a48e6008b76c0eedcab99e013b2
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883462"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Configurar Azure Key Vault com a rotação de chaves e a auditoria

## <a name="introduction"></a>Introdução

Depois de ter um cofre de chaves, você pode começar a usá-lo para armazenar chaves e segredos. Seus aplicativos não precisam mais manter suas chaves ou segredos, mas podem solicitá-los do cofre, conforme necessário. Um cofre de chaves permite que você atualize chaves e segredos sem afetar o comportamento do seu aplicativo, o que abre uma amplitude de possibilidades para o gerenciamento de chave e segredo.

>[!IMPORTANT]
> Os exemplos neste artigo são fornecidos apenas para fins ilustrativos. Eles não são destinados ao uso em produção. 

Este artigo percorre:

- Um exemplo de como usar Azure Key Vault para armazenar um segredo. Neste artigo, o segredo armazenado é a chave de conta de armazenamento do Azure acessada por um aplicativo. 
- Como implementar uma rotação agendada dessa chave de conta de armazenamento.
- Como monitorar os logs de auditoria do cofre de chaves e gerar alertas quando solicitações inesperadas são feitas.

> [!NOTE]
> Este artigo não explica em detalhes a configuração inicial do cofre de chaves. Para obter essas informações, consulte [o que é Azure Key Vault?](key-vault-overview.md). Para obter instruções de interface de linha de comando de plataforma cruzada, consulte [gerenciar Key Vault usando o CLI do Azure](key-vault-manage-with-cli2.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-key-vault"></a>Configurar o Cofre de Chaves

Para permitir que um aplicativo recupere um segredo de Key Vault, você deve primeiro criar o segredo e carregá-lo no cofre.

Abra uma sessão no Azure PowerShell e inicie sessão na sua conta do Azure com o seguinte comando:

```powershell
Connect-AzAccount
```

Na janela pop-up do navegador, insira o nome de usuário e a senha para sua conta do Azure. O PowerShell obterá todas as assinaturas associadas a essa conta. O PowerShell usa o primeiro por padrão.

Se você tiver várias assinaturas, talvez seja necessário especificar aquela que foi usada para criar o cofre de chaves. Insira o seguinte para ver as assinaturas para sua conta:

```powershell
Get-AzSubscription
```

Para especificar a assinatura associada ao cofre de chaves que será registrada, digite:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Como este artigo demonstra o armazenamento de uma chave de conta de armazenamento como um segredo, você deve obter essa chave de conta de armazenamento.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Depois de recuperar seu segredo (nesse caso, a chave da conta de armazenamento), você deve converter essa chave em uma cadeia de caracteres segura e, em seguida, criar um segredo com esse valor em seu cofre de chaves.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Em seguida, obtenha o URI para o segredo que você criou. Você precisará desse URI em uma etapa posterior para chamar o cofre de chaves e recuperar seu segredo. Execute o seguinte comando do PowerShell e anote o valor da ID, que é o URI do segredo:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Configurar o aplicativo

Agora que você tem um segredo armazenado, você pode usar o código para recuperá-lo e usá-lo depois de executar mais algumas etapas.

Primeiro, você deve registrar seu aplicativo com Azure Active Directory. Em seguida, informe Key Vault as informações do aplicativo para que ele possa permitir solicitações do seu aplicativo.

> [!NOTE]
> Seu aplicativo deve ser criado no mesmo locatário de Azure Active Directory que o cofre de chaves.

1. Abra **Azure Active Directory**.
2. Selecione **Registos das aplicações**. 
3. Selecione **novo registro de aplicativo** para adicionar um aplicativo a Azure Active Directory.

    ![Abrir aplicativos no Azure Active Directory](./media/keyvault-keyrotation/azure-ad-application.png)

4. Em **criar**, deixe o tipo de aplicativo como **aplicativo Web/API** e dê um nome ao seu aplicativo. Dê ao seu aplicativo uma **URL de logon**. Essa URL pode ser qualquer coisa que você desejar para esta demonstração.

    ![Criar registro de aplicativo](./media/keyvault-keyrotation/create-app.png)

5. Depois que o aplicativo for adicionado ao Azure Active Directory, a página do aplicativo será aberta. Selecione **configurações**e selecione **Propriedades**. Copie o valor da **ID do aplicativo** . Você precisará dela em etapas posteriores.

Em seguida, gere uma chave para seu aplicativo para que ele possa interagir com Azure Active Directory. Para criar uma chave, selecione **chaves** em **configurações**. Anote a chave recém-gerado para seu aplicativo Azure Active Directory. Irá precisar deles noutro passo. A chave não estará disponível depois que você sair desta seção. 

![Azure Active Directory chaves de aplicativo](./media/keyvault-keyrotation/create-key.png)

Antes de estabelecer qualquer chamada do seu aplicativo no cofre de chaves, você deve informar ao cofre de chaves sobre seu aplicativo e suas permissões. O comando a seguir usa o nome do cofre e a ID do aplicativo do seu aplicativo Azure Active Directory para conceder ao aplicativo **obter** acesso ao seu cofre de chaves.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Agora você está pronto para começar a criar suas chamadas de aplicativo. Em seu aplicativo, você deve instalar os pacotes NuGet necessários para interagir com Azure Key Vault e Azure Active Directory. No console do Gerenciador de pacotes do Visual Studio, insira os comandos a seguir. Na elaboração deste artigo, a versão atual do pacote de Azure Active Directory é 3.10.305231913, portanto, confirme a versão mais recente e atualize conforme necessário.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

No código do aplicativo, crie uma classe para manter o método de autenticação Azure Active Directory. Neste exemplo, essa classe é chamada de **utils**. Adicione a seguinte `using` instrução:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Em seguida, adicione o seguinte método para recuperar o token JWT de Azure Active Directory. Para fins de manutenção, talvez você queira mover os valores de cadeia de caracteres embutidos em código para sua configuração de aplicativo ou Web.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Adicione o código necessário para chamar Key Vault e recuperar o valor secreto. Primeiro, você deve adicionar a seguinte `using` instrução:

```csharp
using Microsoft.Azure.KeyVault;
```

Adicione as chamadas de método para invocar Key Vault e recuperar seu segredo. Nesse método, você fornece o URI secreto que você salvou em uma etapa anterior. Observe o uso do método **GetToken** da classe **utils** criada anteriormente.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Ao executar o aplicativo, agora você deve estar Autenticando para Azure Active Directory e, em seguida, recuperando o valor secreto de Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>Rotação de chaves usando a automação do Azure

> [!IMPORTANT]
> Os runbooks de automação do Azure ainda exigem o `AzureRM` uso do módulo.

Agora você está pronto para configurar uma estratégia de rotação para os valores armazenados como segredos Key Vault. Os segredos podem ser girados de várias maneiras:

- Como parte de um processo manual
- Programaticamente usando chamadas à API
- Por meio de um script de automação do Azure

Para os fins deste artigo, você usará o PowerShell combinado com a automação do Azure para alterar a chave de acesso de uma conta de armazenamento do Azure. Em seguida, você atualizará um segredo do cofre de chaves com essa nova chave.

Para permitir que a automação do Azure defina valores secretos em seu cofre de chaves, você deve obter a ID do cliente para a conexão chamada **AzureRunAsConnection**. Essa conexão foi criada quando você estabeleceu a instância de automação do Azure. Para localizar essa ID, selecione **ativos** da sua instância de automação do Azure. A partir daí, selecione **conexões**e, em seguida, selecione a entidade de serviço **AzureRunAsConnection** . Anote o valor **ApplicationId** .

![ID do cliente de automação do Azure](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Em **ativos**, selecione **módulos**. Selecione **Galeria**e, em seguida, pesquise e importe versões atualizadas de cada um dos seguintes módulos:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Ao escrever este artigo, somente os módulos anotados anteriormente precisavam ser atualizados para o script a seguir. Se seu trabalho de automação falhar, confirme que você importou todos os módulos necessários e suas dependências.

Depois de recuperar a ID do aplicativo para sua conexão de automação do Azure, você deve informar ao cofre da chave que esse aplicativo tem permissão para atualizar segredos em seu cofre. Use o seguinte comando do PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Em seguida, selecione **Runbooks** em sua instância de automação do Azure e, em seguida, selecione **Adicionar runbook**. Selecione **Criação Rápida**. Nomeie seu runbook e selecione **PowerShell** como o tipo de runbook. Você pode adicionar uma descrição. Por fim, selecione **criar**.

![Criar runbook](./media/keyvault-keyrotation/Create_Runbook.png)

Cole o seguinte script do PowerShell no painel do editor para seu novo runbook:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Optionally you can set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

No painel do editor, selecione **painel de teste** para testar o script. Depois que o script for executado sem erros, você poderá selecionar **publicar**e, em seguida, poderá aplicar uma agenda para o runbook no painel de configuração do runbook.

## <a name="key-vault-auditing-pipeline"></a>Key Vault pipeline de auditoria

Ao configurar um cofre de chaves, você pode ativar a auditoria para coletar logs de solicitações de acesso feitas ao cofre de chaves. Esses logs são armazenados em uma conta de armazenamento do Azure designada e podem ser extraídos, monitorados e analisados. O cenário a seguir usa o Azure functions, aplicativos lógicos do Azure e logs de auditoria de cofre de chaves para criar um pipeline que envia um email quando um aplicativo que não corresponde à ID do aplicativo do aplicativo Web recupera segredos do cofre.

Primeiro, você deve habilitar o registro em log no cofre de chaves. Use os comandos do PowerShell a seguir. (Você pode ver os detalhes completos neste [artigo sobre o registro em log de chave-cofre](key-vault-logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Após a habilitação do registro em log, os logs de auditoria começam a ser armazenados na conta de armazenamento designada. Esses logs contêm eventos sobre como e quando seus cofres de chaves são acessados e por quem.

> [!NOTE]
> Você pode acessar as informações de registro em log 10 minutos após a operação do cofre de chaves. Ele geralmente estará disponível antes disso.

A próxima etapa é [criar uma fila do barramento de serviço do Azure](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Essa fila é onde os logs de auditoria do cofre de chaves são enviados por push. Quando as mensagens de log de auditoria estão na fila, o aplicativo lógico as pega e age nelas. Crie uma instância do barramento de serviço com as seguintes etapas:

1. Crie um namespace do barramento de serviço (se você já tiver um que deseja usar, pule para a etapa 2).
2. Navegue até a instância do barramento de serviço no portal do Azure e selecione o namespace no qual você deseja criar a fila.
3. Selecione **criar um recurso** > **Enterprise Integration** > **barramento de serviço**e, em seguida, insira os detalhes necessários.
4. Localize as informações de conexão do barramento de serviço selecionando o namespace e, em seguida, selecionando **informações de conexão**. Você precisará dessas informações para a próxima seção.

Em seguida, [crie uma função do Azure](../azure-functions/functions-create-first-azure-function.md) para sondar os logs do cofre de chaves na conta de armazenamento e selecionar novos eventos. Esta função será disparada em um agendamento.

Para criar um aplicativo de funções do Azure, selecione **criar um recurso**, pesquise no marketplace por **aplicativo de funções**e, em seguida, selecione **criar**. Durante a criação, você pode usar um plano de hospedagem existente ou criar um novo. Você também pode optar pela hospedagem dinâmica. Para obter mais informações sobre as opções de hospedagem para Azure Functions, consulte [como dimensionar Azure Functions](../azure-functions/functions-scale.md).

Depois que o aplicativo de funções do Azure for criado, acesse-o e selecione o cenário do **temporizador** e **C\#**  para o idioma. Em seguida, selecione **criar esta função**.

![Folha iniciar Azure Functions](./media/keyvault-keyrotation/Azure_Functions_Start.png)

Na guia **desenvolver** , substitua o código Run. CSX pelo seguinte:

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //Required to order by time as they might not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

> [!NOTE]
> Altere as variáveis no código anterior para apontar para sua conta de armazenamento na qual os logs do cofre de chaves são gravados, na instância do barramento de serviço que você criou anteriormente e no caminho específico para os logs de armazenamento do cofre de chaves.

A função pega o arquivo de log mais recente da conta de armazenamento em que os logs do cofre de chaves são gravados, obtém os eventos mais recentes desse arquivo e os envia para uma fila do barramento de serviço. 

Como um único arquivo pode ter vários eventos, você deve criar um arquivo Sync. txt que a função também examina para determinar o carimbo de data/hora do último evento que foi selecionado. O uso desse arquivo garante que você não envie o mesmo evento por push várias vezes. 

O arquivo Sync. txt contém um carimbo de data/hora para o último evento encontrado. Quando os logs são carregados, eles devem ser classificados com base em seus carimbos de data/hora para garantir que eles sejam ordenados corretamente.

Para essa função, fazemos referência a algumas bibliotecas adicionais que não estão disponíveis prontamente no Azure Functions. Para incluir essas bibliotecas, precisamos Azure Functions para recebê-las usando o NuGet. Na caixa **código** , selecione **Exibir arquivos**.

![Opção "exibir arquivos"](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Adicione um arquivo chamado Project. JSON com o seguinte conteúdo:

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```

Depois de selecionar **salvar**, Azure Functions baixará os binários necessários.

Alterne para a guia **integrar** e dê ao parâmetro do temporizador um nome significativo para usar na função. No código anterior, a função espera que o temporizador seja chamado *MyTimer*. Especifique uma [expressão cron](../app-service/webjobs-create.md#CreateScheduledCRON) para o temporizador da seguinte `0 * * * * *`maneira:. Essa expressão fará com que a função seja executada uma vez por minuto.

Na mesma guia **integrar** , adicione uma entrada do tipo armazenamento de **BLOBs do Azure**. Essa entrada apontará para o arquivo Sync. txt que contém o carimbo de data/hora do último evento examinado pela função. Essa entrada será acessada dentro da função usando o nome do parâmetro. No código anterior, a entrada do armazenamento de BLOBs do Azure espera que o nome do parâmetro seja *inputBlob*. Selecione a conta de armazenamento na qual o arquivo Sync. txt será localizado (pode ser a mesma conta de armazenamento ou outra). No campo caminho, forneça o caminho para o arquivo no formato `{container-name}/path/to/sync.txt`.

Adicione uma saída do tipo **armazenamento de BLOBs do Azure**. Essa saída apontará para o arquivo Sync. txt que você definiu na entrada. Essa saída é usada pela função para gravar o carimbo de data/hora do último evento examinado. O código anterior espera que esse parâmetro seja chamado de *outputBlob*.

Agora, a função está pronta. Certifique-se de voltar para a guia **desenvolver** e salve o código. Verifique a janela de saída em busca de quaisquer erros de compilação e corrija-os conforme necessário. Se o código for compilado, o código agora deverá verificar os logs do cofre de chaves a cada minuto e enviar por push todos os novos eventos para a fila do barramento de serviço definido. Você deve ver informações de log gravadas na janela de log sempre que a função for disparada.

### <a name="azure-logic-app"></a>Aplicativo lógico do Azure

Em seguida, você deve criar um aplicativo lógico do Azure que pega os eventos que a função está enviando para a fila do barramento de serviço, analisa o conteúdo e envia um email com base em uma condição sendo correspondida.

[Crie um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md) selecionando **criar um recurso** > **aplicativo lógico**de**integração** > .

Depois que o aplicativo lógico for criado, acesse-o e selecione **Editar**. No editor do aplicativo lógico, selecione **fila do barramento de serviço** e insira suas credenciais do barramento de serviço para conectá-lo à fila.

![Barramento de serviço do aplicativo lógico do Azure](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Selecione **Adicionar uma condição**. Na condição, alterne para o editor avançado e insira o código a seguir. Substitua *APP_ID* pela ID de aplicativo real do seu aplicativo Web:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Essa expressão, essencialmente, retorna **false** se a *AppID* do evento de entrada (que é o corpo da mensagem do barramento de serviço) não é a *AppID* do aplicativo.

Agora, crie uma ação em **se não, não faça nada**.

![Escolher ação do aplicativo lógico do Azure](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Para a ação, selecione **Office 365-enviar email**. Preencha os campos para criar um email a ser enviado quando a condição definida retornar **false**. Se você não tiver o Office 365, procure alternativas para obter os mesmos resultados.

Agora você tem um pipeline de ponta a ponta que procura novos logs de auditoria de cofre de chaves uma vez por minuto. Ele envia por push novos logs que encontra para uma fila do barramento de serviço. O aplicativo lógico é disparado quando uma nova mensagem chega na fila. Se o *AppID* dentro do evento não corresponder à ID do aplicativo de chamada, ele enviará um email.
