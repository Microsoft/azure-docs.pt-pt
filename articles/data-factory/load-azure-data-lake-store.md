---
title: Carregar dados para geração 1 de armazenamento do Azure Data Lake ao utilizar o Azure Data Factory | Documentos da Microsoft
description: Utilize o Azure Data Factory para copiar dados para a geração 1 de armazenamento do Azure Data Lake
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: d9bce32e87984193938099b96a358cc4495fd0c9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58119746"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Carregar dados para geração 1 de armazenamento do Azure Data Lake ao utilizar o Azure Data Factory

[Geração de armazenamento 1 do Azure Data Lake](../data-lake-store/data-lake-store-overview.md) (anteriormente conhecido como o Azure Data Lake Store) é um repositório de hiperescala de toda a empresa para cargas de trabalho de análise de macrodados. Geração 1 de armazenamento do Data Lake permite-lhe capturar dados de qualquer tamanho, tipo e velocidade de ingestão. Os dados são capturados num único local para análises exploratórias e operacionais.

O Azure Data Factory é um serviço de integração de dados totalmente gerido com base na cloud. Pode utilizar o serviço para preencher o lake com dados do sistema existente e poupar tempo quando criar as suas soluções de análise.

O Azure Data Factory oferece as seguintes vantagens para carregar dados para a geração 1 de armazenamento do Data Lake:

