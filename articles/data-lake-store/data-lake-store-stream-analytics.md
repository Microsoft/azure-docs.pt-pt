---
title: Stream data do Stream Analytics em Gen1 de armazenamento do Azure Data Lake | Documentos da Microsoft
description: Utiliza o Azure Stream Analytics para transmitir dados na geração 1 de armazenamento do Azure Data Lake
services: data-lake-store,stream-analytics
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: edb58e0b-311f-44b0-a499-04d7e6c07a90
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: nitinme
ms.openlocfilehash: 1b18bd5aae398d2ec942120af5d96943636c346c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101116"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>Dados de Stream do Azure Storage Blob para a geração 1 de armazenamento do Azure Data Lake com o Azure Stream Analytics
Neste artigo, aprenderá como usar Gen1 de armazenamento do Azure Data Lake como uma saída para uma tarefa do Azure Stream Analytics. Este artigo demonstra um cenário simples que lê dados a partir de um blob de armazenamento do Azure (entrada) e escreve os dados de geração 1 de armazenamento do Data Lake (saída).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta de Armazenamento do Azure**. Irá utilizar um contentor de BLOBs a partir desta conta para dados de entrada para uma tarefa do Stream Analytics. Para este tutorial partem do princípio de que tem uma conta de armazenamento denominada **storageforasa** e um contentor na conta chamado **storageforasacontainer**. Depois de criar o contentor, carregar um ficheiro de dados de exemplo para o mesmo. 
  
* **Uma conta do Data Lake Storage Gen1**. Siga as instruções em [introdução ao Azure Data Lake Storage Gen1 através do Portal do Azure](data-lake-store-get-started-portal.md). Vamos supor que tem uma conta do Data Lake Storage Gen1 denominada **myadlsg1**. 

## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics
Comece por criar uma tarefa do Stream Analytics que inclui uma origem de entrada e um destino de saída. Para este tutorial, a origem é um contentor de Blobs do Azure e o destino é a geração 1 de armazenamento do Data Lake.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. No painel esquerdo, clique em **tarefas do Stream Analytics**e, em seguida, clique em **Add**.

    ![Criar uma tarefa do Stream Analytics](./media/data-lake-store-stream-analytics/create.job.png "criar uma tarefa do Stream Analytics")

    > [!NOTE]
    > Certifique-se de criar a tarefa na mesma região que a conta de armazenamento ou irá incorrer em custos adicionais de mover dados entre regiões.
    >

## <a name="create-a-blob-input-for-the-job"></a>Criar uma entrada de BLOBs para a tarefa

1. Abra a página para a tarefa de Stream Analytics, no painel esquerdo clique nas **entradas** separador e, em seguida, clique em **Add**.

    ![Adicionar uma entrada para a tarefa](./media/data-lake-store-stream-analytics/create.input.1.png "adicionar uma entrada para a tarefa")

2. Sobre o **nova entrada** painel, forneça os valores seguintes.

    ![Adicionar uma entrada para a tarefa](./media/data-lake-store-stream-analytics/create.input.2.png "adicionar uma entrada para a tarefa")

   * Para **alias de entrada**, introduza um nome exclusivo para a tarefa de entrada.
   * Para **tipo de origem**, selecione **fluxo de dados**.
   * Para **origem**, selecione **armazenamento de BLOBs**.
   * Para **subscrição**, selecione **armazenamento de BLOBs de utilização da subscrição atual**.
   * Para **conta de armazenamento**, selecione a conta de armazenamento que criou como parte dos pré-requisitos. 
   * Para **contentor**, selecione o contentor que criou na conta de armazenamento selecionada.
   * Para **formato de serialização de eventos**, selecione **CSV**.
   * Para **delimitador**, selecione **separador**.
   * Para **Encoding**, selecione **UTF-8**.

     Clique em **Criar**. Agora, o portal adiciona a entrada e testa a ligação ao mesmo.


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>Criar uma saída de geração 1 de armazenamento do Data Lake para o trabalho

