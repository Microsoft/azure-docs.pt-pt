---
title: Iniciar um livro de execução da Automação Azure com um webhook
description: Um webhook que permite a um cliente iniciar um livro de execução em Automação Azure a partir de uma chamada HTTP.  Este artigo descreve como criar um webhook e como chamar um para iniciar um livro de corridas.
services: automation
ms.subservice: process-automation
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: 043350db2c5372fc81fbb2b68155a4ac75457208
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278405"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Iniciar um livro de execução da Automação Azure com um webhook

Um webhook permite que um serviço externo inicie um livro de execução específico na Automatização Azure através de um único pedido HTTP. Os serviços externos incluem serviços Azure DevOps, GitHub, registos Do Monitor Azure e aplicações personalizadas. Este serviço pode utilizar um webhook para iniciar um livro de execução sem implementar uma solução completa utilizando a API de Automação Azure. Pode comparar webhooks com outros métodos de iniciar um livro de execução em Iniciar um livro de [execução em Automação Azure](automation-starting-a-runbook.md).

> [!NOTE]
> Não é suportado o uso de um webhook para iniciar um livro de execução python.

![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](automation-update-azure-modules.md).

## <a name="webhook-properties"></a>Propriedades webhook

A tabela seguinte descreve as propriedades que deve configurar para um webhook.

| Propriedade | Descrição |
|:--- |:--- |
| Nome |Nome do gancho web. Pode fornecer o nome que quiser, já que não está exposto ao cliente. Só é usado para identificar o livro de execução na Automação Azure. Como uma boa prática, deve dar ao webhook um nome relacionado com o cliente que o utiliza. |
| do IdP |URL do webhook. Este é o endereço único que um cliente liga com um POST HTTP para iniciar o livro de execução ligado ao webhook. É gerado automaticamente quando se cria o webhook. Não pode especificar um URL personalizado. <br> <br> O URL contém um símbolo de segurança que permite que um sistema de terceiros invoque o livro de execução sem mais autenticação. Por esta razão, deve tratar o URL como uma senha. Por razões de segurança, só pode ver o URL no portal Azure ao criar o webhook. Note o URL num local seguro para utilização futura. |
| Data de validade | Data de validade do webhook, após o qual já não pode ser utilizado. Pode modificar a data de validade após a criação do webhook, desde que o webhook não tenha expirado. |
| Ativado | Regulação indicando se o webhook está ativado por padrão quando é criado. Se você definir esta propriedade para Deficientes, nenhum cliente pode usar o webhook. Você pode definir esta propriedade quando você criar o webhook ou qualquer outro momento após a sua criação. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Parâmetros usados quando o webhook inicia um livro de corridas

Um webhook pode definir valores para parâmetros de livro de execução que são usados quando o livro de execução começa. O webhook deve incluir valores para quaisquer parâmetros de livro obrigatórios e pode incluir valores para parâmetros opcionais. Um valor de parâmetro configurado para um webhook pode ser modificado mesmo após a criação do webhook. Vários webhooks ligados a um único livro de execução podem cada um usar diferentes valores de parâmetros de caderneta. Quando um cliente inicia um livro de execução usando um webhook, não pode sobrepor-se aos valores do parâmetro definidos no webhook.

Para receber dados do cliente, o livro de execução suporta um único parâmetro chamado *WebhookData*. Este parâmetro define um objeto que contém dados que o cliente inclui num pedido post.

![Propriedades WebhookData](media/automation-webhooks/webhook-data-properties.png)

O parâmetro *WebhookData* tem as seguintes propriedades:

| Propriedade | Descrição |
|:--- |:--- |
| WebhookName | Nome do gancho web. |
| Cabeçalho de Pedido | Hashtable contendo os cabeçalhos do pedido de correio que chega. |
| requestBody | Corpo do pedido de correio que chega. Este corpo retém qualquer formatação de dados, tais como cordas, JSON, XML ou codificadas por formulários. O livro de execução deve ser escrito para trabalhar com o formato de dados que se espera. |

Não existe nenhuma configuração do webhook necessária para suportar o parâmetro *WebhookData,* e o livro de execução não é necessário para aceitá-lo. Se o livro de execução não definir o parâmetro, quaisquer detalhes do pedido enviado pelo cliente são ignorados.

