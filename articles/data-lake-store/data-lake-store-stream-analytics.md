---
title: Transmita dados do Stream Analytics para o Azure Data Lake Storage Gen1 [ Microsoft Docs
description: Use o Azure Stream Analytics para transmitir dados para o Azure Data Lake Storage Gen1
services: data-lake-store,stream-analytics
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: edb58e0b-311f-44b0-a499-04d7e6c07a90
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: twooley
ms.openlocfilehash: d3dbacd58b3bda3fbf8ee8ad5f175eccc2cb2a24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60194948"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>Transmita dados do Azure Storage Blob para o Azure Data Lake Storage Gen1 usando o Azure Stream Analytics
Neste artigo você aprenderá a usar Azure Data Lake Storage Gen1 como uma saída para um trabalho de Azure Stream Analytics. Este artigo demonstra um cenário simples que lê dados de uma bolha de armazenamento azure (entrada) e escreve os dados para Data Lake Storage Gen1 (saída).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte:

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta de Armazenamento Azure.** Utilizará um recipiente de bolha desta conta para inserir dados para um trabalho de Stream Analytics. Para este tutorial, assuma que tem uma conta de armazenamento chamada **storageforasa** e um recipiente dentro da conta chamada **storageforasacontainer**. Depois de ter criado o recipiente, faça o upload de um ficheiro de dados de amostras para o mesmo. 
  
* **Uma conta Gen1 de Armazenamento de Lago**de Dados. Siga as instruções no [Get started com Azure Data Lake Storage Gen1 utilizando o Portal Azure](data-lake-store-get-started-portal.md). Vamos supor que tem uma conta data Lake Storage Gen1 chamada **myadlsg1**. 

## <a name="create-a-stream-analytics-job"></a>Criar uma Tarefa do Stream Analytics
Começa-se por criar um trabalho de Stream Analytics que inclui uma fonte de entrada e um destino de saída. Para este tutorial, a fonte é um recipiente de blob Azure e o destino é Data Lake Storage Gen1.

1. Inscreva-se no [Portal Azure.](https://portal.azure.com)

2. A partir do painel esquerdo, clique em **trabalhos de Stream Analytics,** e depois clique em **Adicionar**.

    ![Criar um trabalho de análise de fluxo](./media/data-lake-store-stream-analytics/create.job.png "Criar uma tarefa do Stream Analytics")

    > [!NOTE]
    > Certifique-se de criar emprego na mesma região que a conta de armazenamento ou incorrerá em custos adicionais de movimentação de dados entre regiões.
    >

## <a name="create-a-blob-input-for-the-job"></a>Criar uma entrada Blob para o trabalho

1. Abra a página para o trabalho de Stream Analytics, a partir do painel esquerdo clique no separador **Inputs** e, em seguida, clique em **Adicionar**.

    ![Adicione uma entrada ao seu trabalho](./media/data-lake-store-stream-analytics/create.input.1.png "Adicione uma entrada ao seu trabalho")

2. Na nova lâmina de **entrada,** forneça os seguintes valores.

    ![Adicione uma entrada ao seu trabalho](./media/data-lake-store-stream-analytics/create.input.2.png "Adicione uma entrada ao seu trabalho")

   * Para **o pseudónimo de entrada,** insira um nome único para a entrada de trabalho.
   * Para **o tipo Fonte,** selecione **data stream**.
   * Para **origem,** selecione **depósito Blob**.
   * Para **subscrição,** **selecione Utilize o armazenamento blob a partir da subscrição atual**.
   * Para **a conta de Armazenamento,** selecione a conta de armazenamento que criou como parte dos pré-requisitos. 
   * Para **o Recipiente,** selecione o recipiente que criou na conta de armazenamento selecionada.
   * Para o formato de **serialização do evento,** selecione **CSV**.
   * Para **Delimitador,** selecione **.**
   * Para **codificação,** selecione **UTF-8**.

     Clique em **Criar**. O portal adiciona agora a entrada e testa a ligação ao mesmo.


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>Criar uma produção de Gen1 de Armazenamento de Data Lake para o trabalho

1. Abra a página para o trabalho do Stream Analytics, clique no separador **Outputs,** clique em **Adicionar**, e selecione **Data Lake Storage Gen1**.

    ![Adicione uma saída ao seu trabalho](./media/data-lake-store-stream-analytics/create.output.1.png "Adicione uma saída ao seu trabalho")

2. Na lâmina de **saída Nova,** forneça os seguintes valores.

    ![Adicione uma saída ao seu trabalho](./media/data-lake-store-stream-analytics/create.output.2.png "Adicione uma saída ao seu trabalho")

    * Para **o pseudónimo De saída,** insira um nome único para a saída de trabalho. Este é um nome amigável usado em consultas para direcionar a saída de consulta para esta conta Data Lake Storage Gen1.
    * Será solicitado a autorizar o acesso à conta Gen1 de Armazenamento de Data Lake. Clique **em Autorizar**.

3. Na lâmina de **saída Nova,** continue a fornecer os seguintes valores.

    ![Adicione uma saída ao seu trabalho](./media/data-lake-store-stream-analytics/create.output.3.png "Adicione uma saída ao seu trabalho")

   * Para **nome de Conta,** selecione a conta Data Lake Storage Gen1 que já criou para onde pretende que a saída de trabalho seja enviada.
   * Para o padrão de **prefixo path,** introduza um caminho de ficheiro utilizado para escrever os seus ficheiros dentro da conta específica data Lake Storage Gen1.
   * Para **o formato Date**, se usou um token de data no caminho de prefixo, pode selecionar o formato de data no qual os seus ficheiros estão organizados.
   * Para o **formato Time**, se usou um símbolo de tempo no caminho de prefixo, especifique o formato de tempo no qual os seus ficheiros estão organizados.
   * Para o formato de **serialização do evento,** selecione **CSV**.
   * Para **Delimitador,** selecione **.**
   * Para **codificação,** selecione **UTF-8**.
    
     Clique em **Criar**. O portal adiciona agora a saída e testa a ligação ao mesmo.
    
## <a name="run-the-stream-analytics-job"></a>Executar a tarefa do Stream Analytics

1. Para executar um trabalho de Stream Analytics, você deve executar uma consulta a partir do separador **Consulta.** Para este tutorial, pode executar a consulta da amostra substituindo os espaços reservados pelos pseudónimos de entrada e saída de trabalho, como mostra a captura do ecrã abaixo.

    ![Executar consulta](./media/data-lake-store-stream-analytics/run.query.png "Executar consulta")

2. Clique em **Guardar** a partir da parte superior do ecrã e, em seguida, a partir do separador **Overview,** clique em **Iniciar**. A partir da caixa de diálogo, selecione **Custom Time**, e, em seguida, definir a data e hora atuais.

    ![Definir o tempo de trabalho](./media/data-lake-store-stream-analytics/run.query.2.png "Definir o tempo de trabalho")

    Clique **em Começar** a trabalhar. Pode levar até alguns minutos para começar o trabalho.

3. Para desencadear o trabalho para recolher os dados da bolha, copie um ficheiro de dados de amostra para o recipiente de bolhas. Pode obter um ficheiro de dados de amostra do [Repositório Azure Data Lake Git](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Para este tutorial, vamos copiar o ficheiro **vehicle1_09142014.csv.** Pode utilizar vários clientes, como o [Azure Storage Explorer,](https://storageexplorer.com/)para fazer o upload de dados para um contentor de bolhas.

4. A partir do separador **Overview,** sob **Monitorização,** veja como os dados foram processados.

    ![Monitorizar tarefa](./media/data-lake-store-stream-analytics/run.query.3.png "Monitorizar tarefa")

5. Finalmente, pode verificar se os dados de saída de emprego estão disponíveis na conta Data Lake Storage Gen1. 

    ![Verificar a saída](./media/data-lake-store-stream-analytics/run.query.4.png "Verificar a saída")

    No painel do Data Explorer, note que a saída está escrita para um caminho de`streamanalytics/job/output/{date}/{time}`pasta, conforme especificado nas definições de saída do Data Lake Storage Gen1 ().  

## <a name="see-also"></a>Consulte também
* [Crie um cluster HDInsight para usar data lake storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
