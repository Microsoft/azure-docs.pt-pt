---
title: Copiar dados de geração 1 de armazenamento do Azure Data Lake para geração 2 com o Azure Data Factory
description: Utilize o Azure Data Factory para copiar dados de geração 1 de armazenamento do Azure Data Lake para geração 2
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
ms.openlocfilehash: e3a27ab15c72289dd28e31d832b81407a66dc754
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60546271"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Copiar dados de geração 1 de armazenamento do Azure Data Lake para geração 2 com o Azure Data Factory

Geração de armazenamento 2 do Azure Data Lake é um conjunto de recursos dedicado a análise de macrodados, incorporado ao [armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md). Permite-lhe interagir com os dados através de ambos os paradigmas de armazenamento de sistema e o objeto de ficheiro.

Se estiver a utilizar o Azure Data Lake Storage Gen1, pode avaliar a nova capacidade de geração 2 ao copiar dados de geração 1 de armazenamento do Data Lake para geração 2 com o Azure Data Factory.

O Azure Data Factory é um serviço de integração de dados totalmente gerido com base na cloud. Pode utilizar o serviço para preencher o lake com dados a partir de um conjunto avançado de no local e os arquivos de dados com base na cloud e poupam tempo quando criar as suas soluções de análise. Para obter uma lista detalhada de conectores suportados, consulte a tabela de [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).

O Azure Data Factory oferece uma solução de movimento de dados de escalamento horizontal e gerida. Devido à arquitetura de escalamento horizontal do ADF, ele pode ingerir dados em alto débito. Para obter detalhes, consulte [copie o desempenho de atividade](copy-activity-performance.md).

