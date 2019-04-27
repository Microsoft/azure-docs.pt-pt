---
title: Executar tarefas de ponto-a-ponto através de modelos - Azure Batch | Documentos da Microsoft
description: Crie conjuntos, trabalhos e tarefas do Batch com arquivos de modelo e a CLI do Azure.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 80d2e995a18a2d6dafbb8d92fdd5996b10eab17c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60783743"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>Utilizar modelos da CLI do Azure Batch e a transferência de ficheiros

Utilizar uma extensão do Azure Batch para a CLI do Azure, é possível executar tarefas do Batch sem escrever código.

Criar e utilizar ficheiros de modelo JSON com a CLI do Azure para criar conjuntos, trabalhos e tarefas do Batch. Utilizar comandos de extensão CLI para facilmente carregar ficheiros de entrada da tarefa para a conta de armazenamento associados à conta do Batch e tarefa de transferência ficheiros de saída.

## <a name="overview"></a>Descrição geral

Uma extensão para a CLI do Azure permite que o Batch para ser utilizada-a-ponto por utilizadores que não são programadores. Com apenas os comandos da CLI, pode criar um conjunto, carregar dados de entrada, criar trabalhos e tarefas associadas e transferir os dados de saída resultante. Não é necessário nenhum código adicional. Executar os comandos da CLI diretamente ou integrá-los em scripts.