1. Abra a página para a tarefa de Stream Analytics, clique nas **saídas** separador, clique em **Add**e selecione **Gen1 de armazenamento do Data Lake**.

    ![Adicionar uma saída para a sua tarefa](./media/data-lake-store-stream-analytics/create.output.1.png "adicionar uma saída para a sua tarefa")

2. Sobre o **nova saída** painel, forneça os valores seguintes.

    ![Adicionar uma saída para a sua tarefa](./media/data-lake-store-stream-analytics/create.output.2.png "adicionar uma saída para a sua tarefa")

    * Para **alias de saída**, introduza um nome exclusivo para o resultado da tarefa. Este é um nome amigável utilizado nas consultas para direcionar o resultado da consulta para esta conta de geração 1 de armazenamento do Data Lake.
    * Será solicitado para autorizar o acesso à conta de geração 1 de armazenamento do Data Lake. Clique em **autorizar**.

3. Sobre o **nova saída** painel, continuar a fornecer os seguintes valores.

    ![Adicionar uma saída para a sua tarefa](./media/data-lake-store-stream-analytics/create.output.3.png "adicionar uma saída para a sua tarefa")

   * Para **nome da conta**, selecione a conta de geração 1 do Data Lake Storage que já criou onde pretende que a tarefa de saída sejam enviados para.
   * Para **padrão de prefixo do caminho**, introduza um caminho de ficheiro utilizado para escrever ficheiros dentro da conta do Data Lake Storage Gen1 especificado.
   * Para **formato de data**, se utilizou um token de data no caminho de prefixo, pode selecionar o formato de data em que os ficheiros estão organizados.
   * Para **formato de hora**, se utilizou um token de tempo no caminho de prefixo, especifique o formato de hora em que os ficheiros estão organizados.
   * Para **formato de serialização de eventos**, selecione **CSV**.
   * Para **delimitador**, selecione **separador**.
   * Para **Encoding**, selecione **UTF-8**.
    
     Clique em **Criar**. Agora, o portal adiciona a saída e testa a ligação ao mesmo.
    
## <a name="run-the-stream-analytics-job"></a>Executar a tarefa do Stream Analytics

1. Para executar uma tarefa do Stream Analytics, tem de executar uma consulta do **consulta** separador. Para este tutorial, pode executar a consulta de exemplo ao substituir os marcadores de posição a tarefa de entrada e saída de aliases, conforme mostrado na captura de ecrã abaixo.

    ![Executar consulta](./media/data-lake-store-stream-analytics/run.query.png "executar consulta")

2. Clique em **salvar** na parte superior do ecrã e, em seguida, o **descrição geral** separador, clique em **iniciar**. Na caixa de diálogo, selecione **personalizado tempo**e, em seguida, defina a data e hora atuais.

    ![Definir o tempo de tarefa](./media/data-lake-store-stream-analytics/run.query.2.png "definir o tempo de tarefa")

    Clique em **iniciar** para iniciar a tarefa. Pode demorar até para alguns minutos para iniciar a tarefa.

3. Para acionar a tarefa para escolher os dados do blob, copie um ficheiro de dados de exemplo para o contentor de Blobs. Pode obter um ficheiro de dados de exemplo dos [repositório de Git do Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Para este tutorial, vamos copiar o ficheiro **vehicle1_09142014.csv**. Pode utilizar vários clientes, como [Explorador de armazenamento do Azure](https://storageexplorer.com/), para carregar dados para um contentor de Blobs.

4. Do **descrição geral** separador, em **monitorização**, veja como os dados foi processados.

    ![Tarefa de monitorização](./media/data-lake-store-stream-analytics/run.query.3.png "tarefa de monitorização")

5. Por fim, pode verificar que os dados de saída da tarefa estão disponíveis na conta de geração 1 de armazenamento do Data Lake. 

    ![Verificar a saída](./media/data-lake-store-stream-analytics/run.query.4.png "verificar a saída")

    No painel Data Explorer, tenha em atenção que o resultado é escrito para um caminho de pasta conforme especificado na geração de 1 de armazenamento do Data Lake saída configurações (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Consulte também
* [Criar um cluster do HDInsight para utilizar a geração 1 de armazenamento do Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
