---
title: Configurar o Azure Key Vault com auditoria e rotação de ponto-a-ponto | Documentos da Microsoft
description: Utilize este guia de procedimentos para ajudar a configurar a rotação de chaves e monitorizar os registos do Cofre de chaves.
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
tags: ''
ms.assetid: 9cd7e15e-23b8-41c0-a10a-06e6207ed157
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 68fd33dc3e9def11f72b7aec14f83f86b8bb74d0
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749716"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Configurar o Azure Key Vault com auditoria e rotação de chaves

## <a name="introduction"></a>Introdução

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Depois de ter um cofre de chaves, pode começar a usá-lo para armazenar chaves e segredos. Seus aplicativos não precisam mais manter suas chaves ou segredos, mas pode solicitar no cofre conforme necessário. Um cofre de chaves permite-lhe atualizar as chaves e segredos sem afetar o comportamento do seu aplicativo, o qual abre uma variedade de possibilidades para a sua chave e a gestão de segredos.

>[!IMPORTANT]
> Os exemplos neste artigo são fornecidos apenas para fins ilustrativos. Elas não têm o objetivo para utilização em produção. 

Este artigo explica o tempo limite:

- Um exemplo de como utilizar o Azure Key Vault para armazenar um segredo. Neste artigo, o segredo armazenado é a chave de conta de armazenamento do Azure acessada por uma aplicação. 
- Como implementar uma rotação agendada dessa chave de conta de armazenamento.
- Como monitorizar a chave de cofre registos de auditoria e emitir alertas quando são feitos inesperados de pedidos.

> [!NOTE]
> Este artigo não explica detalhadamente a configuração inicial do seu Cofre de chaves. Para obter estas informações, consulte [o que é o Azure Key Vault?](key-vault-overview.md). Para obter instruções de interface de linha de comandos para várias plataformas, consulte [gerir com a CLI do Azure Key Vault](key-vault-manage-with-cli2.md).

## <a name="set-up-key-vault"></a>Configurar o Cofre de Chaves

Para ativar uma aplicação para obter um segredo do Key Vault, tem primeiro de criar o segredo e carregue-o para o cofre.

Abra uma sessão no Azure PowerShell e inicie sessão na sua conta do Azure com o seguinte comando:

```powershell
Connect-AzAccount
```

Na janela de pop-up do browser, introduza o nome de utilizador e palavra-passe para a sua conta do Azure. PowerShell irá obter todas as subscrições que estão associadas esta conta. PowerShell utiliza a primeira por predefinição.

Se tiver várias subscrições, poderá ter de especificar que foi utilizado para criar o seu Cofre de chaves. Introduza o seguinte para ver as subscrições da sua conta:

```powershell
Get-AzSubscription
```

Para especificar a subscrição que está associada ao Cofre de chaves que vai estar a registar, introduza:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Uma vez que este artigo demonstra o armazenamento de uma chave de conta de armazenamento como um segredo, tem de obter essa chave de conta de armazenamento.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Depois de recuperar o seu segredo (no caso, a chave de conta de armazenamento), tem de converter essa chave para uma cadeia segura e, em seguida, crie um segredo com esse valor no seu Cofre de chaves.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Em seguida, obtém o URI para o segredo que criou. Precisará este URI num passo posterior para chamar o Cofre de chaves e obter o seu segredo. Execute o seguinte comando do PowerShell e anote o valor de ID, o que é do URI o segredo:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Configurar a aplicação

Agora que tem um segredo armazenado, pode usar o código para recuperar e utilizá-lo Depois de executar mais algumas etapas.

Em primeiro lugar, deve registrar seu aplicativo com o Azure Active Directory. Em seguida, informar ao Key Vault informações sobre os aplicativos para que ela pode permitir pedidos a partir da sua aplicação.

> [!NOTE]
> A aplicação tem de ser criada no mesmo inquilino do Azure Active Directory como o seu Cofre de chaves.

1. Open **do Azure Active Directory**.
2. Selecione **Registos das aplicações**. 
3. Selecione **novo registo de aplicação** para adicionar uma aplicação ao Azure Active Directory.

    ![Abrir aplicações no Azure Active Directory](./media/keyvault-keyrotation/azure-ad-application.png)

