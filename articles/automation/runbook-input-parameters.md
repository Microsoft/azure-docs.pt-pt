---
title: Configurar parâmetros de entrada de runbook na Azure Automation
description: Este artigo diz como configurar os parâmetros de entrada do runbook, que permitem que os dados sejam passados para um livro de recortes quando é iniciado.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 84e2eaf71326f59102800428479768aeba9ef9ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87042146"
---
# <a name="configure-runbook-input-parameters"></a>Configurar parâmetros de entrada do runbook

Os parâmetros de entrada do runbook aumentam a flexibilidade de um livro de execução, permitindo que os dados sejam transmitidos quando este é iniciado. Estes parâmetros permitem que as ações do runbook sejam direcionadas para cenários e ambientes específicos. Este artigo descreve a configuração e utilização de parâmetros de entrada nos seus livros de execução.

Pode configurar parâmetros de entrada para powerShell, PowerShell Workflow, gráficos e livros python. Um runbook pode ter vários parâmetros com diferentes tipos de dados, ou nenhum parâmetro em tudo. Os parâmetros de entrada podem ser obrigatórios ou opcionais, e pode utilizar valores predefinidos para parâmetros opcionais.

Atribui valores aos parâmetros de entrada para um livro de execução quando o inicia. Pode iniciar um runbook a partir do portal Azure, um serviço web ou PowerShell. Também pode iniciar um como um livro infantil que é chamado inline em outro runbook.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Configurar parâmetros de entrada em livros de powerShell

Os livros de fluxo de trabalho PowerShell e PowerShell em Azure Automation suportam parâmetros de entrada que são definidos através das seguintes propriedades. 

| **Propriedade** | **Descrição** |
|:--- |:--- |
| Tipo |Obrigatório. O tipo de dados esperado para o valor do parâmetro. Qualquer tipo .NET é válido. |
| Nome |Obrigatório. O nome do parâmetro. Este nome deve ser único dentro do livro de bordo, deve começar com uma letra, e pode conter apenas letras, números ou caracteres de sublinhado. |
| Obrigatório |Opcional. Valor booleano especificando se o parâmetro requer um valor. Se o definir para True, deve ser fornecido um valor quando o livro de bordo for iniciado. Se definir isto para Falso, um valor é opcional. Se não especificar um valor para a propriedade, o `Mandatory` PowerShell considera o parâmetro de entrada opcional por padrão. |
| Valor predefinido |Opcional. Um valor que é utilizado para o parâmetro se não for transmitido qualquer valor de entrada quando o livro de execução começar. O livro de execução pode definir um valor predefinido para qualquer parâmetro. |

O Windows PowerShell suporta mais atributos de parâmetros de entrada do que os listados acima, tais como validação, pseudónimos e conjuntos de parâmetros. No entanto, a Azure Automation suporta atualmente apenas as propriedades dos parâmetros de entrada listados.

Como exemplo, vamos olhar para uma definição de parâmetro num livro de fluxo de trabalho PowerShell. Esta definição tem a seguinte forma geral, onde vários parâmetros são separados por vírgulas.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Agora vamos configurar os parâmetros de entrada para um manual de fluxo de trabalho PowerShell que produz detalhes sobre máquinas virtuais, quer um único VM ou todos os VMs dentro de um grupo de recursos. Este livro tem dois parâmetros, como mostra a seguinte imagem: o nome da máquina virtual ( `VMName` ) e o nome do grupo de recursos ( `resourceGroupName` ).

