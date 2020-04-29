---
title: Carregue dados em Azure Data Lake Storage Gen1
description: Utilize a Fábrica de Dados Azure para copiar dados em Azure Data Lake Storage Gen1
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
ms.openlocfilehash: 1b1b19814709451bdbbea97462c459149484e71f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415848"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Carregue dados no Azure Data Lake Storage Gen1 utilizando a Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) (anteriormente conhecido como Azure Data Lake Store) é um repositório de hiperescala em toda a empresa para cargas de trabalho analíticas de big data. Data Lake Storage Gen1 permite-lhe capturar dados de qualquer tamanho, tipo e velocidade de ingestão. Os dados são capturados num único local para análise operacional e exploratória.

A Azure Data Factory é um serviço de integração de dados totalmente gerido em nuvem. Pode utilizar o serviço para povoar o lago com dados do seu sistema existente e economizar tempo ao construir as suas soluções de análise.

A Azure Data Factory oferece os seguintes benefícios para o carregamento de dados no Data Lake Storage Gen1:

* **Fácil de configurar**: Um intuitivo assistente de 5 passos sem necessidade de scripts.
* **Suporte rico em lojas**de dados : Suporte incorporado para um conjunto rico de lojas de dados no local e baseadas na nuvem. Para obter uma lista detalhada, consulte a tabela de lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
* **Seguro e conforme:** Os dados são transferidos para HTTPS ou ExpressRoute. A presença global de serviço garante que os seus dados nunca saem da fronteira geográfica.
* **Alto desempenho**: Até 1-GB/s velocidade de carregamento de dados em Data Lake Storage Gen1. Para mais detalhes, consulte [o desempenho da atividade do Copy.](copy-activity-performance.md)

Este artigo mostra-lhe como usar a ferramenta Data Copy Data da Data Factory para _carregar dados da Amazon S3 para data lake storage Gen1_. Pode seguir passos semelhantes para copiar dados de outros tipos de lojas de dados.

