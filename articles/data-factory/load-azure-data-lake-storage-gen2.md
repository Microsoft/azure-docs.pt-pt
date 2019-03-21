---
title: Carregar dados para Gen2 de armazenamento do Azure Data Lake com o Azure Data Factory
description: Utilize o Azure Data Factory para copiar dados para a geração 2 de armazenamento do Azure Data Lake
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: jingwang
ms.openlocfilehash: ad6fd4a75ca139c4943ca0d079a2d041ccb46d09
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101096"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Carregar dados para Gen2 de armazenamento do Azure Data Lake com o Azure Data Factory

Geração de armazenamento 2 do Azure Data Lake é um conjunto de recursos dedicado a análise de macrodados, incorporado ao [armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md). Permite-lhe interagir com os dados através de ambos os paradigmas de armazenamento de sistema e o objeto de ficheiro.

O Azure Data Factory é um serviço de integração de dados totalmente gerido com base na cloud. Pode utilizar o serviço para preencher o lake com dados a partir de um conjunto avançado de no local e os arquivos de dados com base na cloud e poupam tempo quando criar as suas soluções de análise. Para obter uma lista detalhada de conectores suportados, consulte a tabela de [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).

O Azure Data Factory oferece uma solução de movimento de dados de escalamento horizontal e gerida. Devido à arquitetura de escalamento horizontal do ADF, ele pode ingerir dados em alto débito. Para obter detalhes, consulte [copie o desempenho de atividade](copy-activity-performance.md).

Este artigo mostra-lhe como utilizar a ferramenta copiar dados do Data Factory para carregar dados a partir _serviço Amazon Web Services S3_ em _Gen2 de armazenamento do Azure Data Lake_. Pode seguir passos semelhantes para copiar dados de outros tipos de arquivos de dados.

