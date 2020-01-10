---
title: Parâmetros de entrada no runbook
description: Os parâmetros de entrada do runbook aumentam a flexibilidade dos runbooks, permitindo que você passe dados para um runbook quando ele é iniciado. Este artigo descreve diferentes cenários em que os parâmetros de entrada são usados em runbooks.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: b16219c34ea30b4229195c8f019dfa8e1f147d8b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75417593"
---
# <a name="runbook-input-parameters"></a>Parâmetros de entrada no runbook

Os parâmetros de entrada do runbook aumentam a flexibilidade dos runbooks, permitindo que você passe dados para ele quando ele é iniciado. Os parâmetros permitem que as ações de runbook sejam direcionadas para cenários e ambientes específicos. Neste artigo, você percorre cenários diferentes nos quais os parâmetros de entrada são usados em runbooks.

## <a name="configure-input-parameters"></a>Configurar parâmetros de entrada

Os parâmetros de entrada podem ser configurados no PowerShell, no fluxo de trabalho do PowerShell, no Python e nos runbooks gráficos. Um runbook pode ter vários parâmetros com tipos de dados diferentes ou nenhum parâmetro. Os parâmetros de entrada podem ser obrigatórios ou opcionais, e você pode ter um valor padrão para parâmetros opcionais. Você atribui valores aos parâmetros de entrada para um runbook ao iniciá-lo por meio de um dos métodos disponíveis. Esses métodos incluem iniciar um runbook do portal do Azure, um serviço Web ou o PowerShell. Você também pode iniciar um como um runbook filho que é chamado embutido em outro runbook.

## <a name="configure-input-parameters-in-powershell-runbooks"></a>Configurar parâmetros de entrada em runbooks do PowerShell

Os runbooks de fluxo de trabalho do PowerShell e do PowerShell na automação do Azure dão suporte a parâmetros de entrada que são definidos por meio dos seguintes atributos:  

| **Propriedade** | **Descrição** |
|:--- |:--- |
| `Type` |Necessário. O tipo de dados esperado para o valor do parâmetro. Qualquer tipo .NET é válido. |
| `Name` |Necessário. O nome do parâmetro. Isso deve ser exclusivo dentro do runbook e pode conter apenas letras, números ou caracteres de sublinhado. Ele deve começar com uma letra. |
| `Mandatory` |Opcional. Especifica se um valor deve ser fornecido para o parâmetro. Se você definir isso como **\$true**, um valor deverá ser fornecido quando o runbook for iniciado. Se você definir isso como **\$false**, um valor será opcional. |
| `Default value` |Opcional. Especifica um valor que será usado para o parâmetro se um valor não for passado quando o runbook for iniciado. Um valor padrão pode ser definido para qualquer parâmetro e tornará automaticamente o parâmetro opcional, independentemente da configuração obrigatória. |

O Windows PowerShell dá suporte a mais atributos de parâmetros de entrada do que aqueles listados aqui, como validação, aliases e conjuntos de parâmetros. No entanto, a automação do Azure atualmente dá suporte apenas aos parâmetros de entrada anteriores.

Uma definição de parâmetro em runbooks de fluxo de trabalho do PowerShell tem a seguinte forma geral, em que vários parâmetros são separados por vírgulas.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

> [!NOTE]
> Quando você estiver definindo parâmetros, se não especificar o atributo **obrigatório** , por padrão, o parâmetro será considerado opcional. Além disso, se você definir um valor padrão para um parâmetro em runbooks de fluxo de trabalho do PowerShell, ele será tratado pelo PowerShell como um parâmetro opcional, independentemente do valor de atributo **obrigatório** .

Por exemplo, vamos configurar os parâmetros de entrada para um runbook de fluxo de trabalho do PowerShell que gera detalhes sobre máquinas virtuais, uma única VM ou todas as VMs dentro de um grupo de recursos. Esse runbook tem dois parâmetros, conforme mostrado na seguinte captura de tela: o nome da máquina virtual e o nome do grupo de recursos.