4. Sob **Create**, deixe o tipo de aplicativo como **aplicação Web / API** e dê um nome ao seu aplicativo. Dê o seu aplicativo uma **URL de início de sessão**. Este URL pode ser qualquer coisa que pretende para esta demonstração.

    ![Criar registo de aplicação](./media/keyvault-keyrotation/create-app.png)

5. Depois do aplicativo é adicionado ao Azure Active Directory, é aberta a página de aplicativo. Selecione **configurações**e, em seguida, selecione **propriedades**. Copiar o **ID da aplicação** valor. Precisará das mesmas em passos posteriores.

Em seguida, gere uma chave para a sua aplicação, para que possa interagir com o Azure Active Directory. Para criar uma chave, selecione **chaves** sob **definições**. Tome nota da chave recentemente gerada para a sua aplicação do Azure Active Directory. Irá precisar deles noutro passo. A chave não estará disponível depois de sair desta secção. 

![Chaves de aplicação do Azure Active Directory](./media/keyvault-keyrotation/create-key.png)

Antes de estabelecer todas as chamadas do seu aplicativo para o Cofre de chaves, tem de pedir ao Cofre de chaves sobre seu aplicativo e as respetivas permissões. O comando seguinte utiliza o nome do cofre e o ID da aplicação da sua aplicação do Azure Active Directory para conceder o aplicativo **obter** acesso ao seu Cofre de chaves.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Agora, está pronto para começar a criar as suas chamadas de aplicação. Na sua aplicação, tem de instalar os pacotes de NuGet que são necessários para interagir com o Azure Key Vault e o Azure Active Directory. Na consola do Gestor de pacotes do Visual Studio, introduza os seguintes comandos. No momento da edição deste artigo, a versão atual do pacote do Azure Active Directory é 3.10.305231913, então, confirme a versão mais recente e atualize conforme necessário.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

No código da aplicação, crie uma classe para manter o método para a autenticação do Azure Active Directory. Neste exemplo, essa classe é chamada **Utils**. Adicione o seguinte `using` instrução:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Em seguida, adicione o método seguinte para recuperar o token JWT do Azure Active Directory. Para facilidade de manutenção, pode querer mover os valores de cadeia de caracteres codificada para a configuração da web ou aplicação.

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

Adicione o código necessário para chamar o Cofre de chaves e obter o seu valor secreto. Em primeiro lugar, tem de adicionar o seguinte `using` instrução:

```csharp
using Microsoft.Azure.KeyVault;
```

Adicione as chamadas de método para invocar o Cofre de chaves e obter o seu segredo. Nesse método, forneça o segredo do URI que guardou no passo anterior. Observe o uso do **GetToken** método a partir do **Utils** classe que criou anteriormente.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Quando executar o aplicativo, deverá estar agora autenticar no Azure Active Directory e, em seguida, obter seu valor secreto no Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>Rotação de chaves com a automatização do Azure

Está agora pronto para configurar uma estratégia de rotação para os valores que armazenar como segredos do Key Vault. Podem ser girados segredos de várias formas:

- Como parte de um processo manual
- Por meio de programação utilizando chamadas de API
- Por meio de um script de automatização do Azure

Para os fins deste artigo, irá utilizar o PowerShell combinado com a automatização do Azure para alterar a chave de acesso de uma conta de armazenamento do Azure. Em seguida, deverá atualizar um segredo do Cofre de chaves com essa nova chave.

Para permitir a automatização do Azure definir valores secretos no seu Cofre de chaves, tem de obter o ID de cliente para a ligação com o nome **AzureRunAsConnection**. Esta ligação foi criada quando que estabeleceu a sua instância de automatização do Azure. Para localizar este ID, selecione **ativos** da sua instância de automatização do Azure. Em seguida, selecione **conexões**e, em seguida, selecione a **AzureRunAsConnection** principal de serviço. Anote o **ApplicationId** valor.