>[!TIP]
>Para copiar dados do Azure Data Lake Storage Gen1 na geração 2, consulte [estas instruções específicas](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Conta de armazenamento do Azure com o Data Lake Storage Gen2 ativada: Se não tiver uma conta de armazenamento, clique em [aqui](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) para criar um.
* Conta do AWS com um registo de S3 que contém dados: Este artigo mostra como copiar dados do Amazon S3. Pode utilizar outros arquivos de dados ao seguir passos semelhantes.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu da esquerda, selecione **New** > **dados + análise** > **Data Factory**:
   
   ![Criar uma nova fábrica de dados](./media/load-azure-data-lake-storage-gen2/new-azure-data-factory-menu.png)
2. Na **nova fábrica de dados** página, fornecer valores para os campos que são mostrados na imagem seguinte: 
      
   ![Página Nova fábrica de dados](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **Nome**: Introduza um nome globalmente exclusivo para a fábrica de dados do Azure. Se receber o erro "nome do Data factory \"LoadADLSDemo\" não está disponível," insira um nome diferente para a fábrica de dados. Por exemplo, poderia usar o nome  _**yourname**_**ADFTutorialDataFactory**. Tente criar a fábrica de dados novamente. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Subscrição**: Selecione a sua subscrição do Azure na qual pretende criar a fábrica de dados. 
    * **Grupo de recursos**: Selecione um grupo de recursos existente na lista pendente ou selecione o **criar novo** opção e introduza o nome de um grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versão**: Selecione **V2**.
    * **Localização**: Selecione a localização da fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. Os arquivos de dados que são utilizados pelo data factory podem estar noutras localizações e regiões. 

3. Selecione **Criar**.
4. Depois de concluída a criação, vá para a fábrica de dados. Verá o **fábrica de dados** home page do conforme mostrado na imagem seguinte: 
   
   ![Home page da fábrica de dados](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   Selecione o **criar e monitorizar** mosaico para iniciar o aplicativo de integração de dados num separador à parte.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Carregar dados para a geração 2 de armazenamento do Azure Data Lake

1. Na **começar** página, selecione a **copiar dados** mosaico para iniciar a ferramenta copiar dados: 

   ![Mosaico ferramenta Copiar Dados](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. Na **propriedades** , especifique **CopyFromAmazonS3ToADLS** para o **nome da tarefa** campo e selecione **seguinte**:

    ![Página Propriedades](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. Na **o arquivo de dados de origem** página, clique em **+ criar nova ligação**:

    ![Página de arquivo de dados de origem](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    Selecione **Amazon S3** da galeria do conector e selecione **continuar**
    
    ![Página de s3 do arquivo de dados de origem](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Na **ligação de especificar o Amazon S3** página, efetue os seguintes passos:

   1. Especifique a **ID de chave de acesso** valor.
   2. Especifique a **chave de acesso secreta** valor.
   3. Clique em **Testar ligação** para validar as definições, em seguida, selecione **concluir**.
   4. Verá, que é criada uma nova ligação. Selecione **Seguinte**.
   
      ![Especifique a conta do Amazon S3](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
      
5. Na **escolher o ficheiro de entrada ou a pasta** página, navegue para a pasta e ficheiro que pretende que devem transitar. Selecione o ficheiro/pasta, selecione **escolha**:

    ![Escolher ficheiro ou pasta de entrada](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Especificar o comportamento de cópia, verificando a **recursivamente de ficheiros de cópia** e **cópia binária** opções. Selecione **seguinte**:

    ![Especifique a pasta de saída](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. Na **o arquivo de dados de destino** página, clique em **+ criar nova ligação**e, em seguida, selecione **Gen2 de armazenamento do Azure Data Lake**e selecione **continuar**:

    ![Página arquivo de dados de destino](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Na **ligação de especificar o Azure Data Lake armazenamento** página, efetue os seguintes passos:

   1. Selecione seu Gen2 de armazenamento do Data Lake conta com capacidade do "nome de conta de armazenamento" na lista pendente.
   2. Selecione **concluir** para criar a ligação. Em seguida, selecione **Seguinte**.
   
   ![Especifique a conta de geração 2 de armazenamento do Azure Data Lake](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. Na **escolher o ficheiro de saída ou a pasta** página, introduza **copyfroms3** como o nome da pasta de saída e selecione **seguinte**: 

    ![Especifique a pasta de saída](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. Na **definições** página, selecione **próxima** para utilizar as predefinições:

    ![Página de definições](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. Na **resumo** página, reveja as definições e selecione **próxima**:

    ![Página de resumo](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. Na **página de implementação**, selecione **Monitor** para monitorizar o pipeline:

    ![Página de implementação](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. O **ações** coluna inclui ligações para ver os detalhes da execução da atividade e voltar a executar o pipeline:

    ![Monitorizar execuções de pipeline](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Para ver as execuções de atividades que estão associadas à execução do pipeline, selecione o **ver execuções de atividades** ligação na **ações** coluna. Há apenas uma atividade (atividade copiar) no pipeline, pelo que só vai ver uma entrada. Para regressar à vista de execuções de pipeline, selecione o **Pipelines** link na parte superior. Selecione **Atualizar** para atualizar a lista. 

    ![Monitorização de execuções de atividade](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. Para monitorizar os detalhes da execução para cada atividade de cópia, selecione o **detalhes** ligação (imagem de óculos), em **ações** na atividade de vista de monitorização. Pode monitorizar detalhes como o volume de dados copiados da origem para o sink, taxa de transferência de dados, os passos de execução com duração correspondente e utilizado configurações:

    ![Monitorizar a atividade de detalhes da execução](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. Certifique-se de que os dados são copiados para a sua conta de geração 2 de armazenamento do Data Lake.

## <a name="best-practices"></a>Melhores práticas

Quando copiar grande volume de dados de arquivo de dados baseados em ficheiros, são sugeridas para:

- Os ficheiros de partição para 10TB para fileset 30TB cada.
- Não acionam demasiados execuções de cópia simultâneos para evitar a limitação de arquivos de dados de origem ou sink. Pode começar com uma cópia, executar e monitorizar a taxa de transferência e depois gradualmente a adicionar mais conforme necessário.

## <a name="next-steps"></a>Passos Seguintes

* [Descrição geral da atividade de cópia](copy-activity-overview.md)
* [Conector de geração 2 de armazenamento do Data Lake do Azure](connector-azure-data-lake-storage.md)