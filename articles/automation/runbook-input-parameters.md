---
title: Parâmetros de entrada do runbook
description: Os parâmetros de entrada do livro de execução aumentam a flexibilidade dos livros de execução, permitindo-lhe passar dados para um livro de execução quando é iniciado. Este artigo descreve diferentes cenários onde os parâmetros de entrada são usados em livros de execução.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3741adbab6bcbc68f266c331e3056013afc0105e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80656035"
---
# <a name="runbook-input-parameters"></a>Parâmetros de entrada do runbook

Os parâmetros de entrada do livro de execução aumentam a flexibilidade de um livro de execução, permitindo que os dados lhe sejam transmitidos quando é iniciado. Estes parâmetros permitem que as ações do livro de corridas sejam direcionadas para cenários e ambientes específicos. Este artigo descreve a configuração e utilização de parâmetros de entrada nos seus livros de execução.

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](automation-update-azure-modules.md).

## <a name="configuring-input-parameters"></a>Configurar parâmetros de entrada

Pode configurar parâmetros de entrada para powerShell, PowerShell Workflow, graphical e Python. Um livro de execução pode ter múltiplos parâmetros com diferentes tipos de dados, ou nenhum parâmetro. Os parâmetros de entrada podem ser obrigatórios ou opcionais, podendo utilizar valores predefinidos para parâmetros opcionais.

Atribui valores aos parâmetros de entrada para um livro de execução quando o iniciar. Pode iniciar um livro de execução a partir do portal Azure, um serviço web ou PowerShell. Você também pode começar um como um livro de corridas de crianças que é chamado inline em outro livro de corridas.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Configure parâmetros de entrada em livros de execução PowerShell

Os livros de execução powerShell e PowerShell Workflow em Azure Automation suportam parâmetros de entrada que são definidos através das seguintes propriedades. 

| **Propriedade** | **Descrição** |
|:--- |:--- |
| Tipo |Necessário. O tipo de dados esperado para o valor do parâmetro. Qualquer tipo .NET é válido. |
| Nome |Necessário. O nome do parâmetro. Este nome deve ser único dentro do livro de execução, deve começar com uma letra, e pode conter apenas letras, números ou caracteres de sublinhado. |
| Obrigatório |Opcional. Valor booleano especificando se o parâmetro requer um valor. Se você definir isto para True, um valor deve ser fornecido quando o livro de execução é iniciado. Se definir isto como Falso, um valor é opcional. Se não especificar um valor `Mandatory` para a propriedade, a PowerShell considera o parâmetro de entrada opcional por padrão. |
| Valor predefinido |Opcional. Um valor que é usado para o parâmetro se não for passado qualquer valor de entrada quando o livro de execução começar. O livro de execução pode definir um valor predefinido para qualquer parâmetro. |

O Windows PowerShell suporta mais atributos de parâmetros de entrada do que os acima indicados, tais como validação, pseudónimos e conjuntos de parâmetros. No entanto, a Azure Automation suporta atualmente apenas as propriedades listadas de parâmetros de entrada.

Como exemplo, vamos olhar para uma definição de parâmetro num livro de fluxo de fluxo de fluxo de powerShell. Esta definição tem a seguinte forma geral, onde vários parâmetros são separados por vírgulas.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Agora vamos configurar os parâmetros de entrada para um livro de fluxo de fluxo de fluxo de powerShell que produz detalhes sobre máquinas virtuais, um único VM ou todos os VMs dentro de um grupo de recursos. Este livro tem dois parâmetros, como mostra a seguinte imagem:`VMName`o nome da máquina virtual`resourceGroupName`( ) e o nome do grupo de recursos ( ).

