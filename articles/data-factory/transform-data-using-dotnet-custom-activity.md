---
title: Use atividades personalizadas num oleoduto
description: Aprenda a criar atividades personalizadas e use-as num oleoduto Azure Data Factory.
services: data-factory
ms.service: data-factory
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/26/2018
ms.openlocfilehash: 4913152125b0fafd74db575f835d53fa992b075e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260582"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Utilizar atividades personalizadas num pipeline do Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-use-custom-activities.md)
> * [Versão atual](transform-data-using-dotnet-custom-activity.md)

Existem dois tipos de atividades que pode utilizar num oleoduto Azure Data Factory.

- [Atividades de movimento](copy-activity-overview.md) de dados para mover dados entre [fonte suportada e lojas de dados afundadas](copy-activity-overview.md#supported-data-stores-and-formats).
- [Atividades](transform-data.md) de transformação de dados para transformar dados usando serviços de computação como Azure HDInsight, Azure Batch e Azure Machine Learning.

Para mover dados para/a partir de uma loja de dados que a Data Factory não suporta, ou para transformar/processar dados de uma forma que não seja suportada pela Data Factory, pode criar uma **atividade personalizada** com a sua própria lógica de movimento de dados ou transformação e utilizar a atividade num pipeline. A atividade personalizada executa a sua lógica de código personalizada numa piscina de máquinas virtuais **do Lote Azure.**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Consulte os seguintes artigos se for novo no serviço Azure Batch:

* [O básico do Lote Azure](../batch/batch-technical-overview.md) para uma visão geral do serviço Azure Batch.
* [O cmdlet New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) para criar um [portal Azure](../batch/batch-account-create-portal.md) Batch (ou) Azure para criar a conta Azure Batch utilizando o portal Azure. Consulte [a Utilização do PowerShell para gerir](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) o artigo da Conta de Lote Azure para obter instruções detalhadas sobre a utilização do cmdlet.
* [Cmdlet New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) para criar uma piscina de Lote Azure.

## <a name="azure-batch-linked-service"></a>Serviço ligado ao Lote Azure

O seguinte JSON define uma amostra de serviço ligado ao Lote Azure. Para mais detalhes, consulte [ambientes Compute suportados pela Azure Data Factory](compute-linked-services.md)

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

 Para saber mais sobre o serviço ligado ao Lote Azure, consulte o artigo de [serviços ligados à Compute.](compute-linked-services.md)

## <a name="custom-activity"></a>Atividade personalizada

O seguinte snippet JSON define um pipeline com uma simples Atividade Personalizada. A definição de atividade tem uma referência ao serviço ligado ao Lote Azure.

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

Nesta amostra, o helloworld.exe é uma aplicação personalizada armazenada na pasta customactv2/helloworld da conta Azure Storage utilizada no recursoLinkedService. A atividade personalizada submete esta aplicação personalizada a ser executada no Lote Azure. Pode substituir o comando por qualquer aplicação preferida que possa ser executada no sistema de funcionamento do conjunto de lotes Azure.

A tabela seguinte descreve nomes e descrições de propriedades específicas desta atividade.

| Propriedade              | Descrição                              | Necessário |
| :-------------------- | :--------------------------------------- | :------- |
| nome                  | Nome da atividade no oleoduto     | Sim      |
| descrição           | Texto descrevendo o que a atividade faz.  | Não       |
| tipo                  | Para atividade personalizada, o tipo de atividade é **Personalizado**. | Sim      |
| linkedServiceName     | Serviço Ligado ao Lote Azure. Para conhecer este serviço ligado, consulte o artigo de [serviços ligados à Compute.](compute-linked-services.md)  | Sim      |
| command               | Comando da aplicação personalizada a ser executada. Se a aplicação já estiver disponível no Nó de Pool Azure Batch, os recursosLinkedService e pastaPath podem ser ignorados. Por exemplo, pode especificar o comando a ser `cmd /c dir`, que é suportado de forma nativa pelo nó do Windows Batch Pool. | Sim      |
| resourceLinkedService | Serviço Ligado ao Armazenamento Azure na conta de armazenamento onde a aplicação personalizada é armazenada | Não&#42;       |
| folderPath            | Caminho para a pasta da aplicação personalizada e todas as suas dependências<br/><br/>Se tiver dependências armazenadas em subpastas - isto é, numa estrutura hierárquica de pastas sob *pastaPath* - a estrutura da pasta é atualmente achatada quando os ficheiros são copiados para o Lote Azure. Ou seja, todos os ficheiros são copiados para uma única pasta sem subpastas. Para contornar este comportamento, considere comprimir os ficheiros, copiar o ficheiro comprimido e, em seguida, desapertá-lo com código personalizado no local pretendido. | Não&#42;       |
| referênciaObjetos      | Uma variedade de serviços e conjuntos de dados ligados existentes. Os serviços e conjuntos de dados referenciados são passados para a aplicação personalizada em formato JSON para que o seu código personalizado possa fazer referência aos recursos da Fábrica de Dados | Não       |
| propriedades estendidas    | Propriedades definidas pelo utilizador que podem ser passadas para a aplicação personalizada em formato JSON para que o seu código personalizado possa referenciar propriedades adicionais | Não       |
| retentionTimeInDays | O tempo de retenção dos ficheiros submetidos para atividades personalizadas. O valor padrão é de 30 dias. | Não |

&#42;As propriedades `resourceLinkedService` e `folderPath` devem ser especificadas ou ambas devem ser omitidas.

> [!NOTE]
> Se estiver a passar por serviços ligados como referênciaObjects in Custom Activity, é uma boa prática de segurança passar um serviço ligado à Chave Azure (uma vez que não contém nenhuma strings segura) e obter as credenciais usando o nome secreto diretamente de Key Cofre do código. Pode encontrar [aqui](https://github.com/nabhishek/customactivity_sample/tree/linkedservice) um exemplo que refere o serviço ligado ao AKV, recupera as credenciais do Key Vault e, em seguida, acede ao armazenamento no código.

## <a name="custom-activity-permissions"></a>Permissões de atividade personalizadas

A atividade personalizada define a conta de utilizador automático do Lote Azure para *o acesso não administrativo com âmbito de tarefa* (a especificação predefinida do utilizador automático). Não é possível alterar o nível de permissão da conta de utilizador automático. Para mais informações, consulte Executar tarefas nas contas dos [utilizadores em Lote  Contas de utilizador automático](../batch/batch-user-accounts.md#auto-user-accounts).

## <a name="executing-commands"></a>Comandos de execução

Pode executar diretamente um comando utilizando a Atividade Personalizada. O exemplo seguinte executa o comando "echo hello world" nos nódosos do Azure Batch Pool e imprime a saída para stdout.

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

Esta amostra mostra como pode utilizar os objetos de referência e propriedades estendidas para passar objetos da Data Factory e propriedades definidas pelo utilizador para a sua aplicação personalizada.

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

Quando a atividade é executada, referênciaObjects e extendedProperties são armazenados em seguintes ficheiros que são implantados na mesma pasta de execução do SampleApp.exe:

- `activity.json`

  As lojas ampliaram propriedades e propriedades da atividade personalizada.

- `linkedServices.json`

  Armazena uma variedade de Serviços Linked definidos na propriedade de referênciaObjects.

- `datasets.json`

  Armazena um conjunto de Conjuntos de Dados definidos na propriedade de referênciaObjects.

O seguinte código de amostra demonstra como o SampleApp.exe pode aceder às informações necessárias a partir de ficheiros JSON:

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

Pode iniciar uma execução de gasoduto utilizando o seguinte comando PowerShell:

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

O **stdout** e **o stderr** da sua aplicação personalizada são guardados no recipiente de **adfjobs** no Serviço Ligado ao Armazenamento Azure que definiu ao criar o Serviço Ligado ao Lote Azure com um GUID da tarefa. Pode obter o caminho detalhado da saída do Activity Run, como mostra o seguinte corte:

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

Se quiser consumir o conteúdo de stdout.txt em atividades a jusante, pode obter o caminho para o ficheiro stdout.txt em expressão "\@atividade ('MyCustomActivity').output.outputs[0]".

> [!IMPORTANT]
> - A atividade.json, linkedServices.json e datasets.json são armazenados na pasta de tempo de execução da tarefa Do Lote. Para este exemplo, a atividade.json, linkedServices.json e datasets.json são armazenados em `"https://adfv2storage.blob.core.windows.net/adfjobs/\<GUID>/runtime/"` caminho. Se necessário, tem de limpá-los separadamente.
> - Para os Serviços Ligados que utilizam o Tempo de Execução de Integração Auto-Hospedado, as informações sensíveis como chaves ou palavras-passe são encriptadas pelo Tempo de Execução de Integração Auto-Hospedada para garantir estadias credenciais no ambiente de rede privada definido pelo cliente. Alguns campos sensíveis podem faltar quando referenciados pelo seu código de aplicação personalizado desta forma. Utilize o SecureString em propriedades estendidas em vez de utilizar referência do Linked Service, se necessário.

## <a name="pass-outputs-to-another-activity"></a>Passe saídas para outra atividade

Pode enviar valores personalizados do seu código numa Atividade Personalizada de volta à Azure Data Factory. Pode fazê-lo escrevendo-os em `outputs.json` a partir da sua candidatura. Data Factory copia o conteúdo da `outputs.json` e anexa-o na Produção de Atividade como o valor do imóvel `customOutput`. (O limite de tamanho é de 2MB.) Se quiser consumir o conteúdo de `outputs.json` em atividades a jusante, pode obter o valor utilizando a expressão `@activity('<MyCustomActivity>').output.customOutput`.

## <a name="retrieve-securestring-outputs"></a>Recuperar saídas SecureString

Os valores de propriedade sensíveis designados como Tipo *SecureString,* como mostram alguns dos exemplos deste artigo, são mascarados no separador Monitoramento na interface de utilizador da Data Factory.  No entanto, na execução real do gasoduto, uma propriedade *SecureString* é serializada como JSON dentro do ficheiro `activity.json` como texto simples. Por exemplo:

```json
"extendedProperties": {
  "connectionString": {
    "type": "SecureString",
    "value": "aSampleSecureString"
  }
}
```

Esta serialização não é verdadeiramente segura, e não se destina a ser segura. A intenção é sugerir à Data Factory para mascarar o valor no separador Monitor.

Para aceder a propriedades do tipo *SecureString* a partir de uma atividade personalizada, leia o ficheiro `activity.json`, que é colocado na mesma pasta que o seu . EXE, desserialize o JSON e, em seguida, aceda à propriedade JSON (extendedProperties => [propertyName] => valor).

## <a name="compare-v2-v1"></a>Compare a atividade personalizada v2 e a versão 1 (personalizada) DotNet Activity

Na versão 1 da Azure Data Factory, implementa uma Atividade DotNet (Personalizada) criando um projeto da Biblioteca de Classes .NET com uma classe que implementa o método `Execute` da interface `IDotNetActivity`. Os Serviços Ligados, Conjuntos de Dados e Propriedades Estendidas na carga útil JSON de uma (Personalizada) Atividade DotNet são passados para o método de execução como objetos fortemente digitados. Para mais detalhes sobre o comportamento da versão 1, consulte [(Personalizado) DotNet na versão 1](v1/data-factory-use-custom-activities.md). Devido a esta implementação, o código de atividade da versão 1 do DotNet tem de visar o .NET Framework 4.5.2. A versão 1 DotNet Activity também tem de ser executada em nódos de Pool Azure, baseados no Windows.

Na Fábrica de Dados Azure V2 Atividade personalizada, não é necessário implementar uma interface .NET. Agora pode executar comandos, scripts e o seu próprio código personalizado, compilados como executáveis. Para configurar esta implementação, especifice a propriedade `Command` juntamente com a propriedade `folderPath`. A Atividade Personalizada envia o executável e as suas dependências para `folderpath` e executa o comando para si.

Os Serviços Ligados, Conjuntos de Dados (definidos em referênciaObjects) e Propriedades Estendidas definidas na carga útil JSON de uma Fábrica de Dados v2 Atividade personalizada podem ser acedidas pelo seu executável como ficheiros JSON. Pode aceder às propriedades necessárias utilizando um serializador JSON, como mostra a amostra de código SampleApp.exe anterior.

Com as alterações introduzidas na Data Factory V2 Custom Activity, pode escrever a sua lógica de código personalizada no seu idioma preferido e executá-la nos Sistemas de Operação Windows e Linux suportados pelo Batch Azure.

A tabela seguinte descreve as diferenças entre a Fábrica de Dados V2 Atividade Personalizada e a versão 1 (Personalizada) DatNet:

|Diferenças      | Atividade Personalizada      | versão 1 (Personalizada) Atividade DotNet      |
| ---- | ---- | ---- |
|Como a lógica personalizada é definida      |Ao fornecer um executável      |Implementando um DLL .NET      |
|Ambiente de execução da lógica personalizada      |Janelas ou Linux      |Janelas (.QUADRO NET 4.5.2)      |
|Executar scripts      |Suporta executar scripts diretamente (por exemplo "cmd /c echo hello world" no Windows VM)      |Requer implementação no .NET DLL      |
|Conjunto de dados necessário      |Opcional      |Necessário para a cadeia de atividades e passar informações      |
|Passar informações da atividade para a lógica personalizada      |Através de ReferenceObjects (LinkedServices and Datasets) e ExtendedProperties (propriedades personalizadas)      |Através de ExtendedProperties (propriedades personalizadas), Input e Output Datasets      |
|Recuperar informações em lógica personalizada      |Parses activity.json, linkedServices.json, e datasets.json armazenados na mesma pasta do executável      |Através de .NET SDK (.QUADRO LÍQUIDO 4.5.2)      |
|Registo      |Escreve diretamente para STDOUT      |Implementação de Madeireiro em .NET DLL      |

Se tiver o código .NET existente escrito para uma atividade do DotNet (Personalizada) versão 1 ( Personalizada), precisa de modificar o seu código para que funcione com a versão atual da Atividade Personalizada. Atualize o seu código seguindo estas diretrizes de alto nível:

  - Mude o projeto de uma Biblioteca de Classe .NET para uma App de Consola.
  - Inicie a sua aplicação com o método `Main`. O método `Execute` da interface `IDotNetActivity` já não é necessário.
  - Leia e analise os Serviços Ligados, Conjuntos de Dados e Atividade com um serializador JSON, e não tão fortemente digitado objetos. Passe os valores das propriedades necessárias para a sua lógica de código personalizado principal. Consulte o código SampleApp.exe anterior como exemplo.
  - O objeto Logger já não é suportado. A saída do seu executável pode ser impressa na consola e é guardada para stdout.txt.
  - O pacote Microsoft.Azure.Management.DataFactories NuGet já não é necessário.
  - Compile o seu código, carregue o executável e as suas dependências para o Armazenamento Azure, e defina o caminho na propriedade `folderPath`.

Para uma amostra completa de como a amostra de DLL e pipeline de ponta a ponta descrita na versão 1 da data Factory [Utilize atividades personalizadas num oleoduto Azure Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) pode ser reescrita como uma atividade personalizada da fábrica de dados, consulte a amostra de atividade personalizada da fábrica de [dados.](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFv2CustomActivitySample)

## <a name="auto-scaling-of-azure-batch"></a>Auto-escalação do Lote Azure

Também pode criar uma piscina azure batch com recurso de **escala automática.** Por exemplo, você poderia criar um lote azul com 0 VMs dedicados e uma fórmula de escala automática baseada no número de tarefas pendentes.

A fórmula da amostra aqui consegue o seguinte comportamento: Quando a piscina é inicialmente criada, começa com 1 VM. $PendingTasks métrica define o número de tarefas em execução + estado ativo (em fila). A fórmula encontra o número médio de tarefas pendentes nos últimos 180 segundos e define o TargetDedicated em conformidade. Garante que o TargetDedicated nunca vai além de 25 VMs. Assim, à medida que novas tarefas são submetidas, o pool cresce automaticamente e à medida que as tarefas completam, os VMs tornam-se gratuitos um a um e o autoscalcificante encolhe esses VMs. iniciarNumberOfVMs e maxNumberofVMs podem ser ajustados às suas necessidades.

Fórmula de escala automática:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Consulte [automaticamente os nódosos de cálculo numa piscina de Lote Azure](../batch/batch-automatic-scaling.md) para obter mais detalhes.

Se a piscina estiver a utilizar o [intervalo de avaliação automática](https://msdn.microsoft.com/library/azure/dn820173.aspx)predefinido, o serviço Batch pode demorar 15-30 minutos a preparar o VM antes de executar a atividade personalizada. Se a piscina estiver a utilizar um intervalo de avaliação automática diferente, o serviço Batch poderá demorar a avaliação automática + 10 minutos.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras formas:

* [Atividade U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade da colmeia](transform-data-using-hadoop-hive.md)
* [Atividade de porco](transform-data-using-hadoop-pig.md)
* [MapReduzir a atividade](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming de hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade de faísca](transform-data-using-spark.md)
* [Atividade de execução de lote de aprendizagem automática](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
