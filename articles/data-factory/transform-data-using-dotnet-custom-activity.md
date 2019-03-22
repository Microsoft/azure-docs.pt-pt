---
title: Utilizar atividades personalizadas num pipeline do Azure Data Factory
description: Saiba como criar atividades personalizadas e utilizá-los num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/26/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 849f944235cf1ab4408aeab336310028d6e754f4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57855874"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Utilizar atividades personalizadas num pipeline do Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-use-custom-activities.md)
> * [Versão atual](transform-data-using-dotnet-custom-activity.md)

Existem dois tipos de atividades que podem ser usados num pipeline do Azure Data Factory.

- [Atividades de movimento de dados](copy-activity-overview.md) para mover dados entre [arquivos de dados de origem e sink suportados](copy-activity-overview.md#supported-data-stores-and-formats).
- [Atividades de transformação de dados](transform-data.md) para transformar dados através de serviços de computação como o Azure HDInsight, Azure Batch e do Azure Machine Learning.

Para mover dados em dados de um arquivo que não suporta a fábrica de dados ou para transformar/processar dados de uma forma que não é suportado pelo Data Factory, pode criar uma **atividade personalizada** com seus próprios movimento de dados ou a lógica de transformação e a utilização a atividade num pipeline. A atividade personalizada sua lógica de código personalizado é executado num **do Azure Batch** conjunto de máquinas virtuais.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Consulte o seguinte artigos se estiver familiarizado com o serviço Azure Batch:

* [Noções básicas do Azure Batch](../batch/batch-technical-overview.md) para uma descrição geral do serviço Azure Batch.
* [Novo AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) cmdlet para criar uma conta do Azure Batch (ou) [portal do Azure](../batch/batch-account-create-portal.md) para criar a conta do Azure Batch com portal do Azure. Ver [utilizar o PowerShell para gerir a conta do Azure Batch](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) artigo para obter instruções detalhadas sobre como utilizar o cmdlet.
* [Novo AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) cmdlet para criar um conjunto do Batch do Azure.

## <a name="azure-batch-linked-service"></a>Serviço Azure Batch ligado
O seguinte JSON define um serviço ligado do Azure Batch de exemplo. Para obter detalhes, consulte [suportados pelo Azure Data Factory de ambientes de computação](compute-linked-services.md)

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

 Para saber mais sobre o serviço ligado do Azure Batch, veja [serviços ligados de computação](compute-linked-services.md) artigo.

## <a name="custom-activity"></a>Atividade personalizada

O seguinte fragmento JSON define um pipeline com uma atividade personalizada simples. Definição da atividade possui uma referência para o serviço ligado do Azure Batch.

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

Neste exemplo, o helloworld.exe é um aplicativo personalizado armazenado na pasta da conta de armazenamento do Azure utilizada no resourceLinkedService customactv2/helloworld. A atividade personalizada submete este aplicativo personalizado para ser executado no Azure Batch. Pode substituir o comando para qualquer aplicação preferido que pode ser executada no sistema operativo de nós do conjunto de Batch do Azure de destino.

A tabela seguinte descreve os nomes e descrições das propriedades que são específicas para esta atividade.

| Propriedade              | Descrição                              | Necessário |
| :-------------------- | :--------------------------------------- | :------- |
| nome                  | Nome da atividade no pipeline     | Sim      |
| descrição           | Texto que descreve o que faz a atividade.  | Não       |
| tipo                  | Para a atividade personalizada, é o tipo de atividade **personalizado**. | Sim      |
| linkedServiceName     | Serviço ligado para o Azure Batch. Para saber mais sobre este serviço ligado, veja [serviços ligados de computação](compute-linked-services.md) artigo.  | Sim      |
| command               | Comando do aplicativo personalizado a ser executado. Se o aplicativo já está disponível no nó no conjunto do Azure Batch, podem ser ignorados o resourceLinkedService e folderPath. Por exemplo, pode especificar o comando para ser `cmd /c dir`, que é suportado nativamente por nó no conjunto de Batch do Windows. | Sim      |
| resourceLinkedService | Serviço ligado do armazenamento do Azure para a conta de armazenamento onde está armazenado o aplicativo personalizado | Não&#42;       |
| folderPath            | Caminho para a pasta da aplicação personalizada e todas as suas dependências<br/><br/>Se tiver dependências armazenadas em subpastas - ou seja, numa estrutura de hierarquia de pastas sob *folderPath* -a estrutura de pastas atualmente é aplanada quando os ficheiros são copiados para o Azure Batch. Ou seja, todos os ficheiros são copiados para uma única pasta com sem subpastas. Para contornar este comportamento, considere a comprimir os ficheiros, copiar o ficheiro comprimido e, em seguida, descomprimi-la com código personalizado no local desejado. | Não&#42;       |
| referenceObjects      | Uma matriz de serviços ligados e conjuntos de dados existentes. Os serviços ligados e conjuntos de dados referenciada são transmitidos para o aplicativo personalizado no formato JSON para que seu código personalizado, pode referenciar recursos do Data Factory | Não       |
| extendedProperties    | Propriedades definidas pelo utilizador, que podem ser transmitidas para o aplicativo personalizado no formato JSON, para que seu código personalizado, pode referenciar propriedades adicionais | Não       |
| retentionTimeInDays | O período de retenção para os ficheiros submetido para a atividade personalizada. Valor predefinido é 30 dias. | Não |

&#42;As propriedades `resourceLinkedService` e `folderPath` tem de ser especificados, ou ambos ser omitido.

> [!NOTE]
> Se estiver passando serviços ligados como referenceObjects na atividade personalizada, é uma boa prática de segurança para passar um Azure Key Vault ativado um serviço ligado (uma vez que não contém qualquer cadeia de caracteres segura) e obter as credenciais com o nome do segredo diretamente a partir da chave Cofre do código. Pode encontrar um exemplo [aqui](https://github.com/nabhishek/customactivity_sample/tree/linkedservice) que referências AKV ativado o serviço ligado, obtém as credenciais do Cofre de chaves e, em seguida, acede ao armazenamento no código.  

## <a name="custom-activity-permissions"></a>Permissões de atividade personalizada

A atividade personalizada define a conta de utilizador automático do Azure Batch para *acesso não-administrador com âmbito de tarefa* (a especificação de auto-utilizador padrão). Não é possível alterar o nível de permissão da conta de utilizador automaticamente. Para obter mais informações, consulte [executar tarefas em contas de utilizador no Batch | Contas de utilizador automático](../batch/batch-user-accounts.md#auto-user-accounts).

## <a name="executing-commands"></a>Executar comandos

É possível executar diretamente um comando com a atividade personalizada. O exemplo seguinte executa o comando de "hello world de eco" em nós do conjunto de Batch do Azure de destino e imprime a saída para stdout.

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

## <a name="passing-objects-and-properties"></a>Transmitir objetos e propriedades

Este exemplo mostra como pode usar o referenceObjects e extendedProperties passar objetos de fábrica de dados e propriedades definidas pelo utilizador para a sua aplicação personalizada.


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

Quando a atividade é executada, são armazenadas nos seguintes ficheiros que são implementados na mesma pasta de execução do SampleApp.exe referenceObjects e extendedProperties:

- activity.json

  Armazena extendedProperties e propriedades da atividade personalizada.

- linkedServices.json

  Arquivos de uma matriz de serviços ligados definido na propriedade referenceObjects.

- datasets.json

  Arquivos de uma matriz de conjuntos de dados definido na propriedade referenceObjects.

Seguinte código de exemplo demonstram como o SampleApp.exe pode acessar as informações necessárias a partir de ficheiros JSON:

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

## <a name="retrieve-execution-outputs"></a>Obter as saídas de execução

Pode iniciar uma execução de pipeline com o seguinte comando do PowerShell:

```.powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
```
Quando o pipeline está em execução, pode verificar o resultado da execução com os comandos seguintes:

```.powershell
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

O **stdout** e **stderr** da sua aplicação personalizada são guardados para o **adfjobs** contentor no serviço ligado de armazenamento de Azure definidos durante a criação do Azure Batch ligado Serviço com um GUID da tarefa. Pode obter o caminho detalhado da saída da execução da atividade, conforme mostrado no seguinte fragmento:

```shell
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
Se desejar consumir o conteúdo de stdout.txt em atividades downstream, pode obter o caminho para o ficheiro de stdout.txt na expressão "\@activity('MyCustomActivity').output.outputs [0]".

  > [!IMPORTANT]
  > - O activity.json linkedServices.json e datasets.json são armazenadas na pasta de tempo de execução da tarefa do Batch. Para este exemplo, o activity.json linkedServices.json e datasets.json são armazenados em "https://adfv2storage.blob.core.windows.net/adfjobs/\<GUID>/runtime/" caminho. Se for necessário, terá de limpá-los separadamente.
  > - Para obter serviços ligados que utilizem o Runtime de integração autoalojado, as informações confidenciais, como chaves ou palavras-passe são criptografadas pelo Runtime de integração autoalojado para garantir que a credencial permanece no cliente definido pelo ambiente de rede privada. Alguns campos confidenciais poderiam estar em falta quando referenciados pelo seu código de aplicativo personalizada dessa forma. Utilize SecureString extendedProperties em vez de utilizar a referência de serviço ligado, se necessário.

## <a name="pass-outputs-to-another-activity"></a>Saídas de passagem para outra atividade

Pode enviar valores personalizados do seu código numa atividade personalizada de volta para o Azure Data Factory. Pode fazê-lo ao escrevê-las em `outputs.json` desde a sua aplicação. Fábrica de dados copia o conteúdo do `outputs.json` e o acrescenta na saída de atividade como o valor do `customOutput` propriedade. (O limite de tamanho é de 2MB.) Se quiser consumir o conteúdo do `outputs.json` em atividades downstream, pode obter o valor com a expressão `@activity('<MyCustomActivity>').output.customOutput`.

## <a name="retrieve-securestring-outputs"></a>Obter as saídas de SecureString

Os valores de propriedade confidenciais designados como o tipo *SecureString*, como mostra alguns dos exemplos neste artigo, são mascarados no separador de monitorização na interface do usuário de fábrica de dados.  Na execução de pipeline real, no entanto, uma *SecureString* propriedade é serializada como JSON dentro do `activity.json` arquivo como texto simples. Por exemplo:

```json
"extendedProperties": {
    "connectionString": {
        "type": "SecureString",
        "value": "aSampleSecureString"
    }
}
```

Esta serialização não é realmente protegida e não se destina a ser seguros. A intenção é sugestão à fábrica de dados para mascarar o valor no separador de monitorização.

Para propriedades de tipo de acesso *SecureString* de uma atividade personalizada, leia o `activity.json` arquivo, o que é colocado na mesma pasta que sua. EXE, anular a serialização JSON e, em seguida, acessar a propriedade JSON (extendedProperties = > [propertyName] = > valor).

## <a name="compare-v2-v1"></a> Comparar a atividade personalizada v2 e a versão 1 (personalizado) atividade DotNet

No Azure Data Factory versão 1, implementa uma atividade de DotNet (personalizado) ao criar um projeto de biblioteca de classes do .NET com uma classe que implementa a `Execute` método da `IDotNetActivity` interface. Os serviços ligados, conjuntos de dados e propriedades expandidas no payload de JSON de uma atividade de DotNet (personalizado) são transmitidas para o método de execução, como objetos fortemente tipado. Para obter detalhes sobre o comportamento da versão 1, consulte [DotNet (personalizado) na versão 1](v1/data-factory-use-custom-activities.md). Devido a essa implementação, o seu código de atividade DotNet versão 1 deve visar o .NET Framework 4.5.2. A versão 1 a atividade DotNet também tem a ser executado em nós do Azure Batch Pool baseados em Windows.

Na Azure Data Factory V2 personalizado atividade, não tem de implementar uma interface de .NET. Pode agora executar diretamente comandos de scripts e seu próprio código personalizado, compilado como um executável. Para configurar esta implementação, especifique a `Command` propriedade em conjunto com o `folderPath` propriedade. A atividade personalizada carrega o executável e as respetivas dependências para `folderpath` e executa o comando para.

Os serviços ligados, conjuntos de dados (definidos na referenceObjects) e Propriedades estendidas definidas no payload de JSON de uma atividade personalizada pode ser acessada por seu executável como ficheiros JSON do Data Factory v2. Pode acessar as propriedades necessárias usando um serializador JSON, conforme mostrado no exemplo de código SampleApp.exe anterior.

Com as alterações introduzidas na atividade personalizada do Data Factory V2, pode escrever a lógica de código personalizado em seu idioma preferencial e executá-lo no Windows e sistemas de operativos Linux suportados pelo Azure Batch.

A tabela seguinte descreve as diferenças entre a atividade personalizada do Data Factory V2 e o Data Factory versão 1 (personalizado) atividade DotNet:


|Diferenças      | Atividade personalizada      | versão 1 (personalizado) atividade DotNet      |
| ---- | ---- | ---- |
|Como a lógica personalizada está definida      |Ao fornecer um executável      |Implementando uma DLL do .NET      |
|Ambiente de execução da lógica personalizada      |Windows ou Linux      |Windows (.NET Framework 4.5.2)      |
|Executar scripts      |Suporta a execução de scripts diretamente (por exemplo "cmd /c echo hello world" na VM do Windows)      |Requer a implementação na DLL do .NET      |
|Conjunto de dados necessário      |Opcional      |Necessário para encadear atividades e transmitem informações      |
|Transmitir informações de atividade a lógica personalizada      |Por meio de ReferenceObjects (LinkedServices e conjuntos de dados) e ExtendedProperties (propriedades personalizadas)      |Até ExtendedProperties (propriedades personalizadas), a entrada e conjuntos de dados de saída      |
|Obter as informações na lógica personalizada      |Analisa activity.json linkedServices.json e datasets.json armazenados na mesma pasta do executável      |Através do SDK do .NET (quadro 4.5.2 do .NET)      |
|Registo      |Escreve diretamente para STDOUT      |Implementar o agente de log na DLL do .NET      |


Se tiver o código .NET existente, escrito para uma versão 1 atividade de DotNet (personalizado), terá de modificar o código para que funcione com a versão atual da atividade personalizada. Atualize o seu código ao seguir estas diretrizes de alto nível:

  - Altere o projeto de uma biblioteca de classes do .NET para uma aplicação de consola.
  - Iniciar a aplicação com o `Main` método. O `Execute` método da `IDotNetActivity` interface já não é necessária.
  - Ler e analisar os serviços ligados, conjuntos de dados e atividades com um serializador JSON e não como objetos fortemente tipado. Passe os valores das propriedades necessárias para sua lógica de código personalizado principal. Consulte o código de SampleApp.exe anterior como exemplo.
  - O objeto Logger já não é suportado. Saída do seu executável pode ser impresso no console e é guardado stdout.txt.
  - O pacote Microsoft.Azure.Management.DataFactories NuGet já não é necessário.
  - Compilar seu código, carregar o executável e as respetivas dependências para o armazenamento do Azure e definir o caminho no `folderPath` propriedade.

Para obter um exemplo completo de como o exemplo de pipeline e a DLL de ponto-a-ponto descrito na versão do Data Factory 1 artigo [utilizar atividades personalizadas num pipeline do Azure Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) pode ser reescrito como uma atividade personalizada da fábrica de dados, consulte [ Exemplo de atividade personalizada de fábrica de dados](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFv2CustomActivitySample).

## <a name="auto-scaling-of-azure-batch"></a>Dimensionamento automático do Azure Batch
Também pode criar um conjunto do Azure Batch com **dimensionamento automático** funcionalidade. Por exemplo, pode criar um conjunto do batch do azure com VMs dedicadas 0 e uma fórmula de dimensionamento automático com base no número de tarefas pendentes.

A fórmula de exemplo aqui alcança o seguinte comportamento: Quando o conjunto for criado inicialmente, ele começa com 1 VM. Métrica de $PendingTasks define o número de tarefas em execução + Active Directory (em fila) Estado. A fórmula localiza o número médio de tarefas pendentes nos últimos 180 segundos e define TargetDedicated em conformidade. Ele garante que TargetDedicated nunca vai além de 25 VMs. Então, como novas tarefas submetidas, agrupamento automaticamente cresce e como tarefas são concluídas, as VMs se tornar um gratuito por um e o dimensionamento automático diminui essas VMs. startingNumberOfVMs e maxNumberofVMs pode ser ajustado às suas necessidades.

Fórmula de dimensionamento automático:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Ver [nós num conjunto do Azure Batch de computação de dimensionamento de automaticamente](../batch/batch-automatic-scaling.md) para obter detalhes.

Se o conjunto está a utilizar a predefinição [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), o serviço Batch pode demorar 15 a 30 minutos para preparar a VM antes de executar a atividade personalizada. Se o conjunto está a utilizar um autoScaleEvaluationInterval diferente, o serviço Batch pode demorar autoScaleEvaluationInterval + 10 minutos.

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras formas:

* [Atividade U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade do Hive](transform-data-using-hadoop-hive.md)
* [Atividade PIg](transform-data-using-hadoop-pig.md)
* [Atividade MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de transmissão em fluxo do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade de execução de lotes de Aprendizado de máquina](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
