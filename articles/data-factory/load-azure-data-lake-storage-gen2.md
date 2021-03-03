---
title: Carregar dados para o Azure Data Lake Storage Gen2
description: Utilize a Azure Data Factory para copiar dados em Azure Data Lake Storage Gen2
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 4e2bbe6f0c3b9ff1ffd913365ce21e534208123b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699730"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Carregue os dados no Azure Data Lake Storage Gen2 com a Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2 é um conjunto de capacidades dedicadas à análise de big data, incorporadas no [armazenamento Azure Blob.](../storage/blobs/storage-blobs-introduction.md) Permite-lhe interagir com os seus dados utilizando tanto o sistema de ficheiros como os paradigmas de armazenamento de objetos.

AZure Data Factory (ADF) é um serviço de integração de dados totalmente gerido na nuvem. Você pode usar o serviço para povoar o lago com dados de um rico conjunto de lojas de dados baseadas em nuvem e economizar tempo na construção das suas soluções de análise. Para obter uma lista detalhada de conectores suportados, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

A Azure Data Factory oferece uma solução de movimento de dados gerida e dimensionada. Devido à arquitetura de escala da ADF, pode ingerir dados a uma produção elevada. Para mais detalhes, consulte [o desempenho da atividade da Copy](copy-activity-performance.md).

Este artigo mostra-lhe como utilizar a ferramenta Data Copy Data da Data Factory para carregar dados do _serviço Amazon Web Services S3_ em _Azure Data Lake Storage Gen2_. Pode seguir passos semelhantes para copiar dados de outros tipos de lojas de dados.

>[!TIP]
>Para copiar dados do Azure Data Lake Storage Gen1 para a Gen2, consulte [esta passagem específica.](load-azure-data-lake-storage-gen2-from-gen1.md)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Conta de Armazenamento Azure com Data Lake Storage Gen2 ativada: Se não tiver uma conta de Armazenamento, [crie uma conta](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* Conta AWS com um balde S3 que contém dados: Este artigo mostra como copiar dados do Amazon S3. Pode utilizar outras lojas de dados seguindo passos semelhantes.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu esquerdo, **selecione Criar uma** Fábrica de  >  Dados de **Integração de** Recursos  >  :
   
   ![Seleção do Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Na página new **data factory,** forneça valores para os seguintes campos:
 
    * **Nome**: Introduza um nome globalmente único para a sua fábrica de dados Azure. Se receber o erro "Data factory name *YourDataFactoryName* não está disponível", insira um nome diferente para a fábrica de dados. Por exemplo, pode utilizar o nome _**do seu nome**_**ADFTutorialDataFactory**. Tente criar a fábrica de dados de novo. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Subscrição**: Selecione a sua subscrição Azure na qual criar a fábrica de dados. 
    * **Grupo de Recursos**: Selecione um grupo de recursos existente da lista de drop-down, ou selecione a nova opção **Criar** e insira o nome de um grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).  
    * **Versão**: selecione **V2**.
    * **Localização**: Selecione a localização para a fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. As lojas de dados que são utilizadas pela fábrica de dados podem estar noutros locais e regiões. 

3. Selecione **Criar**.

4. Depois de a criação estar completa, vá à sua fábrica de dados. Veja a página inicial da **Data Factory** como mostrado na seguinte imagem: 
   
   ![Home page da fábrica de dados](./media/doc-common-process/data-factory-home-page.png)

   Selecione o mosaico **Criar e Monitorizar** para iniciar a Aplicação de Integração de Dados num separador à parte.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Carregar dados para o Azure Data Lake Storage Gen2

1. Na página **Introdução**, selecione o mosaico **Copiar Dados** para iniciar a ferramenta Copiar Dados.

2. Na página **Propriedades,** especifique **copyFromAmazonS3ToADLS** para o campo **de nome de tarefa** e selecione **Seguinte**.

    ![Página Propriedades](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. Na página **Arquivo de dados de origem**, clique em **+ Criar nova ligação**. Selecione **Amazon S3** da galeria de conector e selecione **Continue**.
    
    ![Página s3 da loja de dados de origem](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Na página **new linked service (Amazon S3),** faça os seguintes passos:

   1. Especifique o valor do **ID da chave de acesso.**
   2. Especifique o valor **da chave de acesso secreto.**
   3. Clique **na ligação de teste** para validar as definições e, em seguida, selecione **Criar**.

      ![Especificar a conta Amazon S3](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
   4. Verá uma nova ligação AmazonS3 ser criada. Selecione **Seguinte**. 

5. Na página **Escolher o ficheiro ou pasta de entrada**, navegue até à pasta ou ficheiro que pretende copiar. Selecione a pasta/ficheiro e, em seguida, **selecione Escolha**.

    ![Escolher ficheiro ou pasta de entrada](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Especifique o comportamento da cópia verificando as opções de cópia **Recursiva e** **Binary.** Selecione **Seguinte**.

    ![A screenshot mostra o ficheiro de entrada ou pasta onde pode selecionar a cópia Binary e recursivamente.](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. Na página de **loja de dados destino,** clique **em + Crie uma nova ligação** e, em seguida, selecione **Azure Data Lake Storage Gen2**, e selecione **Continue**.

    ![Página arquivo de dados de destino](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Na página **New linked service (Azure Data Lake Storage Gen2),** faça os seguintes passos:

   1. Selecione a sua conta compatível com o Data Lake Storage Gen2 na lista pendente "Nome da conta de armazenamento".
   2. Selecione **Criar** para criar a ligação. Em seguida, selecione **Seguinte**.   

        ![Especificar conta Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2/specify-azure-data-lake-storage.png)

9. Na página Escolha o ficheiro de saída ou a página **de pasta,** introduza **o copyfroms3** como o nome da pasta de saída e selecione **Seguinte**. A ADF criará o respetivo sistema de ficheiros ADLS Gen2 e subclasseduras durante a cópia se não existir.

    ![A imagem mostra o caminho da pasta em que entra.](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. Na página **Definições**, selecione **Seguinte** para utilizar as predefinições.

    ![Página de definições](./media/load-azure-data-lake-storage-gen2/copy-settings.png)

11. Na página **Resumo,** reveja as definições e selecione **Seguinte**.

    ![Página de resumo](./media/load-azure-data-lake-storage-gen2/copy-summary.png)

12. Na **Página de implementação**, selecione **Monitorizar** para monitorizar o pipeline (tarefa). 
 
13. Quando o gasoduto estiver concluído com sucesso, vê-se um gasoduto acionado por um gatilho manual. Pode utilizar links sob a coluna **PIPELINE NAME** para visualizar detalhes da atividade e para refazer o pipeline.

    ![Monitorizar execuções de pipeline](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Para ver as operações executadas associadas ao funcionação do gasoduto, selecione a ligação **CopyFromAmazonS3ToADLS** sob a coluna PIPELINE NAME. Para obter mais informações sobre a operação da cópia, selecione o link **Details** (ícone de óculos) sob a coluna ACTIVITY NAME. Pode monitorizar detalhes como o volume de dados copiados da fonte para a pia, saída de dados, etapas de execução com a duração correspondente e configuração usada.
 
    ![Monitorização de execuções de atividade](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)
    
    ![Monitorize detalhes da atividade](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

15. Para atualizar a vista, selecione Atualizar. Selecione **Todos os gasodutos correm** na parte superior para voltar à vista Pipeline Runs.

16. Verifique se os dados são copiados na sua conta Da Gen2 de Armazenamento de Data Lake.

## <a name="next-steps"></a>Passos seguintes

* [Visão geral da atividade da cópia](copy-activity-overview.md)
* [Conector do Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
