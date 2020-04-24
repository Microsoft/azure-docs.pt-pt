---
title: Executar scripts Python através da Fábrica de Dados - Azure Batch Python
description: Tutorial - Aprenda a executar scripts Python como parte de um oleoduto através da Azure Data Factory usando o Lote Azure.
author: mammask
ms.devlang: python
ms.topic: tutorial
ms.date: 12/11/2019
ms.author: komammas
ms.custom: mvc
ms.openlocfilehash: 6761896a6555c11d7957f923a5951641c1541012
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117068"
---
# <a name="tutorial-run-python-scripts-through-azure-data-factory-using-azure-batch"></a>Tutorial: Executar scripts Python através da Fábrica de Dados Azure usando o Lote Azure

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Autenticar com contas do Batch e de Armazenamento
> * Desenvolva e execute um roteiro em Python
> * Criar um conjunto de nós de computação para executar uma aplicação
> * Agende as suas cargas de trabalho python
> * Monitorize o seu pipeline de análise
> * Aceda aos seus ficheiros de registo

O exemplo abaixo executa um script Python que recebe entrada CSV de um recipiente de armazenamento blob, executa um processo de manipulação de dados, e escreve a saída para um recipiente de armazenamento de bolhas separado.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma distribuição de [Python](https://www.python.org/downloads/) instalada, para testes locais.
* O pacote [Azure.](https://pypi.org/project/azure/) `pip`
* Uma conta do Batch do Azure e uma conta de Armazenamento do Microsoft Azure associada. Consulte criar [uma conta de Lote](quick-create-portal.md#create-a-batch-account) para obter mais informações sobre como criar e ligar contas do Lote a contas de armazenamento.
* Uma conta azure data factory. Consulte [Criar uma fábrica](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory) de dados para obter mais informações sobre como criar uma fábrica de dados através do portal Azure.
* [Explorador de lote](https://azure.github.io/BatchExplorer/).
* [Explorador de Armazenamento Azure.](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="create-a-batch-pool-using-batch-explorer"></a>Criar uma piscina de lote usando o Batch Explorer

Nesta secção, utilizará o Batch Explorer para criar a piscina de lote que o seu oleoduto de fábrica Azure Data utilizará. 

1. Inscreva-se no Batch Explorer utilizando as suas credenciais Azure.
1. Selecione a sua conta 'Lote'
1. Crie uma piscina selecionando **Pools** na barra lateral esquerda e, em seguida, o botão **Adicionar** acima do formulário de pesquisa. 
    1. Escolha um nome de identificação e exibição. Usaremos `custom-activity-pool` para este exemplo.
    1. Dete teo tipo de calcário para **o tamanho fixo,** e desloque a contagem de nós dedicada a 2.
    1. Em **ciência de Dados,** selecione **Dsvm Windows** como o sistema operativo.
    1. Escolha `Standard_f2s_v2` como o tamanho da máquina virtual.
    1. Ative a tarefa inicial `cmd /c "pip install pandas"`e adicione o comando . A identidade do utilizador pode permanecer como utilizador padrão **do Pool**.
    1. Selecione **OK**.

## <a name="create-blob-containers"></a>Criar recipientes de bolha

Aqui irá criar recipientes blob que armazenarão os seus ficheiros de entrada e saída para o trabalho do Lote OCR.

1. Inscreva-se no Storage Explorer utilizando as suas credenciais Azure.
1. Utilizando a conta de armazenamento ligada à sua conta Batch, crie dois recipientes blob (um para ficheiros de entrada, um para ficheiros de saída) seguindo os passos em [Criar um recipiente de bolha](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container).
    * Neste exemplo, chamaremos o nosso `input`recipiente de `output`entrada , e o nosso recipiente de saída .
1. Carregue `main.py` `iris.csv` e para o `input` seu recipiente de entrada utilizando o Storage Explorer seguindo os passos de [Gestão](../vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container) de bolhas num recipiente de bolha


## <a name="develop-a-script-in-python"></a>Desenvolver um roteiro em Python

O seguinte script `iris.csv` Python carrega `input` o conjunto de dados do seu recipiente, executa um processo de manipulação de dados e guarda os resultados de volta para o `output` recipiente.

``` python
# Load libraries
from azure.storage.blob import BlockBlobService
import pandas as pd

# Define parameters
storageAccountName = "<storage-account-name>"
storageKey         = "<storage-account-key>"
containerName      = "output"

# Establish connection with the blob storage account
blobService = BlockBlobService(account_name=storageAccountName,
                               account_key=storageKey
                               )

# Load iris dataset from the task node
df = pd.read_csv("iris.csv")

# Subset records
df = df[df['Species'] == "setosa"]

# Save the subset of the iris dataframe locally in task node
df.to_csv("iris_setosa.csv", index = False)

# Upload iris dataset
blobService.create_blob_from_text(containerName, "iris_setosa.csv", "iris_setosa.csv")
```

Guarde o `main.py` script como e carregue-o para o recipiente de **armazenamento Azure.** Certifique-se de testar e validar a sua funcionalidade localmente antes de a enviar para o seu recipiente de blob:

``` bash
python main.py
```

## <a name="set-up-an-azure-data-factory-pipeline"></a>Criar um oleoduto Azure Data Factory

Nesta secção, você vai criar e validar um oleoduto usando o seu script Python.

1. Siga os passos para criar uma fábrica de dados no âmbito da secção "Criar uma fábrica de dados" [deste artigo](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).
1. Na caixa **de Recursos de Fábrica,** selecione o botão + (mais) e, em seguida, selecione **Pipeline**
1. No separador **Geral,** desloque o nome do oleoduto como "Run Python"

    ![](./media/run-python-batch-azure-data-factory/create-pipeline.png)

1. Na caixa **de Atividades,** expandir o **Serviço de Lote.** Arraste a atividade personalizada da caixa de **ferramentas De Atividades** para a superfície do designer de gasodutos.
1. No separador **Geral,** especifique **o testePipeline** for Name

    ![](./media/run-python-batch-azure-data-factory/create-custom-task.png)
1. No separador **Lote Azure,** adicione a **Conta lote** que foi criada nos passos anteriores e **ligação de teste** para garantir que é bem sucedida

    ![](./media/run-python-batch-azure-data-factory/integrate-pipeline-with-azure-batch.png)

1. No separador **Definições,** introduza o comando `python main.py`.
1. Para o Serviço Ligado ao **Recurso,** adicione a conta de armazenamento que foi criada nos passos anteriores. Teste a ligação para garantir que é bem sucedida.
1. No Caminho das **Pastas,** selecione o nome do recipiente de **armazenamento De blob Azure** que contém o script Python e as inputs associadas. Isto irá transferir os ficheiros selecionados do contentor para as instâncias do nó da piscina antes da execução do script Python.

    ![](./media/run-python-batch-azure-data-factory/create-custom-task-py-script-command.png)
1. Clique em **Validar** na barra de ferramentas do pipeline acima da tela, para validar as definições do pipeline. Confirme que o pipeline foi confirmado com êxito. Para fechar a saída da validação, selecione o botão &gt;&gt; (seta para a direita).
1. Clique em **Debug** para testar o gasoduto e certifique-se de que funciona com precisão.
1. Clique em **Publicar** para publicar o oleoduto.
1. Clique em **Trigger** para executar o script Python como parte de um processo de lote.

    ![](./media/run-python-batch-azure-data-factory/create-custom-task-py-success-run.png)

### <a name="monitor-the-log-files"></a>Monitorizar os ficheiros de registo

Caso os avisos ou erros sejam produzidos pela execução do seu script, pode consultar `stdout.txt` ou `stderr.txt` obter mais informações sobre a saída que foi registada.

1. Selecione **Jobs** do lado esquerdo do Batch Explorer.
1. Escolha o trabalho criado pela sua fábrica de dados. Assumindo que `custom-activity-pool`nomeou `adfv2-custom-activity-pool`a sua piscina, selecione.
1. Clique na tarefa que tinha um código de saída de falha.
1. Veja `stdout.txt` `stderr.txt` e investigue e diagnostice o seu problema.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você explorou um exemplo que o ensinou a executar scripts Python como parte de um oleoduto através da Azure Data Factory usando o Azure Batch.

Para saber mais sobre a Azure Data Factory, consulte:

> [!div class="nextstepaction"]
> [Azure Data Factory](../data-factory/introduction.md)
> [Oleodutos e atividades](../data-factory/concepts-pipelines-activities.md)
> [Custom activities](../data-factory/transform-data-using-dotnet-custom-activity.md) da Fábrica de Dados Azure
