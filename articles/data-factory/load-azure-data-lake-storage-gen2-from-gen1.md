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
ms.date: 05/13/2019
ms.author: jingwang
ms.openlocfilehash: d13dad6e87bd6c821b497138ad75d7ae2b9a052d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068981"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Copiar dados de geração 1 de armazenamento do Azure Data Lake para geração 2 com o Azure Data Factory

Geração de armazenamento 2 do Azure Data Lake é um conjunto de recursos dedicado a análise de macrodados que está incorporado no [armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md). Pode usá-lo para fazer a interface com os seus dados através de ambos os paradigmas de armazenamento de sistema e o objeto de ficheiro.

Se utilizar atualmente o Azure Data Lake Storage Gen1, pode avaliar Gen2 de armazenamento do Azure Data Lake copiando os dados de geração 1 de armazenamento do Data Lake para geração 2 através do Azure Data Factory.

O Azure Data Factory é um serviço de integração de dados totalmente gerido com base na cloud. Pode utilizar o serviço para preencher o lake com dados a partir de um conjunto avançado de no local e arquivos de dados com base na cloud e poupam tempo quando está a desenvolver as soluções de análise. Para obter uma lista de conectores suportados, consulte a tabela de [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).

O Azure Data Factory oferece uma solução de movimento de dados de escalamento horizontal e gerida. Por causa da arquitetura de escalamento horizontal do Data Factory, ele pode ingerir dados em alto débito. Para obter mais informações, consulte [copie o desempenho de atividade](copy-activity-performance.md).

