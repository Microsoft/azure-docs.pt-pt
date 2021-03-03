---
title: Use atividades personalizadas em um oleoduto
description: Aprenda a criar atividades personalizadas utilizando .NET e, em seguida, use as atividades num oleoduto Azure Data Factory.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/26/2018
ms.openlocfilehash: 64588d5968df635c3bb017bd1ff1d10951968f32
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724953"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Utilizar atividades personalizadas num pipeline do Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-use-custom-activities.md)
> * [Versão atual](transform-data-using-dotnet-custom-activity.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Existem dois tipos de atividades que pode utilizar num oleoduto Azure Data Factory.

- [Atividades de movimento de dados](copy-activity-overview.md) para mover dados entre [fontes suportadas e lojas de dados de sumidouros](copy-activity-overview.md#supported-data-stores-and-formats).
- [Atividades de transformação de dados](transform-data.md) para transformar dados usando serviços de computação como Azure HDInsight, Azure Batch e Azure Machine Learning.

Para mover dados de/para uma loja de dados que a Data Factory não suporta, ou para transformar/processar dados de uma forma que não seja suportada pela Data Factory, pode criar uma **atividade Personalizada** com a sua própria lógica de movimento de dados ou transformação e utilizar a atividade num oleoduto. A atividade personalizada executa a sua lógica de código personalizada numa piscina de máquinas virtuais **Azure Batch.**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Consulte os seguintes artigos se é novo no serviço Azure Batch:

* [Básicos do Azure Batch](../batch/batch-technical-overview.md) para uma visão geral do serviço Azure Batch.
* [New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) cmdlet para criar uma conta Azure Batch (ou) [portal Azure](../batch/batch-account-create-portal.md) para criar a conta Azure Batch utilizando o portal Azure. Consulte [a Utilização do PowerShell para gerir o artigo da Conta de Lote Azure](/archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account) para obter instruções detalhadas sobre a utilização do cmdlet.
* [Cmdlet New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) para criar uma piscina Azure Batch.

> [!IMPORTANT]
> Ao criar um novo pool Azure Batch, deve ser utilizado o 'VirtualMachineConfiguration' e NÃO 'CloudServiceConfiguration'. Para mais detalhes, consulte [a orientação de migração do Azure Batch Pool.](../batch/batch-pool-cloud-service-to-virtual-machine-configuration.md) 

## <a name="azure-batch-linked-service"></a>Serviço ligado a Azure Batch

O seguinte JSON define um serviço ligado à amostra Azure Batch. Para mais detalhes, consulte [ambientes computativos apoiados pela Azure Data Factory](compute-linked-services.md)

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
}
```

 Para saber mais sobre o serviço ligado ao Azure Batch, consulte o artigo [de serviços ligados ao Compute.](compute-linked-services.md)

## <a name="custom-activity"></a>Atividade personalizada

O seguinte corte JSON define um oleoduto com uma simples Atividade Personalizada. A definição de atividade tem uma referência ao serviço ligado ao Azure Batch.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "helloworld.exe",
        "folderPath": "customactv2/helloworld",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }]
  }
}
```

Nesta amostra, o helloworld.exe é uma aplicação personalizada armazenada na pasta customactv2/helloworld da conta Azure Storage utilizada no resourceLinkedService. A atividade Custom submete esta aplicação personalizada a ser executada no Azure Batch. Pode substituir o comando a qualquer aplicação preferida que possa ser executada no sistema de operação-alvo dos nós do Agrupamento de Lote Azure.

A tabela seguinte descreve nomes e descrições de propriedades específicas a esta atividade.

| Propriedade              | Descrição                              | Necessário |
| :-------------------- | :--------------------------------------- | :------- |
| name                  | Nome da atividade no oleoduto     | Sim      |
| descrição           | Texto descrevendo o que a atividade faz.  | Não       |
| tipo                  | Para atividades personalizadas, o tipo de atividade é **Personalizado.** | Sim      |
| linkedServiceName     | Serviço ligado ao Azure Batch. Para saber mais sobre este serviço ligado, consulte o artigo [de serviços ligados a Compute.](compute-linked-services.md)  | Sim      |
| command               | Comando da aplicação personalizada a executar. Se a aplicação já estiver disponível no Nó de Piscina de Lote Azure, o recursoLinkedService e a pastaPath podem ser ignorados. Por exemplo, pode especificar o comando a ser `cmd /c dir` , que é suportado de forma nativa pelo nó do Pool do Lote do Windows. | Sim      |
| recursoLinkedService | Serviço ligado ao armazenamento Azure à conta de Armazenamento onde a aplicação personalizada é armazenada | Sem &#42;       |
| folderPath            | Caminho para a pasta da aplicação personalizada e todas as suas dependências<br/><br/>Se tiver dependências armazenadas em subpastas - isto é, numa estrutura hierárquica de pastas em *modo de pasta* - a estrutura da pasta é atualmente achatada quando os ficheiros são copiados para O Lote de Azure. Ou seja, todos os ficheiros são copiados numa única pasta sem sub-dobras. Para contornar este comportamento, considere comprimir os ficheiros, copiar o ficheiro comprimido e, em seguida, desapertá-lo com código personalizado no local pretendido. | Sem &#42;       |
| referênciaObjects      | Uma série de serviços e conjuntos de dados existentes. Os serviços e conjuntos de dados ligados referenciados são transmitidos à aplicação personalizada no formato JSON para que o seu código personalizado possa referenciar recursos da Data Factory | Não       |
| extensões    | Propriedades definidas pelo utilizador que podem ser passadas para a aplicação personalizada no formato JSON para que o seu código personalizado possa referenciar propriedades adicionais | Não       |
| retençãoTimeInDays | O tempo de retenção para os ficheiros submetidos para atividade personalizada. O valor predefinido é de 30 dias. | Não |

&#42; As propriedades `resourceLinkedService` e devem ser `folderPath` especificadas ou ambas omitidas.

> [!NOTE]
> Se estiver a passar serviços ligados como referênciaObjects em Atividade Personalizada, é uma boa prática de segurança passar um cofre de chaves Azure habilitado a um serviço ligado (uma vez que não contém quaisquer cordas seguras) e obter as credenciais usando o nome secreto diretamente do Key Vault a partir do código. Pode encontrar [aqui](https://github.com/nabhishek/customactivity_sample/tree/linkedservice) um exemplo que faz referências ao serviço ligado a AKV, recupera as credenciais do Key Vault e, em seguida, acede ao armazenamento no código.

## <a name="custom-activity-permissions"></a>Permissões de atividades personalizadas

A atividade personalizada define a conta de utilizador automático do Azure Batch para *acesso não administrativo com âmbito de tarefa* (a especificação de utilizador automático predefinido). Não é possível alterar o nível de permissão da conta de utilizador automático. Para obter mais informações, consulte [executar tarefas em contas de utilizador em Batch | Contas de utilizador automático.](../batch/batch-user-accounts.md#auto-user-accounts)

## <a name="executing-commands"></a>Executando comandos

Pode executar diretamente um comando utilizando a Atividade Personalizada. O exemplo a seguir corre o comando "echo hello world" nos nós target Azure Batch Pool e imprime a saída para o stdout.

```json
{
  "name": "MyCustomActivity",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "cmd /c echo hello world"
      }
    }]
  }
}
```

## <a name="passing-objects-and-properties"></a>Objetos e propriedades de passagem

Esta amostra mostra como pode utilizar os Pedidos de Referência e Extensões estendidas para passar objetos da Data Factory e propriedades definidas pelo utilizador para a sua aplicação personalizada.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {          
          "connectionString": {
            "type": "SecureString",
            "value": "aSampleSecureString"
          },
          "PropertyBagPropertyName1": "PropertyBagValue1",
          "propertyBagPropertyName2": "PropertyBagValue2",
          "dateTime1": "2015-04-12T12:13:14Z"
        }
      }
    }]
  }
}
```

Quando a atividade é executada, os pedidos de referência e as extensões são armazenados nos seguintes ficheiros que são implantados na mesma pasta de execução do SampleApp.exe:

- `activity.json`

  As lojas ampliaram as propriedades e propriedades da atividade personalizada.

- `linkedServices.json`

  Armazena uma série de Serviços Linked definidos na propriedade ReferenceObjects.

- `datasets.json`

  Armazena uma série de Conjuntos de Dados definidos na propriedade ReferenceObjects.

Os códigos de amostra que seguem demonstram como o SampleApp.exe pode aceder às informações necessárias a partir dos ficheiros JSON:

```csharp
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.accountName);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>Recuperar saídas de execução

