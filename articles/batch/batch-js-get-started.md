---
title: Utilize a biblioteca de clientes Azure Batch para JavaScript
description: Aprenda os conceitos básicos de Azure Batch e construa uma solução simples usando JavaScript.
ms.topic: how-to
ms.date: 01/01/2021
ms.openlocfilehash: 1f53c793e4c676df319d46f30595ac330846dfa5
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231484"
---
# <a name="get-started-with-batch-sdk-for-javascript"></a>Começar com Batch SDK para JavaScript

Aprenda o básico de construir um cliente Batch em JavaScript usando [Azure Batch JavaScript SDK](https://github.com/Azure/azure-sdk-for-js/). Damos um passo a passo para entender um cenário para uma aplicação de lote e, em seguida, configurando-o usando JavaScript.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que você tem um conhecimento funcional de JavaScript e familiaridade com Linux. Também parte do princípio de que tem uma conta do Azure configurada com direitos de acesso para criar serviços do Batch e de Armazenamento.

Antes de seguir os passos descritos neste artigo, recomendamos ler a [Descrição Geral Técnica do Azure Batch](batch-technical-overview.md).

## <a name="understand-the-scenario"></a>Compreender o cenário

Aqui, temos um simples script escrito em Python que descarrega todos os ficheiros csv de um recipiente de armazenamento Azure Blob e os converte para JSON. Para processar em paralelo vários contentores da conta de armazenamento, podemos implementar o script como uma tarefa do Azure Batch.

## <a name="azure-batch-architecture"></a>Arquitetura Azure Batch

O diagrama que se segue mostra como podemos escalar o script Python usando Azure Batch e um cliente.

![Diagrama mostrando arquitetura de cenário.](./media/batch-js-get-started/batch-scenario.png)

A amostra JavaScript implementa um trabalho de lote com uma tarefa de preparação (explicada em detalhe mais tarde) e um conjunto de tarefas dependendo do número de contentores na conta de armazenamento. Você pode baixar os scripts do repositório GitHub.

- [Código de Exemplo](https://github.com/Azure-Samples/azure-batch-samples/blob/master/JavaScript/Node.js/sample.js)
- [Scripts de shell da tarefa de preparação](https://github.com/Azure-Samples/azure-batch-samples/blob/master/JavaScript/Node.js/startup_prereq.sh)
- [Processador de csv em JSON em Python](https://github.com/Azure-Samples/azure-batch-samples/blob/master/JavaScript/Node.js/processcsv.py)

> [!TIP]
> A amostra JavaScript no link especificado não contém código específico para ser implantado como uma aplicação de função Azure. Para saber como criar uma aplicação destas, pode ver as ligações seguintes:
> - [Criar a aplicação de funções](../azure-functions/functions-get-started.md)
> - [Create timer trigger function](../azure-functions/functions-bindings-timer.md) (Criar função de acionador de temporizador)

## <a name="build-the-application"></a>Criar a aplicação

Agora, vamos seguir o processo passo a passo para construir o cliente JavaScript:

### <a name="step-1-install-azure-batch-sdk"></a>Passo 1: Instalar o SDK do Azure Batch

Pode instalar O Azure Batch SDK para JavaScript utilizando o comando de instalação npm.

`npm install azure-batch`

Este comando instala a versão mais recente do azure-batch JavaScript SDK.

>[!Tip]
> Numa aplicação de função das Funções do Azure, pode aceder à “Consola Kudu”, no separador Definições da aplicação das Funções do Azure, para executar os comandos npm install. Neste caso, instalar O Azure Batch SDK para o JavaScript.

### <a name="step-2-create-an-azure-batch-account"></a>Passo 2: Criar uma conta do Azure Batch

Pode criá-lo a partir do [portal Azure](batch-account-create-portal.md) ou a partir da linha de comando[(PowerShell](batch-powershell-cmdlets-get-started.md)  / [Azure CLI).](/cli/azure)

Seguem-se os comandos para criar a conta com a CLI do Azure.

Crie um Grupo de Recursos; ignore este passo se já tiver um no qual queira criar a conta do Batch:

`az group create -n "<resource-group-name>" -l "<location>"`

Depois, crie uma conta do Azure Batch.

`az batch account create -l "<location>"  -g "<resource-group-name>" -n "<batch-account-name>"`

Cada conta do Batch tem chaves de acesso correspondentes. Estas chaves são necessárias para criar mais recursos nessas contas. Uma boa prática para ambientes de produção consiste em armazenar estas chaves no Azure Key Vault. Depois, pode criar um Principal de serviço para a aplicação. Ao utilizar este principal de serviço, a aplicação pode criar um token OAuth para aceder às chaves no cofre de chaves.

`az batch account keys list -g "<resource-group-name>" -n "<batch-account-name>"`

Copie e armazene a chave, para utilizá-la nos passos subsequentes.

### <a name="step-3-create-an-azure-batch-service-client"></a>Passo 3: Criar um cliente do serviço Azure Batch

Seguindo o código snippet primeiro importa o módulo JavaScript de lote de azul e, em seguida, cria um cliente de Serviço de Lote. Em primeiro lugar, tem de criar um objeto SharedKeyCredentials com a chave da conta do Batch copiada do passo anterior.

```javascript
// Initializing Azure Batch variables

var batch = require('azure-batch');

var accountName = '<azure-batch-account-name>';

var accountKey = '<account-key-downloaded>';

var accountUrl = '<account-url>'

// Create Batch credentials object using account name and account key

var credentials = new batch.SharedKeyCredentials(accountName,accountKey);

// Create Batch service client

var batch_client = new batch.ServiceClient(credentials,accountUrl);

```

O URI do Azure Batch está disponível no separador Descrição Geral do portal do Azure. Tem o formato seguinte:

`https://accountname.location.batch.azure.com`

Veja a captura de ecrã:

![URI do Azure Batch](./media/batch-js-get-started/batch-uri.png)

### <a name="step-4-create-an-azure-batch-pool"></a>Passo 4: Criar um conjunto do Azure Batch

Os conjuntos do Azure Batch consistem em várias VMs (também conhecidas como Nós do Batch). O serviço do Azure Batch implementa a tarefa nestes nós e gere-as. Pode definir os seguintes parâmetros de configuração para o conjunto.

- Tipo de imagem das Máquinas Virtuais
- Tamanho dos nós de Máquinas Virtuais
- Número de nós de Máquinas Virtuais

> [!TIP]
> O tamanho e o número dos nós de máquinas virtuais depende muito do número de tarefas que quer executar em paralelo e também das próprias tarefas. Para determinar o tamanho e o número ideais, recomendamos que faça testes.

O fragmento de código seguinte cria os objetos do parâmetro de configuração.

```javascript
// Creating Image reference configuration for Ubuntu Linux VM
var imgRef = {publisher:"Canonical",offer:"UbuntuServer",sku:"14.04.2-LTS",version:"latest"}

// Creating the VM configuration object with the SKUID
var vmconfig = {imageReference:imgRef,nodeAgentSKUId:"batch.node.ubuntu 14.04"}

// Setting the VM size to Standard F4
var vmSize = "STANDARD_F4"

//Setting number of VMs in the pool to 4
var numVMs = 4
```

> [!TIP]
> Para obter a lista de imagens de VMs do Linux disponíveis para o Azure Batch e IDs dos respetivos SKUs, veja [List of virtual machine images](batch-linux-nodes.md#list-of-virtual-machine-images) (Lista de imagens de máquinas virtuais).

Depois de definida a configuração do conjunto, pode criar o conjunto do Azure Batch. O comando Batch pool cria os nós de Máquinas Virtuais e prepara-os para receberem tarefas a executar. Cada conjunto deve ter um ID único para referência em passos subsequentes.

O fragmento de código seguinte cria um conjunto do Azure Batch.

```javascript
// Create a unique Azure Batch pool ID
var poolid = "pool" + customerDetails.customerid;
var poolConfig = {id:poolid, displayName:poolid,vmSize:vmSize,virtualMachineConfiguration:vmconfig,targetDedicatedComputeNodes:numVms,enableAutoScale:false };
// Creating the Pool for the specific customer
var pool = batch_client.pool.add(poolConfig,function(error,result){
    if(error!=null){console.log(error.response)};
});
```

Pode verificar o estado do conjunto criado e garantir que o estado está “ativo” antes de avançar para a submissão de uma Tarefa para o mesmo.

```javascript
var cloudPool = batch_client.pool.get(poolid,function(error,result,request,response){
        if(error == null)
        {

            if(result.state == "active")
            {
                console.log("Pool is active");
            }
        }
        else
        {
            if(error.statusCode==404)
            {
                console.log("Pool not found yet returned 404...");

            }
            else
            {
                console.log("Error occurred while retrieving pool data");
            }
        }
        });
```

Segue-se um objeto de resultado de exemplo devolvido pela função pool.get.

```
{ id: 'processcsv_201721152',
  displayName: 'processcsv_201721152',
  url: 'https://<batch-account-name>.centralus.batch.azure.com/pools/processcsv_201721152',
  eTag: '<eTag>',
  lastModified: 2017-03-27T10:28:02.398Z,
  creationTime: 2017-03-27T10:28:02.398Z,
  state: 'active',
  stateTransitionTime: 2017-03-27T10:28:02.398Z,
  allocationState: 'resizing',
  allocationStateTransitionTime: 2017-03-27T10:28:02.398Z,
  vmSize: 'standard_a1',
  virtualMachineConfiguration:
   { imageReference:
      { publisher: 'Canonical',
        offer: 'UbuntuServer',
        sku: '14.04.2-LTS',
        version: 'latest' },
     nodeAgentSKUId: 'batch.node.ubuntu 14.04' },
  resizeTimeout:
   { [Number: 900000]
     _milliseconds: 900000,
     _days: 0,
     _months: 0,
     _data:
      { milliseconds: 0,
        seconds: 0,
        minutes: 15,
        hours: 0,
        days: 0,
        months: 0,
        years: 0 },
     _locale:
      Locale {
        _calendar: [Object],
        _longDateFormat: [Object],
        _invalidDate: 'Invalid date',
        ordinal: [Function: ordinal],
        _ordinalParse: /\d{1,2}(th|st|nd|rd)/,
        _relativeTime: [Object],
        _months: [Object],
        _monthsShort: [Object],
        _week: [Object],
        _weekdays: [Object],
        _weekdaysMin: [Object],
        _weekdaysShort: [Object],
        _meridiemParse: /[ap]\.?m?\.?/i,
        _abbr: 'en',
        _config: [Object],
        _ordinalParseLenient: /\d{1,2}(th|st|nd|rd)|\d{1,2}/ } },
  currentDedicated: 0,
  targetDedicated: 4,
  enableAutoScale: false,
  enableInterNodeCommunication: false,
  taskSlotsPerNode: 1,
  taskSchedulingPolicy: { nodeFillType: 'Spread' } }
```

### <a name="step-4-submit-an-azure-batch-job"></a>Passo 4: Submeter uma tarefa do Azure Batch

As tarefas do Azure Batch são grupos lógicos de tarefas semelhantes. No nosso cenário, é "Processar csv para JSON". Cada tarefa neste cenário pode processar ficheiros csv presentes em cada contentor do Armazenamento do Azure.

Estas tarefas serão executadas em paralelo e implementadas em vários nós, orquestradas pelo serviço Azure Batch.

> [!TIP]
> Pode utilizar a [propriedade TaskSlotsPerNode](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) para especificar o número máximo de tarefas que podem ser executadas simultaneamente num único nó.

#### <a name="preparation-task"></a>Tarefa de preparação

Os nós de VMs criados são nós Ubuntu vazios. Muitas vezes, tem de instalar um conjunto de programas como pré-requisitos.
Normalmente, relativamente a nós do Linux, pode ser um script de shell que instala os pré-requisitos antes de a tarefa em si ser executada. No entanto, pode ser qualquer executável programável.

O [script de shell](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/startup_prereq.sh) neste exemplo instala Python-pip e o SDK do Armazenamento do Azure para Python.

Pode carregar o script numa conta de Armazenamento do Azure e gerar um URI de SAS para aceder ao mesmo. Este processo também pode ser automatizado usando o Azure Storage JavaScript SDK.

> [!TIP]
> As tarefas de preparação para tarefas só são executadas nos nós de VMs, nos quais essas tarefas específicas têm de ser executadas. Se quiser instalar pré-requisitos em todos os nós, independentemente das tarefas que são executadas nos mesmos, pode utilizar a propriedade [startTask](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) enquanto estiver a adicionar um conjunto. Pode utilizar a definição de tarefa de preparação seguinte como referência.

As tarefas de preparação são especificadas durante a submissão do trabalho do Azure Batch. Seguem-se todos os parâmetros de configuração da tarefa de preparação:

- **ID**: um identificador exclusivo para a tarefa de preparação
- **commandLine**: a linha de comandos para executar o executável da tarefa
- **resourceFiles**: matriz de objetos que disponibiliza detalhes dos ficheiros que têm de ser transferidos para que esta tarefa seja executada.  Seguem-se as respetivas opções
  - blobSource: o URI de SAS do ficheiro
  - filePath: o caminho local para transferir e guardar o ficheiro
  - fileMode: aplicável apenas a nós do Linux, o fileMode está no formato octal e tem o valor predefinido 0770
- **waitForSuccess**: se definido como verdadeiro, a tarefa não é executada em falhas da tarefa de preparação
- **runElevated**: defina como verdadeiro, se forem necessários privilégios elevados para executar a tarefa.

O fragmento de código seguinte mostra o exemplo de configuração de script de tarefa de preparação:

```javascript
var job_prep_task_config = {id:"installprereq",commandLine:"sudo sh startup_prereq.sh > startup.log",resourceFiles:[{'blobSource':'Blob SAS URI','filePath':'startup_prereq.sh'}],waitForSuccess:true,runElevated:true}
```

Se não for necessário instalar pré-requisitos para que as tarefas sejam executadas, pode ignorar as tarefas de preparação. O código seguinte cria um trabalho com o nome a apresentar “process csv files."

 ```javascript
 // Setting up Batch pool configuration
 var pool_config = {poolId:poolid}
 // Setting up Job configuration along with preparation task
 var jobId = "processcsvjob"
 var job_config = {id:jobId,displayName:"process csv files",jobPreparationTask:job_prep_task_config,poolInfo:pool_config}
 // Adding Azure batch job to the pool
 var job = batch_client.job.add(job_config,function(error,result){
     if(error != null)
     {
         console.log("Error submitting job : " + error.response);
     }});
```

### <a name="step-5-submit-azure-batch-tasks-for-a-job"></a>Passo 5: Submeter tarefas do Azure Batch para trabalhos

Agora que o trabalho “process csv” está criado, vamos criar tarefas para o mesmo. Partindo do princípio de que temos quatro contentores, temos de criar quatro tarefas, uma para cada contentor.

Se olharmos para o [script de Python](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/processcsv.py), este aceita dois parâmetros:

- nome do contentor: o contentor do Armazenamento a partir do qual transferir ficheiros
- padrão: um parâmetro opcional do padrão de nomenclatura do ficheiro

Partindo do princípio de que temos quatro contentores "con1", "con2", "con3" e "con4", o código seguinte mostra a submissão relativa a tarefas para o trabalho do Azure Batch “process csv” que criámos anteriormente.

```javascript
// storing container names in an array
var container_list = ["con1","con2","con3","con4"]
    container_list.forEach(function(val,index){

           var container_name = val;
           var taskID = container_name + "_process";
           var task_config = {id:taskID,displayName:'process csv in ' + container_name,commandLine:'python processcsv.py --container ' + container_name,resourceFiles:[{'blobSource':'<blob SAS URI>','filePath':'processcsv.py'}]}
           var task = batch_client.task.add(poolid,task_config,function(error,result){
                if(error != null)
                {
                    console.log(error.response);
                }
                else
                {
                    console.log("Task for container : " + container_name + "submitted successfully");
                }



           });

    });
```

O código adiciona várias tarefas ao conjunto e cada uma das tarefas é executada num nó no conjunto de VMs criado. Se o número de tarefas exceder o número de VMs numa piscina ou na propriedade TaskSlotsPerNode, as tarefas aguardam até que um nó seja disponibilizado. Esta orquestração é processada pelo Azure Batch automaticamente.

O portal tem vistas detalhadas sobre as tarefas e os estados dos trabalhos. Também pode utilizar a lista e obter funções no Azure JavaScript SDK. Estão disponíveis detalhes na [ligação](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Job.html) da documentação.

## <a name="next-steps"></a>Passos seguintes

- Conheça o fluxo de trabalho do [serviço Batch e os recursos primários,](batch-service-workflow-features.md) tais como piscinas, nós, empregos e tarefas.
- Consulte a [referência De Lote JavaScript](/javascript/api/overview/azure/batch) para explorar a API do lote.