Este artigo mostra-lhe como utilizar a ferramenta copiar dados do Data Factory para copiar dados a partir _Gen1 de armazenamento do Azure Data Lake_ em _Gen2 de armazenamento do Azure Data Lake_. Pode seguir passos semelhantes para copiar dados de outros tipos de arquivos de dados.

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Conta de geração 1 do Data Lake Storage do Azure com dados no mesmo.
* Conta de armazenamento do Azure com o Data Lake Storage Gen2 ativada: Se não tiver uma conta de armazenamento [criar uma conta](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu da esquerda, selecione **criar um recurso** > **dados + análise** > **Data Factory**:
   
   ![Seleção do Data Factory no painel "Novo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na **nova fábrica de dados** página, fornecer valores para os campos que são mostrados na imagem seguinte: 
      
   ![Página Nova fábrica de dados](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Nome**: Introduza um nome globalmente exclusivo para a fábrica de dados do Azure. Se receber o erro "nome do Data factory \"LoadADLSDemo\" não está disponível," insira um nome diferente para a fábrica de dados. Por exemplo, poderia usar o nome  _**yourname**_**ADFTutorialDataFactory**. Tente criar a fábrica de dados novamente. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Subscrição**: Selecione a sua subscrição do Azure na qual pretende criar a fábrica de dados. 
    * **Grupo de recursos**: Selecione um grupo de recursos existente na lista pendente ou selecione o **criar novo** opção e introduza o nome de um grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versão**: Selecione **V2**.
    * **Localização**: Selecione a localização da fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. Os arquivos de dados que são utilizados pelo data factory podem estar noutras localizações e regiões. 

3. Selecione **Criar**.
4. Depois de concluída a criação, vá para a fábrica de dados. Verá o **fábrica de dados** home page do conforme mostrado na imagem seguinte: 
   
   ![Home page da fábrica de dados](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

   Selecione o **criar e monitorizar** mosaico para iniciar o aplicativo de integração de dados num separador à parte.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Carregar dados para a geração 2 de armazenamento do Azure Data Lake

1. Na **começar** página, selecione a **copiar dados** mosaico para iniciar a ferramenta copiar dados: 

   ![Mosaico ferramenta Copiar Dados](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. Na **propriedades** , especifique **CopyFromADLSGen1ToGen2** para o **nome da tarefa** campo e selecione **seguinte**:

    ![Página Propriedades](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. Na **o arquivo de dados de origem** página, clique em **+ criar nova ligação**:

    ![Página de arquivo de dados de origem](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
    Selecione **Gen1 de armazenamento do Azure Data Lake** da galeria do conector e selecione **continuar**
    
    ![Página de geração 1 de armazenamento do Azure Data Lake do arquivo de dados de origem](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
4. Na **ligação de especificar o Azure Data Lake Storage Gen1** página, efetue os seguintes passos:
   1. Selecione seu Gen1 de armazenamento do Data Lake para o nome de conta e especificar ou validar a **inquilino**.
   2. Clique em **Testar ligação** para validar as definições, em seguida, selecione **concluir**.
   3. Verá, que é criada uma nova ligação. Selecione **Seguinte**.
   
   > [!IMPORTANT]
   > Este passo a passo, vai utilizar uma identidade gerida para recursos do Azure para autenticar seu Gen1 de armazenamento do Data Lake. Certifique-se de que conceder o MSI as permissões adequadas na geração 1 de armazenamento do Azure Data Lake, seguindo [estas instruções](connector-azure-data-lake-store.md#managed-identity).
   
   ![Especifique a conta de geração 1 de armazenamento do Azure Data Lake](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
5. Na **escolher o ficheiro de entrada ou a pasta** página, navegue para a pasta e ficheiro que pretende que devem transitar. Selecione o ficheiro/pasta, selecione **escolha**:

    ![Escolher ficheiro ou pasta de entrada](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

6. Especificar o comportamento de cópia, verificando a **recursivamente de ficheiros de cópia** e **cópia binária** opções. Selecione **seguinte**:

    ![Especifique a pasta de saída](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
7. Na **o arquivo de dados de destino** página, clique em **+ criar nova ligação**e, em seguida, selecione **Gen2 de armazenamento do Azure Data Lake**e selecione **continuar**:

    ![Página arquivo de dados de destino](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

8. Na **ligação de especificar o Azure Data Lake Storage Gen2** página, efetue os seguintes passos:

   1. Selecione seu Gen2 de armazenamento do Data Lake conta com capacidade do "nome de conta de armazenamento" na lista pendente.
   2. Selecione **concluir** para criar a ligação. Em seguida, selecione **Seguinte**.
   
   ![Especifique a conta de geração 2 de armazenamento do Azure Data Lake](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

9. Na **escolher o ficheiro de saída ou a pasta** página, introduza **copyfromadlsgen1** como o nome da pasta de saída e selecione **seguinte**. ADF criará o sistema de ficheiros do ADLS Gen2 correspondente e subpastas durante a cópia se não existir.

    ![Especifique a pasta de saída](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

10. Na **definições** página, selecione **próxima** para utilizar as predefinições.

11. Na **resumo** página, reveja as definições e selecione **próxima**:

    ![Página de resumo](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
12. Na **página de implementação**, selecione **Monitor** para monitorizar o pipeline:

    ![Página de implementação](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
13. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. O **ações** coluna inclui ligações para ver os detalhes da execução da atividade e voltar a executar o pipeline:

    ![Monitorizar execuções de pipeline](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

14. Para ver as execuções de atividades que estão associadas à execução do pipeline, selecione o **ver execuções de atividades** ligação na **ações** coluna. Há apenas uma atividade (atividade copiar) no pipeline, pelo que só vai ver uma entrada. Para regressar à vista de execuções de pipeline, selecione o **Pipelines** link na parte superior. Selecione **Atualizar** para atualizar a lista. 

    ![Monitorização de execuções de atividade](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

15. Para monitorizar os detalhes da execução para cada atividade de cópia, selecione o **detalhes** ligação (imagem de óculos), em **ações** na atividade de vista de monitorização. Pode monitorizar detalhes como o volume de dados copiados da origem para o sink, taxa de transferência de dados, os passos de execução com duração correspondente e utilizado configurações:

    ![Monitorizar a atividade de detalhes da execução](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

16. Certifique-se de que os dados são copiados para a sua conta de geração 2 de armazenamento do Data Lake.

## <a name="best-practices"></a>Melhores práticas

Quando copiar grande volume de dados de arquivo de dados baseados em ficheiros, são sugeridas para:

- Os ficheiros de partição para 10TB para fileset 30TB cada.
- Não acionam demasiados execuções de cópia simultâneos para evitar a limitação de arquivos de dados de origem ou sink. Pode começar com uma cópia, executar e monitorizar a taxa de transferência e depois gradualmente a adicionar mais conforme necessário.

## <a name="next-steps"></a>Passos Seguintes

* [Descrição geral da atividade de cópia](copy-activity-overview.md)
* [Conector de geração 2 de armazenamento do Data Lake do Azure](connector-azure-data-lake-storage.md)