Criar modelos do batch a [suporte existente para o Batch na CLI do Azure](batch-cli-get-started.md#json-files-for-resource-creation) para ficheiros JSON especificar valores de propriedade ao criar conjuntos, trabalhos, tarefas e outros itens. Modelos do batch, adicione as seguintes capacidades:

-   Podem definir os parâmetros. Quando o modelo é utilizado, apenas os valores de parâmetro são especificados para criar o item, com outros valores de propriedade do item especificados no corpo do modelo. Um utilizador a quem entenda o Batch e as aplicações para ser executado pelo Batch podem criar modelos, especificando o agrupamento, o trabalho e os valores de propriedade de tarefas. Um utilizador menos familiarizados com o Batch e/ou os aplicativos só precisam especificar os valores para os parâmetros definidos.

-   Fábricas de tarefas de trabalho crie um ou mais tarefas associadas a uma tarefa, evitando a necessidade de muitas definições de tarefa a ser criado e simplificando significativamente a submissão da tarefa.


Tarefas normalmente utilizam ficheiros de dados de entrada e produzem ficheiros de dados de saída. Uma conta de armazenamento está associada, por predefinição, cada conta do Batch. Transferi ficheiros de e para esta conta de armazenamento com a CLI, sem codificação e não existem credenciais de armazenamento.

Por exemplo, [ffmpeg](https://ffmpeg.org/) é um aplicativo popular que processa os ficheiros de áudio e vídeos. Eis os passos com a CLI do Azure Batch para invocar o ffmpeg para transcodificar ficheiros de vídeo de origem a resoluções diferentes.

-   Crie um modelo de agrupamento. O utilizador criar o modelo sabe como chamar a aplicação ffmpeg e os respetivos requisitos; Especifica o sistema operacional adequado, a VM de tamanho, como o ffmpeg está instalado (a partir de um pacote de aplicação ou utilizar um Gestor de pacotes, por exemplo) e outros valores de propriedade do agrupamento. Parâmetros são criados pelo quando o modelo é utilizado, apenas o ID do conjunto e o número de VMs tem de ser especificado.

-   Crie um modelo de tarefa. O utilizador criar o modelo sabe como ffmpeg tem de ser invocado para transcodificar origem de vídeo para uma resolução diferente e especifica a linha de comandos de tarefas; eles também sabem que existe uma pasta que contém os ficheiros de vídeo de origem, com uma tarefa ficheiro de entrada, necessária.

-   Um utilizador final com um conjunto de ficheiros de vídeo para transcodificar primeiro cria um conjunto com o modelo de agrupamento, especificar apenas o ID do conjunto e o número de VMs necessárias. Em seguida, eles podem carregar os ficheiros de origem para transcodificar. Em seguida, pode ser submetida uma tarefa usando o modelo de tarefa, especificar apenas o ID do conjunto e a localização dos ficheiros de origem carregado. A tarefa de lote é criada, com uma tarefa por ficheiro de entrada a ser gerado. Por fim, os ficheiros transcodificados de saída podem ser transferidos.

## <a name="installation"></a>Instalação

Para instalar a extensão de CLI do Azure Batch, primeiro [instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli), ou execute a CLI do Azure [Azure Cloud Shell](../cloud-shell/overview.md).

Instale a versão mais recente da extensão do Batch com o seguinte comando da CLI do Azure:

```azurecli
az extension add --name azure-batch-cli-extensions
```

Para obter mais informações sobre a extensão de CLI do Batch e opções de instalação adicionais, consulte a [repositório do GitHub](https://github.com/Azure/azure-batch-cli-extensions).


Para utilizar as funcionalidades de extensão da CLI, precisa de uma conta do Azure Batch e, para os comandos que transfiram ficheiros para e do armazenamento, uma conta de armazenamento ligada.

Para iniciar sessão numa conta do Batch com a CLI do Azure, veja [recursos do Batch de gerir com a CLI do Azure](batch-cli-get-started.md).

## <a name="templates"></a>Modelos

Modelos do Azure Batch são semelhantes aos modelos do Azure Resource Manager, na funcionalidade e a sintaxe. Eles são ficheiros JSON que contêm valores e nomes de propriedade do item, mas adicionar os seguintes conceitos principais:

-   **Parâmetros**

    -   Permitir que os valores de propriedade seja especificada numa seção de corpo, com apenas valores de parâmetro que precisem de ser fornecido quando o modelo é utilizado. Por exemplo, a definição completa para um conjunto poderia ser colocada no corpo e apenas um parâmetro definido para o id do conjunto; apenas uma cadeia de ID do conjunto, por conseguinte, tem de ser fornecidos para criar um conjunto.
        
    -   O corpo do modelo pode ser criado por alguém com dados de conhecimento do Batch e os aplicativos para ser executado pelo Batch; apenas os valores para os parâmetros definidos pelo autor tem de ser fornecidos quando o modelo é utilizado. Um utilizador sem o Batch aprofundada e/ou dados de conhecimento do aplicativo, por conseguinte, pode utilizar os modelos.

-   **Variáveis**

    -   Permitir que os valores dos parâmetros simples ou complexas seja especificado num único lugar e usado num ou mais lugares no corpo do modelo. Variáveis podem simplificar e reduzir o tamanho do modelo, bem como torná-lo mais fácil de manter, ter um único local para alterar as propriedades.

-   **Construções de nível superior**

    -   Algumas construções de nível superior estão disponíveis no modelo que ainda não estão disponíveis nas APIs de Batch. Por exemplo, uma fábrica de tarefas pode ser definida num modelo de tarefa que cria várias tarefas para a tarefa, utilizando uma definição de tarefa comuns. Essas construções evitar a necessidade de código para dinamicamente criar vários arquivos JSON, como um ficheiro por tarefa, bem como criar arquivos de script para instalar aplicativos por meio de um Gestor de pacotes.

    -   Em algum momento, essas construções podem ser adicionados para o serviço Batch e está disponível nas APIs do Batch, interfaces do usuário, etc.

### <a name="pool-templates"></a>Modelos de conjunto

Os modelos de conjunto suportam as capacidades de modelo padrão de parâmetros e variáveis. Também suportam a construção de nível superior seguinte:

-   **Referências do pacote**

    -   Opcionalmente, permite que o software seja copiado para nós do conjunto com os gestores de pacote. O Gestor de pacotes e o ID de pacote são especificados. Ao declarar um ou mais pacotes, evite criar um script que obtém os pacotes necessários, o script de instalar e executar o script em cada nó do conjunto.

Segue-se um exemplo de um modelo que cria um conjunto de VMs do Linux com o ffmpeg instalado. Para usá-lo, fornece apenas uma cadeia de ID de agrupamento e o número de VMs no conjunto:

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

Se o ficheiro de modelo foi atribuído o nome _ffmpeg.json conjunto_, em seguida, invocar o modelo da seguinte forma:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

A CLI pede-lhe para fornecer valores para o `poolId` e `nodeCount` parâmetros. Também pode fornecer os parâmetros num ficheiro JSON. Por exemplo:

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

Se foi atribuído o nome do ficheiro JSON de parâmetros *Parameters. JSON do conjunto*, em seguida, invocar o modelo da seguinte forma:

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>Modelos de trabalho

Modelos de trabalho suportam as capacidades de modelo padrão de parâmetros e variáveis. Também suportam a construção de nível superior seguinte:

-   **Fábrica de tarefa**

    -   Cria várias tarefas para uma tarefa da definição de uma tarefa. Três tipos de fábrica de tarefa são suportados – paramétrica de abertura, tarefa por ficheiro e a coleção de tarefas.

Segue-se um exemplo de um modelo que cria uma tarefa para transcodificar ficheiros de vídeo de MP4 com o ffmpeg para um dos dois resoluções inferiores. Ele cria uma tarefa por ficheiro de vídeo de origem. Ver [grupos de ficheiros e a transferência de ficheiros](#file-groups-and-file-transfer) para obter mais informações sobre grupos de ficheiros para a tarefa de entrada e saída.

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

Se o ficheiro de modelo foi denominado _tarefa ffmpeg.json_, em seguida, invocar o modelo da seguinte forma:

```azurecli
az batch job create --template job-ffmpeg.json
```

Como antes, a CLI pede-lhe para fornecer valores para os parâmetros. Também pode fornecer os parâmetros num ficheiro JSON.

### <a name="use-templates-in-batch-explorer"></a>Utilizar modelos no Explorador do Batch

Pode carregar um modelo de CLI do Batch para o [Explorador do Batch](https://github.com/Azure/BatchExplorer) o aplicativo de desktop (anteriormente chamado BatchLabs) para criar um conjunto do Batch ou a tarefa. Também pode selecionar a partir de modelos predefinidos de conjunto e o trabalho na galeria do Explorador do Batch.

Para carregar um modelo:

1. No Explorador do Batch, selecione **galeria** > **modelos Local**.

2. Selecione, ou arrastar e soltar, um conjunto local ou o modelo de tarefa.

3. Selecione **Utilize este modelo**e siga as mensagens no ecrã.

## <a name="file-groups-and-file-transfer"></a>Grupos de ficheiros e a transferência de ficheiros

A maioria dos trabalhos e tarefas necessitam de ficheiros de entrada e produzem ficheiros de saída. Normalmente, os ficheiros de entrada e de ficheiros de saída são transferidos, do cliente para o nó ou a partir do nó para o cliente. A extensão da CLI do Azure Batch abstrai a transferência de ficheiros ausente e utiliza a conta de armazenamento que pode associar a cada conta do Batch.

Um grupo de ficheiros equivale a um contentor que é criado na conta de armazenamento do Azure. O grupo de ficheiros pode ter subpastas.

A extensão de CLI do Batch fornece comandos para carregar ficheiros de cliente para um grupo de ficheiros especificado e transferir ficheiros do grupo de ficheiro especificado para um cliente.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Modelos de conjunto e o trabalho permitem que os arquivos armazenados em grupos de ficheiros seja especificado para cópia em nós de conjunto ou desativar nós de conjunto para um grupo de ficheiros. Por exemplo, na tarefa modelo especificado anteriormente, o grupo de ficheiros *introdução de ffmpeg* está especificado para a fábrica de tarefas como a localização dos ficheiros de vídeo de origem copiados para baixo até o nó para transcodificação. O grupo de ficheiros *ffmpeg-output* é a localização onde os ficheiros transcodificados de saída são copiados do nó de execução de cada tarefa.

## <a name="summary"></a>Resumo

Suporte de transferência de ficheiros e modelo atualmente foram adicionados apenas para a CLI do Azure. O objetivo é expandir o público-alvo que pode utilizar o Batch para os utilizadores que não é necessário para desenvolver o código usando as APIs do Batch, como os pesquisadores e usuários de TI. Sem código, os utilizadores com dados de conhecimento do Azure, Batch e os aplicativos para ser executado pelo Batch podem criar modelos para a criação do conjunto e o trabalho. Com parâmetros de modelo, os utilizadores sem conhecimento detalhado do Batch e os aplicativos podem usar os modelos.

Experimente a extensão de Batch para a CLI do Azure e forneça-nos com quaisquer comentários ou sugestões, tanto nos comentários para este artigo ou através do [repositório de Comunidade do Batch](https://github.com/Azure/Batch).

## <a name="next-steps"></a>Passos Seguintes

- Documentação de instalação e utilização detalhada, exemplos e código-fonte estão disponíveis no [repositório do GitHub do Azure](https://github.com/Azure/azure-batch-cli-extensions).

- Saiba mais sobre como utilizar [Explorador do Batch](https://github.com/Azure/BatchExplorer) para criar e gerir recursos do Batch.