![ID de cliente de automatização do Azure](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Na **ativos**, selecione **módulos**. Selecione **galeria**e, em seguida, procurar e importar versões atualizadas de cada um dos seguintes módulos:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> No momento da edição deste artigo, apenas os módulos mencionados anteriormente precisava ser atualizado com o seguinte script. Se falhar a tarefa de automatização, certifique-se que importar todos os módulos necessários e as respetivas dependências.

Depois de recuperar o ID da aplicação para a sua ligação de automatização do Azure, tem de pedir ao seu Cofre de chaves que este aplicativo tem permissão para atualizar os segredos no cofre. Utilize o seguinte comando do PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Em seguida, selecione **Runbooks** sob a sua instância de automatização do Azure e, em seguida, selecione **adicionar Runbook**. Selecione **Criação Rápida**. Dê um nome do runbook e selecione **PowerShell** como o tipo de runbook. Pode adicionar uma descrição. Por fim, selecione **criar**.

![Criar runbook](./media/keyvault-keyrotation/Create_Runbook.png)

Cole o seguinte script do PowerShell no painel do editor para o seu runbook novo:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzAccount `
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
New-AzStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

No painel do editor, selecione **painel de teste** para testar o seu script. Após o script é executado sem erros, pode selecionar **publicar**, e, em seguida, pode aplicar uma agenda para o runbook no painel de configuração de runbook.

## <a name="key-vault-auditing-pipeline"></a>Pipeline de auditoria do Key Vault

Quando configurar um cofre de chaves, pode ativar a auditoria para recolher registos de pedidos de acesso efetuados ao Cofre de chaves. Estes registos são armazenados numa conta de armazenamento do Azure designada e podem ser puxados, monitorizados e analisados. O cenário a seguir utiliza as funções do Azure, Azure logic apps e os registos de auditoria do Cofre de chaves para criar um pipeline que envia um e-mail quando uma aplicação que não corresponde ao ID da aplicação da aplicação web obtém segredos do cofre.

Em primeiro lugar, tem de ativar o registo no seu Cofre de chaves. Utilize os seguintes comandos do PowerShell. (Pode ver os detalhes completos na [este artigo sobre o registo do Cofre de chave](key-vault-logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Depois de registo está ativado, os registos de auditoria começar a ser armazenados na conta de armazenamento designada. Estes registos contém eventos sobre como e quando os seus cofres de chaves são acedidos e por quem.

> [!NOTE]
> Pode acessar suas informações de registo a 10 minutos após a operação de Cofre de chaves. Muitas vezes, estará disponível mais cedo do que isso.

A próxima etapa é [criar uma fila do Azure Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Esta fila é onde são enviados por push registos de auditoria do Cofre de chaves. Quando as mensagens de registo de auditoria estão na fila, a aplicação lógica escolhe-los e atua nos mesmos. Crie uma instância do Service Bus com os seguintes passos:

1. Crie um espaço de nomes do Service Bus (se já tiver um que pretende utilizar, avance para o passo 2).
2. Navegue para a instância do Service Bus no portal do Azure e selecione o espaço de nomes que pretende criar a fila.
3. Selecione **criar um recurso** > **integração empresarial** > **do Service Bus**e, em seguida, introduza os detalhes necessários.
4. Localizar as informações de ligação do Service Bus, selecionando o espaço de nomes e, em seguida, selecionando **informações da ligação**. Precisará estas informações para a secção seguinte.

Em seguida, [criar uma função do Azure](../azure-functions/functions-create-first-azure-function.md) para consultar os registos do Cofre de chaves na conta de armazenamento e recolher eventos de novo. Esta função será acionada com base numa agenda.

Para criar uma aplicação de função do Azure, selecione **criar um recurso**, procure no marketplace **Function App**e, em seguida, selecione **criar**. Durante a criação, pode utilizar um plano de alojamento existente ou crie um novo. Também pode optar pela de alojamento dinâmico. Para obter mais informações sobre as opções de alojamento das funções do Azure, consulte [como dimensionar as funções do Azure](../azure-functions/functions-scale.md).

Depois de criar a aplicação de funções do Azure, aceda ao mesmo e selecione o **temporizador** cenário e **C\#**  para o idioma. Em seguida, selecione **criar esta função**.

![Painel de início de funções do Azure](./media/keyvault-keyrotation/Azure_Functions_Start.png)

Sobre o **desenvolver** separador, substitua o código de csx pelo seguinte:

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
> Altere as variáveis no código anterior para apontar para a sua conta de armazenamento onde os registos do Cofre de chaves são escritos para a instância do Service Bus que criou anteriormente e o caminho específico para os registos de armazenamento do Cofre de chaves.

A função escolhe o ficheiro de registo mais recente da conta de armazenamento onde os registos do Cofre de chaves são escritos, capta os eventos mais recentes desse ficheiro e envia-as para uma fila do Service Bus. 

Como um único ficheiro pode ter vários eventos, deve criar um ficheiro de sync.txt que a função também analisa para determinar o carimbo de data / hora do último evento que foi capturado. Usando esse arquivo, garante que não a emitir o mesmo evento várias vezes. 

O ficheiro de sync.txt contém um carimbo de data / hora para o evento foi encontrado um último. Quando os registos são carregados, eles devem ser ordenados com base nos seus carimbos de data / hora para se certificar de que eles estão ordenados corretamente.

Para essa função, podemos fazer referência a duas bibliotecas adicionais que não estão disponíveis prontos a utilizar nas funções do Azure. Para incluir estas bibliotecas, precisamos de funções do Azure para selecioná-los com o NuGet. Sob o **código** caixa, selecione **ver ficheiros**.

![Opção "Ver ficheiros"](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Adicione um arquivo chamado Project com o seguinte conteúdo:

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

Depois de selecionar **guardar**, as funções do Azure irá transferir os binários necessários.

Mude para o **integrar** separador e dê o parâmetro de temporizador um nome significativo para utilizar dentro da função. No código anterior, a função espera que o temporizador seja chamado *myTimer*. Especifique um [expressão CRON](../app-service/webjobs-create.md#CreateScheduledCRON) para o temporizador da seguinte forma: `0 * * * * *`. Esta expressão fará com que a função ser executado uma vez um minuto.

No mesmo **integrar** separador, adicione uma entrada do tipo **armazenamento de Blobs do Azure**. Esta entrada apontará para o ficheiro de sync.txt que contém o carimbo de data / hora do último evento examinou pela função. Esta entrada será acedida dentro da função utilizando o nome do parâmetro. No código anterior, a entrada de armazenamento de Blobs do Azure espera que o nome do parâmetro seja *inputBlob*. Selecione a conta de armazenamento onde o ficheiro de sync.txt estarão localizado (pode ser o mesmo ou outra conta de armazenamento). No campo do caminho, forneça o caminho para o ficheiro no formato `{container-name}/path/to/sync.txt`.

Adicionar uma saída do tipo **armazenamento de Blobs do Azure**. Esta saída irá apontar para o arquivo de sync.txt que definiu na entrada. Esta saída é utilizada pela função para escrever o carimbo de hora do último evento examinou. Este parâmetro a ser chamado de espera que o código precedente *outputBlob*.

A função agora está pronta. Certifique-se mudar para o **desenvolver** separador e guarde o código. A janela de saída para os erros de compilação e corrija-os conforme necessário. Se o código é compilado, em seguida, o código deve agora ser a verificar os registos do Cofre de chaves a cada minuto e no envio de quaisquer novos eventos na fila do Service Bus definida. Deverá ver informações de registo escrever para a janela de registo sempre que a função é acionada.

### <a name="azure-logic-app"></a>Aplicação lógica do Azure

Em seguida, tem de criar uma aplicação de lógica do Azure que seleciona os eventos que a função é enviar por push para a fila do Service Bus, analisa o conteúdo e envia um e-mail com base numa condição que está a ser correspondida.

[Criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md) selecionando **criar um recurso** > **integração** > **aplicação lógica**.

Depois da aplicação lógica é criada, aceda ao mesmo e selecione **editar**. No editor de aplicação lógica, selecione **fila do Service Bus** e introduza as credenciais do Service Bus para ligá-lo para a fila.

![Barramento de serviço de aplicações lógicas do Azure](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Selecione **adicionar uma condição**. Na condição, mude para o editor avançado e introduza o código a seguir. Substitua *APP_ID* com o ID de aplicação real da sua aplicação web:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Esta expressão devolve essencialmente **false** se o *appid* do evento de entrada (que é o corpo da mensagem do Service Bus) não é o *appid* da aplicação.

Agora, crie uma ação sob **se não, não fazer NADA**.

![O Azure Logic Apps escolher a ação](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Para a ação, selecione **Office 365 - enviar e-mail**. Preencha os campos para criar uma mensagem de e-mail para enviar quando a condição definida devolve **false**. Se não tiver o Office 365, procure por alternativas atingir os mesmos resultados.

Agora tem um pipeline ponto a ponto que procura por novos registos de auditoria do Cofre de chaves, uma vez a uma minuto. Ele envia novos registos que ele encontra uma fila do Service Bus. A aplicação lógica é acionada quando uma nova mensagem, coloca na fila. Se o *appid* dentro do evento não corresponde ao ID da aplicação de aplicativo de chamada, envia uma mensagem de e-mail.