![Fluxo de trabalho powershell de automação](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

Nesta definição de parâmetro, os parâmetros de entrada são parâmetros simples de cadeia tipo.

Note que os livros de fluxo de trabalho PowerShell e PowerShell suportam todos os tipos e tipos complexos simples, tais como `Object` ou `PSCredential` para parâmetros de entrada. Se o seu runbook tiver um parâmetro de entrada de objetos, deve utilizar um hashtable PowerShell com pares de valor-nome para passar num valor. Por exemplo, tem o seguinte parâmetro num livro de recortes.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Neste caso, pode passar o seguinte valor para o parâmetro.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Quando não passa um valor para um parâmetro de corda opcional com um valor predefinido nulo, o valor do parâmetro é uma cadeia vazia em vez de Nulo.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Configurar parâmetros de entrada em runbooks gráficos

Para ilustrar a configuração dos parâmetros de entrada para um runbook gráfico, vamos criar um runbook que produz detalhes sobre máquinas virtuais, seja um único VM ou todos os VMs dentro de um grupo de recursos. Para mais detalhes, consulte [o meu primeiro livro gráfico.](./learn/automation-tutorial-runbook-graphical.md)

Um livro gráfico utiliza estas principais atividades de runbook:

* Configuração do Azure Run Como conta para autenticar com Azure. 
* Definição de um [cmdlet Get-AzVM](/powershell/module/az.compute/get-azvm?view=azps-3.5.0) para obter propriedades VM.
* Utilização da atividade [Write-Output](/powershell/module/microsoft.powershell.utility/write-output) para obter os nomes VM. 

A `Get-AzVM` atividade define duas entradas, o nome VM e o nome do grupo de recursos. Uma vez que estes nomes podem ser diferentes cada vez que o livro de execuções começa, tem de adicionar parâmetros de entrada ao seu runbook para aceitar estas entradas. Consulte a [autoria gráfica da Azure Automation](automation-graphical-authoring-intro.md).

Siga estes passos para configurar os parâmetros de entrada.

1. Selecione o runbook gráfico a partir da página Runbooks e, em seguida, clique em **Editar**.
2. No editor gráfico, clique no botão **entrada e saída e** adicione a **entrada** para abrir o painel de parâmetros de entrada runbook.

   ![Livro gráfico de automação](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. O controlo de entrada e saída apresenta uma lista de parâmetros de entrada definidos para o livro de execução. Aqui pode adicionar um novo parâmetro de entrada ou editar a configuração de um parâmetro de entrada existente. Para adicionar um novo parâmetro para o runbook, clique em **Adicionar entrada** para abrir a lâmina do parâmetro de **entrada Runbook,** onde pode configurar parâmetros utilizando as propriedades definidas na [autoria gráfica em Azure Automation](automation-graphical-authoring-intro.md).

    ![Adicionar nova entrada](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Crie dois parâmetros com as seguintes propriedades a serem utilizadas pela `Get-AzVM` atividade e, em seguida, clique em **OK**.

   * Parâmetro 1:
        * **Nome**  --  **VMName**
        * **Tipo** - Corda
        * **Obrigatório**  --  **Não, não, não**

   * Parâmetro 2:
        * **Nome**  --  **nome do Grupo de Recursos**
        * **Tipo** - Corda
        * **Obrigatório**  --  **Não, não, não**
        * **Valor predefinido**  --  **Personalizado**
        * Valor padrão personalizado -- Nome do grupo de recursos que contém os VMs

5. Ver os parâmetros no controlo de entrada e saída. 
6. Clique **em OK** novamente e, em seguida, clique em **Guardar**.
7. Clique em **Publicar** para publicar o seu runbook.

### <a name="configure-input-parameters-in-python-runbooks"></a>Configurar parâmetros de entrada em livros de python

Ao contrário do PowerShell, powerShell Workflow e dos runbooks gráficos, os livros de gráficos Python não tomam parâmetros nomeados. O editor de runbook analisa todos os parâmetros de entrada como uma variedade de valores de argumento. Você pode aceder à matriz importando o `sys` módulo para o seu script Python, e depois usando a `sys.argv` matriz. É importante notar que o primeiro elemento da matriz, `sys.argv[0]` é o nome do script. Portanto, o primeiro parâmetro de entrada real é `sys.argv[1]` .

Para um exemplo de como usar parâmetros de entrada num livro de aplicação python, consulte [o meu primeiro livro de aplicações Python em Azure Automation](./learn/automation-tutorial-runbook-textual-python2.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Atribuir valores aos parâmetros de entrada nos livros de bordo

Esta secção descreve várias formas de passar valores para os parâmetros de entrada nos livros de execução. Pode atribuir valores de parâmetros quando:

* [Iniciar um runbook](#start-a-runbook-and-assign-parameters)
* [Testar um runbook](#test-a-runbook-and-assign-parameters)
* [Ligue um horário para o livro de corridas](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Criar um webhook para o runbook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Inicie um livro de execução e atribua parâmetros

Um runbook pode ser iniciado de muitas maneiras: através do portal Azure, com um webhook, com cmdlets PowerShell, com a API REST, ou com o SDK. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Inicie um livro publicado utilizando o portal Azure e atribua parâmetros

Quando [inicia o livro](start-runbooks.md#start-a-runbook-with-the-azure-portal) de recortes no portal Azure, a lâmina **Start Runbook** abre-se e pode introduzir valores para os parâmetros que criou.

![Comece a usar o portal](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

Na etiqueta por baixo da caixa de entrada, pode ver as propriedades que foram definidas para definir atributos de parâmetro, por exemplo, obrigatórios ou opcionais, tipo, valor predefinido. O balão de ajuda ao lado do nome do parâmetro também define as informações-chave necessárias para tomar decisões sobre os valores de entrada de parâmetros. 

> [!NOTE]
> Os parâmetros de corda suportam valores vazios do tipo String. A entrada `[EmptyString]` na caixa de parâmetros de entrada passa uma corda vazia para o parâmetro. Além disso, os parâmetros das cordas não suportam o Nulo. Se não passar qualquer valor para um parâmetro de corda, o PowerShell interpreta-o como Nulo.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Inicie um livro de bordo publicado utilizando cmdlets PowerShell e atribua parâmetros

* **Cmdlets Azure Resource Manager:** Pode iniciar um runbook Automation que foi criado num grupo de recursos utilizando [o Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0).

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Cmdlets de modelo clássico de implantação Azure:** Pode iniciar um runbook de automatização criado num grupo de recursos predefinidos utilizando [o Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure.service/start-azureautomationrunbook).
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> Quando inicia um livro de bordo utilizando cmdlets PowerShell, um parâmetro `MicrosoftApplicationManagementStartedBy` padrão, é criado com o valor `PowerShell` . Pode ver este parâmetro no painel de detalhes do Trabalho.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Inicie um livro de execução usando um SDK e atribua parâmetros

* **Método Azure Resource Manager:** Pode iniciar um livro de bordo utilizando o SDK de uma linguagem de programação. Abaixo está um corte de código C# para iniciar um runbook na sua conta Automation. Pode ver todo o código no nosso [repositório GitHub.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)

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

* **Método de modelo clássico de implantação azul:** Pode iniciar um runbook utilizando o SDK de uma linguagem de programação. Abaixo está um corte de código C# para iniciar um runbook na sua conta Automation. Pode ver todo o código no nosso [repositório GitHub.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)

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

   Para iniciar este método, crie um dicionário para armazenar os parâmetros do livro de bordo `VMName`  `resourceGroupName` e os seus valores. Em seguida, começar o livro de corridas. Abaixo está o corte de código C# para chamar o método que está definido acima.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Inicie um livro de execução utilizando a API REST e atribua parâmetros

Pode criar e iniciar um trabalho de runbook com a API AZure Automation REST, utilizando o `PUT` método com o seguinte pedido URI: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

No pedido URI, substitua os seguintes parâmetros:

* `subscriptionId`: O seu ID de assinatura Azure.  
* `resourceGroupName`: O nome do grupo de recursos para a conta Automation.
* `automationAccountName`: O nome da conta Automation que está hospedada no serviço de nuvem especificado.  
* `jobName`: O GUID para o trabalho. Os GUIDs em PowerShell podem ser criados utilizando `[GUID]::NewGuid().ToString()*` .

Para passar parâmetros para a função de livro de bordo, utilize o corpo de pedido. Toma as seguintes informações, fornecidas no formato JSON:

* Nome do livro de corridas: Obrigatório. O nome do livro de corridas para o trabalho começar.  
* Parâmetros do livro de corridas: Opcional. Um dicionário da lista de parâmetros no formato (nome, valor), onde o nome é do tipo String e valor pode ser qualquer valor JSON válido.

Se pretender iniciar o runbook **Get-AzureVMTextual** criado anteriormente com `VMName` e como `resourceGroupName` parâmetros, utilize o seguinte formato JSON para o corpo pedido.

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

Um código de estado HTTP 201 é devolvido se o trabalho for criado com sucesso. Para obter mais informações sobre os cabeçalhos de resposta e o corpo de resposta, consulte [criar um trabalho de runbook utilizando a API REST](/rest/api/automation/job/create).

### <a name="test-a-runbook-and-assign-parameters"></a>Teste um livro de execução e atribua parâmetros

Quando [testar a versão de projeto do seu runbook](./manage-runbooks.md) utilizando a opção de teste, a página de Teste abre.a página de Teste. Utilize esta página para configurar valores para os parâmetros que criou.

![Testar e atribuir parâmetros](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Ligue um horário a um livro de bordo e atribua parâmetros

Pode [ligar um horário](./shared-resources/schedules.md) ao seu runbook para que o livro de bordo comece numa hora específica. Atribui parâmetros de entrada quando cria o horário, e o livro de execução utiliza estes valores quando é iniciado pelo calendário. Não é possível guardar o horário até que todos os valores de parâmetros obrigatórios sejam fornecidos.

![Agendar e atribuir parâmetros](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Crie um webhook para um livro de bordo e atribua parâmetros

Pode criar um [webhook](automation-webhooks.md) para o seu runbook e configurar os parâmetros de entrada do runbook. Não é possível guardar o webhook até que todos os valores de parâmetros obrigatórios sejam fornecidos.

![Crie webhook e atribua parâmetros](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Quando executa um runbook utilizando um webhook, o parâmetro de entrada predefinido `[WebhookData](automation-webhooks.md)` é enviado, juntamente com os parâmetros de entrada que define. 

![Parâmetro WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>Passe um objeto JSON para um livro de execução

Pode ser útil armazenar dados que pretende passar para um livro de aplicação num ficheiro JSON. Por exemplo, pode criar um ficheiro JSON que contenha todos os parâmetros que pretende passar para um livro de aplicação. Para isso, tem de converter o código JSON numa cadeia e, em seguida, converter a cadeia num objeto PowerShell antes de o passar para o livro de recortes.

Esta secção utiliza um exemplo no qual um script PowerShell chama [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) para iniciar um livro de execução PowerShell, passando o conteúdo do ficheiro JSON para o runbook. O manual powerShell inicia um Azure VM recuperando os parâmetros para o VM a partir do objeto JSON.

### <a name="create-the-json-file"></a>Criar o ficheiro JSON

Digite o seguinte código num ficheiro de texto e guarde-o como **test.jsem** algum lugar do seu computador local.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Criar o livro de execução

Crie um novo livro de cabos PowerShell chamado **Test-Json** na Azure Automation. Consulte [o meu primeiro livro powerShell.](./learn/automation-tutorial-runbook-textual-powershell.md)

Para aceitar os dados JSON, o livro deve tomar um objeto como parâmetro de entrada. O livro pode então utilizar as propriedades definidas no ficheiro JSON.

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

Guarde e publique este livro na sua conta de Automação.

### <a name="call-the-runbook-from-powershell"></a>Ligue para o livro de recortes da PowerShell

Agora pode ligar para o livro de recortes da sua máquina local utilizando o Azure PowerShell. 

1. Inscreva-se no Azure como mostrado. Depois, é-lhe pedido que introduza as suas credenciais de Azure.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >Para os livros powerShell, `Add-AzAccount` e `Add-AzureRMAccount` são pseudónimos para `Connect-AzAccount` . Note que estes pseudónimos não estão disponíveis para livros gráficos. Um livro gráfico só pode `Connect-AzAccount` usar-se sozinho.

1. Obtenha o conteúdo do ficheiro JSON guardado e converta-o numa corda. `JsonPath` indica o caminho onde guardou o ficheiro JSON.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Converta o conteúdo das cordas `$json` num objeto PowerShell.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Crie um haxixe para os parâmetros para `Start-AzAutomationRunbook` . 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Note que está a definir o valor do `Parameters` objeto PowerShell que contém os valores do ficheiro JSON.
1. Começa o livro de corridas.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Passos seguintes

* Para preparar um livro textual, consulte [editar livros texuais em Azure Automation](automation-edit-textual-runbook.md).
* Para preparar um runbook gráfico, consulte [os runbooks gráficos do Autor na Azure Automation](automation-graphical-authoring-intro.md).