Este artigo mostra-lhe como utilizar a ferramenta de dados de cópia do Data Factory para copiar dados de geração 1 de armazenamento do Azure Data Lake para geração 2 de armazenamento do Azure Data Lake. Pode seguir passos semelhantes para copiar dados de outros tipos de arquivos de dados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Conta de geração 1 do Data Lake Storage do Azure com dados no mesmo.
* Conta de armazenamento do Azure com o Data Lake Storage Gen2 ativada. Se não tiver uma conta de armazenamento [criar uma conta](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu da esquerda, selecione **criar um recurso** > **dados + análise** > **Data Factory**.
   
   ![Seleção do Data Factory no painel de novo](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Sobre o **nova fábrica de dados** página, fornecer valores para os campos que são mostrados na imagem seguinte: 
      
   ![Nova página da fábrica de dados](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Nome**: Introduza um nome globalmente exclusivo para a fábrica de dados do Azure. Se receber o erro "nome do Data factory \"LoadADLSDemo\" não está disponível," insira um nome diferente para a fábrica de dados. Por exemplo, utilize o nome _**oseunome**_ **ADFTutorialDataFactory**. Volte a criar a fábrica de dados. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Subscrição**: Selecione a sua subscrição do Azure na qual pretende criar a fábrica de dados. 
    * **Grupo de recursos**: Selecione um grupo de recursos existente na lista pendente. Também pode selecionar o **criar novo** opção e introduza o nome de um grupo de recursos. Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md). 
    * **Versão**: Selecione **V2**.
    * **Localização**: Selecione a localização da fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. Os arquivos de dados que são utilizados pela fábrica de dados podem estar noutras localizações e regiões. 

3. Selecione **Criar**.
4. Depois de concluída a criação, vá para a fábrica de dados. Verá o **fábrica de dados** home page do conforme mostrado na imagem seguinte: 
   
   ![Home page da fábrica de dados](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. Selecione o **criar e monitorizar** mosaico para iniciar o aplicativo de integração de dados num separador à parte.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Carregar dados para a geração 2 de armazenamento do Azure Data Lake

1. Sobre o **começar a utilizar** página, selecione a **copiar dados** mosaico para iniciar a ferramenta de dados de cópia. 

   ![Mosaico ferramenta copiar dados](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. Sobre o **propriedades** , especifique **CopyFromADLSGen1ToGen2** para o **nome da tarefa** campo. Selecione **Seguinte**.

    ![Página Propriedades](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. Sobre o **o arquivo de dados de origem** página, selecione **+ criar nova ligação**.

    ![Página de arquivo de dados de origem](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. Selecione **Gen1 de armazenamento do Azure Data Lake** da galeria do conector e selecione **continuar**.
    
    ![Página de geração 1 de armazenamento do Azure Data Lake do arquivo de dados de origem](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. Sobre o **ligação de especificar o Azure Data Lake Storage Gen1** página, siga estes passos:

   a. Selecione seu Gen1 de armazenamento do Data Lake para o nome de conta e especificar ou validar a **inquilino**.
  
   b. Selecione **Testar ligação** para validar as definições. Em seguida, selecione **Concluir**.
  
   c. Verá que uma nova ligação foi criada. Selecione **Seguinte**.
   
   > [!IMPORTANT]
   > Este passo a passo, vai utilizar uma identidade gerida para recursos do Azure para autenticar seu Gen1 de armazenamento do Azure Data Lake. Para conceder as permissões adequadas na geração 1 de armazenamento do Azure Data Lake de a identidade gerida, siga [estas instruções](connector-azure-data-lake-store.md#managed-identity).
   
   ![Especifique a conta de geração 1 de armazenamento do Azure Data Lake](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. Sobre o **escolher o ficheiro de entrada ou a pasta** página, navegue para a pasta e ficheiro que pretende que devem transitar. Selecione o ficheiro ou pasta e selecione **escolha**.

    ![Escolher ficheiro ou pasta de entrada](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. Especificar o comportamento de cópia, selecionando o **recursivamente de ficheiros de cópia** e **cópia binária** opções. Selecione **Seguinte**.

    ![Especifique a pasta de saída](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. Sobre o **o arquivo de dados de destino** página, selecione **+ criar nova ligação** > **Gen2 de armazenamento do Azure Data Lake**  >   **Continuar**.

    ![Página arquivo de dados de destino](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. Sobre o **ligação de especificar o Azure Data Lake Storage Gen2** página, siga estes passos:

   a. Selecione a sua conta de geração 2 de armazenamento do Data Lake com capacidade dos **nome da conta de armazenamento** na lista pendente.
   
   b. Selecione **concluir** para criar a ligação. Em seguida, selecione **Seguinte**.
   
   ![Especifique a conta de geração 2 de armazenamento do Azure Data Lake](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. Sobre o **escolher o ficheiro de saída ou a pasta** página, introduza **copyfromadlsgen1** como o nome da pasta de saída e selecione **seguinte**. Data Factory cria o sistema de ficheiros de geração 2 de armazenamento do Azure Data Lake correspondente e subpastas durante a cópia, caso não existam.

    ![Especifique a pasta de saída](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. Sobre o **definições** página, selecione **seguinte** para utilizar as predefinições.

12. Sobre o **resumo** página, reveja as definições e selecione **próxima**.

    ![Página de resumo](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. Sobre o **página de implementação**, selecione **Monitor** para monitorizar o pipeline.

    ![Página de implementação](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. A coluna **Ações** inclui ligações para ver os detalhes de execução da atividade e voltar a executar o pipeline.

    ![Monitorizar execuções de pipeline](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. Para ver as execuções de atividades que estão associadas à execução do pipeline, selecione o **ver execuções de atividades** ligação na **ações** coluna. Há apenas uma atividade (atividade copiar) no pipeline, pelo que só vai ver uma entrada. Para regressar à vista de execuções de pipeline, selecione o **Pipelines** link na parte superior. Selecione **Atualizar** para atualizar a lista. 

    ![Monitorização de execuções de atividade](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. Para monitorizar os detalhes da execução para cada atividade de cópia, selecione o **detalhes** ligação (imagem de óculos), em **ações** na atividade de vista de monitorização. Pode monitorizar detalhes como o volume de dados copiados da origem para o sink, taxa de transferência de dados, os passos de execução com duração correspondente e utilizado de configurações.

    ![Monitorizar a atividade de detalhes da execução](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Certifique-se de que os dados são copiados para a sua conta de geração 2 de armazenamento do Azure Data Lake.

## <a name="best-practices"></a>Melhores práticas

Para avaliar a atualização do Gen1 de armazenamento do Azure Data Lake para o ger2 de armazenamento do Azure Data Lake em geral, consulte [atualizar as soluções de análise de macrodados de geração 1 de armazenamento do Azure Data Lake para geração 2 de armazenamento do Azure Data Lake](../storage/blobs/data-lake-storage-upgrade.md). As secções seguintes apresentam as melhores práticas para utilizar o Data Factory para uma atualização de dados de geração 1 de armazenamento do Data Lake para geração 2 de armazenamento do Data Lake.

### <a name="data-partition-for-historical-data-copy"></a>Partição de dados para cópia de dados históricos

- Se o tamanho total dos dados na geração 1 de armazenamento do Data Lake é inferior a 30 TB e o número de ficheiros é inferior a 1 milhão, é possível copiar todos os dados numa execução de atividade de cópia única.
- Se tiver uma quantidade maior de dados para copiar ou pretender obter flexibilidade para gerir a migração de dados em lotes e fazer com cada um deles completa num período de tempo específico, particione os dados. Também é a criação de partições reduz o risco de qualquer problema inesperado.

Utilize uma prova de conceito para verificar a solução ponto a ponto e testar o débito de cópia no seu ambiente. Passos de verificação de conceito principais: 

1. Crie um pipeline do Data Factory com uma atividade de cópia única para copiar várias TB de dados de geração 1 de armazenamento do Data Lake para geração 2 de armazenamento do Data Lake para obter uma linha de base de desempenho de cópia. Começar com [unidades de integração de dados (DIUs)](copy-activity-performance.md#data-integration-units) como 128. 
2. Com base no débito cópia que obtenha no passo 1, calcule o tempo estimado necessário para a migração de dados inteiro. 
3. (Opcional) Criar uma tabela de controlo e definir o filtro de ficheiros para particionar os arquivos a serem migrados. A forma dos ficheiros de partição é: 

    - Partição por nome de pasta ou nome de pasta com um filtro de caráter universal. Recomendamos que este método.
    - Partição por um arquivo da hora da última modificação.

### <a name="network-bandwidth-and-storage-io"></a>E/s armazenamento e largura de banda de rede 

Pode controlar a simultaneidade de tarefas de cópia do Data Factory que ler dados de geração 1 de armazenamento do Data Lake e escrever dados para a geração 2 de armazenamento do Data Lake. Dessa forma, pode gerir a utilização no que o armazenamento e/s para evitar que afetam o trabalho de negócio normal na geração 1 de armazenamento do Data Lake durante a migração.

### <a name="permissions"></a>Permissões 

No Data Factory, o [conector de geração 1 de armazenamento do Data Lake](connector-azure-data-lake-store.md) suporta do serviço identidade principal e gerida para autenticações de recursos do Azure. O [conector de geração 2 de armazenamento do Data Lake](connector-azure-data-lake-storage.md) suporta contas de chave, o principal de serviço e identidade gerida para autenticações de recursos do Azure. Para tornar possível navegar e copie todos os ficheiros ou alto, tem de conceder permissões suficientes para a conta fornecida para aceder, ler, ou escrever todos os ficheiros e definir ACLs se optar por listas de controle (ACLs) de acesso do Data Factory. Conceda uma função de Superutilizador ou proprietário durante o período de migração. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Preservar as ACLs do Data Lake Storage Gen1

Se pretender replicar as ACLs juntamente com os ficheiros de dados ao atualizar a partir de geração 1 de armazenamento do Data Lake para geração 2 de armazenamento do Data Lake, veja [preservar as ACLs de geração 1 de armazenamento do Data Lake](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1). 

### <a name="incremental-copy"></a>Cópia incremental 

Pode utilizar várias abordagens para carregar apenas os ficheiros novos ou atualizados a partir de geração 1 de armazenamento do Data Lake:

- Carregar ficheiros novos ou atualizados por nome de ficheiro ou pasta particionado no tempo. Um exemplo é/2019/05/13 / *.
- Carregar ficheiros novos ou atualizados por LastModifiedDate.
- Identifique os arquivos novos ou atualizados por qualquer ferramenta de terceiros ou solução. Em seguida, passe o nome de ficheiro ou pasta para o pipeline da fábrica de dados através do parâmetro ou uma tabela ou ficheiro. 

A frequência adequada para fazer o carregamento incremental depende o número total de ficheiros na geração 1 de armazenamento do Azure Data Lake e o volume de ficheiros novos ou atualizados para ser carregado de cada vez. 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Descrição geral da atividade de cópia](copy-activity-overview.md)
> [conector de geração 1 de armazenamento do Azure Data Lake](connector-azure-data-lake-store.md)
> [conector de geração 2 de armazenamento do Azure Data Lake](connector-azure-data-lake-storage.md)