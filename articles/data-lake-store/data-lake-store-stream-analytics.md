---
title: Os dados do stream analytics para data lake storage gen1 - Azure
description: Aprenda a usar a Azure Data Lake Storage Gen1 como uma saída para um trabalho do Azure Stream Analytics, com um cenário simples que lê dados de uma bolha de armazenamento Azure.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/30/2018
ms.author: twooley
ms.openlocfilehash: 4c289ecb1d8471a7b99f1d4c85a0163de4d0c593
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91576222"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>Transmita os dados da Azure Storage Blob para a Azure Data Lake Storage Gen1 usando a Azure Stream Analytics
Neste artigo, você aprende a usar Azure Data Lake Storage Gen1 como uma saída para um trabalho Azure Stream Analytics. Este artigo demonstra um cenário simples que lê dados a partir de uma bolha de armazenamento Azure (entrada) e escreve os dados para data lake storage gen1 (saída).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte:

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta de Armazenamento Azure**. Utilizará um recipiente blob desta conta para inserir dados para um trabalho de Stream Analytics. Para este tutorial, assuma que tem uma conta de armazenamento chamada **storageforasa** e um contentor dentro da conta chamada **storageforasacontainer**. Depois de ter criado o recipiente, carreve um ficheiro de dados de amostra. 
  
* **Uma conta gen1 de armazenamento de data lake.** Siga as instruções da [Azure Data Lake Storage Gen1 utilizando o portal Azure](data-lake-store-get-started-portal.md). Vamos supor que tem uma conta da Data Lake Storage Gen1 chamada **myadlsg1.** 

## <a name="create-a-stream-analytics-job"></a>Criar uma Tarefa do Stream Analytics
Começa por criar um trabalho stream Analytics que inclui uma fonte de entrada e um destino de saída. Para este tutorial, a fonte é um recipiente de bolhas Azure e o destino é Data Lake Storage Gen1.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. A partir do painel esquerdo, clique em **stream analytics e,** em seguida, clique em **Adicionar**.

    ![Criar um trabalho de análise de fluxo](./media/data-lake-store-stream-analytics/create.job.png "Criar uma tarefa do Stream Analytics")

    > [!NOTE]
    > Certifique-se de criar emprego na mesma região que a conta de armazenamento ou incorrerá em custos adicionais de movimentação de dados entre regiões.
    >

## <a name="create-a-blob-input-for-the-job"></a>Crie uma entrada Blob para o trabalho

1. Abra a página para o trabalho Stream Analytics, a partir do painel esquerdo clique no separador **Entradas** e, em seguida, clique em **Adicionar**.

    ![Screenshot da lâmina de trabalho stream Analytics com a opção Inputs e a opção de entrada de fluxo adicionar chamada.](./media/data-lake-store-stream-analytics/create.input.1.png "Adicione uma entrada ao seu trabalho")

2. Na nova lâmina **de entrada,** forneça os seguintes valores.

    ![Screenshot do armazenamento Blob - nova lâmina de entrada.](./media/data-lake-store-stream-analytics/create.input.2.png "Adicione uma entrada ao seu trabalho")

   * Para **inserir pseudónimo, insira** um nome único para a entrada de trabalho.
   * Para **o tipo de Origem**, selecione Data **stream**.
   * Para **obter**, selecione **Blob storage**.
   * Para **subscrição**, selecione **Use blob storage from current subscription**.
   * Para **a conta de Armazenamento**, selecione a conta de armazenamento que criou como parte dos pré-requisitos. 
   * Para **o Contentor,** selecione o recipiente que criou na conta de armazenamento selecionada.
   * Para **o formato de serialização do evento**, selecione **CSV**.
   * Para **Delimiter**, **selecione separador**.
   * Para **codificação,** selecione **UTF-8**.

     Clique em **Criar**. O portal adiciona agora a entrada e testa a ligação ao mesmo.


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>Criar uma saída gen1 de armazenamento de data lake para o trabalho

1. Abra a página para o trabalho stream Analytics, clique no separador **Saídas,** clique em **Adicionar** e selecione **Data Lake Storage Gen1**.

    ![Screenshot da lâmina stream Analytics Job com a opção Outputs, Adicionar opção e Data Lake Storage Gen 1 chamada.](./media/data-lake-store-stream-analytics/create.output.1.png "Adicione uma saída ao seu trabalho")

