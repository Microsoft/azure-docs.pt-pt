---
title: Iniciar um runbook da automatização do Azure com um webhook
description: Um webhook que permite que um cliente iniciar um runbook na automatização do Azure a partir de uma chamada HTTP.  Este artigo descreve como criar um webhook e como chamar um para iniciar um runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/13/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 920d6ee9b584e908885cf661d32aa700e655c3f8
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56816424"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Iniciar um runbook da automatização do Azure com um webhook

R *webhook* permite-lhe iniciar um runbook específico na automatização do Azure através de um único pedido HTTP. Isso permite que os serviços externos, como serviços de DevOps do Azure, o GitHub, registos do Azure Monitor ou aplicativos personalizados para iniciar runbooks sem implementar uma solução completa com a API de automatização do Azure.  
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Pode comparar webhooks para outros métodos de iniciar um runbook [iniciar um runbook na automatização do Azure](automation-starting-a-runbook.md)

## <a name="details-of-a-webhook"></a>Detalhes de um webhook

A tabela seguinte descreve as propriedades que devem ser configuradas para um webhook.

| Propriedade | Descrição |
|:--- |:--- |
| Name |Pode fornecer qualquer nome que pretende para um webhook, uma vez que isso não é exposto ao cliente. É utilizado apenas para si para identificar o runbook na automatização do Azure. <br> Como melhor prática, deve dar o webhook um nome relacionado com o cliente que o utiliza. |
| do IdP |O URL do webhook é o endereço exclusivo que um cliente chama-se com um HTTP POST para iniciar o runbook associado para o webhook. É gerado automaticamente quando cria o webhook. Não é possível especificar um URL personalizado. <br> <br> O URL contém um token de segurança que permite que o runbook seja invocado por um sistema de terceiros sem autenticação adicional. Por esse motivo, deve ser tratada como uma palavra-passe. Por motivos de segurança, apenas pode ver o URL no portal do Azure no momento que o webhook for criado. Anote o URL numa localização segura para utilização futura. |
| Data de validade |Como um certificado, cada webhook tem uma data de expiração nesse momento que já não pode ser utilizado. Esta data de expiração pode ser modificada depois do webhook for criado, desde que o webhook não está expirado. |
| Ativado |Um webhook está ativado por predefinição, quando é criado. Se definido como desativado, nenhum cliente pode utilizá-lo. Pode definir o **ativado** propriedade ao criar o webhook ou em qualquer altura uma vez é criada. |

### <a name="parameters"></a>Parâmetros

Um webhook pode definir valores para parâmetros do runbook que são utilizados quando o runbook é iniciado por esse webhook. O webhook tem de incluir valores para todos os parâmetros obrigatórios do runbook e pode incluir valores para parâmetros opcionais. Um valor de parâmetro configurado para um webhook pode ser modificado, mesmo depois de criar o webhook. Cada um vários webhooks ligados a um único runbook pode utilizar valores de parâmetros diferentes.

Quando um cliente inicia um runbook com um webhook, ele não é possível substituir os valores dos parâmetros definidos no webhook. Para receber dados do cliente, o runbook pode aceitar um parâmetro único chamado **$WebhookData**. Este parâmetro é do tipo [object] que contém dados que o cliente inclua no pedido POST.

![Propriedades do Webhookdata](media/automation-webhooks/webhook-data-properties.png)

O **$WebhookData** objeto tem as seguintes propriedades:

| Propriedade | Descrição |
|:--- |:--- |
| WebhookName |O nome do webhook. |
| RequestHeader |Tabela de hash que contém os cabeçalhos do pedido de mensagem a receber. |
| RequestBody |O corpo do pedido de mensagem a receber. Isso mantém a qualquer formatação, como a cadeia de caracteres, JSON, XML, ou formulário codificado em dados. O runbook deve ser escrito para trabalhar com o formato de dados que é esperado. |

Não existe nenhuma configuração do webhook necessário para suportar o **$WebhookData** parâmetro e o runbook não é necessário para aceitá-lo. Se o runbook não definir o parâmetro, em seguida, todos os detalhes do pedido enviados do cliente é ignorada.

