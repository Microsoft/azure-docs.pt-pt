---
title: Configurar o Azure Key Vault com rotação de chave de ponta a ponta e auditoria Microsoft Docs
description: Utilize este guia de como fazer para ajudá-lo a configurar a rotação das chaves e monitorizar os registos do cofre das chaves.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 1e8a2bc6f9a8103440b68f2e8d2de9328ed00145
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2020
ms.locfileid: "85118615"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Configurar o Azure Key Vault com rotação e auditoria de chaves

## <a name="introduction"></a>Introdução

Depois de ter um cofre chave, pode começar a usá-lo para armazenar chaves e segredos. As suas aplicações já não precisam de persistir nas suas chaves ou segredos, mas podem solicitá-las do cofre, se necessário. Um cofre-chave permite-lhe atualizar chaves e segredos sem afetar o comportamento da sua aplicação, o que abre uma amplitude de possibilidades para a sua gestão chave e secreta.

Este artigo percorre como implementar uma rotação programada das chaves da conta de armazenamento, monitorizar os registos de auditoria do cofre chave e levantar alertas quando são feitos pedidos inesperados. 

Primeiro, deve criar um cofre-chave utilizando o método à sua escolha:

- [Definir e recuperar um segredo do Azure Key Vault usando Azure CLI](quick-create-cli.md)
- [Definir e recuperar um segredo do Azure Key Vault usando Azure PowerShell](quick-create-powershell.md)
- [Definir e recuperar um segredo do Azure Key Vault usando o portal Azure](quick-create-portal.md)


## <a name="store-a-secret"></a>Armazenar um segredo

Para permitir que uma aplicação recupere um segredo do Key Vault, primeiro deve criar o segredo e enviá-lo para o seu cofre.

Abra uma sessão no Azure PowerShell e inicie sessão na sua conta do Azure com o seguinte comando:

```powershell
Connect-AzAccount
```

Na janela do navegador pop-up, insira o nome de utilizador e a palavra-passe para a sua conta Azure. O PowerShell receberá todas as subscrições que estão associadas a esta conta. PowerShell utiliza o primeiro por defeito.

Se tiver várias subscrições, poderá ter de especificar a que foi usada para criar o seu cofre de chaves. Introduza o seguinte para ver as subscrições da sua conta:

```powershell
Get-AzSubscription
```

Para especificar a subscrição que está associada ao cofre de chaves, insira:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Como este artigo demonstra armazenar uma chave de conta de armazenamento como segredo, você deve obter a chave da conta de armazenamento.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Depois de recuperar o seu segredo (neste caso, a chave da sua conta de armazenamento), deve converter essa chave numa corda segura e, em seguida, criar um segredo com esse valor no seu cofre chave.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Em seguida, pegue o URI para o segredo que criou. Vai precisar deste URI mais tarde para ligar para o cofre e recuperar o seu segredo. Executar o seguinte comando PowerShell e tomar nota do valor de ID, que é o URI do segredo:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Configurar a aplicação

Agora que tem um segredo armazenado, pode usar o código para recuperá-lo e usá-lo depois de realizar mais alguns passos.

Em primeiro lugar, tem de registar a sua candidatura no Azure Ative Directory. Em seguida, informe o Key Vault das suas informações de aplicação para que possa permitir pedidos a partir da sua aplicação.

> [!NOTE]
> A sua candidatura deve ser criada no mesmo inquilino do Azure Ative Directory que o seu cofre chave.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se a sua conta lhe der acesso a mais de um inquilino, selecione a sua conta no canto superior direito. Desconfiem da sua sessão de portal para o inquilino da AD Azure que pretende.
1. Procure e selecione **Azure Active Directory**. Em **Gerir**, selecione **Registos de aplicações**.
1. Selecione **Novo registo**.
1. No **Registo de uma aplicação**, introduza um nome de aplicação significativo para mostrar aos utilizadores.
1. Especificar quem pode usar a aplicação da seguinte forma:

    | Tipos de conta suportados | Description |
    |-------------------------|-------------|
    | **Contas apenas neste diretório organizacional** | Selecione esta opção se estiver a criar uma aplicação de linha de negócio (LOB). Esta opção não está disponível se não estiver a registar o pedido num diretório.<br><br>Esta opção mapeia para o inquilino único do Azure AD.<br><br>Esta opção é o padrão, a menos que esteja a registar a aplicação fora de um diretório. Nos casos em que a aplicação é registada fora de um diretório, a predefinição é contas da Microsoft pessoais e de multi-inquilino do Azure AD. |
    | **Contas em qualquer diretório organizacional** | Selecione esta opção se quiser visar todos os clientes comerciais ou pedagógicos.<br><br>Esta opção mapeia para um multi-inquilino único do Azure AD.<br><br>Se você registrou a app como Azure AD apenas um único inquilino, você pode atualizá-lo para ser Azure AD multi-inquilino e de volta para inquilino único através da página **de Autenticação.** |
    | **Contas em qualquer diretório organizacional e contas Microsoft pessoais** | Selecione esta opção para visar o maior conjunto de clientes.<br><br>Esta opção mapeia para contas da Microsoft pessoais e de multi-inquilino do Azure AD.<br><br>Se registou a aplicação como multi-inquilino e contas pessoais da Microsoft, não pode alterar esta definição na UI. Em vez disso, tem de utilizar o editor de manifesto de aplicação para alterar os tipos de conta suportados. |

