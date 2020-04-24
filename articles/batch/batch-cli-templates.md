---
title: Executar empregos de ponta a ponta usando modelos
description: Com apenas comandos CLI, pode criar um pool, carregar dados de entrada, criar empregos e tarefas associadas, e descarregar os dados de saída resultantes.
ms.topic: article
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 634a0b66379d8c94988d5f974baffe475af94c2e
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117357"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>Utilize modelos CLI de lote azure e transferência de ficheiros

Utilizando uma extensão do Lote Azure para o Azure CLI, é possível executar trabalhos de lote sem código de escrita.

Crie e use ficheiros de modelo JSON com o Azure CLI para criar piscinas, empregos e tarefas de Lote. Utilize comandos de extensão CLI para enviar facilmente ficheiros de entrada de trabalho para a conta de armazenamento associada à conta Batch e descarregar ficheiros de saída de trabalho.

## <a name="overview"></a>Descrição geral

Uma extensão do ClI Azure permite que o Lote seja utilizado de ponta a ponta por utilizadores que não sejam desenvolvedores. Com apenas comandos CLI, pode criar um pool, carregar dados de entrada, criar empregos e tarefas associadas, e descarregar os dados de saída resultantes. Não é necessário nenhum código adicional. Executar os comandos CLI diretamente ou integrá-los em scripts.