![Fluxo de trabalho do PowerShell de automação](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

Nessa definição de parâmetro, os parâmetros **\$VMName** e **\$resourceGroupName** são parâmetros simples do tipo cadeia de caracteres. No entanto, os runbooks de fluxo de trabalho do PowerShell e do PowerShell dão suporte a todos os tipos simples e complexos, como **Object** ou **PSCredential** para parâmetros de entrada.

Se o runbook tiver um parâmetro de entrada de tipo de objeto, use uma tabela de hash do PowerShell com pares (nome, valor) para passar um valor. Por exemplo, se você tiver o seguinte parâmetro em um runbook:

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Em seguida, você pode passar o seguinte valor para o parâmetro:

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Quando você não passa nenhum valor para um parâmetro de tipo de `[String]` opcional que tem um _valor padrão_ de `\$null`, o valor do parâmetro será uma _cadeia de caracteres vazia_, **não** `\$null`.

## <a name="configure-input-parameters-in-graphical-runbooks"></a>Configurar parâmetros de entrada em runbooks gráficos

Para [configurar um runbook gráfico](automation-first-runbook-graphical.md) com parâmetros de entrada, vamos criar um runbook gráfico que produza detalhes sobre máquinas virtuais, uma única VM ou todas as VMs em um grupo de recursos. A configuração de um runbook consiste em duas atividades principais, conforme descrito abaixo.

[**Autentique Runbooks com a conta Executar como do Azure**](automation-sec-configure-azure-runas-account.md) para autenticar com o Azure.

[**Get-AzureRmVm**](/powershell/module/azurerm.compute/get-azurermvm) para obter as propriedades de uma máquina virtual.

Você pode usar a atividade [**Write-Output**](/powershell/module/microsoft.powershell.utility/write-output) para gerar os nomes das máquinas virtuais. A atividade **Get-AzureRmVm** aceita dois parâmetros, o **nome da máquina virtual** e o **nome do grupo de recursos**. Como esses parâmetros podem exigir valores diferentes cada vez que você inicia o runbook, você pode adicionar parâmetros de entrada ao seu runbook. Aqui estão as etapas para adicionar parâmetros de entrada:

1. Selecione o runbook gráfico na folha **Runbooks** e clique em [**Editar**](automation-graphical-authoring-intro.md) .
2. No editor de runbook, clique em **entrada e saída** para abrir a folha **entrada e saída** .

   ![Runbook gráfico de automação](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. A folha **entrada e saída** exibe uma lista de parâmetros de entrada que são definidos para o runbook. Nessa folha, você pode adicionar um novo parâmetro de entrada ou editar a configuração de um parâmetro de entrada existente. Para adicionar um novo parâmetro para o runbook, clique em **Adicionar entrada** para abrir a folha **parâmetro de entrada do runbook** . Lá, você pode configurar os seguintes parâmetros:

   | **Propriedade** | **Descrição** |
   |:--- |:--- |
   | `Name` |Necessário. O nome do parâmetro. Isso deve ser exclusivo dentro do runbook e pode conter apenas letras, números ou caracteres de sublinhado. Ele deve começar com uma letra. |
   | `Description` |Opcional. Descrição sobre a finalidade do parâmetro de entrada. |
   | `Type` |Opcional. O tipo de dados que é esperado para o valor do parâmetro. Os tipos de parâmetro com suporte são **String**, **Int32**, **Int64**, **decimal**, **Boolean**, **DateTime**e **Object**. Se um tipo de dados não for selecionado, o padrão será **String**. |
   | `Mandatory` |Opcional. Especifica se um valor deve ser fornecido para o parâmetro. Se você escolher **Sim**, um valor deverá ser fornecido quando o runbook for iniciado. Se você escolher **não**, um valor não será necessário quando o runbook for iniciado, e um valor padrão poderá ser definido. |
   | `Default Value` |Opcional. Especifica um valor que será usado para o parâmetro se um valor não for passado quando o runbook for iniciado. Um valor padrão pode ser definido para um parâmetro que não é obrigatório. Para definir um valor padrão, escolha **personalizado**. Esse valor é usado, a menos que outro valor seja fornecido quando o runbook for iniciado. Escolha **nenhum** se você não quiser fornecer nenhum valor padrão. |

    ![Adicionar nova entrada](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Crie dois parâmetros com as seguintes propriedades que são usadas pela atividade **Get-AzureRmVm** :

   * **Parâmetro1**
     * Nome-VMName
     * Cadeia de caracteres de tipo
     * Obrigatório-não
   * **Parameter2**
     * Nome-resourceGroupName
     * Cadeia de caracteres de tipo
     * Obrigatório-não
     * Valor padrão-personalizado
     * Valor padrão personalizado-\<nome do grupo de recursos que contém as máquinas virtuais\>

5. Depois de adicionar os parâmetros, clique em **OK**. Agora você pode exibi-los na **página entrada e saída**. Clique em **OK** novamente e, em seguida, clique em **salvar** e **publicar** seu runbook.

## <a name="configure-input-parameters-in-python-runbooks"></a>Configurar parâmetros de entrada em runbooks do Python

Ao contrário do PowerShell, do fluxo de trabalho do PowerShell e dos runbooks gráficos, os runbooks do Python não assumem parâmetros nomeados.
Todos os parâmetros de entrada são analisados como uma matriz de valores de argumento.
Você acessa a matriz importando o módulo `sys` em seu script Python e, em seguida, usando a matriz `sys.argv`.
É importante observar que o primeiro elemento da matriz, `sys.argv[0]`, é o nome do script, portanto, o primeiro parâmetro de entrada real é `sys.argv[1]`.

Para obter um exemplo de como usar parâmetros de entrada em um runbook do Python, consulte [meu primeiro runbook do Python na automação do Azure](automation-first-runbook-textual-python2.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Atribuir valores a parâmetros de entrada em runbooks

Você pode passar valores para parâmetros de entrada em runbooks nos seguintes cenários:

### <a name="start-a-runbook-and-assign-parameters"></a>Iniciar um runbook e atribuir parâmetros

Um runbook pode ser iniciado de várias maneiras: por meio do portal do Azure, com um webhook, com cmdlets do PowerShell, com a API REST ou com o SDK. Abaixo, discutimos métodos diferentes para iniciar um runbook e atribuir parâmetros.

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Iniciar um runbook publicado usando o portal do Azure e atribuir parâmetros

Quando você [inicia o runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal), a folha **Iniciar runbook** é aberta e você pode inserir valores para os parâmetros que você criou.

![Começar a usar o portal](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

No rótulo abaixo da caixa de entrada, você pode ver os atributos que foram definidos para o parâmetro. Os atributos incluem obrigatório ou opcional, tipo e valor padrão. No balão de ajuda ao lado do nome do parâmetro, você pode ver todas as informações importantes de que precisa para tomar decisões sobre valores de entrada de parâmetro. Essas informações incluem se um parâmetro é obrigatório ou opcional. Ele também inclui o tipo e o valor padrão (se houver) e outras observações úteis.

> [!NOTE]
> Parâmetros de tipo String dão suporte a valores de cadeia de caracteres **vazios** .  A inserção de **[emptystring]** na caixa parâmetro de entrada passa uma cadeia de caracteres vazia para o parâmetro. Além disso, os parâmetros de tipo String não dão suporte a valores **nulos** que estão sendo passados. Se você não passar qualquer valor para o parâmetro de cadeia de caracteres, o PowerShell o interpretará como nulo.

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>Iniciar um runbook publicado usando cmdlets do PowerShell e atribuir parâmetros

* **Azure Resource Manager cmdlets:** Você pode iniciar um runbook de automação que foi criado em um grupo de recursos usando [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook).
  
  **Exemplo:**

  ```powershell
  $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
  ```

* **Cmdlets do modelo de implantação clássico do Azure:** Você pode iniciar um runbook de automação que foi criado em um grupo de recursos padrão usando [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook).
  
  **Exemplo:**

  ```powershell
  $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
  Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
  ```

> [!NOTE]
> Quando você inicia um runbook usando cmdlets do PowerShell, um parâmetro padrão, **MicrosoftApplicationManagementStartedBy** , é criado com o valor **PowerShell**. Você pode exibir esse parâmetro na página de **detalhes do trabalho** .  

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>Iniciar um runbook usando um SDK e atribuir parâmetros

* **Método de Azure Resource Manager:** Você pode iniciar um runbook usando o SDK de uma linguagem de programação. Abaixo está um C# trecho de código para iniciar um runbook em sua conta de automação. Você pode exibir todo o código em nosso [repositório GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  

  ```csharp
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
  ```

* **Método de modelo de implantação clássico do Azure:** Você pode iniciar um runbook usando o SDK de uma linguagem de programação. Abaixo está um C# trecho de código para iniciar um runbook em sua conta de automação. Você pode exibir todo o código em nosso [repositório GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).

  ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
  ```

  Para iniciar esse método, crie um dicionário para armazenar os parâmetros de runbook, **VMName** e **resourceGroupName**, e seus valores. Em seguida, inicie o runbook. Abaixo está o C# trecho de código para chamar o método definido acima.

  ```csharp
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook("Get-AzureVMGraphical", RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>Iniciar um runbook usando a API REST e atribuir parâmetros

Um trabalho de runbook pode ser criado e iniciado com a API REST da automação do Azure usando o método **Put** com o seguinte URI de solicitação: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`


No URI de solicitação, substitua os seguintes parâmetros:

* **SubscriptionId:** Sua ID de assinatura do Azure.  
* **resourceGroupName:** O nome do grupo de recursos para a conta de automação.
* **automationAccountName:** O nome da sua conta de automação que é hospedada no serviço de nuvem especificado.  
* **JobName:** O GUID do trabalho. Os GUIDs no PowerShell podem ser criados usando o **[GUID]:: NewGuid (). Comando ToString ()** .

Para passar parâmetros para o trabalho de runbook, use o corpo da solicitação. Ele usa as duas propriedades a seguir fornecidas no formato JSON:

* **Nome do runbook:** Necessário. O nome do runbook para iniciar o trabalho.  
* **Parâmetros de runbook:** Adicional. Um dicionário da lista de parâmetros no formato (nome, valor), em que o nome deve ser do tipo de cadeia de caracteres e o valor pode ser qualquer valor JSON válido.

Se você quiser iniciar o runbook **Get-AzureVMTextual** que foi criado anteriormente com **VMName** e **resourceGroupName** como parâmetros, use o formato JSON a seguir para o corpo da solicitação.

   ```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
   ```

Um código de status HTTP 201 será retornado se o trabalho for criado com êxito. Para obter mais informações sobre cabeçalhos de resposta e o corpo da resposta, consulte o artigo sobre como [criar um trabalho de runbook usando a API REST.](/rest/api/automation/job/create)

### <a name="test-a-runbook-and-assign-parameters"></a>Testar um runbook e atribuir parâmetros

Quando você [testa a versão de rascunho do seu runbook](automation-testing-runbook.md) usando a opção de teste, a página de **teste** é aberta e você pode configurar valores para os parâmetros que você criou.

![Testar e atribuir parâmetros](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Vincular um agendamento a um runbook e atribuir parâmetros

Você pode [vincular uma agenda](automation-schedules.md) ao seu runbook para que o runbook comece em um horário específico. Você atribui parâmetros de entrada ao criar o agendamento e o runbook usa esses valores quando ele é iniciado pelo agendamento. Você não pode salvar o agendamento até que todos os valores de parâmetro obrigatórios sejam fornecidos.

![Agendar e atribuir parâmetros](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Criar um webhook para um runbook e atribuir parâmetros

Você pode criar um [webhook](automation-webhooks.md) para seu runbook e configurar os parâmetros de entrada do runbook. Você não pode salvar o webhook até que todos os valores de parâmetro obrigatório sejam fornecidos.

![Criar webhook e atribuir parâmetros](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Quando você executa um runbook usando um webhook, o parâmetro de entrada predefinido **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** é enviado, juntamente com os parâmetros de entrada que você definiu. Você pode clicar para expandir o parâmetro **WebhookData** para obter mais detalhes.

![Parâmetro WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>Passar um objeto JSON para um runbook

Pode ser útil armazenar dados que você deseja passar para um runbook em um arquivo JSON.
Por exemplo, você pode criar um arquivo JSON que contém todos os parâmetros que você deseja passar para um runbook. Para fazer isso, você precisa converter o JSON em uma cadeia de caracteres e, em seguida, converter a cadeia de caracteres em um objeto do PowerShell antes que ele seja passado para o runbook.

Neste exemplo, você tem um script do PowerShell que chama [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) para iniciar um runbook do PowerShell, passando o conteúdo do JSON para o runbook.
O runbook do PowerShell inicia uma VM do Azure, obtendo os parâmetros para a VM do JSON que foi passado.

### <a name="create-the-json-file"></a>Criar o arquivo JSON

Digite o teste a seguir em um arquivo de texto e salve-o como `test.json` em algum lugar no computador local.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Criar o runbook

Crie um novo runbook do PowerShell chamado "Test-JSON" na automação do Azure.
Para saber como criar um novo runbook do PowerShell, consulte [meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md).

Para aceitar os dados JSON, o runbook deve usar um objeto como um parâmetro de entrada.

O runbook pode usar as propriedades definidas no JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Salve e publique esse runbook em sua conta de automação.

### <a name="call-the-runbook-from-powershell"></a>Chamar o runbook do PowerShell

Agora você pode chamar o runbook do computador local usando Azure PowerShell.
Execute os seguintes comandos do PowerShell:

1. Entrar no Azure:

   ```powershell
   Connect-AzureRmAccount
   ```

   Você será solicitado a inserir suas credenciais do Azure.

   > [!IMPORTANT]
   > **Add-AzureRmAccount** agora é um alias para **Connect-AzureRmAccount**. Ao pesquisar os itens de biblioteca, se você não vir **Connect-AzureRMAccount**, poderá usar **Add-AzureRMAccount**ou poderá atualizar seus módulos na sua conta de automação.

1. Obtenha o conteúdo do arquivo JSON e converta-o em uma cadeia de caracteres:

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

   `JsonPath` é o caminho em que você salvou o arquivo JSON.

1. Converta o conteúdo da cadeia de caracteres de `$json` em um objeto do PowerShell:

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Crie uma tabela de hash para os parâmetros para `Start-AzureRmAutomationRunbook`:

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Observe que você está definindo o valor de `Parameters` para o objeto do PowerShell que contém os valores do arquivo JSON.
1. Iniciar o runbook

   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Passos seguintes

* Para obter detalhes sobre diferentes maneiras de iniciar um runbook, consulte [iniciando um runbook](automation-starting-a-runbook.md).
* Para editar um runbook textual, consulte [editando runbooks textuais](automation-edit-textual-runbook.md).
* Para editar um runbook gráfico, consulte [criação gráfica na automação do Azure](automation-graphical-authoring-intro.md).