![Fluxo de trabalho PowerShell automation](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

Nesta definição de parâmetro, os parâmetros de entrada são parâmetros simples de cadeia de tipo.

Note que os livros de execução powerShell e PowerShell `Object` Workflow suportam todos os tipos simples e tipos complexos, tais como ou `PSCredential` para parâmetros de entrada. Se o seu livro de execução tiver um parâmetro de entrada de objetos, deve utilizar um hashtable PowerShell com pares de valor de nome para passar em um valor. Por exemplo, tem o seguinte parâmetro num livro de corridas.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Neste caso, pode passar o seguinte valor para o parâmetro.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Quando não passa um valor para um parâmetro de corda opcional com um valor predefinido nulo, o valor do parâmetro é uma corda vazia em vez de Nulo.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Configure parâmetros de entrada em livros gráficos

Para ilustrar a configuração dos parâmetros de entrada para um livro de execução gráfico, vamos criar um livro de execução que produz detalhes sobre máquinas virtuais, seja um único VM ou todos os VMs dentro de um grupo de recursos. Para mais detalhes, consulte [o meu primeiro livro de execução gráfico.](automation-first-runbook-graphical.md)

Um livro gráfico usa estas grandes atividades do livro de corridas:

* Configuração do Azure Run Como conta para autenticar com Azure. 
* Definição de cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) para obter propriedades VM.
* Utilização da atividade [Write-Output](/powershell/module/microsoft.powershell.utility/write-output) para a saída dos nomes VM. 

A `Get-AzVM` atividade define duas inputs, o nome VM e o nome do grupo de recursos. Uma vez que estes nomes podem ser diferentes sempre que o livro de execução começa, você deve adicionar parâmetros de entrada ao seu livro de execução para aceitar estas inputs. Consulte a [autoria gráfica em Automação Azure.](automation-graphical-authoring-intro.md)

Siga estes passos para configurar os parâmetros de entrada.

