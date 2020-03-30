---
title: Configurar o Cofre chave Azure com rotação e auditoria de chaves de ponta a ponta Microsoft Docs
description: Utilize este guia de como fazer para ajudá-lo a configurar a rotação da chave e monitorizar os registos do cofre da chave.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 6962a264787bd8a55b6f6a2ebdb6eeb615c33d5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218404"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Configurar o Cofre chave Azure com rotação de chaves e auditoria

## <a name="introduction"></a>Introdução

Depois de ter um cofre chave, pode começar a usá-lo para armazenar chaves e segredos. As suas aplicações já não precisam de persistir nas chaves ou segredos, mas podem solicitá-las do cofre, conforme necessário. Um cofre chave permite-lhe atualizar chaves e segredos sem afetar o comportamento da sua aplicação, o que abre uma amplitude de possibilidades para a sua gestão chave e secreta.

Este artigo passa por como implementar uma rotação programada das chaves da conta de armazenamento, monitorizar os registos de auditoria do cofre chave e levantar alertas quando são feitos pedidos inesperados. 

Primeiro, deve criar um cofre chave utilizando o método da sua escolha:

- [Definir e recuperar um segredo do Cofre de Chaves Azure usando o Azure CLI](quick-create-cli.md)
- [Definir e recuperar um segredo do Cofre de Chaves Azure usando o Azure PowerShell](quick-create-powershell.md)
- [Definir e recuperar um segredo do Cofre chave Azure usando o portal Azure](quick-create-portal.md)


## <a name="store-a-secret"></a>Armazenar um segredo

Para permitir uma aplicação para recuperar um segredo do Cofre chave, primeiro deve criar o segredo e carregá-lo para o seu cofre.

Abra uma sessão no Azure PowerShell e inicie sessão na sua conta do Azure com o seguinte comando:

```powershell
Connect-AzAccount
```

Na janela do navegador pop-up, introduza o nome de utilizador e a palavra-passe para a sua conta Azure. A PowerShell receberá todas as subscrições associadas a esta conta. PowerShell usa o primeiro por padrão.

Se tiver várias subscrições, poderá ter de especificar a que foi usada para criar o seu cofre chave. Insira o seguinte para ver as subscrições da sua conta:

```powershell
Get-AzSubscription
```

Para especificar a subscrição que está associada ao cofre chave que vai registar, introduza:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Como este artigo demonstra guardar uma chave de conta de armazenamento como um segredo, você deve obter a chave da conta de armazenamento.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Depois de recuperar o seu segredo (neste caso, a chave da sua conta de armazenamento), deve converter essa chave numa corda segura e, em seguida, criar um segredo com esse valor no seu cofre chave.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Em seguida, pegue o URI para o segredo que criou. Vai precisar deste URI num passo posterior para ligar para o cofre da chave e recuperar o seu segredo. Executar o seguinte comando PowerShell e tomar nota do valor de identificação, que é o URI do segredo:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Configurar a aplicação

Agora que tem um segredo armazenado, pode usar o código para recuperá-lo e usá-lo depois de realizar mais alguns passos.

Em primeiro lugar, deve registar a sua candidatura no Azure Ative Directory. Em seguida, informe a Key Vault sobre as suas informações de aplicação para que possa permitir pedidos da sua aplicação.

> [!NOTE]
> A sua candidatura deve ser criada no mesmo inquilino do Azure Ative Directory que o seu cofre chave.

1. Diretório **Ativo Open Azure.**
2. Selecione **Registos das aplicações**. 
3. Selecione **novo registo** de candidatura para adicionar uma aplicação ao Azure Ative Directory.

    ![Candidaturas abertas no Diretório Ativo do Azure](./media/keyvault-keyrotation/azure-ad-application.png)

4. Em **Create,** deixe o tipo de aplicação como **aplicação Web / API** e dê um nome à sua aplicação. Dê à sua aplicação um **URL de inscrição**. Este URL pode ser o que quiser para esta demonstração.

    ![Criar inscrição de candidatura](./media/keyvault-keyrotation/create-app.png)

5. Depois de a aplicação ser adicionada ao Diretório Ativo Azure, a página de candidatura abre. Selecione **Definições,** e, em seguida, selecione **Propriedades**. Copiar o valor de ID da **aplicação.** Vai precisar em passos posteriores.