Pode iniciar uma corrida de gasoduto utilizando o seguinte comando PowerShell:

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
```

Quando o gasoduto estiver em funcionamento, pode verificar a saída de execução utilizando os seguintes comandos:

```powershell
while ($True) {
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if(!$result) {
        Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
    }
    elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
    }
    else {
        Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
    ($result | Format-List | Out-String)
    Start-Sleep -Seconds 15
}

Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
$result.Output -join "`r`n"

Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
$result.Error -join "`r`n"
```

A **sesta** e **o stderr** da sua aplicação personalizada são guardados no contentor **adfjobs** no Serviço ligado ao armazenamento Azure que definiu ao criar o Serviço ligado ao lote de Azure com um GUID da tarefa. Pode obter o caminho detalhado da saída Activity Run, como mostrado no seguinte corte:

```
Pipeline ' MyCustomActivity' run finished. Result:

ResourceGroupName : resourcegroupname
DataFactoryName   : datafactoryname
ActivityName      : MyCustomActivity
PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
PipelineName      : MyCustomActivity
Input             : {command}
Output            : {exitcode, outputs, effectiveIntegrationRuntime}
LinkedServiceName :
ActivityRunStart  : 10/5/2017 3:33:06 PM
ActivityRunEnd    : 10/5/2017 3:33:28 PM
DurationInMs      : 21203
Status            : Succeeded
Error             : {errorCode, message, failureType, target}

