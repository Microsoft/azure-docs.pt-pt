---
title: Iniciando um runbook de automação do Azure com um webhook
description: Um webhook que permite que um cliente inicie um runbook na automação do Azure de uma chamada HTTP.  Este artigo descreve como criar um webhook e como chamar um para iniciar um runbook.
services: automation
ms.subservice: process-automation
ms.date: 03/19/2019
ms.topic: conceptual
ms.openlocfilehash: fbf3a48d1e7cb3dd80b6c418d7c916184756b6fa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75418961"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Iniciando um runbook de automação do Azure com um webhook

Um *webhook* permite que você inicie um runbook específico na automação do Azure por meio de uma única solicitação HTTP. Isso permite que serviços externos, como Azure DevOps Services, GitHub, logs de Azure Monitor ou aplicativos personalizados iniciem runbooks sem implementar uma solução completa usando a API de automação do Azure.
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Você pode comparar WebHooks com outros métodos de iniciar um runbook no [início de um runbook na automação do Azure](automation-starting-a-runbook.md)

> [!NOTE]
> Não há suporte para o uso de um webhook para iniciar um runbook do Python.

## <a name="details-of-a-webhook"></a>Detalhes de um webhook

A tabela a seguir descreve as propriedades que você deve configurar para um webhook.

| Propriedade | Descrição |
|:--- |:--- |
| Nome |Você pode fornecer qualquer nome desejado para um webhook, pois ele não é exposto ao cliente. Ele é usado apenas para você identificar o runbook na automação do Azure. <br> Como prática recomendada, você deve dar um nome ao webhook relacionado ao cliente que o utiliza. |
| URL |A URL do webhook é o endereço exclusivo que um cliente chama com um HTTP POST para iniciar o runbook vinculado ao webhook. Ele é gerado automaticamente quando você cria o webhook. Você não pode especificar uma URL personalizada. <br> <br> A URL contém um token de segurança que permite que o runbook seja invocado por um sistema de terceiros sem autenticação adicional. Por esse motivo, ele deve ser tratado como uma senha. Por motivos de segurança, você só pode exibir a URL no portal do Azure no momento em que o webhook é criado. Observe a URL em um local seguro para uso futuro. |
| Data de validade |Como um certificado, cada webhook tem uma data de expiração em que momento ele não pode mais ser usado. Essa data de expiração pode ser modificada depois que o webhook for criado, desde que o webhook não tenha expirado. |
| Ativado |Um webhook é habilitado por padrão quando ele é criado. Se você defini-lo como desabilitado, nenhum cliente poderá usá-lo. Você pode definir a propriedade **Enabled** ao criar o webhook ou a qualquer momento quando ele for criado. |

### <a name="parameters"></a>Parâmetros

Um webhook pode definir valores para parâmetros de runbook que são usados quando o runbook é iniciado por esse webhook. O webhook deve incluir valores para quaisquer parâmetros obrigatórios do runbook e pode incluir valores para parâmetros opcionais. Um valor de parâmetro configurado para um webhook pode ser modificado mesmo após a criação do webhook. Vários WebHooks vinculados a um único runbook podem usar valores de parâmetro diferentes.

Quando um cliente inicia um runbook usando um webhook, ele não pode substituir os valores de parâmetro definidos no webhook. Para receber dados do cliente, o runbook pode aceitar um único parâmetro chamado **$WebhookData**. Esse parâmetro é de um tipo [Object] que contém dados que o cliente inclui na solicitação POST.

![Propriedades de Webhookdata](media/automation-webhooks/webhook-data-properties.png)

O objeto **$WebhookData** tem as seguintes propriedades:

| Propriedade | Descrição |
|:--- |:--- |
| WebhookName |O nome do webhook. |
| RequestHeader |Tabela de hash que contém os cabeçalhos da solicitação POST de entrada. |
| RequestBody |O corpo da solicitação POST de entrada. Isso mantém qualquer formatação, como cadeia de caracteres, JSON, XML ou dados codificados de formulário. O runbook deve ser gravado para funcionar com o formato de dados esperado. |

