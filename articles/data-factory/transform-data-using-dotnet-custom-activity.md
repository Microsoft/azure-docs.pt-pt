---
title: Usar atividades personalizadas em um pipeline
description: Saiba como criar atividades personalizadas e usá-las em um pipeline Azure Data Factory.
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
ms.openlocfilehash: d922100d2747822d5683c0f65b169327c2866318
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893880"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Utilizar atividades personalizadas num pipeline do Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-use-custom-activities.md)
> * [Versão atual](transform-data-using-dotnet-custom-activity.md)

Há dois tipos de atividades que você pode usar em um pipeline de Azure Data Factory.

- [Atividades de movimentação de dados](copy-activity-overview.md) para mover dados entre [armazenamentos de dados de origem e coletor com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
- [Atividades de transformação de dados](transform-data.md) para transformar dados usando serviços de computação, como Azure HDInsight, lote do azure e Azure Machine Learning.

Para mover dados de/para um armazenamento de dados que Data Factory não dá suporte a, ou transformar/processar dados de uma maneira que não tenha suporte do Data Factory, você pode criar uma **atividade personalizada** com sua própria lógica de movimentação de dados ou de transformação e usar a atividade em um pipeline. A atividade personalizada executa sua lógica de código personalizada em um pool de máquinas virtuais do **lote do Azure** .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Consulte os seguintes artigos se você for novo no serviço de lote do Azure:

* [Noções básicas do lote do Azure](../batch/batch-technical-overview.md) para obter uma visão geral do serviço de lote do Azure.
* Cmdlet [New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) para criar uma conta do lote do Azure (ou) [portal do Azure](../batch/batch-account-create-portal.md) para criar a conta do lote do Azure usando portal do Azure. Consulte o artigo [usando o PowerShell para gerenciar a conta do lote do Azure](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) para obter instruções detalhadas sobre como usar o cmdlet.
* Cmdlet [New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) para criar um pool do lote do Azure.

## <a name="azure-batch-linked-service"></a>Serviço vinculado do lote do Azure

O JSON a seguir define um exemplo de serviço vinculado do lote do Azure. Para obter detalhes, consulte [ambientes de computação com suporte pelo Azure data Factory](compute-linked-services.md)

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

 Para saber mais sobre o serviço vinculado do lote do Azure, consulte o artigo [Serviços vinculados de computação](compute-linked-services.md) .

## <a name="custom-activity"></a>Atividade personalizada

O trecho JSON a seguir define um pipeline com uma atividade personalizada simples. A definição da atividade tem uma referência ao serviço vinculado do lote do Azure.

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

Neste exemplo, o HelloWorld. exe é um aplicativo personalizado armazenado na pasta customactv2/HelloWorld da conta de armazenamento do Azure usada no resourceLinkedService. A atividade personalizada envia esse aplicativo personalizado para ser executado no lote do Azure. Você pode substituir o comando para qualquer aplicativo preferencial que possa ser executado no sistema operacional de destino dos nós do pool do lote do Azure.

A tabela a seguir descreve os nomes e as descrições das propriedades que são específicas para essa atividade.

| Propriedade              | Descrição                              | Obrigatório |
| :-------------------- | :--------------------------------------- | :------- |
| nome                  | Nome da atividade no pipeline     | Sim      |
| descrição           | Texto que descreve o que a atividade faz.  | Não       |
| tipo                  | Para atividade personalizada, o tipo de atividade é **personalizado**. | Sim      |
| linkedServiceName     | Serviço vinculado ao lote do Azure. Para saber mais sobre esse serviço vinculado, consulte o artigo [Serviços vinculados de computação](compute-linked-services.md) .  | Sim      |
| command               | Comando do aplicativo personalizado a ser executado. Se o aplicativo já estiver disponível no nó do pool do lote do Azure, resourceLinkedService e folderPath poderão ser ignorados. Por exemplo, você pode especificar o comando a ser `cmd /c dir`, que tem suporte nativo do nó do pool do lote do Windows. | Sim      |
| resourceLinkedService | Serviço vinculado do armazenamento do Azure para a conta de armazenamento na qual o aplicativo personalizado está armazenado | Não&#42;       |
| folderPath            | Caminho para a pasta do aplicativo personalizado e todas as suas dependências<br/><br/>Se você tiver dependências armazenadas em subpastas – ou seja, em uma estrutura de pasta hierárquica em *FolderPath* -a estrutura de pastas é atualmente achatada quando os arquivos são copiados para o lote do Azure. Ou seja, todos os arquivos são copiados em uma única pasta sem subpastas. Para contornar esse comportamento, considere compactar os arquivos, copiar o arquivo compactado e, em seguida, descompactá-los com o código personalizado no local desejado. | Não&#42;       |
| referenceObjects      | Uma matriz de serviços vinculados e conjuntos de os existentes. Os serviços vinculados e os conjuntos de aplicativos referenciados são passados para o aplicativo personalizado no formato JSON para que seu código personalizado possa referenciar recursos do Data Factory | Não       |
| extendedProperties    | Propriedades definidas pelo usuário que podem ser passadas para o aplicativo personalizado no formato JSON para que seu código personalizado possa referenciar propriedades adicionais | Não       |
| retentionTimeInDays | O tempo de retenção para os arquivos enviados para a atividade personalizada. O valor padrão é 30 dias. | Não |

&#42;As propriedades `resourceLinkedService` e `folderPath` devem ser especificadas ou ambas como omitidas.

> [!NOTE]
> Se você estiver passando serviços vinculados como referenceObjects na atividade personalizada, é uma boa prática de segurança passar um serviço vinculado Azure Key Vault habilitado (já que ele não contém nenhuma cadeia de caracteres segura) e buscar as credenciais usando o nome secreto diretamente da chave Cofre do código. Você pode encontrar um exemplo [aqui](https://github.com/nabhishek/customactivity_sample/tree/linkedservice) que faz referência ao serviço vinculado akv habilitado, recupera as credenciais de Key Vault e, em seguida, acessa o armazenamento no código.

## <a name="custom-activity-permissions"></a>Permissões de atividade personalizada

A atividade personalizada define a conta de usuário automático do lote do Azure para *acesso não administrador com escopo de tarefa* (a especificação de usuário automático padrão). Não é possível alterar o nível de permissão da conta de usuário automático. Para obter mais informações, consulte [executar tarefas em contas de usuário em lote | Contas de usuário automático](../batch/batch-user-accounts.md#auto-user-accounts).

## <a name="executing-commands"></a>Executando comandos

Você pode executar diretamente um comando usando a atividade personalizada. O exemplo a seguir executa o comando "echo Hello World" nos nós de destino do pool do lote do Azure e imprime a saída para stdout.

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

## <a name="passing-objects-and-properties"></a>Passando objetos e propriedades

Este exemplo mostra como você pode usar o referenceObjects e o extendedProperties para passar Data Factory objetos e propriedades definidas pelo usuário para seu aplicativo personalizado.

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

Quando a atividade é executada, referenceObjects e extendedProperties são armazenados nos seguintes arquivos que são implantados na mesma pasta de execução do SampleApp. exe:

- `activity.json`

  Armazena extendedProperties e propriedades da atividade personalizada.

- `linkedServices.json`

  Armazena uma matriz de serviços vinculados definidos na propriedade referenceObjects.

- `datasets.json`

  Armazena uma matriz de conjuntos de valores definidos na propriedade referenceObjects.

O código de exemplo a seguir demonstra como o SampleApp. exe pode acessar as informações necessárias de arquivos JSON:

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

Você pode iniciar uma execução de pipeline usando o seguinte comando do PowerShell:

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
```

Quando o pipeline estiver em execução, você poderá verificar a saída de execução usando os seguintes comandos:

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

O **stdout** e o **stderr** do seu aplicativo personalizado são salvos no contêiner **Adfjobs** no serviço vinculado do armazenamento do Azure que você definiu ao criar o serviço vinculado do lote do Azure com um GUID da tarefa. Você pode obter o caminho detalhado da saída da execução da atividade, conforme mostrado no trecho a seguir:

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

Se você quiser consumir o conteúdo de stdout. txt em atividades downstream, poderá obter o caminho para o arquivo stdout. txt na expressão "\@atividade (' MyCustomActivity '). saída. Outputs [0]".

> [!IMPORTANT]
> - O Activity. JSON, o linkedservices. JSON e o DataSets. JSON são armazenados na pasta de tempo de execução da tarefa do lote. Para este exemplo, o Activity. JSON, o linkedservices. JSON e o DataSets. JSON são armazenados no caminho `"https://adfv2storage.blob.core.windows.net/adfjobs/\<GUID>/runtime/"`. Se necessário, você precisa limpá-los separadamente.
> - Para serviços vinculados que usam o Integration Runtime auto-hospedado, as informações confidenciais, como chaves ou senhas, são criptografadas pelo Integration Runtime de hospedagem interna para garantir que a credencial permaneça no ambiente de rede privada definido pelo cliente. Alguns campos confidenciais podem estar ausentes quando referenciados pelo código do aplicativo personalizado dessa maneira. Use SecureString em extendedProperties em vez de usar a referência de serviço vinculada, se necessário.

## <a name="pass-outputs-to-another-activity"></a>Passar saídas para outra atividade

Você pode enviar valores personalizados do seu código em uma atividade personalizada de volta para Azure Data Factory. Você pode fazer isso gravando-os em `outputs.json` do seu aplicativo. Data Factory copia o conteúdo de `outputs.json` e o anexa à saída da atividade como o valor da propriedade `customOutput`. (O limite de tamanho é de 2MB.) Se você quiser consumir o conteúdo de `outputs.json` em atividades de downstream, poderá obter o valor usando a expressão `@activity('<MyCustomActivity>').output.customOutput`.

## <a name="retrieve-securestring-outputs"></a>Recuperar saídas de SecureString

Valores de propriedade confidenciais designados como tipo *SecureString*, conforme mostrado em alguns dos exemplos neste artigo, são mascarados na guia monitoramento na interface do usuário data Factory.  Na execução de pipeline real, no entanto, uma propriedade *SecureString* é serializada como JSON dentro do arquivo de `activity.json` como texto sem formatação. Por exemplo:

```json
"extendedProperties": {
  "connectionString": {
    "type": "SecureString",
    "value": "aSampleSecureString"
  }
}
```

Essa serialização não é verdadeiramente segura e não se destina a ser segura. A intenção é se dedicar a Data Factory para mascarar o valor na guia monitoramento.

Para acessar propriedades do tipo *SecureString* de uma atividade personalizada, leia o arquivo `activity.json`, que é colocado na mesma pasta que o seu. EXE, desserializar o JSON e, em seguida, acessar a propriedade JSON (extendedProperties = > [propertyName] = > valor).

## <a name="compare-v2-v1"></a>Comparar a atividade personalizada V2 e a atividade DotNet da versão 1 (personalizada)

No Azure Data Factory versão 1, você implementa uma atividade DotNet (personalizada) criando um projeto de biblioteca de classes .NET com uma classe que implementa o método `Execute` da interface `IDotNetActivity`. Os serviços vinculados, conjuntos de valores e propriedades estendidas no conteúdo JSON de uma atividade DotNet (personalizada) são passados para o método de execução como objetos fortemente tipados. Para obter detalhes sobre o comportamento da versão 1, consulte [dotNet (personalizado) na versão 1](v1/data-factory-use-custom-activities.md). Devido a essa implementação, seu código de atividade DotNet versão 1 tem que direcionar .NET Framework 4.5.2. A atividade DotNet da versão 1 também precisa ser executada em nós do pool do lote do Azure baseados no Windows.

Na atividade personalizada do Azure Data Factory v2, não é necessário implementar uma interface .NET. Agora você pode executar diretamente comandos, scripts e seu próprio código personalizado, compilado como um executável. Para configurar essa implementação, você especifica a propriedade `Command` junto com a propriedade `folderPath`. A atividade personalizada carrega o executável e suas dependências para `folderpath` e executa o comando para você.

Os serviços vinculados, conjuntos de valores (definidos em referenceObjects) e propriedades estendidas definidas no conteúdo JSON de uma atividade personalizada Data Factory v2 podem ser acessados por seu executável como arquivos JSON. Você pode acessar as propriedades necessárias usando um serializador JSON, conforme mostrado no exemplo de código SampleApp. exe anterior.

Com as alterações introduzidas na atividade personalizada do Data Factory v2, você pode escrever sua lógica de código personalizado em sua linguagem preferida e executá-la em sistemas operacionais Windows e Linux com suporte do lote do Azure.

A tabela a seguir descreve as diferenças entre a atividade personalizada do Data Factory V2 e a atividade DotNet do Data Factory versão 1 (personalizada):

|Pequenas      | Atividade Personalizada      | Atividade de DotNet da versão 1 (personalizada)      |
| ---- | ---- | ---- |
|Como a lógica personalizada é definida      |Fornecendo um executável      |Implementando uma DLL do .NET      |
|Ambiente de execução da lógica personalizada      |Windows ou Linux      |Windows (.NET Framework 4.5.2)      |
|Executando scripts      |Oferece suporte à execução de scripts diretamente (por exemplo, "cmd/c echo Olá, mundo" na VM do Windows)      |Requer implementação na DLL do .NET      |
|DataSet necessário      |Opcional      |Necessário para encadear atividades e passar informações      |
|Passar informações de atividade para lógica personalizada      |Por meio de ReferenceObjects (Linkedservices e DataSets) e ExtendedProperties (Propriedades personalizadas)      |Por meio de ExtendedProperties (Propriedades personalizadas), entrada e conjuntos de dados de saída      |
|Recuperar informações na lógica personalizada      |Analisa o Activity. JSON, o linkedservices. JSON e o DataSets. JSON armazenados na mesma pasta do executável      |Por meio do SDK do .NET (.NET frame 4.5.2)      |
|Registo      |Grava diretamente em STDOUT      |Implementando o agente de log na DLL do .NET      |

Se você tiver um código .NET escrito para uma atividade DotNet da versão 1 (personalizada), precisará modificar seu código para que ele funcione com a versão atual da atividade personalizada. Atualize seu código seguindo estas diretrizes de alto nível:

  - Altere o projeto de uma biblioteca de classes .NET para um aplicativo de console.
  - Inicie seu aplicativo com o método `Main`. O método `Execute` da interface `IDotNetActivity` não é mais necessário.
  - Leia e analise os serviços vinculados, os conjuntos de valores e a atividade com um serializador JSON e não como objetos fortemente tipados. Passe os valores das propriedades obrigatórias para sua lógica de código personalizado principal. Consulte o código anterior SampleApp. exe como exemplo.
  - Não há mais suporte para o objeto Logger. A saída do executável pode ser impressa no console e salva em stdout. txt.
  - O pacote NuGet Microsoft. Azure. Management. datafactors não é mais necessário.
  - Compile seu código, carregue o executável e suas dependências no armazenamento do Azure e defina o caminho na propriedade `folderPath`.

Para obter um exemplo completo de como a DLL de ponta a ponta e o exemplo de pipeline descritos no artigo Data Factory versão 1 [usam atividades personalizadas em um pipeline Azure data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) podem ser reescritos como uma atividade personalizada data Factory, consulte [Data Factory exemplo de atividade personalizada](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFv2CustomActivitySample).

## <a name="auto-scaling-of-azure-batch"></a>Dimensionamento automático do lote do Azure

Você também pode criar um pool do lote do Azure com o recurso de **dimensionamento automático** . Por exemplo, você pode criar um pool do lote do Azure com 0 VMs dedicadas e uma fórmula de dimensionamento automático com base no número de tarefas pendentes.

A fórmula de exemplo obtém o seguinte comportamento: quando o pool é inicialmente criado, ele começa com 1 VM. $PendingTasks métrica define o número de tarefas no estado executando + ativo (na fila). A fórmula localiza o número médio de tarefas pendentes nos últimos 180 segundos e define TargetDedicated de acordo. Ele garante que o TargetDedicated nunca vá além de 25 VMs. Assim, à medida que novas tarefas são enviadas, o pool aumenta automaticamente e, à medida que as tarefas são concluídas, as VMs tornam-se gratuitas uma a uma e o dimensionamento automático reduz essas VMs. startingNumberOfVMs e maxNumberofVMs podem ser ajustados para suas necessidades.

Fórmula de autoescala:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Consulte [dimensionar automaticamente nós de computação em um pool do lote do Azure](../batch/batch-automatic-scaling.md) para obter detalhes.

Se o pool estiver usando o [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)padrão, o serviço de lote poderá levar de 15-30 minutos para preparar a VM antes de executar a atividade personalizada. Se o pool estiver usando um autoScaleEvaluationInterval diferente, o serviço de lote poderá levar autoScaleEvaluationInterval + 10 minutos.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras:

* [Atividade de U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade do hive](transform-data-using-hadoop-hive.md)
* [Atividade Pig](transform-data-using-hadoop-pig.md)
* [Atividade MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade de execução de Machine Learning lote](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