Activity Output section:
"exitcode": 0
"outputs": [
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
]
"effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
Activity Error section:
"errorCode": ""
"message": ""
"failureType": ""
"target": "MyCustomActivity"
```

Se quiser consumir o conteúdo de stdout.txt em atividades a jusante, pode obter o caminho para o ficheiro stdout.txt em expressão " \@ atividade('MyCustomActivity').outputs[0]".

> [!IMPORTANT]
> - Os activity.jsligados, linkedServices.jse datasets.jsestão armazenados na pasta de tempo de funcionaamento da tarefa Lote. Para este exemplo, os activity.js, linkedServices.jse datasets.jsestão armazenados no `https://adfv2storage.blob.core.windows.net/adfjobs/<GUID>/runtime/` caminho. Se necessário, tem de limpá-los separadamente.
> - Para os Serviços Ligados que utilizam o tempo de execução de integração Self-Hosted, as informações sensíveis como chaves ou palavras-passe são encriptadas pelo tempo de execução de integração Self-Hosted para garantir estadias credenciais no ambiente de rede privada definida pelo cliente. Alguns campos sensíveis podem estar em falta quando referenciados pelo seu código de aplicação personalizado desta forma. Utilize SecureString em extensões prolongadas em vez de utilizar a referência do Serviço Linked, se necessário.

## <a name="pass-outputs-to-another-activity"></a>Passar saídas para outra atividade

Pode enviar valores personalizados do seu código numa Atividade Personalizada de volta à Azure Data Factory. Pode fazê-lo escrevendo-os `outputs.json` a partir da sua candidatura. A Data Factory copia o conteúdo `outputs.json` e anexa-o à Produção de Atividade como o valor do `customOutput` imóvel. (O limite de tamanho é de 2MB.) Se quiser consumir o conteúdo de atividades a `outputs.json` jusante, pode obter o valor utilizando a expressão `@activity('<MyCustomActivity>').output.customOutput` .