> [!NOTE]
> Ao ligar para um webhook, o cliente deve sempre armazenar quaisquer valores de parâmetro sintetizador caso a chamada falhe. Se houver uma falha de rede ou problema de ligação, a aplicação não pode recuperar chamadas falhadas do webhook.

Se especificar um valor para *webhookData* na criação do webhook, é ultrapassado quando o webhook inicia o livro de execução com os dados do pedido do cliente POST. Isto acontece mesmo que a aplicação não inclua quaisquer dados no organismo de pedido. 

Se iniciar um livro de execução que define o *WebhookData* utilizando um mecanismo diferente de um webhook, pode fornecer um valor para *o WebhookData* que o livro de execução reconhece. Este valor deve ser um objeto com as mesmas [propriedades](#webhook-properties) que o parâmetro *WebhookData* para que o livro de execução possa funcionar com ele assim que funcione com objetos *WebhookData* reais passados por um webhook.

Por exemplo, se estiver a iniciar o seguinte livro de execução do portal Azure e pretender passar alguns dados de webhook de amostra para testes, tem de passar os dados em JSON na interface do utilizador.

![Parâmetro WebhookData da UI](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Para o próximo exemplo do livro de executantes, vamos definir as seguintes propriedades *WebhookData:*

* **WebhookName**: MyWebhook
* **RequestBody**: `*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

Agora passamos o seguinte objeto JSON na UI para o parâmetro *WebhookData.* Este exemplo, com retornos de carruagem e caracteres de newline, corresponde ao formato que é passado a partir de um webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Iniciar parâmetro WebhookData a partir de UI](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> A Azure Automation regista os valores de todos os parâmetros de entrada com o trabalho do livro de execução. Assim, qualquer entrada fornecida pelo cliente no pedido webhook é registada e disponível para qualquer pessoa com acesso ao trabalho de automação. Por esta razão, deve ser cauteloso em incluir informações sensíveis em chamadas webhook.

## <a name="webhook-security"></a>Segurança webhook

A segurança de um webhook depende da privacidade do seu URL, que contém um símbolo de segurança que permite que o webhook seja invocado. A Azure Automation não realiza qualquer autenticação num pedido desde que seja feita no URL correto. Por esta razão, os seus clientes não devem utilizar webhooks para livros de execução que realizem operações altamente sensíveis sem utilizar em meios alternativos de validação do pedido.

Você pode incluir lógica dentro de um livro de execução para determinar se é chamado por um webhook. Peça ao livro de execução que verifique a propriedade **WebhookName** do parâmetro *WebhookData.* O livro de execução pode realizar uma validação adicional procurando informações específicas nas propriedades **RequestHeader** e **RequestBody.**

Outra estratégia é fazer com que o livro de execução execute alguma validação de uma condição externa quando recebe um pedido de webhook. Por exemplo, considere um livro de corridas que é chamado pelo GitHub sempre que houver um novo compromisso com um repositório GitHub. O livro de execução pode ligar-se ao GitHub para validar que um novo compromisso ocorreu antes de continuar.

## <a name="creating-a-webhook"></a>Criar um webhook

Utilize o seguinte procedimento para criar um novo webhook ligado a um livro de execução no portal Azure.

1. A partir da página Runbooks no portal Azure, clique no livro de execução que o webhook começa a visualizar os detalhes do livro de execução. Certifique-se de que o campo **de estado** do livro de execução está definido para **publicado**.
2. Clique no **Webhook** na parte superior da página para abrir a página Adicionar Webhook.
3. Clique em **Criar um novo webhook** para abrir a página Create Webhook.
4. Preencha os campos **de data** de nome e **expiração** para o webhook e especifique se deve ser ativado. Consulte [as propriedades do Webhook](#webhook-properties) para obter mais informações sobre estas propriedades.
5. Clique no ícone da cópia e prima Ctrl+C para copiar o URL do webhook. Então grave-o num lugar seguro. 

    > [!NOTE]
    > Uma vez criado o webhook, não pode recuperar o URL novamente.

   ![Webhook URL](media/automation-webhooks/copy-webhook-url.png)

1. Clique em **Parâmetros** para fornecer valores para os parâmetros do livro de execução. Se o livro de execução tiver parâmetros obrigatórios, não pode criar o webhook a menos que forneça valores.
1. Clique em **Criar** para criar o webhook.

## <a name="using-a-webhook"></a>Usando um webhook

Para utilizar um webhook depois de ter sido criado, o seu cliente deve emitir um pedido HTTP POST com o URL para o webhook. A sintaxe é:

```http
http://<Webhook Server>/token?=<Token Value>
```

O cliente recebe um dos seguintes códigos de devolução do pedido do POST.

| Código | Texto | Descrição |
|:--- |:--- |:--- |
| 202 |Aceite |O pedido foi aceite, e o livro de corridas foi feito com sucesso na fila. |
| 400 |Pedido Incorreto |O pedido não foi aceite por uma das seguintes razões: <ul> <li>O gancho expirou.</li> <li>O gancho de teia está desativado.</li> <li>O símbolo no URL é inválido.</li>  </ul> |
| 404 |Não foi encontrado |O pedido não foi aceite por uma das seguintes razões: <ul> <li>O gancho não foi encontrado.</li> <li>O livro não foi encontrado.</li> <li>A conta não foi encontrada.</li>  </ul> |
| 500 |Erro interno do servidor |O URL era válido, mas ocorreu um erro. Por favor, reenvie o pedido. |

Assumindo que o pedido é bem sucedido, a resposta do webhook contém o ID de trabalho no formato JSON, como mostrado abaixo. Contém um único ID de trabalho, mas o formato JSON permite potenciais melhorias futuras.

```json
{"JobIds":["<JobId>"]}
```

O cliente não pode determinar quando o trabalho do livro de reprodução termina ou o seu estado de conclusão a partir do webhook. Pode descobrir esta informação utilizando o ID de trabalho com outro mecanismo, como [o Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) ou a [API de Automação Azure](/rest/api/automation/job).

## <a name="renew-webhook"></a>Renovação de um webhook

Quando um webhook é criado, tem um prazo de validade de dez anos, após o qual expira automaticamente. Uma vez expirado um webhook, não pode reativar. Só pode removê-lo e recriá-lo. 

Pode estender um webhook que não tenha atingido o seu tempo de validade. Para estender um webhook:

1. Navegue para o livro de execução que contém o webhook. 
2. Selecione **Webhooks** em **Recursos**. 
3. Clique no webhook que pretende estender. 
4. Na página Webhook, escolha uma nova data e hora de validade e clique em **Guardar**.

## <a name="sample-runbook"></a>Livro de amostras

O livro de execução da amostra seguinte aceita os dados do webhook e inicia as máquinas virtuais especificadas no organismo de pedido. Para testar este livro de execução, na sua conta De automação em **'Runbooks',** clique em Criar um livro de **execução**. Se não sabe criar um livro de corridas, consulte Criar um livro de [corridas.](automation-quickstart-create-runbook.md)

> [!NOTE]
> Para livros de execução powerShell não gráficos, **Add-AzAccount** e **Add-AzureRMAccount** são pseudónimos de [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Pode utilizar estes cmdlets ou pode [atualizar os seus módulos](automation-update-azure-modules.md) na sua conta Automation para as versões mais recentes. Pode precisar de atualizar os seus módulos mesmo que tenha acabado de criar uma nova conta Automation.

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

    # Retrieve VMs from Webhook request body
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
            Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="testing-the-sample"></a>Testar a amostra

O exemplo que se segue utiliza o Windows PowerShell para iniciar um livro de execução com um webhook. Qualquer idioma que possa fazer um pedido HTTP pode usar um webhook. O Windows PowerShell é utilizado aqui como exemplo.

O livro de recortes está à espera de uma lista de máquinas virtuais formatadas em JSON no corpo do pedido. O livro de execução também valida que os cabeçalhos contêm uma mensagem definida para validar que o chamador webhook é válido.

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

O exemplo seguinte mostra o corpo do pedido que está disponível para o livro de execução na propriedade **RequestBody** do *WebhookData*. Este valor é formatado em JSON para ser compatível com o formato incluído no corpo do pedido.

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

A imagem seguinte mostra o pedido enviado do Windows PowerShell e a resposta resultante. O ID de trabalho é extraído da resposta e convertido em uma corda.

![Botão Webhooks](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Passos seguintes

* Para aprender a usar a Automação Azure para tomar medidas nos alertas Do Azure, consulte Utilize um alerta para desencadear um livro de [execução da Automação Azure](automation-create-alert-triggered-runbook.md).
