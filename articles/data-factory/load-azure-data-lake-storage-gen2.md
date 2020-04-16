---
title: Carregar dados para o Azure Data Lake Storage Gen2
description: Utilize a Fábrica de Dados Azure para copiar dados em Azure Data Lake Storage Gen2
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
ms.openlocfilehash: 96674f059e9cbc21c5c8c64eff8c94c810c4aa32
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417781"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Carregue dados no Armazenamento de Lagos Azure Data Gen2 com fábrica de dados azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Lake Storage Gen2 é um conjunto de capacidades dedicadas à análise de big data, incorporadas no [armazenamento Azure Blob.](../storage/blobs/storage-blobs-introduction.md) Permite-lhe interagir com os seus dados utilizando tanto o sistema de ficheiros como os paradigmas de armazenamento de objetos.

A Azure Data Factory (ADF) é um serviço de integração de dados totalmente gerido em nuvem. Você pode usar o serviço para povoar o lago com dados de um conjunto rico de lojas de dados no local e baseadas em nuvem e economizar tempo ao construir as suas soluções de análise. Para obter uma lista detalhada de conectores suportados, consulte a tabela de lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).

A Azure Data Factory oferece uma solução de movimento de dados gerida em escala. Devido à arquitetura de escala da ADF, pode ingerir dados a uma alta entrada. Para mais detalhes, consulte [o desempenho da atividade do Copy.](copy-activity-performance.md)

Este artigo mostra-lhe como usar a ferramenta Data Copy Data da Fábrica de Dados para carregar dados do _serviço Amazon Web Services S3_ para o _Azure Data Lake Storage Gen2_. Pode seguir passos semelhantes para copiar dados de outros tipos de lojas de dados.

>[!TIP]
>Para copiar dados do Azure Data Lake Storage Gen1 para gen2, consulte [este passe específico](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure: Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Conta de Armazenamento Azure com Data Lake Storage Gen2 ativada: Se não tiver uma conta de Armazenamento, [crie uma conta](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* Conta AWS com um balde S3 que contém dados: Este artigo mostra como copiar dados do Amazon S3. Pode utilizar outras lojas de dados seguindo passos semelhantes.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu esquerdo, selecione **Criar um recurso** > **Data + Analytics** > **Data Factory**:
   
   ![Seleção do Data Factory no painel "Novo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na página da nova fábrica de **dados,** forneça valores para os campos que são mostrados na seguinte imagem: 
      
   ![Página Nova fábrica de dados](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **Nome**: Introduza um nome globalmente único para a sua fábrica de dados Azure. Se receber o erro "O nome \"de fábrica\" de dados LoadADLSDemo não está disponível", introduza um nome diferente para a fábrica de dados. Por exemplo, pode utilizar o nome _**yourname**_**aDFTutorialDataFactory**. Tente criar a fábrica de dados de novo. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Subscrição**: Selecione a sua subscrição Azure para criar a fábrica de dados. 
    * **Grupo de Recursos**: Selecione um grupo de recursos existente da lista de drop-down, ou selecione a nova opção **Criar** e introduza o nome de um grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).  
    * **Versão**: Selecione **V2**.
    * **Localização**: Selecione a localização da fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. As lojas de dados que são utilizadas pela fábrica de dados podem estar noutros locais e regiões. 

3. Selecione **Criar**.
4. Depois de a criação estar completa, vá à sua fábrica de dados. Vê a página inicial da **Data Factory** como mostra a seguinte imagem: 
   
   ![Home page da fábrica de dados](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   Selecione o mosaico **Criar e Monitorizar** para iniciar a Aplicação de Integração de Dados num separador à parte.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Carregar dados para o Azure Data Lake Storage Gen2

1. Na página **Iniciar-se,** selecione o azulejo **Copy Data** para lançar a ferramenta Copy Data: 

   ![Mosaico ferramenta Copiar Dados](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. Na página **Propriedades,** especifique **CopyFromAmazonS3ToADLS** para o campo de **nome de tarefas** e selecione **Seguinte:**

    ![Página Propriedades](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. Na página da loja de **dados Fonte,** clique **+ Criar nova ligação:**

    ![Página de arquivo de dados de origem](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    Selecione **Amazon S3** na galeria de conector e selecione **Continuar**
    
    ![Página s3 loja de dados de origem](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Na página de **ligação Especifique amazon S3,** faça os seguintes passos:

   1. Especifique o valor de ID da chave de **acesso.**
   2. Especifique o valor da chave de **acesso secreto.**
   3. Clique em **Testar ligação** para validar as definições e, em seguida, selecione **Concluir**.
   4. Verá uma nova ligação ser criada. Selecione **Seguinte**.
   
      ![Especificar a conta Amazon S3](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
      
5. Na página **Escolher o ficheiro ou pasta de entrada**, navegue até à pasta ou ficheiro que pretende copiar. Selecione a pasta/ficheiro, **selecione Escolher**:

    ![Escolher ficheiro ou pasta de entrada](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Especifique o comportamento de cópia ao selecionar as opções **Copiar ficheiros recursivamente** e **Cópia binária**. Selecione **Seguinte**:

    ![Especificar pasta de saída](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. Na página da loja de **dados Destination,** clique **+ Crie uma nova ligação,** e depois selecione **Azure Data Lake Storage Gen2**, e selecione **Continue:**

    ![Página arquivo de dados de destino](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Na página de ligação de armazenamento de dados **do Lago De dados Do Azure,** faça os seguintes passos:

   1. Selecione a sua conta de armazenamento de data Lake Gen2 capaz da lista de "Nome da conta de armazenamento".
   2. Selecione **Concluir** para criar a ligação. Em seguida, selecione **Seguinte**.
   
   ![Especificar conta de Armazenamento de Lagos Azure Gen2](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. Na página escolha do ficheiro de saída ou da **página da pasta,** introduza **o copys3** como nome da pasta de saída e selecione **Seguinte**. A ADF criará o sistema de ficheiros ADLS Gen2 correspondente e sub-pastas durante a cópia, caso não exista.

    ![Especificar pasta de saída](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. Na página **Definições,** selecione **Next** para utilizar as definições predefinidas:

    ![Página de definições](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. Na página **Resumo,** reveja as definições e selecione **Seguinte:**

    ![Página de resumo](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. Na **página de Implantação,** selecione **Monitor** para monitorizar o gasoduto:

    ![Página de implementação](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. A coluna **Ações** inclui links para visualizar detalhes de execução de atividade e para reexecutar o gasoduto:

    ![Monitorizar execuções de pipeline](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Para visualizar as operações associadas à execução do gasoduto, selecione o link **'Executar's View Activity Runs** na coluna **Ações.** Há apenas uma atividade (atividade copiar) no pipeline, pelo que só vai ver uma entrada. Para voltar à vista de funcionação do gasoduto, selecione a ligação **Pipelines** na parte superior. Selecione **Atualizar** para atualizar a lista. 

    ![Monitorização de execuções de atividade](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. Para monitorizar os detalhes de execução de cada atividade de cópia, selecione o link **Detalhes** (imagem de óculos) em **Ações** na vista de monitorização da atividade. Pode monitorizar detalhes como o volume de dados copiados da fonte para o lavatório, a entrada de dados, os passos de execução com a duração correspondente e as configurações usadas:

    ![Monitorizar detalhes de execução de atividade](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. Verifique se os dados são copiados na sua conta Data Lake Storage Gen2.

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral da atividade de cópia](copy-activity-overview.md)
* [Conector do Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
