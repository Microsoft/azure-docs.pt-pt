---
title: Executar empregos de ponta a ponta usando modelos
description: Com apenas comandos CLI, pode criar um pool, carregar dados de entrada, criar empregos e tarefas associadas e descarregar os dados de saída resultantes.
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 845a32c2feda5a5a3b8d44d237c62db94cae1779
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91848726"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>Use modelos CLI de lote de Azure e transferência de ficheiros

Utilizando uma extensão de lote ao Azure CLI, é possível executar trabalhos de Lote sem código de escrita.

Crie e use ficheiros de modelo JSON com o CLI Azure para criar piscinas, empregos e tarefas do Batch. Utilize comandos de extensão CLI para carregar facilmente ficheiros de entradas de trabalho na conta de armazenamento associada à conta Batch e descarregar ficheiros de saída de trabalho.

> [!NOTE]
> Os ficheiros JSON não suportam a mesma funcionalidade que os [modelos do Gestor de Recursos Azure.](../azure-resource-manager/templates/template-syntax.md) Destinam-se a ser formatados como o corpo de pedido de REST bruto. A extensão CLI não altera nenhum comando existente, mas tem uma opção de modelo semelhante que adiciona a funcionalidade parcial do modelo do Gestor de Recursos Azure. Consulte [extensões CLI do lote Azure para Windows, Mac e Linux](https://github.com/Azure/azure-batch-cli-extensions).

## <a name="overview"></a>Descrição Geral

Uma extensão do Azure CLI permite que o Batch seja utilizado de ponta a ponta por utilizadores que não sejam desenvolvedores. Com apenas comandos CLI, pode criar um pool, carregar dados de entrada, criar empregos e tarefas associadas e descarregar os dados de saída resultantes. Não é necessário um código adicional. Executar os comandos CLI diretamente ou integrá-los em scripts.

Os modelos de lote baseiam-se no suporte do Lote existente no [CLI Azure](batch-cli-get-started.md#json-files-for-resource-creation) para ficheiros JSON para especificar os valores de propriedade ao criar piscinas, empregos, tarefas e outros itens. Os modelos de lote adicionam as seguintes capacidades:

-   Os parâmetros podem ser definidos. Quando o modelo é utilizado, apenas os valores dos parâmetros são especificados para criar o item, com outros valores de propriedade de item especificados no corpo do modelo. Um utilizador que compreenda o Batch e as aplicações a executar pelo Batch pode criar modelos, especificando valores de pool, trabalho e propriedade de tarefa. Um utilizador menos familiarizado com o Batch e/ou as aplicações só precisa de especificar os valores para os parâmetros definidos.

-   As fábricas de tarefas de trabalho criam uma ou mais tarefas associadas a um trabalho, evitando a necessidade de criar muitas definições de tarefas e simplificando significativamente a submissão de emprego.


Normalmente, os trabalhos utilizam ficheiros de dados de entrada e produzem ficheiros de dados de saída. Uma conta de armazenamento está associada, por padrão, a cada conta Batch. Transfira ficheiros de e para esta conta de armazenamento utilizando o CLI, sem codificação e sem credenciais de armazenamento.

Por exemplo, [ffmpeg](https://ffmpeg.org/) é uma aplicação popular que processa ficheiros de áudio e vídeo. Aqui estão os passos com o Azure Batch CLI para invocar ffmpeg para transcodificar ficheiros de vídeo de origem para diferentes resoluções.

-   Crie um modelo de piscina. O utilizador que cria o modelo sabe como chamar a aplicação ffmpeg e seus requisitos; especificam o sistema operativo adequado, o tamanho VM, a instalação de ffmpeg (a partir de um pacote de aplicações ou usando um gestor de pacotes, por exemplo) e outros valores de propriedade da piscina. Os parâmetros são criados para que, quando o modelo é utilizado, apenas o ID da piscina e o número de VMs precisam de ser especificados.

-   Crie um modelo de trabalho. O utilizador que cria o modelo sabe como o ffmpeg precisa de ser invocado para transcodificar o vídeo de origem para uma resolução diferente e especifica a linha de comando da tarefa; também sabem que existe uma pasta que contém os ficheiros de vídeo de origem, com uma tarefa necessária por ficheiro de entrada.

-   Um utilizador final com um conjunto de ficheiros de vídeo para transcodificar primeiro cria uma piscina usando o modelo de piscina, especificando apenas o ID do pool e o número de VMs necessários. Podem então enviar os ficheiros de origem para transcodificar. Um trabalho pode então ser submetido usando o modelo de trabalho, especificando apenas o ID do pool e a localização dos ficheiros de origem enviados. O trabalho Batch é criado, com uma tarefa por ficheiro de entrada a ser gerada. Finalmente, os ficheiros de saída transcodificados podem ser descarregados.

## <a name="installation"></a>Instalação

Para instalar a extensão CLI do Lote Azure, instale primeiro [o Azure CLI 2.0](/cli/azure/install-azure-cli), ou execute o Azure CLI em [Azure Cloud Shell](../cloud-shell/overview.md).

Instale a versão mais recente da extensão do Lote utilizando o seguinte comando Azure CLI:

```azurecli
az extension add --name azure-batch-cli-extensions
```

Para obter mais informações sobre a extensão do Lote CLI e opções de instalação adicionais, consulte o [repo GitHub](https://github.com/Azure/azure-batch-cli-extensions).


Para utilizar as funcionalidades de extensão CLI, precisa de uma conta Azure Batch e, para os comandos que transferem ficheiros de e para o armazenamento, uma conta de armazenamento ligada.

Para iniciar sessão numa conta batch com o Azure CLI, consulte [gerir os recursos do Lote com o Azure CLI](batch-cli-get-started.md).

## <a name="templates"></a>Modelos

Os modelos do Azure Batch são semelhantes aos modelos do Gestor de Recursos Azure, na funcionalidade e na sintaxe. São ficheiros JSON que contêm nomes e valores de propriedade de item, mas adicionam os seguintes conceitos principais:

-   **Parâmetros**

    -   Permitir que os valores de propriedade sejam especificados numa secção do corpo, com apenas valores de parâmetros que precisam de ser fornecidos quando o modelo é utilizado. Por exemplo, a definição completa de uma piscina poderia ser colocada no corpo e apenas um parâmetro definido `poolId` para; apenas uma cadeia de ID de piscina precisa, portanto, ser fornecida para criar uma piscina.

    -   O corpo do modelo pode ser da autoria de alguém com conhecimento de Batch e das aplicações a executar por Batch; só devem ser fornecidos valores para os parâmetros definidos pelo autor quando o gabarito for utilizado. Um utilizador sem o conhecimento aprofundado do Lote e/ou da aplicação pode, portanto, utilizar os modelos.

-   **Variáveis**

    -   Permita que valores de parâmetros simples ou complexos sejam especificados num só local e utilizados em um ou mais lugares no corpo do modelo. As variáveis podem simplificar e reduzir o tamanho do modelo, bem como torná-lo mais sustentável por ter uma localização para alterar propriedades.

-   **Construções de nível superior**

    -   Algumas construções de nível superior estão disponíveis no modelo que ainda não estão disponíveis nas APIs do lote. Por exemplo, uma fábrica de tarefas pode ser definida num modelo de trabalho que cria múltiplas tarefas para o trabalho, usando uma definição de tarefa comum. Estas construções evitam a necessidade de codificar para criar dinamicamente vários ficheiros JSON, como um ficheiro por tarefa, bem como criar ficheiros de script para instalar aplicações através de um gestor de pacotes.

    -   Em algum momento, estas construções podem ser adicionadas ao serviço Batch e disponíveis nas APIs do lote, UIs, etc.

### <a name="pool-templates"></a>Modelos de piscina

Os modelos de piscina suportam as capacidades padrão do modelo de parâmetros e variáveis. Apoiam igualmente a seguinte construção de nível superior:

-   **Referências do pacote**

    -   Opcionalmente, permite que o software seja copiado para os nós de piscina utilizando gestores de pacotes. O gestor do pacote e o iD do pacote são especificados. Ao declarar um ou mais pacotes, evita criar um script que obtenha os pacotes necessários, instalando o script e executando o script em cada nó de piscina.

Segue-se um exemplo de um modelo que cria uma piscina de VMs Linux com ffmpeg instalado. Para usá-lo, forneça apenas uma cadeia de ID de piscina e o número de VMs na piscina:

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
            "taskSlotsPerNode": 1,
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

Se o ficheiro do modelo foi nomeado _pool-ffmpeg.js,_ então invoque o modelo da seguinte forma:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

O CLI pede-lhe que forneça valores para os `poolId` parâmetros e `nodeCount` parâmetros. Também pode fornecer os parâmetros num ficheiro JSON. Por exemplo:

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

Se o ficheiro JSON dos parâmetros foi nomeado *pool-parameters.js,* então invoque o modelo da seguinte forma:

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>Modelos de emprego

Os modelos de trabalho suportam as capacidades padrão do modelo de parâmetros e variáveis. Apoiam igualmente a seguinte construção de nível superior:

-   **Fábrica de tarefas**

    -   Cria múltiplas tarefas para um trabalho a partir de uma definição de tarefa. Três tipos de fábrica de tarefas são suportados - varredura paramétrica, tarefa por ficheiro e recolha de tarefas.

Segue-se um exemplo de um modelo que cria um trabalho para transcodificar ficheiros de vídeo MP4 com ffmpeg para uma das duas resoluções mais baixas. Cria uma tarefa por ficheiro de vídeo de origem. Consulte [os grupos de ficheiros e a transferência de ficheiros](#file-groups-and-file-transfer) para obter mais sobre grupos de ficheiros para entrada e saída de emprego.

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

Se o ficheiro do modelo foi nomeado _job-ffmpeg.js,_ então invoque o modelo da seguinte forma:

```azurecli
az batch job create --template job-ffmpeg.json
```

Como antes, o CLI pede-lhe que forneça valores para os parâmetros. Também pode fornecer os parâmetros num ficheiro JSON.

### <a name="use-templates-in-batch-explorer"></a>Use modelos no Explorador de Lote

Pode enviar um modelo de CLI do Lote para a aplicação de ambiente de trabalho [Do Batch Explorer](https://github.com/Azure/BatchExplorer) (anteriormente chamado BatchLabs) para criar um pool ou trabalho de Lote. Também pode selecionar entre modelos de piscina e trabalho pré-finidos na Galeria do Explorador de Lotes.

Para carregar um modelo:

1. No Batch Explorer, selecione **Gallery**  >  **Local modelos.**

2. Selecione, ou arraste e deixe cair, uma piscina local ou modelo de trabalho.

3. Selecione **Utilize este modelo** e siga as indicações no ecrã.

## <a name="file-groups-and-file-transfer"></a>Grupos de ficheiros e transferência de ficheiros

A maioria dos trabalhos e tarefas requer ficheiros de entrada e produz ficheiros de saída. Normalmente, os ficheiros de entrada e de saída são transferidos, quer do cliente para o nó, quer do nó para o cliente. A extensão Azure Batch CLI abstrata a transferência de ficheiros e utiliza a conta de armazenamento que pode associar a cada conta Batch.

Um grupo de ficheiros equivale a um recipiente que é criado na conta de armazenamento Azure. O grupo de ficheiros pode ter sub-dobradeiras.

A extensão do Lote CLI fornece comandos para carregar ficheiros do cliente para um grupo de ficheiros especificado e transferir ficheiros do grupo de ficheiros especificado para um cliente.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Os modelos de piscina e de trabalho permitem que os ficheiros armazenados em grupos de ficheiros sejam especificados para cópia nos nós da piscina ou nos nós de piscina de volta para um grupo de ficheiros. Por exemplo, no modelo de trabalho especificado anteriormente, o grupo *de ficheiros ffmpeg-input* é especificado para a fábrica de tarefas como a localização dos ficheiros de vídeo de origem copiados para o nó para transcoding. O grupo *de ficheiros ffmpeg-output* é o local onde os ficheiros de saída transcodificados são copiados do nó que executa cada tarefa.

## <a name="summary"></a>Resumo

Atualmente, o suporte de transferência de modelos e ficheiros foi adicionado apenas ao CLI do Azure. O objetivo é expandir o público que pode usar o Batch para utilizadores que não precisam de desenvolver código usando as APIs do Lote, como investigadores e utilizadores de TI. Sem codificação, os utilizadores com conhecimento de Azure, Batch, e as aplicações a serem executadas pela Batch podem criar modelos para criação de piscina e emprego. Com os parâmetros do modelo, os utilizadores sem conhecimento detalhado do Batch e as aplicações podem usar os modelos.

Experimente a extensão do Lote para o CLI Azure e forneça-nos qualquer feedback ou sugestões, quer nos comentários para este artigo, quer através do [repo da Comunidade de Lote.](https://github.com/Azure/Batch)

## <a name="next-steps"></a>Passos seguintes

- Documentação detalhada de instalação e utilização, amostras e código fonte estão disponíveis no [repo Azure GitHub](https://github.com/Azure/azure-batch-cli-extensions).

- Saiba mais sobre a utilização [do Batch Explorer](https://github.com/Azure/BatchExplorer) para criar e gerir os recursos do Batch.
