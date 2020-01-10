---
title: Carregar dados em Azure Data Lake Storage Gen1
description: Use Azure Data Factory para copiar dados em Azure Data Lake Storage Gen1
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/17/2018
ms.openlocfilehash: 1325910877d1e030b3bf4114e16d0f81ecea8cf7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443984"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Carregar dados em Azure Data Lake Storage Gen1 usando Azure Data Factory

[Azure data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) (anteriormente conhecido como Azure data Lake Store) é um repositório de hiperescala em toda a empresa para Big data cargas de trabalho analíticas. Data Lake Storage Gen1 permite capturar dados de qualquer tamanho, tipo e velocidade de ingestão. Os dados são capturados em um único lugar para análise operacional e exploratório.

Azure Data Factory é um serviço de integração de dados baseado em nuvem totalmente gerenciado. Você pode usar o serviço para popular o Lake com os dados do seu sistema existente e economizar tempo ao criar suas soluções de análise.

O Azure Data Factory oferece os seguintes benefícios para carregar dados no Data Lake Storage Gen1:

* **Fácil de configurar**: um assistente intuitivo de 5 etapas sem nenhum script necessário.
* **Suporte a armazenamento de dados avançado**: suporte interno para um conjunto avançado de armazenamentos de dados locais e baseados em nuvem. Para obter uma lista detalhada, consulte a tabela de [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
* **Seguro e em conformidade**: os dados são transferidos por HTTPS ou ExpressRoute. A presença do serviço global garante que seus dados nunca saiam do limite geográfico.
* **Alto desempenho**: velocidade de carregamento de dados de até 1 GB/s em data Lake Storage Gen1. Para obter detalhes, consulte [desempenho da atividade de cópia](copy-activity-performance.md).

Este artigo mostra como usar a ferramenta de Copiar Dados de Data Factory para _carregar dados do Amazon S3_para o data Lake Storage Gen1. Você pode seguir etapas semelhantes para copiar dados de outros tipos de armazenamentos de dados.

> [!NOTE]
> Para obter mais informações, consulte [copiar dados de ou para data Lake Storage Gen1 usando Azure data Factory](connector-azure-data-lake-store.md).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure: se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Conta de Data Lake Storage Gen1: se você não tiver uma conta de Data Lake Storage Gen1, consulte as instruções em [criar uma conta de data Lake Storage Gen1](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account).
* Amazon S3: Este artigo mostra como copiar dados do Amazon S3. Você pode usar outros armazenamentos de dados seguindo etapas semelhantes.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu à esquerda, selecione **criar um recurso** > **Analytics** > **Data Factory**:
   
   ![Seleção do Data Factory no painel "Novo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na página **novo data Factory** , forneça valores para os campos mostrados na imagem a seguir: 
      
   ![Página Nova fábrica de dados](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Nome**: Insira um nome globalmente exclusivo para sua data Factory do Azure. Se você receber o erro "o nome do data Factory \"LoadADLSG1Demo\" não está disponível", insira um nome diferente para o data factory. Por exemplo, você pode usar o nome _**Your**_ name**ADFTutorialDataFactory**. Tente criar o data factory novamente. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Assinatura**: selecione sua assinatura do Azure na qual criar o data Factory. 
    * **Grupo de recursos**: selecione um grupo de recursos existente na lista suspensa ou selecione a opção **criar novo** e insira o nome de um grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).  
    * **Versão**: selecione **v2**.
    * **Local**: selecione o local para o data Factory. Apenas são apresentadas as localizações suportadas na lista pendente. Os armazenamentos de dados usados pelo data factory podem estar em outros locais e regiões. Esses armazenamentos de dados incluem Azure Data Lake Storage Gen1, armazenamento do Azure, banco de dados SQL do Azure e assim por diante.

3. Selecione **Criar**.
4. Após a conclusão da criação, vá para o data factory. Você verá o home page de **Data Factory** conforme mostrado na imagem a seguir: 
   
   ![Home page da fábrica de dados](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Selecione o mosaico **Criar e Monitorizar** para iniciar a Aplicação de Integração de Dados num separador à parte.

## <a name="load-data-into-data-lake-storage-gen1"></a>Carregar dados em Data Lake Storage Gen1

1. Na página de **introdução** , selecione o bloco **copiar dados** para iniciar a ferramenta de copiar dados: 

   ![Mosaico ferramenta Copiar Dados](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. Na página **Propriedades** , especifique **CopyFromAmazonS3ToADLS** para o campo **nome da tarefa** e selecione **Avançar**:

    ![Página Propriedades](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. Na página **armazenamento de dados de origem** , clique em **+ criar nova conexão**:

    ![Página de arquivo de dados de origem](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    Selecione **Amazon S3**e selecione **continuar**
    
    ![Página S3 do armazenamento de dados de origem](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. Na página **especificar conexão do Amazon S3** , execute as seguintes etapas: 
   1. Especifique o valor da **ID de chave de acesso** .
   2. Especifique o valor da **chave de acesso de segredo** .
   3. Selecione **Concluir**.
   
      ![Especificar conta do Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. Você verá uma nova conexão. Selecione **Seguinte**.
   
   ![Especificar conta do Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. Na página **Escolher o ficheiro ou pasta de entrada**, navegue até à pasta ou ficheiro que pretende copiar. Selecione a pasta/arquivo, selecione **escolher**e, em seguida, selecione **Avançar**:

    ![Escolher ficheiro ou pasta de entrada](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Escolha o comportamento de cópia selecionando as opções **copiar arquivos recursivamente** e **cópia binária** (copiar arquivos no estado em que se encontram). Selecione **Seguinte**:

    ![Especificar pasta de saída](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. Na página **armazenamento de dados de destino** , clique em **+ criar nova conexão**e selecione **Azure data Lake Storage Gen1**e selecione **continuar**:

    ![Página arquivo de dados de destino](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. Na página **novo serviço vinculado (Azure data Lake Storage Gen1)** , execute as seguintes etapas: 

   1. Selecione sua conta de Data Lake Storage Gen1 para o **nome da conta de data Lake Store**.
   2. Especifique o **locatário**e selecione concluir.
   3. Selecione **Seguinte**.
   
   > [!IMPORTANT]
   > Neste tutorial, você usa uma identidade gerenciada para recursos do Azure para autenticar sua conta de Data Lake Storage Gen1. Certifique-se de conceder ao MSI as permissões apropriadas em Data Lake Storage Gen1 seguindo [estas instruções](connector-azure-data-lake-store.md#managed-identity).
   
   ![Especificar conta de Data Lake Storage Gen1](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. Na página **escolher o arquivo de saída ou a pasta** , insira **copyfroms3** como o nome da pasta de saída e selecione **Avançar**: 

    ![Especificar pasta de saída](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. Na página **configurações** , selecione **Avançar**:

    ![Página de definições](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. Na página **Resumo** , examine as configurações e selecione **Avançar**:

    ![Página de resumo](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. Na **página implantação**, selecione **Monitor** para monitorar o pipeline (tarefa):

    ![Página de implementação](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. A coluna **ações** inclui links para exibir detalhes da execução da atividade e executar novamente o pipeline:

    ![Monitorizar execuções de pipeline](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Para exibir as execuções de atividade que estão associadas à execução do pipeline, selecione o link **Exibir execuções de atividade** na coluna **ações** . Há apenas uma atividade (atividade copiar) no pipeline, pelo que só vai ver uma entrada. Para voltar para a exibição de execuções de pipeline, selecione o link **pipelines** na parte superior. Selecione **Atualizar** para atualizar a lista. 

    ![Monitorização de execuções de atividade](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Para monitorar os detalhes de execução de cada atividade de cópia, selecione o link **detalhes** em **ações** na exibição monitoramento de atividade. Você pode monitorar detalhes como o volume de dados copiados da origem para o coletor, a taxa de transferência de dados, as etapas de execução com a duração correspondente e as configurações usadas:

    ![Detalhes da execução da atividade de monitor](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Verifique se os dados são copiados em sua conta de Data Lake Storage Gen1: 

    ![Verificar Data Lake Storage Gen1 saída](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Passos seguintes

Avance para o seguinte artigo para saber mais sobre o suporte a Data Lake Storage Gen1: 

> [!div class="nextstepaction"]
>[Conector de Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