Em seguida, gere uma chave para a sua aplicação para que possa interagir com o Diretório Ativo Azure. Para criar uma tecla, selecione **Teclas** em **Definições**. Tome nota da chave recém-gerada para a sua aplicação Azure Ative Directory. Irá precisar deles noutro passo. A chave não estará disponível depois de deixar esta secção. 

![Chaves de aplicativo sinuoso Azure Ative Diretório](./media/keyvault-keyrotation/create-key.png)

Antes de estabelecer chamadas da sua aplicação para o cofre da chave, deve contar ao cofre da chave sobre a sua aplicação e as suas permissões. O comando seguinte utiliza o nome do cofre e o ID da aplicação da sua aplicação Azure Ative Directory para conceder a aplicação **Ter** acesso ao seu cofre chave.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Está pronto para começar a construir as suas chamadas de candidatura. Na sua aplicação, tem de instalar os pacotes NuGet que são necessários para interagir com o Azure Key Vault e o Azure Ative Directory. A partir da consola Visual Studio Package Manager, introduza os seguintes comandos. Na escrita deste artigo, a versão atual do pacote Azure Ative Diretório é 3.10.305231913, por isso confirme a versão mais recente e a atualização conforme necessário.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

No seu código de aplicação, crie uma classe para manter o método para a autenticação do Seu Diretório Ativo Azure. Neste exemplo, essa classe chama-se **Utils**. Adicione a `using` seguinte declaração:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Em seguida, adicione o seguinte método para recuperar o símbolo JWT do Diretório Ativo Azure. Para manter a sua capacidade, é melhor mover os valores de cadeia codificados para a sua configuração web ou aplicação.

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

Adicione o código necessário para ligar para o Cofre chave e recuperar o seu valor secreto. Em primeiro lugar, `using` deve adicionar a seguinte declaração:

```csharp
using Microsoft.Azure.KeyVault;
```

Adicione as chamadas para invocar o Cofre chave e recuperar o seu segredo. Neste método, fornece o URI secreto que guardou num passo anterior. Note a utilização do método **GetToken** da classe **Utils** que criou anteriormente.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Quando executar a sua aplicação, deverá agora autenticar o Diretório Ativo Azure e, em seguida, recuperar o seu valor secreto do Cofre de Chaves Azure.

## <a name="key-rotation-using-azure-automation"></a>Rotação de chaves utilizando a Automação Azure

> [!IMPORTANT]
> Os livros de execução da `AzureRM` Azure Automation ainda requerem a utilização do módulo.

Está agora pronto para estabelecer uma estratégia de rotação para os valores que armazena como segredos do Cofre Chave. Os segredos podem ser rodados de várias formas:

- Como parte de um processo manual
- Programáticamente através da utilização de chamadas API
- Através de um roteiro de Automação Azure

Para efeitos deste artigo, utilizará a PowerShell combinada com a Azure Automation para alterar a chave de acesso de uma conta de armazenamento Azure. Em seguida, atualizará um segredo de cofre com a nova chave.

Para permitir que a Azure Automation estabeleça valores secretos no seu cofre chave, tem de obter o ID do cliente para a ligação chamada **AzureRunAsConnection**. Esta ligação foi criada quando estabeleceu a sua instância de Automação Azure. Para encontrar este ID, selecione **Assets** da sua instância De automação Azure. A partir daí, selecione **Ligações**e, em seguida, selecione o diretor de serviço **AzureRunAsConnection.** Tome nota do valor do **ApplicationId.**