## <a name="retrieve-securestring-outputs"></a>Recuperar saídas SecureString

Os valores de propriedade sensíveis designados como tipo *SecureString,* como mostrado em alguns dos exemplos deste artigo, estão mascarados no separador Monitor na interface de utilizador da Data Factory.  No entanto, na execução do gasoduto real, uma propriedade *SecureString* é serializada como JSON dentro do `activity.json` ficheiro como texto simples. Por exemplo:

```json
"extendedProperties": {
  "connectionString": {
    "type": "SecureString",
    "value": "aSampleSecureString"
  }
}
```

Esta serialização não é verdadeiramente segura, e não se destina a ser segura. A intenção é sugerir à Data Factory que mascare o valor no separador Monitor.

Para aceder a propriedades do tipo *SecureString a* partir de uma atividade personalizada, leia o `activity.json` ficheiro, que é colocado na mesma pasta que o seu . EXE, deserialize o JSON e, em seguida, aceda à propriedade JSON (extensõesproperias => [propertyName] = valor>).

## <a name="compare-v2-custom-activity-and-version-1-custom-dotnet-activity"></a><a name="compare-v2-v1"></a> Compare a atividade personalizada v2 e a versão 1 (personalizada) Atividade do DotNet

Na versão 1 da Azure Data Factory, implementa uma Atividade DotNet (Personalizada) criando um projeto da Biblioteca classe .NET com uma classe que implementa o `Execute` método da `IDotNetActivity` interface. Os Serviços Ligados, Conjuntos de Dados e Propriedades Estendidas na carga útil JSON de uma Atividade DotNet (Personalizada) são passados para o método de execução como objetos fortemente digitados. Para mais detalhes sobre o comportamento da versão 1, consulte [(Personalizado) DotNet na versão 1](v1/data-factory-use-custom-activities.md). Devido a esta implementação, o seu código de atividade do dotnet versão 1 tem de visar o Quadro 4.NET 4.5.2. A versão 1 DotNet Activity também tem de ser executada nos nós do Azure Batch Pool baseados no Windows.

Na Azure Data Factory V2 Custom Activity, não é obrigado a implementar uma interface .NET. Agora pode executar diretamente comandos, scripts e o seu próprio código personalizado, compilado como um executável. Para configurar esta implementação, você especifica o `Command` imóvel juntamente com o `folderPath` imóvel. A Atividade Personalizada envia o executável e as suas dependências para `folderpath` e executa o comando para si.

Os Serviços Ligados, Conjuntos de Dados (definidos em referênciaObjects) e Propriedades Estendidas definidas na carga útil JSON de uma Data Factory v2 Custom Activity podem ser acedidos pelos seus ficheiros JSON. Pode aceder às propriedades necessárias utilizando um serializador JSON, como mostrado na amostra de código SampleApp.exe anterior.

Com as alterações introduzidas na Atividade Personalizada da Fábrica de Dados V2, pode escrever a sua lógica de código personalizado no seu idioma preferido e executá-la em Sistemas de Operação Windows e Linux suportados pelo Azure Batch.

A tabela a seguir descreve as diferenças entre a Atividade Personalizada da Fábrica de Dados V2 e a versão 1 (Personalizada) da Data Factory:

