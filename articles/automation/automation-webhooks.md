---
title: Inicie um runbook Azure Automation a partir de um webhook
description: Este artigo diz como usar um webhook para iniciar um livro de formulários na Azure Automation a partir de uma chamada HTTP.
services: automation
ms.subservice: process-automation
ms.date: 03/18/2021
ms.topic: conceptual
ms.openlocfilehash: c46a8753c87e981d9e3d6ecdd698bbbe6cba9894
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775787"
---
# <a name="start-a-runbook-from-a-webhook"></a>Iniciar um runbook a partir de um webhook

Um webhook permite que um serviço externo inicie um determinado runbook na Azure Automation através de um único pedido HTTP. Os serviços externos incluem serviços Azure DevOps, GitHub, registos Azure Monitor e aplicações personalizadas. Tal serviço pode usar um webhook para iniciar um runbook sem implementar a API total da Azure Automation. Pode comparar webhooks com outros métodos de iniciar um livro de formulários no [Início de um livro de formulários na Azure Automation](./start-runbooks.md).

> [!NOTE]
> Não é suportado o suporte de um webhook para iniciar um livro de aplicações Python.

![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Para compreender os requisitos do cliente para TLS 1.2 com webhooks, consulte [a aplicação TLS 1.2 para a Azure Automation](automation-managing-data.md#tls-12-enforcement-for-azure-automation).

## <a name="webhook-properties"></a>Propriedades webhook

A tabela a seguir descreve as propriedades que deve configurar para um webhook.

| Propriedade | Descrição |
|:--- |:--- |
| Nome |O nome do webhook. Pode dar o nome que quiser, já que não está exposto ao cliente. Só é utilizado para identificar o livro de recortes na Azure Automation. Como uma boa prática, deve dar ao webhook um nome relacionado com o cliente que o utiliza. |
| URL |URL do webhook. Este é o endereço único que um cliente chama com um HTTP POST para iniciar o livro de aplicação ligado ao webhook. É gerado automaticamente quando se cria o webhook. Não é possível especificar uma URL personalizada. <br> <br> O URL contém um símbolo de segurança que permite a um sistema de terceiros invocar o livro de bordo sem mais autenticação. Por esta razão, deve tratar o URL como uma palavra-passe. Por razões de segurança, só pode ver o URL no portal Azure ao criar o webhook. Note o URL num local seguro para utilização futura. |
| Data de validade | Data de validade do webhook, após o qual já não pode ser utilizado. Pode modificar a data de validade após a criação do webhook, desde que o webhook não tenha expirado. |
| Ativado | Definição indicando se o webhook é ativado por padrão quando é criado. Se definir esta propriedade para Disabled, nenhum cliente pode usar o webhook. Você pode definir esta propriedade quando você criar o webhook ou qualquer outro tempo após a sua criação. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Parâmetros utilizados quando o webhook inicia um livro de bordo

Um webhook pode definir valores para parâmetros de runbook que são usados quando o livro de execução começa. O webhook deve incluir valores para quaisquer parâmetros de runbook obrigatórios e pode incluir valores para parâmetros opcionais. Um valor de parâmetro configurado para um webhook pode ser modificado mesmo após a criação do webhook. Vários webhooks ligados a um único runbook podem cada um usar diferentes valores de parâmetros de runbook. Quando um cliente inicia um livro de aplicação usando um webhook, não pode sobrepor os valores de parâmetro definidos no webhook.

Para receber dados do cliente, o livro de recortes suporta um único parâmetro chamado `WebhookData` . Este parâmetro define um objeto contendo dados que o cliente inclui num pedido DEM.

![Propriedades webhookData](media/automation-webhooks/webhook-data-properties.png)

O `WebhookData` parâmetro tem as seguintes propriedades:

| Propriedade | Descrição |
|:--- |:--- |
| `WebhookName` | O nome do webhook. |
| `RequestHeader` | Hashtable contendo os cabeçalhos do pedido de POST que chega. |
| `RequestBody` | Corpo do pedido de entrada do CORREIO. Este corpo retém qualquer formatação de dados, tais como string, JSON, XML ou codificado por formulários. O livro deve ser escrito para funcionar com o formato de dados que se espera. |

Não há nenhuma configuração do webhook necessária para suportar o `WebhookData` parâmetro, e o livro de execução não é obrigado a aceitá-lo. Se o livro não definir o parâmetro, quaisquer detalhes do pedido enviado do cliente são ignorados.

> [!NOTE]
> Ao ligar para um webhook, o cliente deve sempre armazenar quaisquer valores de parâmetros caso a chamada falhe. Se houver uma falha de rede ou problema de ligação, a aplicação não pode recuperar chamadas falhadas do webhook.

Se especificar um valor para `WebhookData` a criação do webhook, é ultrapassado quando o webhook inicia o livro com os dados do pedido do cliente POST. Isto acontece mesmo que a aplicação não inclua quaisquer dados no organismo de pedido. 

Se iniciar um runbook que define `WebhookData` usando um mecanismo diferente de um webhook, pode fornecer um valor para `WebhookData` o que o livro de bordo reconhece. Este valor deve ser um objeto com as [mesmas propriedades](#webhook-properties) que o `WebhookData` parâmetro para que o livro de execuções possa trabalhar com ele assim como funciona com objetos reais `WebhookData` passados por um webhook.

Por exemplo, se estiver a iniciar o seguinte livro de aplicação a partir do portal Azure e quiser passar alguns dados do webhook de amostra para testes, tem de passar os dados em JSON na interface do utilizador.

![Parâmetro WebhookData da UI](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Para o próximo exemplo do runbook, vamos definir as seguintes propriedades `WebhookData` para:

* **WebhookName**: MyWebhook
* **PedidoCorpo:**`*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

Agora passamos o seguinte objeto JSON na UI para o `WebhookData` parâmetro. Este exemplo, com devoluções de carruagens e caracteres newline, corresponde ao formato que é passado a partir de um webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Iniciar o parâmetro WebhookData a partir da UI](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> A Azure Automation regista os valores de todos os parâmetros de entrada com a função de runbook. Assim, qualquer entrada fornecida pelo cliente no pedido webhook é registada e disponível para qualquer pessoa com acesso ao trabalho de automação. Por esta razão, deve ser cauteloso quanto à inclusão de informações sensíveis em chamadas webhook.

## <a name="webhook-security"></a>Segurança Webhook

A segurança de um webhook depende da privacidade do seu URL, que contém um símbolo de segurança que permite que o webhook seja invocado. A Azure Automation não efetua qualquer autenticação num pedido desde que seja feita ao URL correto. Por esta razão, os seus clientes não devem utilizar webhooks para runbooks que realizem operações altamente sensíveis sem utilizar um meio alternativo de validação do pedido.

Considere as seguintes estratégias:

* Pode incluir lógica dentro de um livro de aplicação para determinar se é chamado por um webhook. Que o livro verifique a `WebhookName` propriedade do `WebhookData` parâmetro. O runbook pode realizar uma validação adicional procurando informações específicas nas `RequestHeader` propriedades e `RequestBody` propriedades.

* Peça ao livro de aplicação para realizar alguma validação de uma condição externa quando receber um pedido webhook. Por exemplo, considere um livro de corridas que é chamado pelo GitHub sempre que há um novo compromisso com um repositório GitHub. O livro pode ligar-se ao GitHub para validar que ocorreu um novo compromisso antes de continuar.

* A Azure Automation suporta tags de serviço de rede virtual Azure, especificamente [GuestAndHybridManagement](../virtual-network/service-tags-overview.md). Pode utilizar tags de serviço para definir controlos de acesso à rede em [grupos de segurança](../virtual-network/network-security-groups-overview.md#security-rules) de rede ou [Azure Firewall](../firewall/service-tags.md) e desencadear webhooks a partir da sua rede virtual. As etiquetas de serviço podem ser utilizadas no lugar de endereços IP específicos quando cria regras de segurança. Ao especificar o nome da etiqueta de serviço **GuestAndHybridManagement**  no campo de origem ou destino apropriado de uma regra, pode permitir ou negar o tráfego para o serviço Dem automação. Esta etiqueta de serviço não suporta permitir um maior controlo granular limitando as gamas IP a uma região específica.

## <a name="create-a-webhook"></a>Criar um webhook

Utilize o seguinte procedimento para criar um novo webhook ligado a um livro de bordo no portal Azure.

1. Na página Runbooks no portal Azure, clique no runbook que o webhook começa a ver os detalhes do livro de aplicação. Certifique-se de que o campo **de estado do** livro de bordo está definido para **Publicado**.
2. Clique em **Webhook** no topo da página para abrir a página Add Webhook.
3. Clique **em Criar novo webhook** para abrir a página Criar Webhook.
4. Preencha os campos **nome** e **data de validade** para o webhook e especifique se deve ser ativado. Consulte [as propriedades da Webhook](#webhook-properties) para obter mais informações sobre estas propriedades.
5. Clique no ícone da cópia e prima Ctrl+C para copiar o URL do webhook. Em seguida, grave-o num lugar seguro. 

    > [!IMPORTANT]
    > Uma vez criado o webhook, não poderá recuperar o URL novamente. Certifique-se de copiar e gravar como acima.

   ![Webhook URL](media/automation-webhooks/copy-webhook-url.png)

1. Clique em **Parâmetros** para fornecer valores para os parâmetros do livro de bordo. Se o livro de bordo tiver parâmetros obrigatórios, não pode criar o webhook a menos que forneça valores.

2. Clique em **Criar** para criar o webhook.

## <a name="use-a-webhook"></a>Use um webhook

Para utilizar um webhook depois de ter sido criado, o seu cliente deve emitir um pedido HTTP `POST` com o URL para o webhook. A sintaxe é:

```http
http://<Webhook Server>/token?=<Token Value>
```

O cliente recebe um dos seguintes códigos de devolução do `POST` pedido.

| Código | Texto | Description |
|:--- |:--- |:--- |
| 202 |Aceite |O pedido foi aceite, e o livro foi feito com sucesso. |
| 400 |Pedido Incorreto |O pedido não foi aceite por uma das seguintes razões: <ul> <li>O webhook expirou.</li> <li>O webhook está desativado.</li> <li>O símbolo na URL é inválido.</li>  </ul> |
| 404 |Não Encontrado |O pedido não foi aceite por uma das seguintes razões: <ul> <li>O webhook não foi encontrado.</li> <li>O livro não foi encontrado.</li> <li>A conta não foi encontrada.</li>  </ul> |
| 500 |Erro interno do servidor |A URL era válida, mas ocorreu um erro. Por favor, reenvia o pedido. |

Assumindo que o pedido é bem sucedido, a resposta webhook contém o ID de trabalho no formato JSON, como mostrado abaixo. Contém um único ID de trabalho, mas o formato JSON permite potenciais melhorias futuras.

```json
{"JobIds":["<JobId>"]}
```

O cliente não pode determinar quando o trabalho do runbook termina ou o seu estado de conclusão a partir do webhook. Pode descobrir esta informação utilizando o ID do trabalho com outro mecanismo, como o [Windows PowerShell](/powershell/module/servicemanagement/azure.service/get-azureautomationjob) ou a API da [Automação Azure](/rest/api/automation/job).

### <a name="use-a-webhook-from-an-arm-template"></a>Use um webhook de um modelo ARM

Os webhooks de automação também podem ser invocados pelos [modelos Azure Resource Manager (ARM).](/azure/azure-resource-manager/templates/overview) O modelo ARM emite um `POST` pedido e recebe um código de devolução como qualquer outro cliente. Ver [Utilizar um webhook](#use-a-webhook).

   > [!NOTE]
   > Por razões de segurança, o URI só é devolvido na primeira vez que um modelo é implantado.

Este modelo de amostra cria um ambiente de teste e devolve o URI para o webhook que cria.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "automationAccountName": {
            "type": "String",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "webhookName": {
            "type": "String",
            "metadata": {
                "description": "Webhook Name"
            }
        },
        "runbookName": {
            "type": "String",
            "metadata": {
                "description": "Runbook Name for which webhook will be created"
            }
        },
        "WebhookExpiryTime": {
            "type": "String",
            "metadata": {
                "description": "Webhook Expiry time"
            }
        },
        "_artifactsLocation": {
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-automation/",
            "type": "String",
            "metadata": {
                "description": "URI to artifacts location"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2020-01-13-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": {
                    "name": "Free"
                }
            },
            "resources": [
                {
                    "type": "runbooks",
                    "apiVersion": "2018-06-30",
                    "name": "[parameters('runbookName')]",
                    "location": "[resourceGroup().location]",
                    "dependsOn": [
                        "[parameters('automationAccountName')]"
                    ],
                    "properties": {
                        "runbookType": "Python2",
                        "logProgress": "false",
                        "logVerbose": "false",
                        "description": "Sample Runbook",
                        "publishContentLink": {
                            "uri": "[uri(parameters('_artifactsLocation'), 'scripts/AzureAutomationTutorialPython2.py')]",
                            "version": "1.0.0.0"
                        }
                    }
                },
                {
                    "type": "webhooks",
                    "apiVersion": "2018-06-30",
                    "name": "[parameters('webhookName')]",
                    "dependsOn": [
                        "[parameters('automationAccountName')]",
                        "[parameters('runbookName')]"
                    ],
                    "properties": {
                        "isEnabled": true,
                        "expiryTime": "[parameters('WebhookExpiryTime')]",
                        "runbook": {
                            "name": "[parameters('runbookName')]"
                        }
                    }
                }
            ]
        }
    ],
    "outputs": {
        "webhookUri": {
            "type": "String",
            "value": "[reference(parameters('webhookName')).uri]"
        }
    }
}
```

## <a name="renew-a-webhook"></a>Renovar um webhook

Quando um webhook é criado, tem um período de validade de dez anos, após o qual expira automaticamente. Uma vez expirado um webhook, não se pode reativá-lo. Só pode removê-lo e depois recriá-lo. 

Pode estender um webhook que não atingiu o seu tempo de validade. Para estender um webhook:

1. Navegue para o runbook que contém o webhook. 
2. Selecione **Webhooks** em **Recursos.** 
3. Clique no webhook que deseja estender. 
4. Na página Webhook, escolha uma nova data e hora de validade e clique em **Guardar**.

## <a name="sample-runbook"></a>Livro de amostras

O seguinte livro de amostras aceita os dados do webhook e inicia as máquinas virtuais especificadas no corpo de pedido. Para testar este livro de execução, na sua conta de Automação em **Runbooks,** clique em **Criar um runbook**. Se não sabe como criar um livro de bordo, consulte [criar um livro de recortes](automation-quickstart-create-runbook.md).

> [!NOTE]
> Para livros de execução powershell não gráficos, `Add-AzAccount` e `Add-AzureRMAccount` são pseudónimos para [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Pode utilizar estes cmdlets ou [atualizar os seus módulos](automation-update-azure-modules.md) na sua conta Automation para as versões mais recentes. Poderá ter de atualizar os seus módulos mesmo que tenha acabado de criar uma nova conta Automation.

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

## <a name="test-the-sample"></a>Testar a amostra

O exemplo a seguir utiliza o Windows PowerShell para iniciar um livro com um webhook. Qualquer idioma que possa fazer um pedido HTTP pode usar um webhook. O Windows PowerShell é usado aqui como exemplo.

O runbook está à espera de uma lista de máquinas virtuais formatadas em JSON no corpo do pedido. O runbook também valida que os cabeçalhos contêm uma mensagem definida para validar que o chamador webhook é válido.

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

O exemplo a seguir mostra o corpo do pedido que está disponível para o livro de bordo na `RequestBody` propriedade de `WebhookData` . Este valor é formatado em JSON para ser compatível com o formato incluído no corpo do pedido.

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

A imagem que se segue mostra o pedido enviado do Windows PowerShell e a resposta resultante. A identificação do trabalho é extraída da resposta e convertida numa corda.

![Botão Webhooks](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Passos seguintes

* Para ativar um livro de bordo a partir de um alerta, consulte [Utilize um alerta para acionar um livro de execução da Azure Automation](automation-create-alert-triggered-runbook.md).