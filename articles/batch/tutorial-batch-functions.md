---
title: Acionar um trabalho do Batch com as funções do Azure
description: Tutorial - aplicar OCR para documentos digitalizados como estes serão adicionados a um blob de armazenamento
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc
ms.openlocfilehash: d5a5197227ff62ca0c610e2c4e269480690d3faf
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342031"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Tutorial: Acionar um trabalho do Batch com as funções do Azure

Neste tutorial, irá aprender como acionar um trabalho do Batch com as funções do Azure. Vamos examinar um exemplo em que os documentos adicionados a um contentor de BLOBs de armazenamento do Azure têm reconhecimento ótico de carateres (OCR) aplicado aos mesmos através do Azure Batch. Para simplificar o processamento de OCR, vamos configurar uma função do Azure que executa uma tarefa de lote OCR sempre que for adicionado um ficheiro para o contentor de Blobs.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma conta do Batch do Azure e uma conta de Armazenamento do Microsoft Azure associada. Ver [criar uma conta do Batch](quick-create-portal.md#create-a-batch-account) para obter mais informações sobre como criar e ligar as contas.
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Explorador do Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Criar um conjunto do Batch e o trabalho do Batch com o Explorador do Batch

Nesta secção, irá utilizar o Explorador do Batch para criar o conjunto do Batch e a tarefa de lote que irá executar tarefas de OCR. 

### <a name="create-a-pool"></a>Criar um conjunto

1. Inicie sessão no Explorador do Batch com as suas credenciais do Azure.
1. Criar um conjunto, selecionando **agrupamentos** na barra do lado esquerdo, em seguida, o **adicionar** botão acima do formulário de pesquisa. 
    1. Escolha um ID e nome a apresentar. Vamos utilizar `ocr-pool` para este exemplo.
    1. Defina o tipo de dimensionamento para **fixos de tamanho**e defina a contagem de nós dedicado para 3.
    1. Selecione **18.04-LTS do Ubuntu** como o sistema operativo.
    1. Escolha `Standard_f2s_v2` como o tamanho de máquina virtual.
    1. Ativar a tarefa de início e adicionar o comando `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"`. Certifique-se de que definir a identidade de utilizador como **utilizador predefinido de tarefa (administrador)** , que permite que as tarefas de início incluir os comandos com `sudo`.
    1. Selecione **OK**.
### <a name="create-a-job"></a>Criar uma tarefa

1. Criar uma tarefa no conjunto, selecionando **trabalhos** na barra do lado esquerdo, em seguida, o **adicionar** botão acima do formulário de pesquisa. 
    1. Escolha um ID e nome a apresentar. Vamos utilizar `ocr-job` para este exemplo.
    1. Definir o conjunto `ocr-pool`, ou qualquer nome escolheu para o seu conjunto.
    1. Selecione **OK**.


## <a name="create-blob-containers"></a>Criar contentores de BLOBs

Aqui vai criar contentores de BLOBs que armazenará os dados introduzidos e ficheiros para a tarefa de lote de OCR de saída.

1. Inicie sessão no Explorador de armazenamento com as suas credenciais do Azure.
1. Com a conta de armazenamento ligada à sua conta do Batch, crie dois contentores de BLOBs (para ficheiros de entrada, uma para ficheiros de saída), seguindo os passos indicados em [criar um contentor de BLOBs](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#create-a-blob-container).

Neste exemplo, o contentor de entrada é denominado `input` e é onde todos os documentos sem OCR inicialmente são carregados para o processamento. O contentor de saída é denominado `output` e é onde a tarefa de lote grava documentos processados com o OCR.  
    * Neste exemplo, vamos chamar nossa contentor de entrada `input`e o nosso contentor de saída `output`.  
    * O contentor de entrada é onde todos os documentos sem OCR inicialmente são carregados.  
    * O contentor de saída é onde a tarefa de lote grava documentos com o OCR.  

Crie uma assinatura de acesso partilhado para o contentor de saída no Explorador de armazenamento. Fazê-lo ao clicar com o botão direito no contentor de saída e ao selecionar **obter assinatura de acesso partilhado...** . Sob **permissões**, verifique **escrever**. Sem outras permissões são necessárias.  

## <a name="create-an-azure-function"></a>Criar uma Função do Azure

Nesta secção irá criar a função do Azure que aciona a tarefa de lote de OCR, sempre que um ficheiro é carregado para o contentor de entrada.

1. Siga os passos em [criar uma função acionada pelo armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-storage-blob-triggered-function) para criar uma função.
    1. Quando lhe for pedido para uma conta de armazenamento, utilize a mesma conta de armazenamento que associou à conta do Batch.
    1. Para **pilha de runtime**, escolha o .NET. Vamos escrever nossa função C# utilizar o SDK de .NET do Batch.
1. Depois de criar a função acionada por blob, utilize o [ `run.csx` ](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) e [ `function.proj` ](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) do GitHub na função.
    * `run.csx` é executada quando é adicionado um blob de novo para o seu contentor de BLOBs de entrada.
    * `function.proj` lista as bibliotecas externas no código da função, por exemplo, o SDK .NET do Batch.
1. Alterar os valores de marcador de posição de variáveis no `Run()` função do `run.csx` ficheiros para refletir as suas credenciais do Batch e de armazenamento. Pode encontrar as credenciais de conta do Batch e de armazenamento no portal do Azure no **chaves** secção da sua conta do Batch.
    * Obter as suas credenciais de conta do Batch e armazenamento no portal do Azure no **chaves** secção da sua conta do Batch. 

## <a name="trigger-the-function-and-retrieve-results"></a>Acione a função e obter resultados

Carregar algum ou todos os ficheiros digitalizados a partir da [ `input_files` ](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) diretório no GitHub para o contentor de entrada. Monitorizar o Explorador do Batch para confirmar que uma tarefa é adicionada à `ocr-pool` para cada ficheiro. Após alguns segundos, o ficheiro com o OCR aplicada é adicionado para o contentor de saída. O ficheiro, em seguida, é visível e recuperáveis no Explorador de armazenamento.

Além disso, pode ver o ficheiro de registos na parte inferior da janela do editor de funções do Azure web, onde verá mensagens como esta para todos os ficheiros que carrega para o contentor de entrada:

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-06-21T20:02:35.129 [Information] Name of output text file: <outputTxtFile>
2019-06-21T20:02:35.130 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

Para transferir os ficheiros de saída a partir do Explorador de armazenamento no seu computador local, selecione os ficheiros que pretende e, em seguida, selecione o **transferir** da faixa de opções principais. 

> [!TIP]
> Os ficheiros transferidos são pesquisáveis se aberto num leitor PDF.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como: 

> [!div class="checklist"]
> * Utilize o Explorador do Batch para criar conjuntos e tarefas
> * Utilize o Explorador de armazenamento para criar contentores de BLOBs e uma assinatura de acesso partilhado (SAS)
> * Criar uma função de acionada por blob do Azure
> * Carregar ficheiros de entrada para o Armazenamento
> * Monitorizar a execução de tarefas
> * Obter ficheiros de saída

* Para obter mais exemplos de utilização da API de .NET para agendar e processar cargas de trabalho do Batch, consulte [os exemplos no GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp). 

* Para ver mais acionadores de funções do Azure que pode utilizar para executar cargas de trabalho do Batch, consulte [a documentação das funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings).