2. Na lâmina **de saída Nova,** forneça os seguintes valores.

    ![Screenshot da Data Lake Storage Gen 1 - nova lâmina de saída com a opção Authorize chamada.](./media/data-lake-store-stream-analytics/create.output.2.png "Adicione uma saída ao seu trabalho")

    * Para **o pseudónimo de saída, insira** um nome único para a saída do trabalho. Este é um nome amigável usado em consultas para direcionar a saída de consulta para esta conta de Data Lake Storage Gen1.
    * Será solicitado que autorize o acesso à conta Gen1 de armazenamento de data lake. Clique **em Autorizor**.

3. Na **lâmina de saída Nova,** continue a fornecer os seguintes valores.

    ![Screenshot do Data Lake Storage Gen 1 - nova lâmina de saída.](./media/data-lake-store-stream-analytics/create.output.3.png "Adicione uma saída ao seu trabalho")

   * Para **o nome da conta,** selecione a conta Desasusição gen1 de Armazenamento de Dados que já criou para onde pretende que a saída do trabalho seja enviada.
   * Para **o padrão de prefixo path,** insira um caminho de ficheiro utilizado para escrever os seus ficheiros dentro da conta gen1 de armazenamento de datas especificada.
   * Para **o formato Data**, se utilizar um token de data no caminho do prefixo, pode selecionar o formato de data em que os seus ficheiros estão organizados.
   * Para **o formato Time**, se utilizar um token de tempo no caminho do prefixo, especifique o formato de tempo em que os seus ficheiros estão organizados.
   * Para **o formato de serialização do evento**, selecione **CSV**.
   * Para **Delimiter**, **selecione separador**.
   * Para **codificação,** selecione **UTF-8**.
    
     Clique em **Criar**. O portal adiciona agora a saída e testa a ligação ao mesmo.
    
## <a name="run-the-stream-analytics-job"></a>Executar a tarefa do Stream Analytics

1. Para executar um trabalho stream Analytics, você tem que executar uma consulta a partir do **separador Consulta.** Para este tutorial, pode executar a consulta de amostra substituindo os espaços reservados com os pseudónimos de entrada e saída de trabalho, como mostra a captura do ecrã abaixo.

    ![Consulta de execução](./media/data-lake-store-stream-analytics/run.query.png "Executar consulta")

2. Clique em **Guardar** a partir da parte superior do ecrã e, em seguida, a partir do **separador Vista Geral,** clique em **Iniciar**. A partir da caixa de diálogo, selecione **Hora Personalizada** e, em seguida, defina a data e hora atuais.

    ![Definir tempo de trabalho](./media/data-lake-store-stream-analytics/run.query.2.png "Definir tempo de trabalho")

    Clique **em Começar** a trabalhar. Pode levar até alguns minutos para começar o trabalho.

3. Para ativar o trabalho para recolher os dados da bolha, copie um ficheiro de dados de amostra para o recipiente blob. Pode obter um ficheiro de dados de amostra do [Repositório Azure Data Lake Git.](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt) Para este tutorial, vamos copiar o ficheiro **vehicle1_09142014.csv**. Pode utilizar vários clientes, como [o Azure Storage Explorer,](https://storageexplorer.com/)para enviar dados para um recipiente de bolhas.

4. A partir do **separador Visão** Geral, em **Monitorização,** veja como os dados foram tratados.

    ![Monitorizar tarefa](./media/data-lake-store-stream-analytics/run.query.3.png "Monitorizar tarefa")

5. Finalmente, pode verificar se os dados de saída do trabalho estão disponíveis na conta da Data Lake Storage Gen1. 

    ![Verificar a saída](./media/data-lake-store-stream-analytics/run.query.4.png "Verificar a saída")

    No painel do Data Explorer, note que a saída é escrita para um caminho de pasta, conforme especificado nas definições de saída da Data Lake Storage Gen1 `streamanalytics/job/output/{date}/{time}` ().  

## <a name="see-also"></a>Ver também
* [Crie um cluster HDInsight para utilizar a Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
