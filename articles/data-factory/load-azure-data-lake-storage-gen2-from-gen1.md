---
title: Copiar dados do Azure Data Lake Storage Gen1 para gen2
description: Utilize a Fábrica de Dados Azure para copiar dados do Azure Data Lake Storage Gen1 para gen2
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
ms.openlocfilehash: 81f8577592f1d53627bc09a2f9ace8c060ad4660
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668856"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Copiar dados do Azure Data Lake Storage Gen1 para gen2 com azure data factory

Azure Data Lake Storage Gen2 é um conjunto de capacidades dedicadas à análise de big data que está incorporada no [armazenamento azure Blob.](../storage/blobs/storage-blobs-introduction.md) Pode usá-los para interagir com os seus dados utilizando tanto o sistema de ficheiros como os paradigmas de armazenamento de objetos.

Se utilizar atualmente o Azure Data Lake Storage Gen1, pode avaliar o Azure Data Lake Storage Gen2 copiando dados do Data Lake Storage Gen1 para gen2 utilizando a Azure Data Factory.

A Azure Data Factory é um serviço de integração de dados totalmente gerido em nuvem. Você pode usar o serviço para povoar o lago com dados de um conjunto rico de lojas de dados no local e baseadas em nuvem e economizar tempo quando você constrói suas soluções de análise. Para obter uma lista de conectores suportados, consulte a tabela de lojas de [dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).

A Azure Data Factory oferece uma solução de movimento de dados gerida em escala. Devido à arquitetura de escala da Data Factory, pode ingerir dados a uma produção elevada. Para mais informações, consulte o [desempenho da atividade do Copy.](copy-activity-performance.md)