Se especificar um valor para $WebhookData ao criar o webhook que valor é substituído quando o webhook do runbook é iniciado com os dados a partir do pedido POST do cliente, mesmo que o cliente não inclui todos os dados no corpo do pedido. Se iniciar um runbook que tenha $WebhookData usando um método que não seja um webhook, pode fornecer um valor para $Webhookdata é reconhecido pelo runbook. Este valor deve ser um objeto com o mesmo [propriedades](#details-of-a-webhook) como $Webhookdata para que o runbook pode trabalhar corretamente com o mesmo como se ele estava trabalhando WebhookData real passada por um webhook.

Por exemplo, se estiver a iniciar o runbook abaixo do portal do Azure e pretender passar alguns WebhookData de exemplo para fins de teste, uma vez que o WebhookData é um objeto, ele deve ser passado como JSON na interface do Usuário.

![Parâmetro de WebhookData da interface do Usuário](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Para o runbook seguinte, se tiver as propriedades seguintes para o parâmetro WebhookData:

* WebhookName: *MyWebhook*
* RequestBody: *[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*

Em seguida, passa o seguinte valor JSON na interface de Usuário para o parâmetro WebhookData. O exemplo seguinte com o símbolo devolve e carateres de nova linha corresponde ao formato que é transmitido a partir de um webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Iniciar o WebhookData parâmetro a partir da interface do Usuário](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Os valores de todos os parâmetros de entrada são registados com a tarefa de runbook. Isso significa que qualquer entrada fornecida pelo cliente no pedido de webhook será iniciada e disponível para qualquer pessoa com acesso para a tarefa de automatização.  Por esse motivo, deve ter cuidado sobre informações confidenciais, incluindo em chamadas de webhook.

## <a name="security"></a>Segurança

A segurança de um webhook conta com a privacidade dos seu URL, que contém um token de segurança que permite que ele seja invocado. A automatização do Azure não efetuar qualquer autenticação no pedido, desde que ele é feito para o URL correto. Por esse motivo, os webhooks não deve ser utilizados para runbooks que executam funções altamente confidenciais sem utilizar um meio alternativo de validar o pedido.

Pode incluir a lógica dentro do runbook para determinar o que ele era chamado por um webhook, verificando a **WebhookName** propriedade do parâmetro $WebhookData. O runbook poderia efetuar a validação, procurando por informações específicas no **RequestHeader** ou **RequestBody** propriedades.

Outra estratégia consiste em efetuar uma validação de uma condição externa quando ele recebeu um pedido webhook do runbook. Por exemplo, considere um runbook que é chamado pelo GitHub sempre que houver uma consolidação de novo para um repositório do GitHub. O runbook pode ligar-se ao GitHub para validar que uma consolidação novo tinha ocorreu antes de continuar.

## <a name="creating-a-webhook"></a>Criar um webhook

Utilize o procedimento seguinte para criar um novo webhook ligado a um runbook no portal do Azure.

1. Partir do **Runbooks página** no portal do Azure, clique no runbook que o webhook começa a ver a página de detalhes. Certifique-se o runbook **Status** é **publicada**.
2. Clique em **Webhook** na parte superior da página para abrir o **adicionar Webhook** página.
3. Clique em **criar novo webhook** para abrir o **página de criação de webhook**.
4. Especifique um **Name**, **data de expiração** para o webhook e se devia estar ativada. Ver [detalhes de um webhook](#details-of-a-webhook) para obter mais informações estas propriedades.
5. Clique no ícone de cópia e pressione Ctrl + C para copiar o URL do webhook. Em seguida, guarde-a num local seguro. **Depois de criar o webhook, não é possível obter o URL novamente.**

   ![URL de webhook](media/automation-webhooks/copy-webhook-url.png)

1. Clique em **parâmetros** para fornecer valores para parâmetros do runbook. Se o runbook tiver parâmetros obrigatórios, em seguida, não é possível criar o webhook, a menos que os valores são fornecidos.
1. Clique em **criar** para criar o webhook.

## <a name="using-a-webhook"></a>Utilizar um webhook

Para utilizar um webhook depois de este ter sido criado, a aplicação cliente tem de emitir um HTTP POST com o URL do webhook. A sintaxe do webhook é no seguinte formato:

```http
http://<Webhook Server>/token?=<Token Value>
```

O cliente recebe um dos seguintes códigos de retornados da solicitação POST.

| Código | Texto | Descrição |
|:--- |:--- |:--- |
| 202 |Aceite |O pedido foi aceite e o runbook com êxito foi colocado em fila. |
| 400 |Pedido Inválido |O pedido não foi aceite para um dos seguintes motivos: <ul> <li>O webhook expirou.</li> <li>O webhook está desativado.</li> <li>O token no URL é inválido.</li>  </ul> |
| 404 |Não Encontrado |O pedido não foi aceite para um dos seguintes motivos: <ul> <li>O webhook não foi encontrado.</li> <li>O runbook não foi encontrado.</li> <li>A conta não foi encontrada.</li>  </ul> |
| 500 |Erro Interno do Servidor |O URL era válido, mas ocorreu um erro. Volte a submeter o pedido. |

Partindo do princípio de que a solicitação for bem-sucedida, a resposta do webhook contém o ID da tarefa no formato JSON da seguinte forma. Ele conterá uma ID de tarefa única, mas permite que o formato JSON para aperfeiçoamentos futuros potenciais.

```json
{"JobIds":["<JobId>"]}
```

O cliente não consegue determinar quando a tarefa de runbook é concluída ou o estado de conclusão de que o webhook. Ele pode determinar essas informações com o ID da tarefa com outro método, como [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) ou o [API de automação de Azure](/rest/api/automation/job).

## <a name="renew-webhook"></a>Renovar um webhook

Quando é criado um webhook, ele tem um tempo de validade de um ano. Depois desse ano tempo o webhook automaticamente expira. Após a expiração de um webhook não pode ser reativado, tem de ser removido e recriado. Se um webhook não atingiu o seu tempo de expiração, ele pode ser estendido.

Para expandir um webhook, navegue para o runbook que contém o webhook. Selecione **Webhooks** sob **recursos**. Clique com o webhook que deseja estender, esta ação abre o **Webhook** página.  Escolha uma nova data de expiração e hora e clique em **guardar**.

## <a name="sample-runbook"></a>Runbook de exemplo

O runbook de exemplo seguintes aceita os dados de webhook e inicia as máquinas virtuais especificadas no corpo do pedido. Para testar este runbook na conta de automatização sob **Runbooks**, clique em **+ adicionar um runbook**. Se não sabe como criar um runbook, consulte [criar um runbook](automation-quickstart-create-runbook.md).

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

## <a name="test-the-example"></a>O exemplo de teste

O exemplo seguinte utiliza o Windows PowerShell para iniciar um runbook com um webhook. Qualquer linguagem que pode fazer uma solicitação HTTP pode utilizar um webhook; Windows PowerShell é utilizado aqui como um exemplo.

O runbook está esperando uma lista de máquinas virtuais formatado em JSON no corpo do pedido. O runbook também valida que os cabeçalhos de contenham um definido mensagem para validar o chamador de webhook é válida.

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

O exemplo seguinte mostra o corpo do pedido que está disponível para o runbook no **RequestBody** propriedade da **WebhookData**. Esse valor é formatado como JSON, porque essa era o formato que foi incluído no corpo do pedido.

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

A imagem seguinte mostra a solicitação a ser enviada pelo Windows PowerShell e a resposta resultante. O ID da tarefa é extraído da resposta e convertido numa cadeia.

![Botão de Webhooks](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Passos Seguintes

* Para saber como utilizar a automatização do Azure para tomar medidas em alertas do Azure, veja [utilize um alerta para acionar um runbook da automatização do Azure](automation-create-alert-triggered-runbook.md).