![ID do cliente azure Automation](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Em **Ativos,** selecione **Módulos**. Selecione **Gallery**, e, em seguida, procure e importe versões atualizadas de cada um dos seguintes módulos:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Na escrita deste artigo, apenas os módulos anteriormente notados precisavam de ser atualizados para o seguinte script. Se o seu trabalho de automação falhar, confirme que importou todos os módulos necessários e as suas dependências.

Depois de ter recuperado o ID de aplicação da sua ligação Azure Automation, deve dizer ao seu cofre chave que esta aplicação tem permissão para atualizar segredos no seu cofre. Utilize o seguinte comando PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Em seguida, selecione **Runbooks** sob a sua instância de Automação Azure e, em seguida, selecione **Add Runbook**. Selecione **Criação Rápida**. Nomeie o seu livro de execução e selecione **PowerShell** como o tipo de livro de execução. Pode adicionar uma descrição. Finalmente, selecione **Criar**.

![Criar runbook](./media/keyvault-keyrotation/Create_Runbook.png)

Colhe o seguinte guião PowerShell no painel do editor para o seu novo livro de execução:

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

No painel do editor, selecione **test e painel** para testar o seu script. Depois de o script ser executado sem erros, pode selecionar **Publicar**, e depois pode aplicar uma programação para o livro de execução no painel de configuração do livro de execução.

## <a name="key-vault-auditing-pipeline"></a>Pipeline de auditoria key vault

Quando configurar um cofre de chaves, pode ligar a auditoria para recolher registos de pedidos de acesso feitos ao cofre da chave. Estes registos são armazenados numa conta de armazenamento azure designada e podem ser retirados, monitorizados e analisados. O cenário seguinte utiliza funções Azure, aplicações lógicas Azure e registos de auditoria key-vault para criar um pipeline que envia um e-mail quando uma aplicação que não corresponde ao ID da aplicação da web recupera segredos do cofre.

Primeiro, tens de ativar o registo no cofre da chave. Utilize os seguintes comandos PowerShell. (Pode ver todos os detalhes deste artigo sobre a [exploração de porta-chaves .)](key-vault-logging.md)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Após a ativação da exploração madeireira, os registos de auditoria começam a ser armazenados na conta de armazenamento designada. Estes registos contêm eventos sobre como e quando os seus cofres chave são acedidos, e por quem.

> [!NOTE]
> Pode aceder à sua informação de registo 10 minutos após a operação do cofre da chave. Muitas vezes estará disponível mais cedo do que isso.

O próximo passo é [criar uma fila azure service bus.](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) Esta fila é onde os registos de auditoria do cofre de chaves são empurrados. Quando as mensagens de registo de auditoria estão na fila, a aplicação lógica recolhe-as e atua sobre elas. Crie uma instância de ônibus de serviço com os seguintes passos:

1. Crie um espaço de nome de ônibus de serviço (se já tem um que deseja usar, salte para o passo 2).
2. Navegue na instância do Ônibus de serviço no portal Azure e selecione o espaço de nome sinuoso em que pretende criar a fila.
3. Selecione Criar um**ônibus**de serviço de**integração** > empresarial de **recursos,** > e depois introduza os detalhes necessários.
4. Encontre as informações de ligação do Ônibus de serviço selecionando o espaço de nome e, em seguida, selecionando informações de **ligação**. Vai precisar desta informação para a próxima secção.

Em seguida, [crie uma função Azure](../azure-functions/functions-create-first-azure-function.md) para sondar os registos do cofre chave dentro da conta de armazenamento e recolher novos eventos. Esta função será ativada num horário.

Para criar uma aplicação de função Azure, selecione **Criar um recurso,** pesquisar no mercado para **app de função**, e, em seguida, selecione **Criar**. Durante a criação, você pode usar um plano de hospedagem existente ou criar um novo. Também pode optar por hospedagem dinâmica. Para obter mais informações sobre as opções de hospedagem para funções Azure, consulte [Como escalar as Funções Azure](../azure-functions/functions-scale.md).

Depois da aplicação de função Azure ser criada, vá até ela e selecione o cenário **temporizador** e **\# C** para o idioma. Em seguida, **selecione Criar esta função**.

![Funções Azure Iniciar lâmina](./media/keyvault-keyrotation/Azure_Functions_Start.png)

No separador **Desenvolver,** substitua o código run.csx pelo seguinte:

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
> Altere as variáveis do código anterior para indicar a sua conta de armazenamento onde estão escritos os registos do cofre chave, para a instância do Ônibus de serviço que criou anteriormente, e para o caminho específico para os registos de armazenamento do cofre chave.

A função recolhe o último ficheiro de registo da conta de armazenamento onde os registos do cofre da chave são escritos, agarra os últimos eventos desse ficheiro e empurra-os para uma fila de autocarros de serviço. 

Como um único ficheiro pode ter vários eventos, você deve criar um ficheiro sync.txt que a função também olha para determinar o carimbo de tempo do último evento que foi recolhido. A utilização deste ficheiro garante que não pressiona o mesmo evento várias vezes. 

O ficheiro sync.txt contém um carimbo de tempo para o último evento encontrado. Quando os troncos estão carregados, devem ser classificados com base nos seus selos temporais para garantir que são encomendados corretamente.

Para esta função, referimos algumas bibliotecas adicionais que não estão disponíveis fora da caixa em Funções Azure. Para incluir estas bibliotecas, precisamos de Funções Azure para as puxar usando o NuGet. Na caixa **Código,** selecione **Ver Ficheiros**.

![Opção "Ver ficheiros"](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Adicione um ficheiro chamado project.json com o seguinte conteúdo:

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

Depois de selecionar **Save,** as Funções Azure descarregarão os binários necessários.

Mude para o separador **Integração** e dê ao parâmetro temporizador um nome significativo para utilizar dentro da função. No código anterior, a função espera que o temporizador seja chamado *de myTimer*. Especifique uma [expressão CRON](../app-service/webjobs-create.md#CreateScheduledCRON) `0 * * * * *`para o temporizador da seguinte forma: . Esta expressão fará com que a função funcione uma vez por minuto.

No mesmo separador **Integração,** adicione uma entrada do tipo **de armazenamento Azure Blob**. Esta entrada irá apontar para o ficheiro sync.txt que contém o carimbo de tempo do último evento analisado pela função. Esta entrada será acedida dentro da função utilizando o nome do parâmetro. No código anterior, a entrada de armazenamento do Blob Azure espera que o nome do parâmetro seja *inputBlob*. Selecione a conta de armazenamento onde o ficheiro sync.txt será localizado (pode ser a mesma ou uma conta de armazenamento diferente). No campo de caminho, forneça o caminho `{container-name}/path/to/sync.txt`para o ficheiro no formato .

Adicione uma saída do tipo **de armazenamento Azure Blob**. Esta saída irá apontar para o ficheiro sync.txt definido na entrada. Esta saída é utilizada pela função para escrever o carimbo de tempo do último evento analisado. O código anterior espera que este parâmetro seja chamado *outputBlob*.

A função está agora pronta. Certifique-se de voltar a mudar para o separador **Desenvolver** e guardar o código. Verifique se a janela de saída está a verificar se há erros de compilação e corrija-os conforme necessário. Se o código estiver compilado, o código deve agora verificar os registos do cofre chave a cada minuto e empurrar quaisquer novos eventos para a fila definida do Service Bus. Deve ver a informação de registo escrita na janela de registo sempre que a função é acionada.

### <a name="azure-logic-app"></a>App lógica Azure

Em seguida, você deve criar uma aplicação lógica Azure que capte os eventos que a função está empurrando para a fila service Bus, analisa o conteúdo, e envia um e-mail com base numa condição que está sendo correspondida.

[Crie uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md) selecionando Criar uma**Aplicação Lógica**de**Integração** > de **Recursos.** > 

Depois da aplicação lógica ser criada, vá até ela e **selecione Editar**. No editor de aplicações lógica, selecione **Service Bus Queue** e introduza as credenciais do Ônibus de serviço para ligá-la à fila.

![Ônibus de serviço de aplicativo sonárlo Azure Logic](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

**Selecione Adicionar uma condição**. Na condição, mude para o editor avançado e introduza o seguinte código. Substitua *APP_ID* com o ID real da sua aplicação web:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Esta expressão essencialmente devolve **falso** se o *apendicite* do evento de entrada (que é o corpo da mensagem Service Bus) não for o *apendicite* da app.

Agora, crie uma ação sob **se NÃO, NÃO FAÇA NADA.**

![Aplicativos Da Lógica Azure escolhem ação](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Para a ação, selecione **Office 365 - envie e-mail**. Preencha os campos para criar um e-mail para enviar quando a condição definida retorna **falsa**. Se não tiver o Office 365, procure alternativas para obter os mesmos resultados.

Agora tem um oleoduto de ponta a ponta que procura novos registos de auditoria do cofre de chaves uma vez por minuto. Empurra novos troncos que encontra para uma fila de ônibus de serviço. A aplicação lógica é desencadeada quando uma nova mensagem aterra na fila. Se o *appid* dentro do evento não corresponder ao ID da aplicação de chamada, envia um e-mail.