1. Em **Redirecionamento URI (opcional)**, selecione o tipo de aplicação que está a construir: **Web** ou Cliente **Público (mobile & desktop)**. Em seguida, insira o URI de redirecionamento, ou URL de resposta, para a sua aplicação.

    * Para aplicações Web, indique o URL base da sua aplicação. Por exemplo, `https://localhost:31544` pode ser o URL de uma aplicação Web em execução no seu computador local. Os utilizadores utilizariam este URL para iniciar sessão numa aplicação de cliente Web.
    * Para aplicações cliente públicas, indique o URI utilizado pelo Azure AD para devolver respostas de token. Introduza um valor específico para a aplicação, por exemplo `myapp://auth`.

1. Quando terminar, selecione **Registar**.

    ![Mostra o ecrã para registar uma nova aplicação no portal Azure](../media/new-app-registration.png)

A Azure AD atribui uma aplicação única, ou cliente, ID à sua aplicação. O portal abre a página **geral** da sua aplicação. Note o valor de **ID de aplicação (cliente).**

Para adicionar capacidades à sua aplicação, pode selecionar outras opções de configuração, incluindo branding, certificados e segredos, permissões API e muito mais.

![Exemplo de uma página geral de aplicações recentemente registada](../media//new-app-overview-page-expanded.png)

Antes de estabelecer qualquer chamada da sua aplicação para o cofre de chaves, deve dizer ao cofre da chave sobre a sua aplicação e as suas permissões. O seguinte comando utiliza o nome do cofre e o **ID da Aplicação (cliente)** da sua aplicação Azure Ative Directory para conceder à aplicação **Obter** acesso ao seu cofre de chaves.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Está pronto para começar a construir as suas chamadas. Na sua aplicação, tem de instalar os pacotes NuGet que são necessários para interagir com o Azure Key Vault e o Azure Ative Directory. A partir da consola Visual Studio Package Manager, insira os seguintes comandos. Na redação deste artigo, a versão atual do pacote Azure Ative Directory é 3.10.305231913, pelo que confirme a versão mais recente e atualização conforme necessário.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

No seu código de aplicação, crie uma classe para manter o método para a autenticação do Azure Ative Directory. Neste exemplo, esta classe **chama-se Utils.** Adicione a seguinte `using` declaração:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Em seguida, adicione o seguinte método para recuperar o token JWT do Azure Ative Directory. Para manter a capacidade, é melhor mover os valores de cadeia codificados para a sua web ou configuração de aplicação.

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

Adicione o código necessário para ligar para o Key Vault e recuperar o seu valor secreto. Em primeiro lugar, deve acrescentar a seguinte `using` declaração:

```csharp
using Microsoft.Azure.KeyVault;
```

Adicione as chamadas de método para invocar o Cofre de Chaves e recuperar o seu segredo. Neste método, fornece o URI secreto que salvou num passo anterior. Note a utilização do método **GetToken** da classe **Utils** que criou anteriormente.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Ao executar a sua aplicação, deverá agora autenticar o Azure Ative Directory e, em seguida, recuperar o seu valor secreto a partir do Cofre da Chave Azure.

## <a name="key-rotation-using-azure-automation"></a>Rotação de chave usando Azure Automation

Está agora pronto para definir uma estratégia de rotação para os valores que armazena como segredos key vault. Os segredos podem ser rodados de várias maneiras:

- Como parte de um processo manual
- Programáticamente usando chamadas API
- Através de um script Azure Automation

Para efeitos deste artigo, utilizará o PowerShell combinado com a Azure Automation para alterar a chave de acesso de uma conta de armazenamento Azure. Em seguida, atualizará um segredo chave do cofre com a nova chave.

Para permitir que a Azure Automation desaperte valores secretos no seu cofre de chaves, tem de obter o ID do cliente para a ligação **denominada AzureRunAsConnection**. Esta ligação foi criada quando estabeleceu a sua instância Azure Automation. Para encontrar este ID, selecione **Ativos** da sua instância Azure Automation. A partir daí, selecione **Connections**e, em seguida, selecione o principal do serviço **AzureRunAsConnection.** Tome nota do valor **ApplicationId.**

![ID do cliente da Azure Automation](../media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Em **Ativos**, selecione **Módulos**. Selecione **Gallery**, e, em seguida, procure e importe versões atualizadas de cada um dos seguintes módulos:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Na redação deste artigo, apenas os módulos previamente anotados precisavam de ser atualizados para o seguinte script. Se o seu trabalho de automação falhar, confirme que importou todos os módulos necessários e as suas dependências.

Depois de ter recuperado o ID da aplicação para a sua ligação Azure Automation, tem de dizer ao seu cofre-chave que esta aplicação tem permissão para atualizar segredos no seu cofre. Utilize o seguinte comando PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Em seguida, selecione **Runbooks** sob a sua instância Azure Automation e, em seguida, **selecione Add Runbook**. Selecione **Criação Rápida**. Nomeie o seu runbook e selecione **PowerShell** como o tipo de livro de execução. Pode adicionar uma descrição. Por fim, **selecione Criar**.

![Criar runbook](../media/keyvault-keyrotation/Create_Runbook.png)

Cole o seguinte script PowerShell no painel de editores para o seu novo runbook:

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

No painel de editores, selecione **Test pane** para testar o seu script. Depois de o script ser executado sem erros, pode selecionar **Publicar**e, em seguida, pode aplicar um crono de programação no painel de configuração do runbook.

## <a name="key-vault-auditing-pipeline"></a>Pipeline de auditoria do Cofre-Chave

Quando configurar um cofre chave, pode ligar a auditoria para recolher registos de pedidos de acesso feitos ao cofre de chaves. Estes registos são armazenados numa conta de armazenamento Azure designada e podem ser retirados, monitorizados e analisados. O cenário a seguir utiliza funções Azure, aplicações lógicas Azure e registos de auditoria de cofre-chave para criar um pipeline que envia um e-mail quando uma aplicação que não corresponde ao ID da aplicação web recupera segredos do cofre.

Primeiro, tem de ativar o registo no cofre da chave. Utilize os seguintes comandos PowerShell. (Pode ver todos os detalhes [deste artigo sobre a extração de cofres.)](../general/logging.md)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Após a ativação do registo, os registos de auditoria começam a ser armazenados na conta de armazenamento designada. Estes registos contêm eventos sobre como e quando os cofres são acedidos, e por quem.

> [!NOTE]
> Pode aceder à sua informação de registo 10 minutos após a operação do cofre da chave. Muitas vezes estará disponível mais cedo do que isso.

O próximo passo é [criar uma fila de autocarros da Azure Service.](../../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) Esta fila é onde os registos de auditoria do cofre são empurrados. Quando as mensagens de registo de auditoria estão na fila, a aplicação lógica apanha-as e atua sobre elas. Criar uma instância de ônibus de serviço com os seguintes passos:

1. Crie um espaço de nomes de Service Bus (se já tiver um que pretende utilizar, salte para o passo 2).
2. Navegue pela instância de ônibus de serviço no portal Azure e selecione o espaço de nomes em que pretende criar a fila.
3. Selecione **Criar um**  >  ônibus de serviço**de integração empresarial**de recurso  >  **Service Bus**e, em seguida, insira os detalhes necessários.
4. Encontre as informações de ligação do Service Bus selecionando o espaço de nomes e, em seguida, selecionando **Informações de Ligação**. Vai precisar desta informação para a próxima secção.

Em seguida, [crie uma função Azure](../../azure-functions/functions-create-first-azure-function.md) para sondar os registos do cofre chave dentro da conta de armazenamento e recolher novos eventos. Esta função será ativada num horário.

Para criar uma aplicação de função Azure, selecione **Criar um recurso,** procure no mercado para **a App de funções**e, em seguida, selecione **Criar**. Durante a criação, você pode usar um plano de hospedagem existente ou criar um novo. Também pode optar por hospedagem dinâmica. Para obter mais informações sobre as opções de hospedagem para Funções Azure, consulte [Como escalar as Funções Azure](../../azure-functions/functions-scale.md).

Depois da aplicação de função Azure ser criada, vá até ela e selecione o cenário **Timer** e **C \# ** para o idioma. Em seguida, **selecione Criar esta função**.

![Funções Azure Start Blade](../media/keyvault-keyrotation/Azure_Functions_Start.png)

No **separador Desenvolvimento,** substitua o código run.csx pelo seguinte:

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
            log.Verbose($"Sync point file didn't have a date. Setting to now.");
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
> Altere as variáveis do código anterior para indicar para a sua conta de armazenamento onde estão escritos os registos do cofre de chaves, para a instância de Service Bus que criou anteriormente e para o caminho específico para os registos de armazenamento do cofre de chaves.

A função recolhe o ficheiro de registo mais recente da conta de armazenamento onde os registos do cofre são escritos, agarra os eventos mais recentes desse ficheiro e empurra-os para uma fila de Autocarros de Serviço. 

Como um único ficheiro pode ter vários eventos, deve criar um ficheiro sync.txt que a função também analisa para determinar o carimbo de tempo do último evento que foi recolhido. A utilização deste ficheiro garante que não empurre o mesmo evento várias vezes. 

O ficheiro sync.txt contém uma marca de tempo para o último evento encontrado. Quando os registos estiverem carregados, devem ser classificados com base nos carimbos de tempo para garantir que são encomendados corretamente.

Para esta função, referimos algumas bibliotecas adicionais que não estão disponíveis fora da caixa em Funções Azure. Para incluir estas bibliotecas, precisamos de Funções Azure para as puxar usando o NuGet. Na caixa **código,** selecione **Ver Ficheiros**.

![Opção "Ver ficheiros"](../media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Adicione um ficheiro chamado project.jscom o seguinte conteúdo:

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

Depois de selecionar **Save**, as Funções Azure descarregarão os binários necessários.

Mude para o separador **Integrar** e dê ao parâmetro do temporizador um nome significativo para utilizar dentro da função. No código anterior, a função espera que o temporizador seja chamado *de myTimer*. Especificar uma [expressão CRON](../../app-service/webjobs-create.md#CreateScheduledCRON) para o temporizador da seguinte forma: `0 * * * * *` . Esta expressão fará com que a função funcione uma vez por minuto.

No mesmo **separador Integrar,** adicione uma entrada do tipo **Azure Blob de armazenamento**. Esta entrada indicará o ficheiro sync.txt que contém o carimbo de tempo do último evento analisado pela função. Esta entrada será acedida dentro da função utilizando o nome do parâmetro. No código anterior, a entrada de armazenamento Azure Blob espera que o nome do parâmetro seja *inputBlob*. Selecione a conta de armazenamento onde o ficheiro sync.txt será localizado (pode ser o mesmo ou uma conta de armazenamento diferente). No campo do caminho, forneça o caminho para o ficheiro no formato `{container-name}/path/to/sync.txt` .

Adicione uma saída do tipo **Azure Blob.** Esta saída apontará para o ficheiro sync.txt definido na entrada. Esta saída é utilizada pela função para escrever o carimbo de tempo do último evento analisado. O código anterior espera que este parâmetro seja chamado *outputBlob*.

A função está agora pronta. Certifique-se de voltar a mudar para o **separador Desenvolver** e guardar o código. Verifique a janela de saída para obter quaisquer erros de compilação e corrija-os conforme necessário. Se o código compilar, o código deve agora verificar os registos do cofre de chaves a cada minuto e empurrar quaisquer novos eventos para a fila definida do Service Bus. Deve ver a informação de registo escrever na janela de registo sempre que a função for ativada.

### <a name="azure-logic-app"></a>App de lógica Azure

Em seguida, você deve criar uma aplicação lógica Azure que recolhe os eventos que a função está empurrando para a fila do Service Bus, analisa o conteúdo, e envia um e-mail com base numa condição que está sendo correspondida.

[Crie uma aplicação lógica](../../logic-apps/quickstart-create-first-logic-app-workflow.md) selecionando Criar uma App lógica de **Create a resource**  >  **integração**  >  **de**recursos.

Depois de criar a aplicação lógica, vá até ela e **selecione Editar.** No editor de aplicativos de lógica, selecione **Service Bus Queue** e insira as suas credenciais de Service Bus para conectá-la à fila.

![Ônibus de serviço de aplicativo azure logic](../media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

**Selecione Adicione uma condição**. Na condição, mude para o editor avançado e introduza o seguinte código. Substitua *APP_ID* pelo ID da aplicação real da sua aplicação web:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Esta expressão essencialmente retorna **falsa** se o *appid* do evento de entrada (que é o corpo da mensagem Service Bus) não é o *app da* app.

Agora, crie uma ação sob **o IF NO, NÃO FAÇA NADA.**

![Azure Logic Apps escolhem ação](../media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Para a ação, selecione **Office 365 - envie um e-mail**. Preencha os campos para criar um e-mail para enviar quando a condição definida voltar **falsa.** Se não tiver o Office 365, procure alternativas para obter os mesmos resultados.

Agora tem um oleoduto de ponta a ponta que procura novos registos de auditoria do cofre de chaves uma vez por minuto. Empurra novos troncos que encontra para uma fila de autocarros de serviço. A aplicação lógica é desencadeada quando uma nova mensagem aterra na fila. Se o *appid* dentro do evento não corresponder ao ID da aplicação de chamadas, envia um e-mail.
