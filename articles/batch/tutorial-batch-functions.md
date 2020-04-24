---
title: Desencadear um trabalho de lote usando funções azure
description: Tutorial - Aplique OCR em documentos digitalizados à medida que são adicionados a uma bolha de armazenamento
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc
ms.openlocfilehash: 01c3ab167239affa4d7ae94f5649d60072c3c270
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117170"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Tutorial: Desencadear um trabalho de lote usando funções azure

Neste tutorial, você aprenderá a desencadear um trabalho de Lote usando funções Azure. Vamos percorrer um exemplo em que os documentos adicionados a um recipiente de blob Azure Storage têm reconhecimento ótico de caracteres (OCR) aplicado saqueado através do Lote Azure. Para simplificar o processamento de OCR, configuraremos uma função Azure que executa um trabalho de OCR de lote cada vez que um ficheiro é adicionado ao recipiente de bolhas.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma conta do Batch do Azure e uma conta de Armazenamento do Microsoft Azure associada. Consulte [criar uma conta de Lote](quick-create-portal.md#create-a-batch-account) para obter mais informações sobre como criar e ligar contas.
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Explorador do Storage do Azure](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Crie uma piscina de lote e trabalho de lote usando o Batch Explorer

Nesta secção, você usará o Batch Explorer para criar a piscina de lote e o trabalho de Lote que executará tarefas OCR. 

### <a name="create-a-pool"></a>Criar um conjunto

1. Inscreva-se no Batch Explorer utilizando as suas credenciais Azure.
1. Crie uma piscina selecionando **Pools** na barra lateral esquerda e, em seguida, o botão **Adicionar** acima do formulário de pesquisa. 
    1. Escolha um nome de identificação e exibição. Usaremos `ocr-pool` para este exemplo.
    1. Dete teo tipo de calcário para **o tamanho fixo,** e desloque a contagem de nódedicada para 3.
    1. Selecione **Ubuntu 18.04-LTS** como sistema operativo.
    1. Escolha `Standard_f2s_v2` como o tamanho da máquina virtual.
    1. Ative a tarefa inicial `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"`e adicione o comando . Certifique-se de definir a identidade do utilizador como **utilizador predefinido de tarefa (Admin),** que permite que as tarefas de início incluam comandos com `sudo`.
    1. Selecione **OK**.
### <a name="create-a-job"></a>Criar uma tarefa

1. Crie um trabalho na piscina selecionando **Jobs** na barra lateral esquerda e, em seguida, o botão **Adicionar** acima do formulário de pesquisa. 
    1. Escolha um nome de identificação e exibição. Usaremos `ocr-job` para este exemplo.
    1. Detete `ocr-pool`a piscina para, ou qualquer nome que você escolheu para a sua piscina.
    1. Selecione **OK**.


## <a name="create-blob-containers"></a>Criar recipientes de bolha

Aqui irá criar recipientes blob que armazenarão os seus ficheiros de entrada e saída para o trabalho do Lote OCR.

1. Inscreva-se no Storage Explorer utilizando as suas credenciais Azure.
1. Utilizando a conta de armazenamento ligada à sua conta Batch, crie dois recipientes blob (um para ficheiros de entrada, um para ficheiros de saída) seguindo os passos em [Criar um recipiente de bolha](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#create-a-blob-container).

Neste exemplo, o recipiente de `input` entrada é nomeado e é onde todos os documentos sem OCR são inicialmente carregados para processamento. O recipiente de `output` saída está nomeado e é onde o trabalho do Lote escreve documentos processados com OCR.  
    * Neste exemplo, chamaremos o nosso `input`recipiente de `output`entrada , e o nosso recipiente de saída .  
    * O recipiente de entrada é onde todos os documentos sem OCR são inicialmente carregados.  
    * O recipiente de saída é onde o trabalho do Lote escreve documentos com OCR.  

Crie uma assinatura de acesso partilhada para o seu recipiente de saída no Storage Explorer. Faça-o clicando à direita no recipiente de saída e selecionando Obter Assinatura de **Acesso Partilhado...**. Sob **permissões,** verifique **Escrever**. Não são necessárias outras permissões.  

## <a name="create-an-azure-function"></a>Criar uma Função do Azure

Nesta secção, criará a Função Azure que aciona o trabalho do Lote OCR sempre que um ficheiro é enviado para o seu recipiente de entrada.

1. Siga os passos em [Criar uma função desencadeada pelo armazenamento De Blob Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-storage-blob-triggered-function) para criar uma função.
    1. Quando solicitado para uma conta de armazenamento, use a mesma conta de armazenamento que ligou à sua conta 'Lote'.
    1. Para a pilha de **tempo de execução,** escolha .NET. Vamos escrever a nossa função em C# para alavancar o Lote .NET SDK.
1. Uma vez criada a função desencadeada [`run.csx`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) por [`function.proj`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) bolhas, utilize a e a partir do GitHub na Função.
    * `run.csx`é executado quando uma nova bolha é adicionada ao seu recipiente de entrada blob.
    * `function.proj`lista as bibliotecas externas no seu código de função, por exemplo, o Lote .NET SDK.
1. Altere os valores do espaço `Run()` reservado `run.csx` das variáveis na função do ficheiro para refletir as suas credenciais de Lote e armazenamento. Pode encontrar as suas credenciais de conta de Lote e armazenamento no portal Azure na secção **Chaves** da sua conta Batch.
    * Recupere as credenciais da sua conta de Lote e armazenamento no portal Azure na secção **Chaves** da sua conta Batch. 

## <a name="trigger-the-function-and-retrieve-results"></a>Desencadear a função e recuperar resultados

Faça upload de todos ou todos [`input_files`](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) os ficheiros digitalizados do diretório do GitHub para o seu recipiente de entrada. Monitor Ize o Batch Explorer para `ocr-pool` confirmar que uma tarefa é adicionada para cada ficheiro. Após alguns segundos, o ficheiro com OCR aplicado é adicionado ao recipiente de saída. O ficheiro é então visível e recuperável no Storage Explorer.

Além disso, pode ver o ficheiro de registos na parte inferior da janela do editor web do Azure Functions, onde verá mensagens como esta para cada ficheiro que enviar para o seu recipiente de entrada:

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-05-29T19:45:25.848 [Information] Name of output text file: <outputTxtFile>
2019-05-29T19:45:25.848 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

Para descarregar os ficheiros de saída do Storage Explorer para a sua máquina local, primeiro selecione os ficheiros que deseja e, em seguida, selecione o **Download** na fita superior. 

> [!TIP]
> Os ficheiros descarregados são pesquisáveis se forem abertos num leitor pdf.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como: 

> [!div class="checklist"]
> * Use o Batch Explorer para criar piscinas e empregos
> * Utilize o Storage Explorer para criar recipientes blob e uma assinatura de acesso partilhado (SAS)
> * Criar uma função Azure desencadeada por bolhas
> * Carregar ficheiros de entrada para o Armazenamento
> * Monitorizar a execução de tarefas
> * Obter ficheiros de saída

* Para mais exemplos de utilização da API .NET para agendar e processar as cargas de trabalho do Lote, consulte [as amostras no GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp). 

* Para ver mais funções Azure dispara que pode utilizar para executar cargas de trabalho do Lote, consulte [a documentação das Funções Azure](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings).
