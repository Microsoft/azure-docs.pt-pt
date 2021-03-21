---
title: Tutorial - Desencadear um trabalho de lote usando funções Azure
description: Tutorial - Aplique OCR em documentos digitalizados, uma vez que são adicionados a uma bolha de armazenamento
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: b441b4c4fcbeb089cef24c3a84fa33021e7840de
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97106387"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Tutorial: Desencadear um trabalho de lote utilizando funções Azure

Neste tutorial, você aprenderá a desencadear um trabalho de Batch usando [funções Azure](../azure-functions/functions-overview.md). Vamos percorrer um exemplo em que documentos adicionados a um recipiente de blob de armazenamento Azure têm reconhecimento de caracteres óticos (OCR) aplicados a eles através de Azure Batch. Para simplificar o processamento de OCR, configuraremos uma função Azure que executa uma função de OCR de lote cada vez que um ficheiro é adicionado ao recipiente blob. Saiba como:

> [!div class="checklist"]
> * Use o Batch Explorer para criar piscinas e empregos
> * Use o Storage Explorer para criar recipientes blob e uma assinatura de acesso partilhado (SAS)
> * Criar uma função Azure acionada por bolhas
> * Carregar ficheiros de entrada para o Armazenamento
> * Monitorizar a execução de tarefas
> * Obter ficheiros de saída

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma conta do Batch do Azure e uma conta de Armazenamento do Microsoft Azure associada. Consulte [a conta Criar um Lote](quick-create-portal.md#create-a-batch-account) para obter mais informações sobre como criar e ligar contas.
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Explorador do Storage do Azure](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Crie uma piscina de lote e um trabalho de lote usando o Batch Explorer

Nesta secção, você usará o Batch Explorer para criar o lote de lote e o trabalho de Lote que executará tarefas de OCR. 

### <a name="create-a-pool"></a>Criar um conjunto

1. Inscreva-se no Batch Explorer utilizando as suas credenciais Azure.
1. Crie uma piscina selecionando **Pools** na barra lateral esquerda e, em seguida, o botão **Adicionar** acima do formulário de pesquisa. 
    1. Escolha um ID e um nome de exibição. Vamos usar `ocr-pool` para este exemplo.
    1. Desagre o tipo de balança para **o tamanho fixo,** e desagre a contagem de nós dedicada para 3.
    1. Selecione **Ubuntu 18.04-LTS** como o sistema operativo.
    1. Escolha `Standard_f2s_v2` como o tamanho da máquina virtual.
    1. Ativar a tarefa inicial e adicionar o comando `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"` . Certifique-se de definir a identidade do utilizador como **utilizador predefinido de tarefa (Administrador)**, o que permite iniciar tarefas para incluir comandos com `sudo` .
    1. Selecione **OK**.
### <a name="create-a-job"></a>Criar uma tarefa

1. Crie um trabalho na piscina selecionando **Jobs** na barra lateral esquerda e, em seguida, o botão **Adicionar** acima do formulário de pesquisa. 
    1. Escolha um ID e um nome de exibição. Vamos usar `ocr-job` para este exemplo.
    1. Desemaça a piscina `ocr-pool` para, ou qualquer nome que escolha para a sua piscina.
    1. Selecione **OK**.


## <a name="create-blob-containers"></a>Criar recipientes blob

Aqui irá criar recipientes blob que armazenarão os seus ficheiros de entrada e saída para a função OCR Batch.

1. Inscreva-se no Storage Explorer utilizando as suas credenciais Azure.
1. Utilizando a conta de armazenamento ligada à sua conta Batch, crie dois recipientes blob (um para ficheiros de entrada, um para ficheiros de saída) seguindo os passos da [Create a blob container](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container).

Neste exemplo, o recipiente de entrada é nomeado `input` e é onde todos os documentos sem OCR são inicialmente carregados para processamento. O recipiente de saída é nomeado `output` e é onde o trabalho do Batch escreve documentos processados com OCR.  
    * Neste exemplo, chamaremos o nosso recipiente de `input` entrada, e o nosso recipiente de `output` saída.  
    * O recipiente de entrada é onde todos os documentos sem OCR são inicialmente carregados.  
    * O recipiente de saída é onde o trabalho do Batch escreve documentos com OCR.  

Crie uma assinatura de acesso partilhado para o seu recipiente de saída no Storage Explorer. Faça-o clicando à direita no recipiente de saída e selecionando **Obter Assinatura de Acesso Partilhado...**. Sob **Permissões,** verifique **write**. Não são necessárias outras permissões.  

## <a name="create-an-azure-function"></a>Criar uma Função do Azure

Nesta secção irá criar a Função Azure que aciona a função OCR Batch sempre que um ficheiro é enviado para o seu recipiente de entrada.

1. Siga os passos na [Criar uma função desencadeada pelo armazenamento Azure Blob](../azure-functions/functions-create-storage-blob-triggered-function.md) para criar uma função.
    1. Quando solicitado para uma conta de armazenamento, use a mesma conta de armazenamento que ligou à sua conta Batch.
    1. Para **a pilha de tempo de execução,** escolha .NET. Escreveremos a nossa função em C# para alavancar o Batch .NET SDK.
1. Assim que a função acionada pela bolha for criada, utilize o [`run.csx`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) e [`function.proj`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) do GitHub na Função.
    * `run.csx` é executado quando uma nova bolha é adicionada ao seu recipiente de bolha de entrada.
    * `function.proj` lista as bibliotecas externas no seu código de função, por exemplo, o Batch .NET SDK.
1. Altere os valores de espaço reservado das variáveis na `Run()` função do ficheiro para refletir as `run.csx` suas credenciais de Lote e armazenamento. Pode encontrar as suas credenciais de conta de lote e armazenamento no portal Azure na secção **Chaves** da sua conta Batch.
    * Recupere as credenciais de lote e de armazenamento no portal Azure na secção **Chaves** da sua conta Batch. 

## <a name="trigger-the-function-and-retrieve-results"></a>Desencadear a função e recuperar resultados

Faça o upload de todos ou de todos os ficheiros digitalizados do [`input_files`](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) diretório do GitHub para o seu recipiente de entrada. Monitorize o Batch Explorer para confirmar que uma tarefa é adicionada `ocr-pool` para cada ficheiro. Após alguns segundos, o ficheiro com OCR aplicado é adicionado ao recipiente de saída. O ficheiro é então visível e recuperável no Explorador de Armazenamento.

Além disso, pode ver o ficheiro de registos na parte inferior da janela do editor web Azure Functions, onde verá mensagens como esta para cada ficheiro que carregar para o seu recipiente de entrada:

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-05-29T19:45:25.848 [Information] Name of output text file: <outputTxtFile>
2019-05-29T19:45:25.848 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

Para descarregar os ficheiros de saída do Storage Explorer para a sua máquina local, selecione primeiro os ficheiros que pretende e, em seguida, selecione o **Download** na fita superior. 

> [!TIP]
> Os ficheiros descarregados são pesmáveis se abertos num leitor PDF.

## <a name="clean-up-resources"></a>Limpar os recursos

É cobrado o conjunto enquanto os nós estiverem em execução, mesmo se não existirem tarefas agendadas. Quando já não precisar do conjunto, elimine-o. Na vista da conta, selecione **Conjuntos** e o nome do conjunto. Em seguida, selecione **Eliminar**. Quando eliminar o conjunto, todos os resultados da tarefa nos nós são eliminados. No entanto, os ficheiros de saída permanecem na conta de armazenamento. Quando já não é necessário, pode também eliminar a conta Batch e a conta de armazenamento.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Use o Batch Explorer para criar piscinas e empregos
> * Use o Storage Explorer para criar recipientes blob e uma assinatura de acesso partilhado (SAS)
> * Criar uma função Azure acionada por bolhas
> * Carregar ficheiros de entrada para o Armazenamento
> * Monitorizar a execução de tarefas
> * Obter ficheiros de saída


Continue explorando as aplicações de renderização disponíveis através do Batch Explorer na secção **Galeria.** Há vários modelos disponíveis para cada aplicação, que vão aumentar ao longo do tempo. Por exemplo, para os modelos do Blender, existe um que divide uma imagem em mosaicos, pelo que é possível compor partes de uma imagem em paralelo.

Para obter mais exemplos de utilização da API .NET para agendar e processar cargas de trabalho do Batch, veja os exemplos no GitHub.

> [!div class="nextstepaction"]
> [Exemplos de C# para o Batch](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp)