|Diferenças      | Atividade Personalizada      | versão 1 (Personalizada) Atividade do DotNet      |
| ---- | ---- | ---- |
|Como a lógica personalizada é definida      |Ao fornecer um executável      |Implementando um .NET DLL      |
|Ambiente de execução da lógica personalizada      |Janelas ou Linux      |Janelas (.Quadro NET 4.5.2)      |
|Executando scripts      |Suporta a execução direta de scripts (por exemplo ,cmd /c echo hello world" no Windows VM)      |Requer implementação no .NET DLL      |
|Conjunto de dados necessário      |Opcional      |Obrigado a acorrentar atividades e passar informações      |
|Passar informações da atividade para a lógica personalizada      |Através de ReferenceObjects (LinkedServices and Datasets) e ExtendedProperties (propriedades personalizadas)      |Através de Extensões (propriedades personalizadas), conjuntos de dados de entrada e saída      |
|Recuperar informação em lógica personalizada      |Parses activity.jsligado, linkedServices.jsligado, e datasets.jsarmazenado na mesma pasta do executável      |Através de .NET SDK (.NET Frame 4.5.2)      |
|Registo      |Escreve diretamente para STDOUT      |Implementação do Madeiriro em .NET DLL      |

Se tiver o código .NET existente escrito para uma atividade dotnet da versão 1 (personalizada), tem de modificar o seu código para que funcione com a versão atual da Atividade Personalizada. Atualize o seu código seguindo estas diretrizes de alto nível:

  - Mude o projeto de uma Biblioteca classe .NET para uma App de Consola.
  - Inicie a sua aplicação com o `Main` método. O `Execute` método da interface já não é `IDotNetActivity` necessário.
  - Leia e analise os Serviços Ligados, Conjuntos de Dados e Atividade com um serializador JSON, e não tão fortemente digitado. Passe os valores das propriedades requeridas para a sua lógica de código personalizado principal. Consulte o código SampleApp.exe anterior como exemplo.
  - O objeto Logger já não está suportado. A saída do seu executável pode ser impressa na consola e é guardada para stdout.txt.
  - O pacote Microsoft.Azure.Management.DataFactories NuGet já não é necessário.
  - Compile o seu código, faça o upload do executável e das suas dependências para o Azure Storage e defina o caminho na `folderPath` propriedade.

Para uma amostra completa de como a amostra de DLL e pipeline de ponta a ponta descrita na versão 1 da Data Factory [Utilize atividades personalizadas num pipeline da Azure Data Factory](./v1/data-factory-use-custom-activities.md) pode ser reescrito como uma Atividade Personalizada da Fábrica de Dados, consulte a amostra de Atividade Personalizada da Fábrica de [Dados.](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFv2CustomActivitySample)

## <a name="auto-scaling-of-azure-batch"></a>Auto-dimensionamento do Lote Azure

Também pode criar uma piscina Azure Batch com funcionalidade **de autoescala.** Por exemplo, pode criar um lote azul com 0 VMs dedicados e uma fórmula de autoescala com base no número de tarefas pendentes.

A fórmula da amostra aqui consegue o seguinte comportamento: Quando a piscina é criada inicialmente, começa com 1 VM. $PendingTasks métrica define o número de tarefas em execução + estado ativo (em fila). A fórmula encontra o número médio de tarefas pendentes nos últimos 180 segundos e define o TargetDedicated em conformidade. Garante que o TargetDedicated nunca vai além dos 25 VMs. Assim, à medida que novas tarefas são submetidas, o pool cresce automaticamente e à medida que as tarefas terminam, os VMs tornam-se gratuitos um a um e o autoscaling encolhe esses VMs. startingNumberOfVMs e maxNumberofVMs podem ser ajustados às suas necessidades.

Fórmula de autoescala:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Consulte [os nós computacional de escala automática numa piscina do Azure Batch](../batch/batch-automatic-scaling.md) para obter mais detalhes.

Se a piscina estiver a utilizar o [autoScaleEvaluationInterval](/rest/api/batchservice/pool/enableautoscale)predefinido, o serviço Batch pode demorar 15 a 30 minutos a preparar o VM antes de executar a atividade personalizada. Se a piscina estiver a utilizar um autoScaleEvaluationInterval diferente, o serviço Batch pode demorar automaticamente a Reavaliação + 10 minutos.

## <a name="next-steps"></a>Passos seguintes
Veja os seguintes artigos que explicam como transformar dados de outras formas:

* [Atividade u-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade da colmeia](transform-data-using-hadoop-hive.md)
* [Atividade do porco](transform-data-using-hadoop-pig.md)
* [Atividade mapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming de Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Apache Spark](transform-data-using-spark.md)
* [Azure Machine Learning Studio (clássico) Atividade de execução de lote](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)