1. Selecione o livro de execução gráfico na página 'Livros De Rineais' e, em seguida, clique em **Editar**.
2. No editor gráfico, clique no botão **de entrada e saída** **e,** em seguida, adicione a entrada para abrir a vidraça do parâmetro de entrada de saída.

   ![Livro gráfico de automação](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. O controlo de entrada e saída apresenta uma lista de parâmetros de entrada definidos para o livro de execução. Aqui pode adicionar um novo parâmetro de entrada ou editar a configuração de um parâmetro de entrada existente. Para adicionar um novo parâmetro para o livro de execução, clique em **Adicionar entrada** para abrir a lâmina de parâmetro de **entrada Runbook,** onde pode configurar parâmetros utilizando as propriedades definidas na [autoria gráfica em Automação Azure](automation-graphical-authoring-intro.md).

    ![Adicione nova entrada](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Crie dois parâmetros com as seguintes propriedades a utilizar pela atividade **e,** em `Get-AzVM` seguida, clique OK .

   * Parâmetro 1:
        * **Nome** -- **VMName**
        * **Tipo** - Corda
        * **Não obrigatório** -- **No**

   * Parâmetro 2:
        * **Nome de** -- **recursoGroupName**
        * **Tipo** - Corda
        * **Não obrigatório** -- **No**
        * **Valor padrão** -- **Personalizado**
        * Valor padrão personalizado -- Nome do grupo de recursos que contém os VMs

5. Ver os parâmetros no controlo de entrada e saída. 
6. Clique **OK** ok novamente e, em seguida, clique em **Guardar**.
7. Clique em **Publicar** para publicar o seu livro de execução.

### <a name="configure-input-parameters-in-python-runbooks"></a>Configure parâmetros de entrada em livros de execução python

Ao contrário da PowerShell, PowerShell Workflow e livros gráficos, os livros de execução python não têm parâmetros nomeados. O editor do livro de corridas analisa todos os parâmetros de entrada como uma variedade de valores de argumento. Pode aceder à matriz `sys` importando o módulo para o `sys.argv` seu script Python e, em seguida, usando a matriz. É importante notar que o primeiro elemento `sys.argv[0]`da matriz, é o nome do script. Portanto, o primeiro parâmetro de `sys.argv[1]`entrada real é .

Para um exemplo de como usar parâmetros de entrada num livro python, consulte o meu primeiro livro python [em Automação Azure](automation-first-runbook-textual-python2.md).

## <a name="assigning-values-to-input-parameters-in-runbooks"></a>Atribuir valores aos parâmetros de entrada em livros de execução

Esta secção descreve várias formas de passar valores para os parâmetros de entrada em livros de execução. Pode atribuir valores de parâmetros quando:

* [Iniciar um runbook](#start-a-runbook-and-assign-parameters)
* [Testar um runbook](#test-a-runbook-and-assign-parameters)
* [Ligue um horário para o livro de corridas](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Criar um webhook para o runbook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Inicie um livro de corridas e atribua parâmetros

Um livro de corridas pode ser iniciado de muitas maneiras: através do portal Azure, com um webhook, com cmdlets PowerShell, com a API REST, ou com o SDK. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Inicie um livro publicado utilizando o portal Azure e atribua parâmetros

Quando [iniciar o livro](start-runbooks.md#start-a-runbook-with-the-azure-portal) de corridas no portal Azure, a lâmina **Start Runbook** abre-se e pode introduzir valores para os parâmetros que criou.

![Comece a usar o portal](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

Na etiqueta por baixo da caixa de entrada, pode ver as propriedades que foram definidas para definir atributos de parâmetros, por exemplo, obrigatórios ou opcionais, tipo, valor predefinido. O balão de ajuda ao lado do nome do parâmetro também define a informação chave necessária para tomar decisões sobre os valores de entrada de parâmetros. 

> [!NOTE]
> Os parâmetros de corda suportam valores vazios do tipo String. A `[EmptyString]` entrada na caixa de parâmetros de entrada passa uma corda vazia para o parâmetro. Além disso, os parâmetros das cordas não suportam o Nulo. Se não passar qualquer valor a um parâmetro de corda, a PowerShell interpreta-o como Nulo.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Inicie um livro publicado utilizando cmdlets PowerShell e atribua parâmetros

* **Cmdlets do Gestor de Recursos Azure:** Pode iniciar um livro de execução automation que foi criado num grupo de recursos utilizando o [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0
).

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Cmdlets modelo de implantação clássico Azure:** Pode iniciar um livro de execução de automatização que foi criado num grupo de recursos padrão utilizando o [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook).
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> Quando inicia um livro de execução utilizando cmdlets `MicrosoftApplicationManagementStartedBy`PowerShell, um `PowerShell`parâmetro predefinido, é criado com o valor . Pode ver este parâmetro no painel de detalhes do trabalho.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Inicie um livro de corridas usando um SDK e atribua parâmetros

* **Método de Gestor de Recursos Azure:** Pode iniciar um livro de execução utilizando o SDK de uma linguagem de programação. Abaixo está um snippet de código C# para iniciar um livro de execução na sua conta Deautomação. Pode ver todo o código no nosso [repositório GitHub.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)

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

* Método de modelo de **implantação clássico azure:** Pode iniciar um livro de recorrem utilizando o SDK de uma linguagem de programação. Abaixo está um snippet de código C# para iniciar um livro de execução na sua conta Deautomação. Pode ver todo o código no nosso [repositório GitHub.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)

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

   Para iniciar este método, crie um dicionário `VMName` para `resourceGroupName` armazenar os parâmetros do livro e os seus valores. Então comece o livro de corridas. Abaixo está o snippet de código C# para chamar o método que é definido acima.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Inicie um livro de corridas utilizando a API REST e atribua parâmetros

Pode criar e iniciar um trabalho de livro com a `PUT` API de Automação Azure, utilizando o método com o seguinte pedido URI:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

No pedido URI, substitua os seguintes parâmetros:

* `subscriptionId`: O seu ID de subscrição Azure.  
* `resourceGroupName`: O nome do grupo de recursos para a conta Automation.
* `automationAccountName`: O nome da conta Automation que está alojada dentro do serviço de nuvem especificado.  
* `jobName`: O GUID para o trabalho. GuiDs em PowerShell podem `[GUID]::NewGuid().ToString()*`ser criados usando .

Para passar os parâmetros para o trabalho do livro de corridas, use o corpo de pedido. Tem as seguintes informações, fornecidas no formato JSON:

* Nome do livro de corridas: Obrigatório. O nome do livro de corridas para o trabalho começar.  
* Parâmetros do livro de corridas: Opcional. Um dicionário da lista de parâmetros em formato (nome, valor), onde o nome é de tipo String e valor pode ser qualquer valor JSON válido.

Se pretender iniciar o livro de execução **Get-AzureVMTextual** criado anteriormente com `VMName` e `resourceGroupName` como parâmetros, utilize o seguinte formato JSON para o organismo de pedido.

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

Um código de estado HTTP 201 é devolvido se o trabalho for criado com sucesso. Para obter mais informações sobre os cabeçalhos de resposta e o corpo de resposta, consulte criar um trabalho de [livro de rés-do-lista utilizando a API REST](/rest/api/automation/job/create).

### <a name="test-a-runbook-and-assign-parameters"></a>Teste um livro de execução e atribua parâmetros

Quando [testar a versão de projeto do seu livro](automation-testing-runbook.md) de ensaios utilizando a opção de teste, a página de Teste abre. Utilize esta página para configurar valores para os parâmetros que criou.

![Parâmetros de teste e atribuição](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Ligue um horário a um livro de execução e atribua parâmetros

Pode [ligar um horário](automation-schedules.md) ao seu livro de corridas para que o livro de corridas comece num momento específico. Você atribui parâmetros de entrada quando cria o horário, e o livro de execução usa estes valores quando é iniciado pela programação. Não pode guardar o horário até que todos os valores obrigatórios dos parâmetros sejam fornecidos.

![Agendar e atribuir parâmetros](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Crie um webhook para um livro de execução e atribua parâmetros

Pode criar um [webhook](automation-webhooks.md) para o seu livro de execução e configurar parâmetros de entrada de livro de execução. Não pode guardar o webhook até que todos os valores obrigatórios do parâmetro sejam fornecidos.

![Criar webhook e atribuir parâmetros](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Quando executa um livro de execução utilizando um webhook, o parâmetro `[WebhookData](automation-webhooks.md)` de entrada predefinido é enviado, juntamente com os parâmetros de entrada que define. 

![Parâmetro WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="passing-a-json-object-to-a-runbook"></a>Passar um objeto JSON para um livro de corridas

Pode ser útil armazenar dados que pretende passar para um livro de execução num ficheiro JSON. Por exemplo, pode criar um ficheiro JSON que contenha todos os parâmetros que pretende passar para um livro de execução. Para isso, tem de converter o código JSON numa corda e depois converter a corda num objeto PowerShell antes de o passar para o livro de execução.

Esta secção utiliza um exemplo no qual um script PowerShell chama [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) para iniciar um livro de execução PowerShell, passando o conteúdo do ficheiro JSON para o livro de execução. O livro de execução PowerShell inicia um VM Azure recuperando os parâmetros para o VM do objeto JSON.

### <a name="create-the-json-file"></a>Criar o ficheiro JSON

Digite o seguinte código num ficheiro de texto e guarde-o como **test.json** algures no seu computador local.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Criar o livro de corridas

Crie um novo livro de execução powerShell chamado **Test-Json** na Automação Azure. Veja o meu primeiro livro de [corridas powerShell.](automation-first-runbook-textual-powershell.md)

Para aceitar os dados da JSON, o livro de execução deve tomar um objeto como parâmetro de entrada. O livro de execução pode então utilizar as propriedades definidas no ficheiro JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Guarde e publique este livro na sua conta De automação.

### <a name="call-the-runbook-from-powershell"></a>Ligue para o livro de corridas da PowerShell

Agora pode ligar para o livro de execução da sua máquina local utilizando o Azure PowerShell. 

1. Inscreva-se no Azure como mostrado. Depois, é-lhe pedido que introduza as suas credenciais Azure.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >Para os livros `Add-AzAccount` de `Add-AzureRMAccount` execução da `Connect-AzAccount`PowerShell, e são pseudónimos para . Note que estes pseudónimos não estão disponíveis para livros gráficos. Um livro gráfico só `Connect-AzAccount` pode usar-se sozinho.

1. Obtenha o conteúdo do ficheiro JSON guardado e converta-o numa corda. `JsonPath`indica o caminho onde guardou o ficheiro JSON.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Converta o `$json` conteúdo da corda num objeto PowerShell.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Crie um hashtable para `Start-AzAutomationRunbook`os parâmetros para . 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Note que está a `Parameters` definir o valor do objeto PowerShell que contém os valores do ficheiro JSON.
1. Inicie o livro de corridas.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Passos seguintes

* Para mais detalhes sobre diferentes formas de iniciar um livro de corridas, consulte Iniciar um livro de [corridas](automation-starting-a-runbook.md).
* Para editar um livro textual, consulte os [livros textual](automation-edit-textual-runbook.md)de edição .
* Para editar um livro de execução gráfico, consulte a [autoria gráfica em Automação Azure.](automation-graphical-authoring-intro.md)
