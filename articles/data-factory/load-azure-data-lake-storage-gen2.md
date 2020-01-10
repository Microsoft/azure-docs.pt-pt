---
title: Carregar dados para o Azure Data Lake Storage Gen2
description: Use Azure Data Factory para copiar dados em Azure Data Lake Storage Gen2
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/13/2019
ms.openlocfilehash: 90573f77c77d614923f882053145d2f84598953d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440240"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Carregar dados em Azure Data Lake Storage Gen2 com Azure Data Factory

Azure Data Lake Storage Gen2 é um conjunto de recursos dedicados à análise de Big Data, criado no [armazenamento de BLOBs do Azure](../storage/blobs/storage-blobs-introduction.md). Permite-lhe interagir com os dados através de ambos os paradigmas de armazenamento de sistema e o objeto de ficheiro.

Azure Data Factory (ADF) é um serviço de integração de dados baseado em nuvem totalmente gerenciado. Você pode usar o serviço para popular o Lake com dados de um conjunto avançado de armazenamentos de dados locais e baseados em nuvem e economizar tempo ao criar soluções de análise. Para obter uma lista detalhada dos conectores com suporte, consulte a tabela de [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

O Azure Data Factory oferece uma solução de movimentação de dados gerenciada e escalável. Devido à arquitetura de expansão do ADF, ele pode ingerir dados em uma alta taxa de transferência. Para obter detalhes, consulte [desempenho da atividade de cópia](copy-activity-performance.md).

Este artigo mostra como usar a ferramenta de Copiar Dados de Data Factory para carregar dados do _serviço Amazon Web Services S3_ no _Azure data Lake Storage Gen2_. Você pode seguir etapas semelhantes para copiar dados de outros tipos de armazenamentos de dados.

>[!TIP]
>Para copiar dados de Azure Data Lake Storage Gen1 para Gen2, consulte [este passo a passos específico](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure: se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Conta de armazenamento do Azure com Data Lake Storage Gen2 habilitado: se você não tiver uma conta de armazenamento, [crie uma conta](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* Conta do AWS com um Bucket S3 que contém dados: Este artigo mostra como copiar dados do Amazon S3. Você pode usar outros armazenamentos de dados seguindo etapas semelhantes.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu à esquerda, selecione **criar um recurso** > **dados + análise** > **Data Factory**:
   
   ![Seleção do Data Factory no painel "Novo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na página **novo data Factory** , forneça valores para os campos mostrados na imagem a seguir: 
      
   ![Página Nova fábrica de dados](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **Nome**: Insira um nome globalmente exclusivo para sua data Factory do Azure. Se você receber o erro "o nome do data Factory \"LoadADLSDemo\" não está disponível", insira um nome diferente para o data factory. Por exemplo, você pode usar o nome _**Your**_ name**ADFTutorialDataFactory**. Tente criar o data factory novamente. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Assinatura**: selecione sua assinatura do Azure na qual criar o data Factory. 
    * **Grupo de recursos**: selecione um grupo de recursos existente na lista suspensa ou selecione a opção **criar novo** e insira o nome de um grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).  
    * **Versão**: selecione **v2**.
    * **Local**: selecione o local para o data Factory. Apenas são apresentadas as localizações suportadas na lista pendente. Os armazenamentos de dados usados pelo data factory podem estar em outros locais e regiões. 

3. Selecione **Criar**.
4. Após a conclusão da criação, vá para o data factory. Você verá o home page de **Data Factory** conforme mostrado na imagem a seguir: 
   
   ![Home page da fábrica de dados](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   Selecione o mosaico **Criar e Monitorizar** para iniciar a Aplicação de Integração de Dados num separador à parte.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Carregar dados para o Azure Data Lake Storage Gen2

1. Na página de **introdução** , selecione o bloco **copiar dados** para iniciar a ferramenta de copiar dados: 

   ![Mosaico ferramenta Copiar Dados](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. Na página **Propriedades** , especifique **CopyFromAmazonS3ToADLS** para o campo **nome da tarefa** e selecione **Avançar**:

    ![Página Propriedades](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. Na página **armazenamento de dados de origem** , clique em **+ criar nova conexão**:

    ![Página de arquivo de dados de origem](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    Selecione **Amazon S3** na galeria do conector e selecione **continuar**
    
    ![Página S3 do armazenamento de dados de origem](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Na página **especificar conexão do Amazon S3** , execute as seguintes etapas:

   1. Especifique o valor da **ID de chave de acesso** .
   2. Especifique o valor da **chave de acesso de segredo** .
   3. Clique em **Testar ligação** para validar as definições e, em seguida, selecione **Concluir**.
   4. Você verá que uma nova conexão é criada. Selecione **Seguinte**.
   
      ![Especificar conta do Amazon S3](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
      
5. Na página **Escolher o ficheiro ou pasta de entrada**, navegue até à pasta ou ficheiro que pretende copiar. Selecione a pasta/arquivo, selecione **escolher**:

    ![Escolher ficheiro ou pasta de entrada](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Especifique o comportamento de cópia ao selecionar as opções **Copiar ficheiros recursivamente** e **Cópia binária**. Selecione **Seguinte**:

    ![Especificar pasta de saída](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. Na página **armazenamento de dados de destino** , clique em **+ criar nova conexão**e selecione **Azure data Lake Storage Gen2**e selecione **continuar**:

    ![Página arquivo de dados de destino](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Na página **especificar conexão Azure data Lake Storage** , execute as seguintes etapas:

   1. Selecione sua conta com capacidade de Data Lake Storage Gen2 na lista suspensa "nome da conta de armazenamento".
   2. Selecione **Concluir** para criar a ligação. Em seguida, selecione **Seguinte**.
   
   ![Especificar conta de Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. Na página **escolher o arquivo de saída ou a pasta** , insira **copyfroms3** como o nome da pasta de saída e selecione **Avançar**. O ADF criará o sistema de arquivos ADLS Gen2 e as subpastas correspondentes durante a cópia, se ela não existir.

    ![Especificar pasta de saída](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. Na página **configurações** , selecione **Avançar** para usar as configurações padrão:

    ![Página de definições](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. Na página **Resumo** , examine as configurações e selecione **Avançar**:

    ![Página de resumo](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. Na **página implantação**, selecione **Monitor** para monitorar o pipeline:

    ![Página de implementação](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. A coluna **ações** inclui links para exibir detalhes da execução da atividade e executar novamente o pipeline:

    ![Monitorizar execuções de pipeline](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Para exibir as execuções de atividade que estão associadas à execução do pipeline, selecione o link **Exibir execuções de atividade** na coluna **ações** . Há apenas uma atividade (atividade copiar) no pipeline, pelo que só vai ver uma entrada. Para voltar para a exibição de execuções de pipeline, selecione o link **pipelines** na parte superior. Selecione **Atualizar** para atualizar a lista. 

    ![Monitorização de execuções de atividade](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. Para monitorar os detalhes de execução de cada atividade de cópia, selecione o link **detalhes** (imagem óculos) em **ações** no modo de exibição de monitoramento de atividade. Você pode monitorar detalhes como o volume de dados copiados da origem para o coletor, a taxa de transferência de dados, as etapas de execução com a duração correspondente e as configurações usadas:

    ![Detalhes da execução da atividade de monitor](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. Verifique se os dados são copiados para sua conta de Data Lake Storage Gen2.

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral da atividade de cópia](copy-activity-overview.md)
* [Conector de Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
