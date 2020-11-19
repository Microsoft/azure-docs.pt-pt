---
title: Tutorial - Executar scripts Python através da Fábrica de Dados
description: Aprenda a executar scripts Python como parte de um oleoduto através da Azure Data Factory usando Azure Batch.
author: pkshultz
ms.devlang: python
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: peshultz
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 7752bc3f768aec7a3e98fb1813c4194f81fb9dfb
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917635"
---
# <a name="tutorial-run-python-scripts-through-azure-data-factory-using-azure-batch"></a>Tutorial: Executar scripts Python através da Azure Data Factory usando Azure Batch

Neste tutorial, irá aprender a:

> [!div class="checklist"]
> * Autenticar com contas do Batch e de Armazenamento
> * Desenvolver e executar um script em Python
> * Criar um conjunto de nós de computação para executar uma aplicação
> * Agende as suas cargas de trabalho python
> * Monitorize o seu pipeline de análise
> * Aceda aos seus registos

O exemplo abaixo executa um script Python que recebe entrada de CSV de um recipiente de armazenamento de bolhas, executa um processo de manipulação de dados, e escreve a saída para um recipiente de armazenamento de bolhas separado.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma distribuição [de Python](https://www.python.org/downloads/) instalada para testes locais.
* O pacote [azure-storage-blob.](https://pypi.org/project/azure-storage-blob/) `pip`
* O [ conjunto de dadosiris.csv](https://www.kaggle.com/uciml/iris/version/2#Iris.csv)
* Uma conta do Batch do Azure e uma conta de Armazenamento do Microsoft Azure associada. Consulte [a conta De criar um Lote](quick-create-portal.md#create-a-batch-account) para obter mais informações sobre como criar e ligar contas batch a contas de armazenamento.
* Uma conta da Azure Data Factory. Consulte [Criar uma fábrica de dados](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory) para obter mais informações sobre como criar uma fábrica de dados através do portal Azure.
* [Explorador de Lote](https://azure.github.io/BatchExplorer/).
* [Explorador de Armazenamento Azure.](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="create-a-batch-pool-using-batch-explorer"></a>Criar uma piscina de lote usando o Batch Explorer

Nesta secção, utilizará o Batch Explorer para criar a piscina batch que o seu oleoduto de fábrica de dados Azure utilizará. 

1. Inscreva-se no Batch Explorer utilizando as suas credenciais Azure.
1. Selecione a sua conta Batch
1. Crie uma piscina selecionando **Pools** na barra lateral esquerda e, em seguida, o botão **Adicionar** acima do formulário de pesquisa. 
    1. Escolha um ID e um nome de exibição. Vamos usar `custom-activity-pool` para este exemplo.
    1. Desagre o tipo de balança para **o tamanho fixo,** e desagre a contagem de nós dedicada para 2.
    1. Em **termos de ciência de dados,** selecione o **Dsvm Windows** como o sistema operativo.
    1. Escolha `Standard_f2s_v2` como o tamanho da máquina virtual.
    1. Ativar a tarefa inicial e adicionar o comando `cmd /c "pip install azure-storage-blob pandas"` . A identidade do utilizador pode permanecer como utilizador padrão **do Pool**.
    1. Selecione **OK**.

## <a name="create-blob-containers"></a>Criar recipientes blob

Aqui irá criar recipientes blob que armazenarão os seus ficheiros de entrada e saída para a função OCR Batch.

1. Inscreva-se no Storage Explorer utilizando as suas credenciais Azure.
1. Utilizando a conta de armazenamento ligada à sua conta Batch, crie dois recipientes blob (um para ficheiros de entrada, um para ficheiros de saída) seguindo os passos da [Create a blob container](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container).
    * Neste exemplo, chamaremos o nosso recipiente de `input` entrada, e o nosso recipiente de `output` saída.
1. Faça o upload [`iris.csv`](https://www.kaggle.com/uciml/iris/version/2#Iris.csv) para o seu recipiente de entrada utilizando o Storage Explorer seguindo os `input` passos na [Gestão de bolhas num recipiente de bolhas](../vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container)

## <a name="develop-a-script-in-python"></a>Desenvolver um script em Python

O seguinte script Python carrega o `iris.csv` conjunto de dados do seu recipiente, executa um processo de `input` manipulação de dados e guarda os resultados de volta para o `output` recipiente.

``` python
# Load libraries
from azure.storage.blob import BlobServiceClient
import pandas as pd

# Define parameters
storageAccountURL = "<storage-account-url>"
storageKey         = "<storage-account-key>"
containerName      = "output"

# Establish connection with the blob storage account
blob_service_client = BlockBlobService(account_url=storageAccountURL,
                               credential=storageKey
                               )

# Load iris dataset from the task node
df = pd.read_csv("iris.csv")

# Subset records
df = df[df['Species'] == "setosa"]

# Save the subset of the iris dataframe locally in task node
df.to_csv("iris_setosa.csv", index = False)

# Upload iris dataset
container_client = blob_service_client.get_container_client(containerName)
with open("iris_setosa.csv", "rb") as data:
    blob_client = container_client.upload_blob(name="iris_setosa.csv", data=data)
```

Guarde o script como `main.py` e faça o upload para o recipiente de armazenamento **Azure.** `input` Certifique-se de testar e validar a sua funcionalidade localmente antes de a enviar para o seu recipiente blob:

``` bash
python main.py
```

## <a name="set-up-an-azure-data-factory-pipeline"></a>Criar um oleoduto Azure Data Factory

Nesta secção, irá criar e validar um oleoduto utilizando o seu script Python.

1. Siga os passos para criar uma fábrica de dados na secção "Criar uma fábrica de dados" [deste artigo.](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)
1. Na caixa **Recursos de Fábrica,** selecione o botão + (mais) e, em seguida, selecione **Pipeline**
1. No separador **Geral,** definir o nome do oleoduto como "Run Python"

    ![No separador Geral, definir o nome do oleoduto como "Run Python"](./media/run-python-batch-azure-data-factory/create-pipeline.png)

1. Na caixa **de Atividades,** expandir o **Serviço de Lote.** Arraste a atividade personalizada da caixa de **ferramentas Das Atividades** para a superfície do designer de gasodutos.
1. No separador **Geral,** especifique **testPipeline** para nome

    ![No separador Geral, especifique testPipeline para nome](./media/run-python-batch-azure-data-factory/create-custom-task.png)
1. No **separador Azure Batch,** adicione a **Conta lote** criada nos passos anteriores e **a ligação de teste** para garantir que é bem sucedida

    ![No separador Azure Batch, adicione a Conta lote que foi criada nos passos anteriores e, em seguida, testar a ligação](./media/run-python-batch-azure-data-factory/integrate-pipeline-with-azure-batch.png)

1. No separador **Definições,** introduza o comando `python main.py` .
1. Para o **Serviço Ligado a Recursos,** adicione a conta de armazenamento que foi criada nos passos anteriores. Teste a ligação para se certificar de que é bem sucedida.
1. No Caminho da **Pasta,** selecione o nome do recipiente de **armazenamento Azure Blob** que contém a escrita Python e as entradas associadas. Isto irá transferir os ficheiros selecionados do contentor para os casos do nó de piscina antes da execução do script Python.

    ![No Caminho da Pasta, selecione o nome do recipiente de armazenamento Azure Blob](./media/run-python-batch-azure-data-factory/create-custom-task-py-script-command.png)
1. Clique em **Validar** na barra de ferramentas do pipeline acima da tela, para validar as definições do pipeline. Confirme que o pipeline foi confirmado com êxito. Para fechar a saída da validação, selecione o botão &gt;&gt; (seta para a direita).
1. Clique em **Debug** para testar o oleoduto e certifique-se de que funciona com precisão.
1. Clique em **Publicar** para publicar o pipeline.
1. Clique **em Trigger** para executar o script Python como parte de um processo de lote.

    ![Clique em Trigger para executar o script Python como parte de um processo de lote](./media/run-python-batch-azure-data-factory/create-custom-task-py-success-run.png)

### <a name="monitor-the-log-files"></a>Monitorize os ficheiros de registo

Caso sejam produzidos avisos ou erros pela execução do seu script, pode verificar `stdout.txt` ou `stderr.txt` obter mais informações sobre a saída que foi registada.

1. Selecione **Jobs** do lado esquerdo do Batch Explorer.
1. Escolha o trabalho criado pela sua fábrica de dados. Assumindo que nomeou a sua `custom-activity-pool` piscina, selecione. `adfv2-custom-activity-pool`
1. Clique na tarefa que tinha um código de saída de falha.
1. Ver `stdout.txt` e investigar e diagnosticar o seu `stderr.txt` problema.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você explorou um exemplo que o ensinou a executar scripts Python como parte de um oleoduto através da Azure Data Factory usando Azure Batch.

Para saber mais sobre a Azure Data Factory, consulte:

> [!div class="nextstepaction"]
> [Visão geral da Fábrica de Dados Azure](../data-factory/introduction.md)