> [!NOTE]
> Para mais informações, consulte os dados da Cópia de ou para data [Lake Storage Gen1 utilizando](connector-azure-data-lake-store.md)a Azure Data Factory .

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure: Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Conta Data Lake Storage Gen1: Se não tiver uma conta Gen1 de Armazenamento de Data Lake, consulte as instruções em [Create a Data Lake Storage Gen1](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account).
* Amazon S3: Este artigo mostra como copiar dados da Amazon S3. Pode utilizar outras lojas de dados seguindo passos semelhantes.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu esquerdo, selecione **Criar um recurso** > **Analytics** > **Data Factory:**
   
   ![Seleção do Data Factory no painel "Novo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na página da nova fábrica de **dados,** forneça valores para os campos que são mostrados na seguinte imagem: 
      
   ![Página Nova fábrica de dados](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Nome**: Introduza um nome globalmente único para a sua fábrica de dados Azure. Se receber o erro "O nome \"de fábrica de\" dados LoadADLSG1Demo não está disponível", introduza um nome diferente para a fábrica de dados. Por exemplo, pode utilizar o nome _**yourname**_**aDFTutorialDataFactory**. Tente criar a fábrica de dados de novo. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Subscrição**: Selecione a sua subscrição Azure para criar a fábrica de dados. 
    * **Grupo de Recursos**: Selecione um grupo de recursos existente da lista de drop-down, ou selecione a nova opção **Criar** e introduza o nome de um grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).  
    * **Versão**: Selecione **V2**.
    * **Localização**: Selecione a localização da fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. As lojas de dados que são utilizadas pela fábrica de dados podem estar noutros locais e regiões. Estas lojas de dados incluem Azure Data Lake Storage Gen1, Azure Storage, Azure SQL Database, e assim por diante.

3. Selecione **Criar**.
4. Depois de a criação estar completa, vá à sua fábrica de dados. Vê a página inicial da **Data Factory** como mostra a seguinte imagem: 
   
   ![Home page da fábrica de dados](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Selecione o mosaico **Criar e Monitorizar** para iniciar a Aplicação de Integração de Dados num separador à parte.

## <a name="load-data-into-data-lake-storage-gen1"></a>Carregue dados em Data Lake Storage Gen1

1. Na página **Iniciar-se,** selecione o azulejo **Copy Data** para lançar a ferramenta Copy Data: 

   ![Mosaico ferramenta Copiar Dados](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. Na página **Propriedades,** especifique **CopyFromAmazonS3ToADLS** para o campo de **nome de tarefas** e selecione **Seguinte:**

    ![Página Propriedades](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. Na página da loja de **dados Fonte,** clique **+ Criar nova ligação:**

    ![Página de arquivo de dados de origem](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    Selecione **Amazon S3**e selecione **Continuar**
    
    ![Página s3 loja de dados de origem](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. Na página de **ligação Especifique amazon S3,** faça os seguintes passos: 
   1. Especifique o valor de ID da chave de **acesso.**
   2. Especifique o valor da chave de **acesso secreto.**
   3. Selecione **Concluir**.
   
      ![Especificar a conta Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. Verá uma nova ligação. Selecione **Seguinte**.
   
   ![Especificar a conta Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. Na página **Escolher o ficheiro ou pasta de entrada**, navegue até à pasta ou ficheiro que pretende copiar. Selecione a pasta/ficheiro, selecione **Escolher**e, em seguida, selecione **Seguinte**:

    ![Escolher ficheiro ou pasta de entrada](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Escolha o comportamento da cópia selecionando os **ficheiros Copy de forma recursiva** e as opções **de cópia binária** (copiar ficheiros como estão). Selecione **Seguinte**:

    ![Especificar pasta de saída](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. Na página da loja de **dados Destination,** clique **+ Crie uma nova ligação,** e depois selecione **Azure Data Lake Storage Gen1**, e selecione **Continue:**

    ![Página arquivo de dados de destino](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. Na página **New Linked Service (Azure Data Lake Storage Gen1),** faça os seguintes passos: 

   1. Selecione a sua conta Data Lake Storage Gen1 para o nome da **conta Data Lake Store**.
   2. Especifique o **Inquilino**e selecione Terminar.
   3. Selecione **Seguinte**.
   
   > [!IMPORTANT]
   > Neste passeio, você usa uma identidade gerida para os recursos Azure para autenticar a sua conta Data Lake Storage Gen1. Certifique-se de conceder ao MSI as permissões adequadas no Data Lake Storage Gen1 seguindo [estas instruções](connector-azure-data-lake-store.md#managed-identity).
   
   ![Especificar conta Gen1 de armazenamento de lago de dados](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. Na página **escolha do ficheiro de saída ou da página de pastas,** introduza **copyfroms3** como nome da pasta de saída e selecione **Seguinte:** 

    ![Especificar pasta de saída](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. Na página **Definições,** selecione **Seguinte**:

    ![Página de definições](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. Na página **Resumo,** reveja as definições e selecione **Seguinte:**

    ![Página de resumo](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. Na **página de Implantação,** selecione **Monitor** para monitorizar o gasoduto (tarefa):

    ![Página de implementação](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. A coluna **Ações** inclui links para visualizar detalhes de execução de atividade e para reexecutar o gasoduto:

    ![Monitorizar execuções de pipeline](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Para visualizar as operações associadas à execução do gasoduto, selecione o link **'Executar's View Activity Runs** na coluna **Ações.** Há apenas uma atividade (atividade copiar) no pipeline, pelo que só vai ver uma entrada. Para voltar à vista de funcionação do gasoduto, selecione a ligação **Pipelines** na parte superior. Selecione **Atualizar** para atualizar a lista. 

    ![Monitorização de execuções de atividade](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Para monitorizar os detalhes de execução de cada atividade de cópia, selecione o link **Detalhes** no âmbito **de Ações** na vista de monitorização da atividade. Pode monitorizar detalhes como o volume de dados copiados da fonte para o lavatório, a entrada de dados, os passos de execução com a duração correspondente e as configurações usadas:

    ![Monitorizar detalhes de execução de atividade](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Verifique se os dados são copiados na sua conta Data Lake Storage Gen1: 

    ![Verifique a saída da Gen1 de armazenamento de data lake](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Passos seguintes

Avance para o seguinte artigo para saber sobre o suporte da Gen1 de Armazenamento de Data Lake: 

> [!div class="nextstepaction"]
>[Conector De Armazenamento de Lagos Azure Gen1](connector-azure-data-lake-store.md)
