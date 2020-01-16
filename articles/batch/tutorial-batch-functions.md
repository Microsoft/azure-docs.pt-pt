---
title: Disparar um trabalho em lotes usando Azure Functions
description: Tutorial – aplicar OCR a documentos digitalizados conforme eles são adicionados a um blob de armazenamento
services: batch
author: ju-shim
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc
ms.openlocfilehash: 6e3cdb6c7e2774eeb29df6986088f822cbb894cf
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029236"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Tutorial: disparar um trabalho em lotes usando Azure Functions

Neste tutorial, você aprenderá a disparar um trabalho em lotes usando Azure Functions. Veremos um exemplo em que os documentos adicionados a um contêiner de blob do armazenamento do Azure têm OCR (reconhecimento óptico de caracteres) aplicado a eles por meio do lote do Azure. Para simplificar o processamento de OCR, configuraremos uma função do Azure que executa um trabalho de OCR em lote sempre que um arquivo é adicionado ao contêiner de BLOB.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma conta do Batch do Azure e uma conta de Armazenamento do Microsoft Azure associada. Consulte [criar uma conta do lote](quick-create-portal.md#create-a-batch-account) para obter mais informações sobre como criar e vincular contas.
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Explorador do Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com).

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Criar um pool do lote e um trabalho do lote usando Batch Explorer

Nesta seção, você usará Batch Explorer para criar o pool do lote e o trabalho em lotes que executarão tarefas de OCR. 

### <a name="create-a-pool"></a>Criar um conjunto

1. Entre no Batch Explorer usando suas credenciais do Azure.
1. Crie um pool selecionando **pools** na barra lateral esquerda e, em seguida, o botão **Adicionar** acima do formulário de pesquisa. 
    1. Escolha uma ID e um nome para exibição. Usaremos `ocr-pool` para este exemplo.
    1. Defina o tipo de escala como **tamanho fixo**e defina a contagem de nós dedicados como 3.
    1. Selecione **Ubuntu 18, 4-LTS** como o sistema operacional.
    1. Escolha `Standard_f2s_v2` como o tamanho da máquina virtual.
    1. Habilite a tarefa inicial e adicione o comando `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"`. Certifique-se de definir a identidade do usuário como **usuário padrão da tarefa (admin)** , que permite que as tarefas iniciais incluam comandos com `sudo`.
    1. Selecione **OK**.
### <a name="create-a-job"></a>Criar uma tarefa

1. Crie um trabalho no pool selecionando **trabalhos** na barra lateral esquerda e, em seguida, o botão **Adicionar** acima do formulário de pesquisa. 
    1. Escolha uma ID e um nome para exibição. Usaremos `ocr-job` para este exemplo.
    1. Defina o pool como `ocr-pool`ou qualquer nome que você escolher para o pool.
    1. Selecione **OK**.


## <a name="create-blob-containers"></a>Criar contêineres de BLOB

Aqui, você criará contêineres de BLOB que armazenarão seus arquivos de entrada e saída para o trabalho em lotes de OCR.

1. Entre no Gerenciador de Armazenamento usando suas credenciais do Azure.
1. Usando a conta de armazenamento vinculada à sua conta do lote, crie dois contêineres de BLOB (um para arquivos de entrada, um para arquivos de saída) seguindo as etapas em [criar um contêiner de blob](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#create-a-blob-container).

Neste exemplo, o contêiner de entrada é nomeado `input` e é onde todos os documentos sem OCR são carregados inicialmente para processamento. O contêiner de saída é nomeado `output` e é onde o trabalho do lote grava documentos processados com OCR.  
    * Neste exemplo, vamos chamar nosso contêiner de entrada `input`e nosso contêiner de saída `output`.  
    * O contêiner de entrada é onde todos os documentos sem OCR são carregados inicialmente.  
    * O contêiner de saída é onde o trabalho em lotes grava documentos com OCR.  

Crie uma assinatura de acesso compartilhado para seu contêiner de saída no Gerenciador de Armazenamento. Faça isso clicando com o botão direito do mouse no contêiner de saída e selecionando **obter assinatura de acesso compartilhado...** . Em **permissões**, marque **gravar**. Nenhuma outra permissão é necessária.  

## <a name="create-an-azure-function"></a>Criar uma Função do Azure

Nesta seção, você criará a função do Azure que dispara o trabalho do lote de OCR sempre que um arquivo é carregado em seu contêiner de entrada.

1. Siga as etapas em [criar uma função disparada pelo armazenamento de BLOBs do Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-storage-blob-triggered-function) para criar uma função.
    1. Quando uma conta de armazenamento for solicitada, use a mesma conta de armazenamento que você vinculou à sua conta do lote.
    1. Para **pilha de tempo de execução**, escolha .net. Vamos escrever nossa função em C# para aproveitar o SDK do .net do lote.
1. Depois que a função disparada por blob for criada, use o [`run.csx`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) e [`function.proj`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) do GitHub na função.
    * `run.csx` é executado quando um novo BLOB é adicionado ao seu contêiner de blob de entrada.
    * `function.proj` lista as bibliotecas externas em seu código de função, por exemplo, o SDK do .NET do lote.
1. Altere os valores de espaço reservado das variáveis na função `Run()` do arquivo `run.csx` para refletir suas credenciais de armazenamento e lote. Você pode encontrar suas credenciais de conta de armazenamento e lote no portal do Azure na seção **chaves** da sua conta do lote.
    * Recupere suas credenciais de conta de armazenamento e lote no portal do Azure na seção **chaves** da sua conta do lote. 

## <a name="trigger-the-function-and-retrieve-results"></a>Disparar a função e recuperar os resultados

Carregue um ou todos os arquivos digitalizados do diretório [`input_files`](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) no GitHub para seu contêiner de entrada. Monitor Batch Explorer para confirmar que uma tarefa é adicionada ao `ocr-pool` para cada arquivo. Depois de alguns segundos, o arquivo com OCR aplicado é adicionado ao contêiner de saída. O arquivo é então visível e recuperável em Gerenciador de Armazenamento.

Além disso, você pode observar o arquivo de logs na parte inferior da janela do editor da Web Azure Functions, em que você verá mensagens como esta para cada arquivo que carregar para o contêiner de entrada:

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-05-29T19:45:25.848 [Information] Name of output text file: <outputTxtFile>
2019-05-29T19:45:25.848 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

Para baixar os arquivos de saída de Gerenciador de Armazenamento para seu computador local, primeiro selecione os arquivos desejados e, em seguida, selecione o **Download** na faixa de opções superior. 

> [!TIP]
> Os arquivos baixados serão pesquisáveis se forem abertos em um leitor de PDF.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como: 

> [!div class="checklist"]
> * Usar Batch Explorer para criar pools e trabalhos
> * Usar Gerenciador de Armazenamento para criar contêineres de BLOB e uma assinatura de acesso compartilhado (SAS)
> * Criar uma função do Azure disparada por blob
> * Carregar ficheiros de entrada para o Armazenamento
> * Monitorizar a execução de tarefas
> * Obter ficheiros de saída

* Para obter mais exemplos de como usar a API do .NET para agendar e processar cargas de trabalho do lote, consulte [os exemplos no GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp). 

* Para ver mais Azure Functions gatilhos que você pode usar para executar cargas de trabalho do lote, consulte [a documentação do Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings).
