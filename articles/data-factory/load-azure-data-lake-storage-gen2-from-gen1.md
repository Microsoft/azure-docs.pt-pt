---
title: Copiar dados de Azure Data Lake Storage Gen1 para Gen2
description: Use Azure Data Factory para copiar dados de Azure Data Lake Storage Gen1 para Gen2
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
ms.openlocfilehash: 5809307ff8e047ebc6120cb5ebf36590f2a2a51a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444013"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Copiar dados de Azure Data Lake Storage Gen1 para Gen2 com Azure Data Factory

Azure Data Lake Storage Gen2 é um conjunto de recursos dedicados à análise de Big Data que é incorporada ao [armazenamento de BLOBs do Azure](../storage/blobs/storage-blobs-introduction.md). Você pode usá-lo para fazer a interface com seus dados usando paradigmas de armazenamento de objeto e de sistema de arquivos.

Se, no momento, você usar Azure Data Lake Storage Gen1, poderá avaliar Azure Data Lake Storage Gen2 copiando dados de Data Lake Storage Gen1 para Gen2 usando Azure Data Factory.

Azure Data Factory é um serviço de integração de dados baseado em nuvem totalmente gerenciado. Você pode usar o serviço para popular o Lake com dados de um conjunto avançado de armazenamentos de dados locais e baseados em nuvem e economizar tempo ao criar suas soluções de análise. Para obter uma lista de conectores com suporte, consulte a tabela de [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

O Azure Data Factory oferece uma solução de movimentação de dados gerenciada e escalável. Devido à arquitetura de expansão do Data Factory, ele pode ingerir dados em uma alta taxa de transferência. Para obter mais informações, consulte [desempenho da atividade de cópia](copy-activity-performance.md).

Este artigo mostra como usar a ferramenta Data Factory copiar dados para copiar dados de Azure Data Lake Storage Gen1 para Azure Data Lake Storage Gen2. Você pode seguir etapas semelhantes para copiar dados de outros tipos de armazenamentos de dados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Azure Data Lake Storage Gen1 conta com dados.
* Conta de armazenamento do Azure com Data Lake Storage Gen2 habilitado. Se você não tiver uma conta de armazenamento, [crie uma conta](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu à esquerda, selecione **criar um recurso** > **dados + análise** > **Data Factory**.
   
   ![Data Factory seleção no novo painel](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na página **novo data Factory** , forneça valores para os campos mostrados na imagem a seguir: 
      
   ![Página novo data Factory](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Nome**: Insira um nome globalmente exclusivo para sua data Factory do Azure. Se você receber o erro "o nome do data Factory \"LoadADLSDemo\" não está disponível", insira um nome diferente para o data factory. Por exemplo, utilize o nome _**oseunome**_ **ADFTutorialDataFactory**. Crie o data factory novamente. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Assinatura**: selecione sua assinatura do Azure na qual criar o data Factory. 
    * **Grupo de recursos**: selecione um grupo de recursos existente na lista suspensa. Você também pode selecionar a opção **criar novo** e inserir o nome de um grupo de recursos. Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md). 
    * **Versão**: selecione **v2**.
    * **Local**: selecione o local para o data Factory. Apenas são apresentadas as localizações suportadas na lista pendente. Os arquivos de dados utilizados pela fábrica de dados podem estar noutras localizações e regiões. 

3. Selecione **Criar**.
4. Após a conclusão da criação, vá para o data factory. Você verá o home page de **Data Factory** conforme mostrado na imagem a seguir: 
   
   ![Home page da fábrica de dados](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. Selecione o bloco **criar & monitor** para iniciar o aplicativo de integração de dados em uma guia separada.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Carregar dados para o Azure Data Lake Storage Gen2

1. Na página **introdução** , selecione o bloco **copiar dados** para iniciar a ferramenta copiar dados. 

   ![Bloco da ferramenta copiar dados](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. Na página **Propriedades** , especifique **CopyFromADLSGen1ToGen2** para o campo **nome da tarefa** . Selecione **Seguinte**.

    ![Página Propriedades](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. Na página **armazenamento de dados de origem** , selecione **+ criar nova conexão**.

    ![Página de arquivo de dados de origem](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. Selecione **Azure Data Lake Storage Gen1** na galeria de conectores e selecione **Continuar**.
    
    ![Página de Azure Data Lake Storage Gen1 do repositório de dados de origem](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. Na página **especificar conexão Azure data Lake Storage Gen1** , siga estas etapas:

   a. Selecione o Data Lake Storage Gen1 para o nome da conta e especifique ou valide o **locatário**.
  
   b. Selecione **testar conexão** para validar as configurações. Em seguida, selecione **Concluir**.
  
   c. Você verá que uma nova conexão foi criada. Selecione **Seguinte**.
   
   > [!IMPORTANT]
   > Neste passo a passo, você usa uma identidade gerenciada para recursos do Azure para autenticar seu Azure Data Lake Storage Gen1. Para conceder à identidade gerenciada as permissões apropriadas em Azure Data Lake Storage Gen1, siga [estas instruções](connector-azure-data-lake-store.md#managed-identity).
   
   ![Especificar conta de Azure Data Lake Storage Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. Na página **escolher o arquivo de entrada ou a pasta** , navegue até a pasta e o arquivo que você deseja copiar. Selecione a pasta ou o arquivo e selecione **escolher**.

    ![Escolher ficheiro ou pasta de entrada](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. Especifique o comportamento de cópia selecionando as opções copiar **arquivos recursivamente** e **cópia binária** . Selecione **Seguinte**.

    ![Especificar pasta de saída](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. Na página **armazenamento de dados de destino** , selecione **+ criar nova conexão** > **Azure data Lake Storage Gen2** > **continuar**.

    ![Página arquivo de dados de destino](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. Na página **especificar conexão Azure data Lake Storage Gen2** , siga estas etapas:

   a. Selecione sua conta com capacidade de Data Lake Storage Gen2 na lista suspensa **nome da conta de armazenamento** .
   
   b. Selecione **Concluir** para criar a ligação. Em seguida, selecione **Seguinte**.
   
   ![Especificar conta de Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. Na página **escolher o arquivo de saída ou a pasta** , insira **copyfromadlsgen1** como o nome da pasta de saída e selecione **Avançar**. Data Factory cria o Azure Data Lake Storage Gen2 sistema de arquivos e as subpastas correspondentes durante a cópia, se eles não existirem.

    ![Especificar pasta de saída](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. Na página **Definições**, selecione **Seguinte** para utilizar as predefinições.

12. Na página **Resumo** , examine as configurações e selecione **Avançar**.

    ![Página de resumo](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. Na **página implantação**, selecione **Monitor** para monitorar o pipeline.

    ![Página de implementação](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. A coluna **Ações** inclui ligações para ver os detalhes de execução da atividade e voltar a executar o pipeline.

    ![Monitorizar execuções de pipeline](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. Para exibir as execuções de atividade que estão associadas à execução do pipeline, selecione o link **Exibir execuções de atividade** na coluna **ações** . Há apenas uma atividade (atividade copiar) no pipeline, pelo que só vai ver uma entrada. Para voltar para a exibição de execuções de pipeline, selecione o link **pipelines** na parte superior. Selecione **Atualizar** para atualizar a lista. 

    ![Monitorização de execuções de atividade](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. Para monitorar os detalhes de execução de cada atividade de cópia, selecione o link **detalhes** (imagem óculos) em **ações** no modo de exibição de monitoramento de atividade. Você pode monitorar detalhes como o volume de dados copiados da origem para o coletor, a taxa de transferência de dados, as etapas de execução com a duração correspondente e as configurações usadas.

    ![Detalhes da execução da atividade de monitor](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Verifique se os dados são copiados para sua conta de Azure Data Lake Storage Gen2.

## <a name="best-practices"></a>Melhores práticas

Para avaliar a atualização de Azure Data Lake Storage Gen1 para Azure Data Lake Storage Gen2 em geral, consulte [atualizar suas soluções de análise de Big data de Azure data Lake Storage Gen1 para Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md). As seções a seguir apresentam as práticas recomendadas para o uso de Data Factory para uma atualização de dados de Data Lake Storage Gen1 para Data Lake Storage Gen2.

### <a name="data-partition-for-historical-data-copy"></a>Partição de dados para cópia de dados históricos

- Se o tamanho total dos dados em Data Lake Storage Gen1 for menor que 30 TB e o número de arquivos for menor que 1 milhão, você poderá copiar todos os dados em uma única execução da atividade de cópia.
- Se você tiver uma quantidade maior de dados a serem copiados ou se quiser a flexibilidade para gerenciar a migração de dados em lotes e fazer com que cada um deles seja concluído em um intervalo de tempo específico, Particione os dados. O particionamento também reduz o risco de qualquer problema inesperado.

Use uma prova de conceito para verificar a solução de ponta a ponta e testar a taxa de transferência de cópia em seu ambiente. Principais etapas de verificação de conceito: 

1. Crie um pipeline de Data Factory com uma única atividade de cópia para copiar vários TBs de dados de Data Lake Storage Gen1 para Data Lake Storage Gen2 para obter uma linha de base de desempenho de cópia. Comece com [DIUs (unidades de integração de dados)](copy-activity-performance.md#data-integration-units) como 128. 
2. Com base na taxa de transferência de cópia que você obtém na etapa 1, calcule o tempo estimado necessário para a migração de dados inteira. 
3. Adicional Crie uma tabela de controle e defina o filtro de arquivo para particionar os arquivos a serem migrados. A maneira de particionar os arquivos é: 

    - Particionar por nome da pasta ou nome da pasta com um filtro curinga. Recomendamos esse método.
    - Partição pela hora da última modificação do arquivo.

### <a name="network-bandwidth-and-storage-io"></a>Largura de banda de rede e e/s de armazenamento 

Você pode controlar a simultaneidade de Data Factory trabalhos de cópia que lêem dados de Data Lake Storage Gen1 e gravar dados no Data Lake Storage Gen2. Dessa forma, você pode gerenciar o uso nessa e/s de armazenamento para evitar afetar o trabalho de negócios normal em Data Lake Storage Gen1 durante a migração.

### <a name="permissions"></a>Permissões 

No Data Factory, o [conector de data Lake Storage Gen1](connector-azure-data-lake-store.md) dá suporte à entidade de serviço e à identidade gerenciada para autenticações de recursos do Azure. O [conector de data Lake Storage Gen2](connector-azure-data-lake-storage.md) dá suporte à chave de conta, entidade de serviço e identidade gerenciada para autenticações de recursos do Azure. Para tornar Data Factory capaz de navegar e copiar todos os arquivos ou ACLs (listas de controle de acesso) de que você precisa, conceda permissões alta o suficiente para a conta que você fornece para acessar, ler ou gravar todos os arquivos e definir ACLs se optar por. Conceda a ele uma função de superusuário ou proprietário durante o período de migração. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Preservar ACLs de Data Lake Storage Gen1

Se você quiser replicar as ACLs junto com os arquivos de dados ao atualizar de Data Lake Storage Gen1 para Data Lake Storage Gen2, consulte [preservar ACLs de data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1). 

### <a name="incremental-copy"></a>Cópia incremental 

Você pode usar várias abordagens para carregar somente os arquivos novos ou atualizados do Data Lake Storage Gen1:

- Carregue arquivos novos ou atualizados por tempo, pasta particionada ou nome do arquivo. Um exemplo é/2019/05/13/*.
- Carregar arquivos novos ou atualizados por LastModifiedDate.
- Identifique arquivos novos ou atualizados por qualquer ferramenta ou solução de terceiros. Em seguida, passe o nome do arquivo ou da pasta para o pipeline Data Factory via parâmetro ou uma tabela ou arquivo. 

A frequência correta de fazer a carga incremental depende do número total de arquivos em Azure Data Lake Storage Gen1 e do volume de arquivos novos ou atualizados a serem carregados a cada vez. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Visão geral da atividade de cópia](copy-activity-overview.md)
> [conector de Azure data Lake Storage Gen1](connector-azure-data-lake-store.md)
> conector de [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md)