Não há nenhuma configuração do webhook necessária para dar suporte ao parâmetro **$WebhookData** e o runbook não é necessário para aceitá-lo. Se o runbook não definir o parâmetro, todos os detalhes da solicitação enviada do cliente serão ignorados.

> [!NOTE]
> Ao chamar um webhook, você sempre deve armazenar qualquer valor de parâmetro em caso de falha da chamada. Se houver uma interrupção de rede ou um problema de conexão, você não poderá recuperar chamadas de webhook com falha.

Se você especificar um valor para $WebhookData ao criar o webhook, esse valor será substituído quando o webhook iniciar o runbook com os dados da solicitação POST do cliente, mesmo que o cliente não inclua nenhum dado no corpo da solicitação. Se você iniciar um runbook que tem $WebhookData usando um método diferente de um webhook, poderá fornecer um valor para $Webhookdata reconhecido pelo runbook. Esse valor deve ser um objeto com as mesmas [Propriedades](#details-of-a-webhook) que $Webhookdata para que o runbook possa trabalhar corretamente com ele como se estivesse trabalhando com o Webhookdata real passado por um webhook.

Por exemplo, se você estiver iniciando o runbook a seguir no portal do Azure e quiser passar algum WebhookData de exemplo para teste, como WebhookData é um objeto, ele deve ser passado como JSON na interface do usuário.

![Parâmetro WebhookData da interface do usuário](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Para o runbook a seguir, se você tiver as seguintes propriedades para o parâmetro WebhookData:

* Webhookname: *MyWebhook*
* RequestBody: *[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*

Em seguida, você passaria o valor JSON a seguir na interface do usuário para o parâmetro WebhookData. O exemplo a seguir com retornos de carro e caracteres de nova linha corresponde ao formato passado de um webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Iniciar o parâmetro WebhookData da interface do usuário](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Os valores de todos os parâmetros de entrada são registrados com o trabalho de runbook. Isso significa que qualquer entrada fornecida pelo cliente na solicitação de webhook será registrada e estará disponível para qualquer pessoa com acesso ao trabalho de automação.  Por esse motivo, você deve ter cuidado ao incluir informações confidenciais em chamadas de webhook.

## <a name="security"></a>Segurança

A segurança de um webhook depende da privacidade de sua URL, que contém um token de segurança que permite que ele seja invocado. A automação do Azure não executa nenhuma autenticação na solicitação, contanto que seja feita na URL correta. Por esse motivo, os WebHooks não devem ser usados para runbooks que executam funções altamente confidenciais sem usar um meio alternativo de validar a solicitação.

Você pode incluir lógica dentro do runbook para determinar que ele foi chamado por um webhook, verificando a propriedade **webhookname** do parâmetro $WebhookData. O runbook pode executar uma validação adicional procurando informações específicas nas propriedades **RequestHeader** ou **RequestBody** .

Outra estratégia é fazer com que o runbook execute alguma validação de uma condição externa quando recebeu uma solicitação de webhook. Por exemplo, considere um runbook chamado pelo GitHub sempre que houver uma nova confirmação para um repositório GitHub. O runbook pode se conectar ao GitHub para validar que uma nova confirmação ocorreu antes de continuar.

## <a name="creating-a-webhook"></a>Criando um webhook

Use o procedimento a seguir para criar um novo webhook vinculado a um runbook no portal do Azure.

1. Na **página Runbooks** no portal do Azure, clique no runbook que o webhook começa a exibir sua página de detalhes. Verifique se o **status** do runbook está **publicado**.
2. Clique em **webhook** na parte superior da página para abrir a página **Adicionar webhook** .
3. Clique em **criar novo webhook** para abrir a **página Criar webhook**.
4. Especifique um **nome**, uma **data de validade** para o webhook e se ele deve ser habilitado. Consulte os [detalhes de um webhook](#details-of-a-webhook) para obter mais informações sobre essas propriedades.
5. Clique no ícone de cópia e pressione CTRL + C para copiar a URL do webhook. Em seguida, registre-o em um local seguro. **Depois de criar o webhook, você não poderá recuperar a URL novamente.**

   ![URL do Webhook](media/automation-webhooks/copy-webhook-url.png)

1. Clique em **parâmetros** para fornecer valores para os parâmetros de runbook. Se o runbook tiver parâmetros obrigatórios, não poderá criar o webhook, a não ser que sejam fornecidos os valores.
1. Clique em **Criar** para criar o webhook.

## <a name="using-a-webhook"></a>Usando um webhook

Para usar um webhook depois que ele tiver sido criado, o aplicativo cliente deverá emitir um HTTP POST com a URL para o webhook. A sintaxe do webhook está no seguinte formato:

```http
http://<Webhook Server>/token?=<Token Value>
```

O cliente recebe um dos seguintes códigos de retorno da solicitação POST.

| Código | Texto | Descrição |
|:--- |:--- |:--- |
| 202 |Aceite |A solicitação foi aceita e o runbook foi enfileirado com êxito. |
| 400 |Pedido Incorreto |A solicitação não foi aceita por um dos seguintes motivos: <ul> <li>O webhook expirou.</li> <li>O webhook está desabilitado.</li> <li>O token na URL é inválido.</li>  </ul> |
| 404 |Não foi encontrado |A solicitação não foi aceita por um dos seguintes motivos: <ul> <li>O webhook não foi encontrado.</li> <li>O runbook não foi encontrado.</li> <li>A conta não foi encontrada.</li>  </ul> |
| 500 |Erro interno do servidor |A URL era válida, mas ocorreu um erro. Envie a solicitação novamente. |

Supondo que a solicitação foi bem-sucedida, a resposta do webhook contém a ID do trabalho no formato JSON da seguinte maneira. Ele conterá uma única ID de trabalho, mas o formato JSON permite possíveis aprimoramentos futuros.

```json
{"JobIds":["<JobId>"]}
```

O cliente não pode determinar quando o trabalho de runbook é concluído ou seu status de conclusão do webhook. Ele pode determinar essas informações usando a ID do trabalho com outro método, como o [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) ou a [API de automação do Azure](/rest/api/automation/job).

## <a name="renew-webhook"></a>Renovar um webhook

Quando um webhook é criado, ele tem um tempo de validade de um ano. Após esse ano, o webhook expirará automaticamente. Depois que um webhook estiver expirado, ele não poderá ser reativado, ele deverá ser removido e recriado. Se um webhook não tiver atingido sua hora de expiração, ele poderá ser estendido.

Para estender um webhook, navegue até o runbook que contém o webhook. Selecione **WebHooks** em **recursos**. Clique no webhook que você deseja estender, essa ação abre a página **webhook** .  Escolha uma nova data e hora de expiração e clique em **salvar**.

## <a name="sample-runbook"></a>Runbook de exemplo

O exemplo de runbook a seguir aceita os dados de webhook e inicia as máquinas virtuais especificadas no corpo da solicitação. Para testar esse runbook, em sua conta de automação em **Runbooks**, clique em **+ Adicionar um runbook**. Se você não souber como criar um runbook, consulte [criando um runbook](automation-quickstart-create-runbook.md).

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)



# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

    # Retrieve VM's from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName"

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal."
            Add-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzureRMVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-example"></a>Testar o exemplo

O exemplo a seguir usa o Windows PowerShell para iniciar um runbook com um webhook. Qualquer linguagem que possa fazer uma solicitação HTTP pode usar um webhook; O Windows PowerShell é usado aqui como exemplo.

O runbook está esperando uma lista de máquinas virtuais formatadas em JSON no corpo da solicitação. O runbook é validado também que os cabeçalhos contêm uma mensagem definida para validar se o chamador do webhook é válido.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

O exemplo a seguir mostra o corpo da solicitação que está disponível para o runbook na propriedade **RequestBody** de **WebhookData**. Esse valor é formatado como JSON porque era o formato que foi incluído no corpo da solicitação.

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

A imagem a seguir mostra a solicitação que está sendo enviada do Windows PowerShell e a resposta resultante. A ID do trabalho é extraída da resposta e convertida em uma cadeia de caracteres.

![Botão WebHooks](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Passos seguintes

* Para saber como usar a automação do Azure para tomar medidas sobre alertas do Azure, consulte [usar um alerta para disparar um runbook de automação do Azure](automation-create-alert-triggered-runbook.md).