Este artigo mostra-lhe como usar a ferramenta de dados de cópia da Data Factory para copiar dados do Azure Data Lake Storage Gen1 para o Azure Data Lake Storage Gen2. Pode seguir passos semelhantes para copiar dados de outros tipos de lojas de dados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Conta Azure Data Lake Storage Gen1 com dados.
* Conta de armazenamento azure com Data Lake Storage Gen2 ativado. Se não tiver uma conta de Armazenamento, [crie uma conta.](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu esquerdo, selecione **Criar um recurso** > **Data + Analytics** > **Data Factory**.
   
   ![Seleção da Fábrica de Dados no Novo Painel](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na página da nova fábrica de **dados,** forneça valores para os campos que são mostrados na seguinte imagem: 
      
   ![Página de fábrica de novos dados](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Nome**: Introduza um nome globalmente único para a sua fábrica de dados Azure. Se receber o erro "O nome \"de fábrica\" de dados LoadADLSDemo não está disponível", introduza um nome diferente para a fábrica de dados. Por exemplo, utilize o nome _**oseunome**_**ADFTutorialDataFactory**. Crie a fábrica de dados novamente. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Subscrição**: Selecione a sua subscrição Azure para criar a fábrica de dados. 
    * **Grupo de Recursos**: Selecione um grupo de recursos existente da lista de abandono. Também pode selecionar a nova opção **Criar** e introduzir o nome de um grupo de recursos. Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md). 
    * **Versão**: Selecione **V2**.
    * **Localização**: Selecione a localização da fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. Os arquivos de dados utilizados pela fábrica de dados podem estar noutras localizações e regiões. 

3. Selecione **Criar**.
4. Depois de terminar a criação, vá à sua fábrica de dados. Vê a página inicial da **Data Factory** como mostra a seguinte imagem: 
   
   ![Home page da fábrica de dados](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. Selecione o **azulejo do Autor & Monitor** para lançar a aplicação de Integração de Dados num separador.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Carregar dados para o Azure Data Lake Storage Gen2

1. Na página **Iniciar-se,** selecione o azulejo **Copy Data** para lançar a ferramenta de dados de cópia. 

   ![Copiar azulejo da ferramenta de dados](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. Na página **Propriedades,** especifique **CopyFromADLSGen1ToGen2** para o campo de **nome de tarefas.** Selecione **Next**.

    ![Página Propriedades](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. Na página da loja de **dados Source,** selecione **+ Criar uma nova ligação**.

    ![Página de arquivo de dados de origem](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. Selecione **Azure Data Lake Storage Gen1** na galeria de conectores e selecione **Continuar**.
    
    ![Página de Armazenamento de Dados de Origem Azure Data Lake Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. Na página de **ligação De tese De armazenamento de dados do lago Detetos Gen1,** siga estes passos:

   a. Selecione o seu Data Lake Storage Gen1 para o nome da conta e especifique ou valide o **Arrendatário**.
  
   b. Selecione a **ligação de teste** para validar as definições. Em seguida, selecione **Concluir**.
  
   c. Sabe que foi criada uma nova ligação. Selecione **Next**.
   
   > [!IMPORTANT]
   > Neste walk-through, você usa uma identidade gerida para os recursos Azure para autenticar o seu Azure Data Lake Storage Gen1. Para conceder à identidade gerida as permissões adequadas no Azure Data Lake Storage Gen1, siga [estas instruções](connector-azure-data-lake-store.md#managed-identity).
   
   ![Especificar conta De armazenamento de dados azure Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. Na página **Escolha o ficheiro de entrada ou a página da pasta,** navegue na pasta e arquivo que pretende copiar. Selecione a pasta ou o ficheiro e selecione **Escolher**.

    ![Escolher ficheiro ou pasta de entrada](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. Especifique o comportamento da cópia selecionando os **ficheiros Copy de forma recursiva** e as opções **de cópia binária.** Selecione **Next**.

    ![Especificar pasta de saída](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. Na página da loja de **dados Destination,** selecione **+ Crie uma nova ligação** > **Azure Data Lake Storage Gen2** > **Continue**.

    ![Página arquivo de dados de destino](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. Na página de **ligação Especificada Bluee Data Lake Storage Gen2,** siga estes passos:

   a. Selecione a sua conta de Armazenamento de Data Lake Gen2 capaz da lista de drop-down da **conta de armazenamento.**
   
   b. Selecione **Concluir** para criar a ligação. Em seguida, selecione **Seguinte**.
   
   ![Especificar conta de Armazenamento de Lagos Azure Gen2](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. Na página escolha o ficheiro de **saída ou a página de pasta,** introduza **copyfromadlsgen1** como nome da pasta de saída e selecione **Seguinte**. Data Factory cria o sistema de ficheiros e subpastas de armazenamento de lagos de dados do Lago Azur2 correspondentes durante a cópia, caso não existam.

    ![Especificar pasta de saída](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. Na página **Definições**, selecione **Seguinte** para utilizar as predefinições.

12. Na página **Resumo,** reveja as definições e selecione **Next**.

    ![Página de resumo](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. Na **página de Implantação,** selecione **Monitor** para monitorizar o gasoduto.

    ![Página de implementação](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. A coluna **Ações** inclui ligações para ver os detalhes de execução da atividade e voltar a executar o pipeline.

    ![Monitorizar execuções de pipeline](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. Para visualizar as operações associadas à execução do gasoduto, selecione o link **'Executar's View Activity Runs** na coluna **Ações.** Há apenas uma atividade (atividade copiar) no pipeline, pelo que só vai ver uma entrada. Para voltar à vista de funcionação do gasoduto, selecione a ligação **Pipelines** na parte superior. Selecione **Atualizar** para atualizar a lista. 

    ![Monitorização de execuções de atividade](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. Para monitorizar os detalhes de execução de cada atividade de cópia, selecione o link **Detalhes** (imagem de óculos) em **Ações** na vista de monitorização da atividade. Pode monitorizar detalhes como o volume de dados copiados da fonte para o lavatório, a entrada de dados, passos de execução com a duração correspondente e configurações usadas.

    ![Monitorizar detalhes de execução de atividade](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Verifique se os dados são copiados na sua conta Azure Data Lake Storage Gen2.

## <a name="best-practices"></a>Melhores práticas

Para avaliar a atualização do Azure Data Lake Storage Gen1 para o Azure Data Lake Storage Gen2 em geral, consulte a [atualização das suas soluções de análise de big data do Azure Data Lake Storage Gen1 para o Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md). As seguintes secções introduzem as melhores práticas para a utilização da Data Factory para uma atualização de dados do Data Lake Storage Gen1 para data Lake Storage Gen2.

### <a name="data-partition-for-historical-data-copy"></a>Partição de dados para cópia de dados históricos

- Se o tamanho total dos dados no Data Lake Storage Gen1 for inferior a 30 TB e o número de ficheiros for inferior a 1 milhão, pode copiar todos os dados numa única execução de atividade de cópia.
- Se tiver uma maior quantidade de dados para copiar, ou quiser a flexibilidade para gerir a migração de dados em lotes e tornar cada um deles completo dentro de um prazo específico, dividir os dados. A partilha também reduz o risco de qualquer problema inesperado.

Utilize uma prova de conceito para verificar a solução de ponta a ponta e testar a entrada de cópia no seu ambiente. Principais passos de prova de conceito: 

1. Crie um oleoduto data factory com uma única atividade de cópia para copiar vários TBs de dados de Data Lake Storage Gen1 para Data Lake Storage Gen2 para obter uma linha de base de desempenho de cópia. Comece com unidades de integração de [dados (DIUs)](copy-activity-performance-features.md#data-integration-units) como 128. 
2. Com base na cópia de entrada que obtém no passo 1, calcule o tempo estimado necessário para toda a migração de dados. 
3. (Opcional) Crie uma tabela de controlo e defina o filtro de ficheiros para a partilha dos ficheiros a migrar. A forma de dividir os ficheiros é: 

    - Partição por nome de pasta ou nome de pasta com um filtro wildcard. Recomendamos este método.
    - Partição pelo último tempo modificado de um ficheiro.

### <a name="network-bandwidth-and-storage-io"></a>Largura de banda da rede e armazenamento I/O 

Você pode controlar a conmoeda de data factory copy jobs que lê dados de Data Lake Storage Gen1 e escrever dados para Data Lake Storage Gen2. Desta forma, você pode gerir o uso nesse armazenamento I/O para evitar afetar o trabalho normal de negócios em Data Lake Storage Gen1 durante a migração.

### <a name="permissions"></a>Permissões 

Na Data Factory, o [conector Data Lake Storage Gen1](connector-azure-data-lake-store.md) suporta o principal de serviço e a identidade gerida para autenticações de recursos Azure. O [conector Data Lake Storage Gen2](connector-azure-data-lake-storage.md) suporta a chave da conta, o principal de serviço e a identidade gerida para autenticações de recursos Azure. Para tornar a Data Factory capaz de navegar e copiar todos os ficheiros ou listas de controlo de acesso (ACLs) de que necessita, conceda permissões suficientemente elevadas para a conta que fornece para aceder, ler ou escrever todos os ficheiros e definir ACLs se assim o desejar. Conceda-lhe um papel de super utilizador ou proprietário durante o período de migração. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Preservar ACLs de Data Lake Storage Gen1

Se pretender replicar os ACLs juntamente com ficheiros de dados quando atualizar do Data Lake Storage Gen1 para data Lake Storage Gen2, consulte [Preserve ACLs do Data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls). 

### <a name="incremental-copy"></a>Cópia incremental 

Pode utilizar várias abordagens para carregar apenas os ficheiros novos ou atualizados a partir de Data Lake Storage Gen1:

- Carregue ficheiros novos ou atualizados por pasta ou nome de ficheiro dividido seleção. Um exemplo é /2019/05/13/*.
- Carregue ficheiros novos ou atualizados pelo LastModifiedDate.
- Identifique ficheiros novos ou atualizados por qualquer ferramenta ou solução de terceiros. Em seguida, passe o ficheiro ou nome da pasta para o pipeline Data Factory via parâmetro ou uma tabela ou ficheiro. 

A frequência adequada para fazer a carga incremental depende do número total de ficheiros em Azure Data Lake Storage Gen1 e do volume de ficheiros novos ou atualizados a serem carregados cada vez. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Visão geral](copy-activity-overview.md)
> da atividade[Azure Data Lake Storage Gen1 conector](connector-azure-data-lake-store.md)
> [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)