* **Fácil de configurar**: Um assistente passo 5 intuitivo com nenhum script necessário.
* **Suporte de arquivo de dados de rich**: Suporte interno para um conjunto avançado de arquivos de dados com base na cloud e no local. Para obter uma lista detalhada, consulte a tabela de [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
* **Segura e em conformidade**: Dados são transferidos através de HTTPS ou ExpressRoute. A presença de serviço global assegura que seus dados nunca sai do limite geográfico.
* **Elevado desempenho**: Até a velocidade de carregamento de dados de 1-GB/s na geração 1 de armazenamento do Data Lake. Para obter detalhes, consulte [copie o desempenho de atividade](copy-activity-performance.md).

Este artigo mostra-lhe como utilizar a ferramenta copiar dados do Data Factory para _carregar dados do Amazon S3 para geração 1 de armazenamento do Data Lake_. Pode seguir passos semelhantes para copiar dados de outros tipos de arquivos de dados.

> [!NOTE]
> Para obter mais informações, consulte [copiar dados de ou para a geração 1 de armazenamento do Data Lake com o Azure Data Factory](connector-azure-data-lake-store.md).

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Conta do Data Lake Gen1 de armazenamento: Se não tiver uma conta de geração 1 de armazenamento do Data Lake, veja as instruções em [criar uma conta do Data Lake Storage Gen1](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account).
* Amazon S3: Este artigo mostra como copiar dados do Amazon S3. Pode utilizar outros arquivos de dados ao seguir passos semelhantes.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu da esquerda, selecione **criar um recurso** > **análise** > **Data Factory**:
   
   ![Criar uma nova fábrica de dados](./media/load-data-into-azure-data-lake-store/new-azure-data-factory-menu.png)
2. Na **nova fábrica de dados** página, fornecer valores para os campos que são mostrados na imagem seguinte: 
      
   ![Página Nova fábrica de dados](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Nome**: Introduza um nome globalmente exclusivo para a fábrica de dados do Azure. Se receber o erro "nome do Data factory \"LoadADLSG1Demo\" não está disponível," insira um nome diferente para a fábrica de dados. Por exemplo, poderia usar o nome  _**yourname**_**ADFTutorialDataFactory**. Tente criar a fábrica de dados novamente. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Subscrição**: Selecione a sua subscrição do Azure na qual pretende criar a fábrica de dados. 
    * **Grupo de recursos**: Selecione um grupo de recursos existente na lista pendente ou selecione o **criar novo** opção e introduza o nome de um grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versão**: Selecione **V2**.
    * **Localização**: Selecione a localização da fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. Os arquivos de dados que são utilizados pelo data factory podem estar noutras localizações e regiões. Esses arquivos de dados incluem Gen1 de armazenamento do Azure Data Lake, o armazenamento do Azure, base de dados do Azure SQL e assim por diante.

3. Selecione **Criar**.
4. Depois de concluída a criação, vá para a fábrica de dados. Verá o **fábrica de dados** home page do conforme mostrado na imagem seguinte: 
   
   ![Home page da fábrica de dados](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Selecione o **criar e monitorizar** mosaico para iniciar o aplicativo de integração de dados num separador à parte.

## <a name="load-data-into-data-lake-storage-gen1"></a>Carregar dados para a geração 1 de armazenamento do Data Lake

1. Na **começar** página, selecione a **copiar dados** mosaico para iniciar a ferramenta copiar dados: 

   ![Mosaico ferramenta Copiar Dados](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. Na **propriedades** , especifique **CopyFromAmazonS3ToADLS** para o **nome da tarefa** campo e selecione **seguinte**:

    ![Página Propriedades](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. Na **o arquivo de dados de origem** página, clique em **+ criar nova ligação**:

    ![Página de arquivo de dados de origem](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    Selecione **Amazon S3**e selecione **continuar**
    
    ![Página de s3 do arquivo de dados de origem](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. Na **ligação de especificar o Amazon S3** página, efetue os seguintes passos: 
   1. Especifique a **ID de chave de acesso** valor.
   2. Especifique a **chave de acesso secreta** valor.
   3. Selecione **Concluir**.
   
      ![Especifique a conta do Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. Verá uma nova ligação. Selecione **Seguinte**.
   
   ![Especifique a conta do Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. Na **escolher o ficheiro de entrada ou a pasta** página, navegue para a pasta e ficheiro que pretende que devem transitar. Selecione o ficheiro/pasta, selecione **Choose**e, em seguida, selecione **próxima**:

    ![Escolher ficheiro ou pasta de entrada](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Escolher o comportamento de cópia, selecionando o **recursivamente de ficheiros de cópia** e **cópia binária** (copiar ficheiros como-é) opções. Selecione **seguinte**:

    ![Especifique a pasta de saída](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. Na **o arquivo de dados de destino** página, clique em **+ criar nova ligação**e, em seguida, selecione **Gen1 de armazenamento do Azure Data Lake**e selecione **continuar**:

    ![Página arquivo de dados de destino](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. Na **novo serviço ligado (Azure Data Lake Storage Gen1)** página, efetue os seguintes passos: 

   1. Selecione a sua conta de geração 1 de armazenamento do Data Lake para o **nome da conta do Data Lake Store**.
   2. Especifique a **inquilino**e selecione Finish.
   3. Selecione **Seguinte**.
   
   > [!IMPORTANT]
   > Este passo a passo, vai utilizar uma identidade gerida para recursos do Azure para autenticar a sua conta de geração 1 de armazenamento do Data Lake. Certifique-se de que conceder o MSI as permissões adequadas na geração 1 de armazenamento do Data Lake, seguindo [estas instruções](connector-azure-data-lake-store.md#managed-identity).
   
   ![Especifique a conta de geração 1 de armazenamento do Data Lake](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. Na **escolher o ficheiro de saída ou a pasta** página, introduza **copyfroms3** como o nome da pasta de saída e selecione **seguinte**: 

    ![Especifique a pasta de saída](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. Na **configurações** página, selecione **próxima**:

    ![Página de definições](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. Na **resumo** página, reveja as definições e selecione **próxima**:

    ![Página de resumo](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. Na **página de implementação**, selecione **Monitor** para monitorizar o pipeline (tarefa):

    ![Página de implementação](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. O **ações** coluna inclui ligações para ver os detalhes da execução da atividade e voltar a executar o pipeline:

    ![Monitorizar execuções de pipeline](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Para ver as execuções de atividades que estão associadas à execução do pipeline, selecione o **ver execuções de atividades** ligação na **ações** coluna. Há apenas uma atividade (atividade copiar) no pipeline, pelo que só vai ver uma entrada. Para regressar à vista de execuções de pipeline, selecione o **Pipelines** link na parte superior. Selecione **Atualizar** para atualizar a lista. 

    ![Monitorização de execuções de atividade](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Para monitorizar os detalhes da execução para cada atividade de cópia, selecione o **detalhes** ligação sob **ações** na atividade de vista de monitorização. Pode monitorizar detalhes como o volume de dados copiados da origem para o sink, taxa de transferência de dados, os passos de execução com duração correspondente e utilizado configurações:

    ![Monitorizar a atividade de detalhes da execução](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Certifique-se de que os dados são copiados para a sua conta de geração 1 de armazenamento do Data Lake: 

    ![Verificar a saída de geração 1 de armazenamento do Data Lake](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Passos Seguintes

Avance para o seguinte artigo para saber mais sobre o suporte de geração 1 de armazenamento do Data Lake: 

> [!div class="nextstepaction"]
>[Conector de geração 1 do Data Lake Storage do Azure](connector-azure-data-lake-store.md)