Os modelos de lote baseiam-se no suporte do Lote existente no [Azure CLI](batch-cli-get-started.md#json-files-for-resource-creation) para ficheiros JSON para especificar valores de propriedade ao criar piscinas, empregos, tarefas e outros itens. Os modelos de lote adicionam as seguintes capacidades:

-   Os parâmetros podem ser definidos. Quando o modelo é utilizado, apenas os valores do parâmetro são especificados para criar o item, com outros valores de propriedade do item especificados no corpo do modelo. Um utilizador que compreenda o Batch e as aplicações a serem executadas pelo Batch pode criar modelos, especificando valores de pool, job e task property. Um utilizador menos familiarizado com o Lote e/ou as aplicações apenas precisa especificar os valores para os parâmetros definidos.

-   As fábricas de tarefas de trabalho criam uma ou mais tarefas associadas a um trabalho, evitando a necessidade de criar muitas definições de tarefas e simplificar significativamente a submissão de emprego.


Os trabalhos normalmente usam ficheiros de dados de entrada e produzem ficheiros de dados de saída. Uma conta de armazenamento está associada, por padrão, a cada conta de Lote. Transfira ficheiros de e para esta conta de armazenamento utilizando o CLI, sem codificação e sem credenciais de armazenamento.

Por exemplo, [a ffmpeg](https://ffmpeg.org/) é uma aplicação popular que processa ficheiros de áudio e vídeo. Aqui estão os passos com o Azure Batch CLI para invocar a ffmpeg para transcodificar ficheiros de vídeo de origem para diferentes resoluções.

-   Crie um modelo de piscina. O utilizador que cria o modelo sabe como chamar a aplicação ffmpeg e os seus requisitos; especificam o tamanho de OS, VM adequado, como a ffmpeg é instalada (a partir de um pacote de aplicação ou utilizando um gestor de pacotes, por exemplo), e outros valores de propriedade da piscina. Os parâmetros são criados para que quando o modelo é usado, apenas o ID da piscina e o número de VMs precisam ser especificados.

-   Crie um modelo de trabalho. O utilizador que cria o modelo sabe como é que o ffmpeg precisa de ser invocado para transcodificar o vídeo de origem para uma resolução diferente e especifica a linha de comando de tarefas; também sabem que existe uma pasta que contém os ficheiros de vídeo de origem, com uma tarefa exigida por ficheiro de entrada.

-   Um utilizador final com um conjunto de ficheiros de vídeo para transcodificar primeiro cria uma piscina usando o modelo de piscina, especificando apenas o ID da piscina e o número de VMs necessários. Podem então enviar os ficheiros de origem para transcodificar. Um trabalho pode então ser submetido usando o modelo de trabalho, especificando apenas o ID do pool e a localização dos ficheiros de origem enviados. O trabalho do Lote é criado, com uma tarefa por ficheiro de entrada a ser gerada. Finalmente, os ficheiros de saída transcodificados podem ser descarregados.

## <a name="installation"></a>Instalação

Para instalar a extensão CLI do lote Azure, primeiro [instale o Azure CLI 2.0](/cli/azure/install-azure-cli), ou executar o Azure CLI em [Azure Cloud Shell](../cloud-shell/overview.md).

Instale a versão mais recente da extensão do Lote utilizando o seguinte comando Azure CLI:

```azurecli
az extension add --name azure-batch-cli-extensions
```

Para obter mais informações sobre a extensão do Lote CLI e opções adicionais de instalação, consulte o [repo GitHub](https://github.com/Azure/azure-batch-cli-extensions).


Para utilizar as funcionalidades de extensão CLI, necessita de uma conta Azure Batch e, para os comandos que transferem ficheiros de e para o armazenamento, uma conta de armazenamento ligada.

Para iniciar sessão numa conta de Lote com o Azure CLI, consulte [gerir os recursos do Lote com o Azure CLI](batch-cli-get-started.md).

## <a name="templates"></a>Modelos

Os modelos de Lote Azure são semelhantes aos modelos do Gestor de Recursos Azure, em funcionalidade e sintaxe. São ficheiros JSON que contêm nomes e valores de propriedade de item, mas adicionam os seguintes conceitos principais:

-   **Parâmetros**

    -   Permitir que os valores de propriedade sejam especificados numa secção do corpo, com apenas valores de parâmetros a necessitar em fornecer quando o modelo for utilizado. Por exemplo, a definição completa para uma piscina poderia ser `poolId`colocada no corpo e apenas um parâmetro definido para; apenas uma cadeia de ID de piscina precisa, portanto, ser fornecida para criar uma piscina.
        
    -   O corpo do modelo pode ser da autoria de alguém com conhecimento de Batch e as aplicações a serem executadas por Batch; só devem ser fornecidos valores para os parâmetros definidos pelo autor quando o modelo é utilizado. Um utilizador sem o lote aprofundado e/ou conhecimento de aplicação pode, portanto, utilizar os modelos.

-   **Variáveis**

    -   Permita especificar valores simples ou complexos de parâmetros num só local e utilizados num ou mais lugares no corpo do modelo. As variáveis podem simplificar e reduzir o tamanho do modelo, bem como torná-lo mais manejável por ter um local para mudar de propriedade.

-   **Construções de nível superior**

    -   Algumas construções de nível superior estão disponíveis no modelo que ainda não estão disponíveis nas APIs do Lote. Por exemplo, uma fábrica de tarefas pode ser definida num modelo de trabalho que cria múltiplas tarefas para o trabalho, utilizando uma definição de tarefa comum. Estas construções evitam a necessidade de codificar para criar dinamicamente vários ficheiros JSON, como um ficheiro por tarefa, bem como criar ficheiros script para instalar aplicações através de um gestor de pacotes.

    -   Em algum momento, estas construções podem ser adicionadas ao serviço Batch e disponíveis nas APIs, UIs, etc.

### <a name="pool-templates"></a>Modelos de piscina

Os modelos de piscina suportam as capacidades padrão do modelo de parâmetros e variáveis. Também apoiam a seguinte construção de alto nível:

-   **Referências de pacotes**

    -   Opcionalmente permite que o software seja copiado para piscina de nódosos usando gestores de pacotes. O gestor do pacote e o ID do pacote são especificados. Ao declarar um ou mais pacotes, evite criar um script que obtenha os pacotes necessários, instalando o script e executando o script em cada nó da piscina.

Segue-se um exemplo de um modelo que cria uma piscina de VMs Linux com ffmpeg instalado. Para usá-lo, forneça apenas uma cadeia de identificação de piscina e o número de VMs na piscina:

```json
{
    "parameters": {
        "nodeCount": {
            "type": "int",
            "metadata": {
                "description": "The number of pool nodes"
            }
        },
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The pool ID "
            }
        }
    },
    "pool": {
        "type": "Microsoft.Batch/batchAccounts/pools",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('poolId')]",
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "Canonical",
                    "offer": "UbuntuServer",
                    "sku": "16.04-LTS",
                    "version": "latest"
                },
                "nodeAgentSKUId": "batch.node.ubuntu 16.04"
            },
            "vmSize": "STANDARD_D3_V2",
            "targetDedicatedNodes": "[parameters('nodeCount')]",
            "enableAutoScale": false,
            "maxTasksPerNode": 1,
            "packageReferences": [
                {
                    "type": "aptPackage",
                    "id": "ffmpeg"
                }
            ]
        }
    }
}
```

Se o ficheiro do modelo foi nomeado _pool-ffmpeg.json,_ então invoque o modelo da seguinte forma:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

O CLI pede-lhe que `poolId` forneça `nodeCount` valores para os e parâmetros. Também pode fornecer os parâmetros num ficheiro JSON. Por exemplo:

```json
{
  "poolId": {
    "value": "mypool"
  },
  "nodeCount": {
    "value": 2
  }
}
```

Se o ficheiro JSON dos parâmetros foi nomeado *paramímetros de piscina.json,* então invoque o modelo da seguinte forma:

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>Modelos de trabalho

Os modelos de trabalho suportam as capacidades padrão do modelo de parâmetros e variáveis. Também apoiam a seguinte construção de alto nível:

-   **Fábrica de tarefas**

    -   Cria múltiplas tarefas para um trabalho a partir de uma definição de tarefa. São suportados três tipos de fábrica de tarefas – varredura paramétrica, tarefa por ficheiro e recolha de tarefas.

Segue-se um exemplo de um modelo que cria um trabalho para transcodificar ficheiros de vídeo MP4 com ffmpeg para uma de duas resoluções inferiores. Cria uma tarefa por ficheiro de vídeo de origem. Consulte [os grupos de ficheiros e](#file-groups-and-file-transfer) a transferência de ficheiros para mais informações sobre grupos de ficheiros para entrada e saída de emprego.

```json
{
    "parameters": {
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch pool which runs the job"
            }
        },
        "jobId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch job"
            }
        },
        "resolution": {
            "type": "string",
            "defaultValue": "428x240",
            "allowedValues": [
                "428x240",
                "854x480"
            ],
            "metadata": {
                "description": "Target video resolution"
            }
        }
    },
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('jobId')]",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "[parameters('poolId')]"
            },
            "taskFactory": {
                "type": "taskPerFile",
                "source": { 
                    "fileGroup": "ffmpeg-input"
                },
                "repeatTask": {
                    "commandLine": "ffmpeg -i {fileName} -y -s [parameters('resolution')] -strict -2 {fileNameWithoutExtension}_[parameters('resolution')].mp4",
                    "resourceFiles": [
                        {
                            "blobSource": "{url}",
                            "filePath": "{fileName}"
                        }
                    ],
                    "outputFiles": [
                        {
                            "filePattern": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                            "destination": {
                                "autoStorage": {
                                    "path": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                                    "fileGroup": "ffmpeg-output"
                                }
                            },
                            "uploadOptions": {
                                "uploadCondition": "TaskSuccess"
                            }
                        }
                    ]
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Se o ficheiro do modelo foi nomeado _job-ffmpeg.json,_ então invoque o modelo da seguinte forma:

```azurecli
az batch job create --template job-ffmpeg.json
```

Como antes, o CLI pede-lhe que forneça valores para os parâmetros. Também pode fornecer os parâmetros num ficheiro JSON.

### <a name="use-templates-in-batch-explorer"></a>Usar modelos no Batch Explorer

Pode enviar um modelo DE CLI de lote para a aplicação de ambiente de trabalho [do Batch Explorer](https://github.com/Azure/BatchExplorer) (anteriormente chamada BatchLabs) para criar uma piscina ou trabalho de Lote. Também pode selecionar a partir de modelos de piscina e de trabalho predefinidos na Galeria do Explorador de Lote.

Para fazer upload de um modelo:

1. No Batch Explorer, selecione**Modelos Locais** **da Galeria** > .

2. Selecione, ou arraste e deixe cair, uma piscina local ou modelo de trabalho.

3. Selecione **Utilize este modelo**e siga as instruções no ecrã.

## <a name="file-groups-and-file-transfer"></a>Grupos de ficheiros e transferência de ficheiros

A maioria dos trabalhos e tarefas requer ficheiros de entrada e produz ficheiros de saída. Normalmente, os ficheiros de entrada e os ficheiros de saída são transferidos, quer do cliente para o nó, quer do nó para o cliente. A extensão cli do lote Azure abstrata a transferência de ficheiros e utiliza a conta de armazenamento que pode associar a cada conta de Lote.

Um grupo de ficheiros equivale a um contentor que é criado na conta de armazenamento Azure. O grupo de ficheiros pode ter subpastas.

A extensão do Batch CLI fornece comandos para enviar ficheiros do cliente para um grupo de ficheiros especificado e transferir ficheiros do grupo de ficheiros especificado para um cliente.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Os modelos de pool e de trabalho permitem que os ficheiros armazenados em grupos de ficheiros sejam especificados para cópia em nódos de piscina ou nódosos de piscina de volta a um grupo de ficheiros. Por exemplo, no modelo de trabalho especificado anteriormente, a entrada de *ffmpeg-grupo* de ficheiros é especificada para a fábrica de tarefas como a localização dos ficheiros de vídeo de origem copiados para o nó para transcodificação. A *saída de ffmpeg do* grupo de ficheiros é o local onde os ficheiros de saída transcodificados são copiados do nó que executa cada tarefa.

## <a name="summary"></a>Resumo

O suporte de transferência de modelos e ficheiros foi atualmente adicionado apenas ao ClI Azure. O objetivo é expandir o público que pode usar o Batch para utilizadores que não precisam de desenvolver código usando as APIs do Lote, como investigadores e utilizadores de TI. Sem codificação, os utilizadores com conhecimento de Azure, Batch, e as aplicações a serem geridas pelo Batch podem criar modelos para a criação de piscinas e empregos. Com parâmetros de modelo, os utilizadores sem conhecimento detalhado do Lote e as aplicações podem usar os modelos.

Experimente a extensão do Lote para o Azure CLI e forneça-nos qualquer feedback ou sugestão, quer nos comentários relativos a este artigo quer através do [repo comunitário](https://github.com/Azure/Batch)de lote .

## <a name="next-steps"></a>Passos seguintes

- Documentação detalhada de instalação e utilização, amostras e código fonte estão disponíveis no [repo Azure GitHub](https://github.com/Azure/azure-batch-cli-extensions).

- Saiba mais sobre a utilização [do Batch Explorer](https://github.com/Azure/BatchExplorer) para criar e gerir os recursos do Lote.
