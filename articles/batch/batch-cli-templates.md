---
title: Trabalhos de corrida de ponta a ponta usando modelos - Lote Azure
description: Com apenas comandos da CLI, você pode criar um pool, carregar dados de entrada, criar trabalhos e tarefas associadas e baixar os dados de saída resultantes.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: c7459c4dc700f034feafbf133b831a52b9233d11
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020170"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>Usar modelos da CLI do lote do Azure e transferência de arquivos

Usando uma extensão do lote do Azure para o CLI do Azure, é possível executar trabalhos do lote sem escrever código.

Crie e use arquivos de modelo JSON com o CLI do Azure para criar pools, trabalhos e tarefas do lote. Use comandos de extensão da CLI para carregar facilmente os arquivos de entrada do trabalho para a conta de armazenamento associada à conta do lote e baixar os arquivos de saída do trabalho.

## <a name="overview"></a>Visão geral

Uma extensão para a CLI do Azure permite que o lote seja usado de ponta a ponta por usuários que não são desenvolvedores. Com apenas comandos da CLI, você pode criar um pool, carregar dados de entrada, criar trabalhos e tarefas associadas e baixar os dados de saída resultantes. Nenhum código adicional é necessário. Execute os comandos da CLI diretamente ou integre-os a scripts.

Os modelos de lote são criados no [suporte de lote existente no CLI do Azure](batch-cli-get-started.md#json-files-for-resource-creation) para arquivos JSON para especificar valores de propriedade ao criar pools, trabalhos, tarefas e outros itens. Os modelos de lote adicionam os seguintes recursos:

-   Os parâmetros podem ser definidos. Quando o modelo é usado, somente os valores de parâmetro são especificados para criar o item, com outros valores de propriedade de item especificados no corpo do modelo. Um usuário que entende o lote e os aplicativos a serem executados pelo lote pode criar modelos, especificar o pool, o trabalho e os valores de propriedade de tarefa. Um usuário menos familiarizado com o lote e/ou os aplicativos só precisa especificar os valores para os parâmetros definidos.

-   As fábricas de tarefas de trabalho criam uma ou mais tarefas associadas a um trabalho, evitando a necessidade de muitas definições de tarefas serem criadas e simplificando significativamente o envio de trabalhos.


Os trabalhos normalmente usam arquivos de dados de entrada e produzem arquivos de dados de saída. Uma conta de armazenamento é associada, por padrão, a cada conta do lote. Transfira arquivos de e para essa conta de armazenamento usando a CLI, sem codificação e nenhuma credencial de armazenamento.

Por exemplo, [ffmpeg](https://ffmpeg.org/) é um aplicativo popular que processa arquivos de áudio e vídeo. Aqui estão as etapas com a CLI do lote do Azure para invocar FFmpeg para transcodificar arquivos de vídeo de origem para resoluções diferentes.

-   Crie um modelo de pool. O usuário que cria o modelo sabe como chamar o aplicativo ffmpeg e seus requisitos; Eles especificam o sistema operacional apropriado, o tamanho da VM, como o ffmpeg é instalado (de um pacote de aplicativos ou usando um Gerenciador de pacotes, por exemplo) e outros valores de Propriedade do pool. Os parâmetros são criados para que, quando o modelo for usado, apenas a ID do pool e o número de VMs precisem ser especificados.

-   Crie um modelo de trabalho. O usuário que cria o modelo sabe como o ffmpeg precisa ser invocado para transcodificar o vídeo de origem para uma resolução diferente e especifica a linha de comando da tarefa; Eles também sabem que há uma pasta que contém os arquivos de vídeo de origem, com uma tarefa necessária por arquivo de entrada.

-   Um usuário final com um conjunto de arquivos de vídeo para transcodificação primeiro cria um pool usando o modelo de pool, especificando apenas a ID do pool e o número de VMs necessárias. Em seguida, eles podem carregar os arquivos de origem para transcodificar. Um trabalho pode ser enviado usando o modelo de trabalho, especificando apenas a ID do pool e o local dos arquivos de origem carregados. O trabalho do lote é criado, com uma tarefa por arquivo de entrada sendo gerada. Por fim, os arquivos de saída transcodificados podem ser baixados.

## <a name="installation"></a>Instalação

Para instalar a extensão CLI do lote do Azure, primeiro [Instale o CLI do Azure 2,0](/cli/azure/install-azure-cli)ou execute o CLI do Azure no [Azure cloud Shell](../cloud-shell/overview.md).

Instale a versão mais recente da extensão de lote usando o seguinte comando de CLI do Azure:

```azurecli
az extension add --name azure-batch-cli-extensions
```

Para obter mais informações sobre a extensão da CLI do lote e opções de instalação adicionais, consulte o [repositório do GitHub](https://github.com/Azure/azure-batch-cli-extensions).


Para usar os recursos de extensão da CLI, você precisa de uma conta do lote do Azure e, para os comandos que transferem arquivos de e para o armazenamento, uma conta de armazenamento vinculada.

Para fazer logon em uma conta do lote com o CLI do Azure, consulte [gerenciar recursos do lote com CLI do Azure](batch-cli-get-started.md).

## <a name="templates"></a>Modelos

Os modelos do lote do Azure são semelhantes aos modelos de Azure Resource Manager, em funcionalidade e sintaxe. Eles são arquivos JSON que contêm valores e nomes de propriedade de item, mas adicionam os seguintes conceitos principais:

-   **Parâmetros**

    -   Permite que os valores de propriedade sejam especificados em uma seção de corpo, com apenas os valores de parâmetro que precisam ser fornecidos quando o modelo é usado. Por exemplo, a definição completa de um pool poderia ser colocada no corpo e apenas um parâmetro definido para a ID do pool; Portanto, somente uma cadeia de caracteres de ID de pool precisa ser fornecida para criar um pool.
        
    -   O corpo do modelo pode ser criado por alguém com conhecimento do lote e dos aplicativos a serem executados pelo lote; somente os valores para os parâmetros definidos pelo autor devem ser fornecidos quando o modelo é usado. Portanto, um usuário sem o lote detalhado e/ou o conhecimento do aplicativo pode usar os modelos.

-   **Variáveis**

    -   Permitir que valores de parâmetro simples ou complexos sejam especificados em um único local e usados em um ou mais lugares no corpo do modelo. As variáveis podem simplificar e reduzir o tamanho do modelo, bem como torná-lo mais passível de manutenção por ter um local para alterar as propriedades.

-   **Construções de nível superior**

    -   Algumas construções de nível superior estão disponíveis no modelo que ainda não estão disponíveis nas APIs do lote. Por exemplo, uma fábrica de tarefas pode ser definida em um modelo de trabalho que cria várias tarefas para o trabalho, usando uma definição de tarefa comum. Essas construções evitam a necessidade de codificar para criar dinamicamente vários arquivos JSON, como um arquivo por tarefa, bem como criar arquivos de script para instalar aplicativos por meio de um Gerenciador de pacotes.

    -   Em algum momento, essas construções podem ser adicionadas ao serviço de lote e disponibilizadas nas APIs do lote, UIs, etc.

### <a name="pool-templates"></a>Modelos de pool

Os modelos de pool dão suporte aos recursos de modelo padrão de parâmetros e variáveis. Eles também dão suporte à seguinte construção de nível superior:

-   **Referências de pacote**

    -   Opcionalmente, permite que o software seja copiado para nós de pool usando gerenciadores de pacotes. O Gerenciador de pacotes e a ID do pacote são especificados. Ao declarar um ou mais pacotes, você evita criar um script que obtém os pacotes necessários, instalando o script e executando o script em cada nó de pool.

Veja a seguir um exemplo de um modelo que cria um pool de VMs do Linux com o ffmpeg instalado. Para usá-lo, forneça apenas uma cadeia de caracteres de ID de pool e o número de VMs no pool:

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

Se o arquivo de modelo foi nomeado _pool-ffmpeg. JSON_, invoque o modelo da seguinte maneira:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

A CLI solicita que você forneça valores para os parâmetros `poolId` e `nodeCount`. Você também pode fornecer os parâmetros em um arquivo JSON. Por exemplo:

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

Se o arquivo de parâmetros JSON tiver sido nomeado *pool-Parameters. JSON*, invoque o modelo da seguinte maneira:

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>Modelos de trabalho

Os modelos de trabalho dão suporte aos recursos de modelo padrão de parâmetros e variáveis. Eles também dão suporte à seguinte construção de nível superior:

-   **Fábrica de tarefas**

    -   Cria várias tarefas para um trabalho de uma definição de tarefa. Há suporte para três tipos de fábrica de tarefas – limpeza paramétrica, tarefa por arquivo e coleção de tarefas.

Veja a seguir um exemplo de um modelo que cria um trabalho para transcodificar arquivos de vídeo MP4 com ffmpeg para uma das duas resoluções mais baixas. Ele cria uma tarefa por arquivo de vídeo de origem. Consulte [grupos de arquivos e transferência de arquivos](#file-groups-and-file-transfer) para obter mais informações sobre grupos de arquivos para entrada e saída de trabalho.

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

Se o arquivo de modelo tiver sido denominado _Job-ffmpeg. JSON_, invoque o modelo da seguinte maneira:

```azurecli
az batch job create --template job-ffmpeg.json
```

Como antes, a CLI solicita que você forneça valores para os parâmetros. Você também pode fornecer os parâmetros em um arquivo JSON.

### <a name="use-templates-in-batch-explorer"></a>Usar modelos no Batch Explorer

Você pode carregar um modelo da CLI do lote para o aplicativo de área de trabalho [batch Explorer](https://github.com/Azure/BatchExplorer) (anteriormente chamado de BatchLabs) para criar um pool ou trabalho do lote. Você também pode selecionar um pool predefinido e modelos de trabalho na Galeria de Batch Explorer.

Para carregar um modelo:

1. Em Batch Explorer, selecione **galeria** > **modelos locais**.

2. Selecione ou arraste e solte um modelo de pool ou trabalho local.

3. Selecione **usar este modelo**e siga os prompts na tela.

## <a name="file-groups-and-file-transfer"></a>Grupos de arquivos e transferência de arquivos

A maioria dos trabalhos e tarefas exigem arquivos de entrada e geram arquivos de saída. Normalmente, os arquivos de entrada e de saída são transferidos do cliente para o nó ou do nó para o cliente. A extensão CLI do lote do Azure abstrai a transferência de arquivos e utiliza a conta de armazenamento que você pode associar a cada conta do lote.

Um grupo de arquivos é igual a um contêiner que é criado na conta de armazenamento do Azure. O grupo de arquivos pode ter subpastas.

A extensão da CLI do lote fornece comandos para carregar arquivos do cliente para um grupo de arquivos especificado e baixar arquivos do grupo de arquivos especificado para um cliente.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Os modelos de pool e de trabalho permitem que os arquivos armazenados em grupos de arquivos sejam especificados para cópia nos nós do pool ou fora dos nós de pool de volta para um grupo de arquivos. Por exemplo, no modelo de trabalho especificado anteriormente, o grupo de arquivos *ffmpeg-Input* é especificado para a fábrica de tarefas como o local dos arquivos de vídeo de origem copiados para o nó para transcodificação. O grupo de arquivos *ffmpeg-output* é o local onde os arquivos de saída transcodificados são copiados do nó que executa cada tarefa.

## <a name="summary"></a>Resumo

O suporte de modelo e transferência de arquivo foi adicionado somente ao CLI do Azure. O objetivo é expandir o público que pode usar o lote para usuários que não precisam desenvolver código usando as APIs do lote, como pesquisadores e usuários de ti. Sem codificação, os usuários com conhecimento do Azure, do lote e dos aplicativos a serem executados pelo lote podem criar modelos para criação de pool e trabalho. Com parâmetros de modelo, os usuários sem conhecimento detalhado do lote e dos aplicativos podem usar os modelos.

Experimente a extensão do lote para o CLI do Azure e forneça comentários ou sugestões, nos comentários deste artigo ou por meio do [repositório da Comunidade do lote](https://github.com/Azure/Batch).

## <a name="next-steps"></a>Passos seguintes

- Documentação detalhada de instalação e uso, exemplos e código-fonte estão disponíveis no [repositório GitHub do Azure](https://github.com/Azure/azure-batch-cli-extensions).

- Saiba mais sobre como usar [batch Explorer](https://github.com/Azure/BatchExplorer) para criar e gerenciar recursos do lote.
