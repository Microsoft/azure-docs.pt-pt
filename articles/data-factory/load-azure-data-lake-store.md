---
title: Carregue os dados na Azure Data Lake Storage Gen1
description: Utilize a Azure Data Factory para copiar dados em Azure Data Lake Storage Gen1
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81415848"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Carregue os dados no Azure Data Lake Storage Gen1 utilizando a Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) (anteriormente conhecido como Azure Data Lake Store) é um repositório de hiperescala em toda a empresa para grandes cargas analíticas de dados. Data Lake Storage Gen1 permite-lhe capturar dados de qualquer tamanho, tipo e velocidade de ingestão. Os dados são capturados num único local para análise operacional e exploratória.

AZure Data Factory é um serviço de integração de dados totalmente gerido na nuvem. Pode utilizar o serviço para povoar o lago com dados do seu sistema existente e economizar tempo na construção das suas soluções de análise.

A Azure Data Factory oferece os seguintes benefícios para o carregamento de dados na Data Lake Storage Gen1:

* **Fácil de configurar:** Um assistente intuitivo de 5 passos sem necessidade de scripts.
* Suporte rico em lojas de **dados**: Suporte incorporado para um conjunto rico de lojas de dados no local e lojas de dados baseadas em nuvem. Para obter uma lista detalhada, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
* **Seguro e em conformidade:** Os dados são transferidos sobre HTTPS ou ExpressRoute. A presença global de serviços garante que os seus dados nunca saiam da fronteira geográfica.
* **Alto desempenho**: Velocidade de carregamento de dados até 1-GB/s na Data Lake Storage Gen1. Para mais detalhes, consulte [o desempenho da atividade da Copy](copy-activity-performance.md).

Este artigo mostra-lhe como utilizar a ferramenta Data Copy Data da Data Factory para _carregar dados do Amazon S3 na Data Lake Storage Gen1_. Pode seguir passos semelhantes para copiar dados de outros tipos de lojas de dados.

> [!NOTE]
> Para obter mais informações, consulte [copiar dados para ou a partir da Data Lake Storage Gen1 utilizando a Azure Data Factory](connector-azure-data-lake-store.md).

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Conta Gen1 de armazenamento de dados: Se não tiver uma conta Gen1 de armazenamento de dados, consulte as instruções na [Create a Data Lake Storage Gen1](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account)account .
* Amazon S3: Este artigo mostra como copiar dados do Amazon S3. Pode utilizar outras lojas de dados seguindo passos semelhantes.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu esquerdo, **selecione Criar uma**Fábrica de  >  Dados de**Análise**  >  **de**Recursos:
   
   ![Seleção do Data Factory no painel "Novo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na nova página de **fábrica de dados,** forneça valores para os campos que são mostrados na seguinte imagem: 
      
   ![Página Nova fábrica de dados](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Nome**: Introduza um nome globalmente único para a sua fábrica de dados Azure. Se receber o erro "O nome da fábrica \" de dados LoadADLSG1Demo \" não está disponível", insira um nome diferente para a fábrica de dados. Por exemplo, pode utilizar o nome _**do seu nome**_**ADFTutorialDataFactory**. Tente criar a fábrica de dados de novo. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Subscrição**: Selecione a sua subscrição Azure na qual criar a fábrica de dados. 
    * **Grupo de Recursos**: Selecione um grupo de recursos existente da lista de drop-down, ou selecione a nova opção **Criar** e insira o nome de um grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).  
    * **Versão**: selecione **V2**.
    * **Localização**: Selecione a localização para a fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. As lojas de dados que são utilizadas pela fábrica de dados podem estar noutros locais e regiões. Estas lojas de dados incluem Azure Data Lake Storage Gen1, Azure Storage, Azure SQL Database, e assim por diante.

3. Selecione **Criar**.
4. Depois de a criação estar completa, vá à sua fábrica de dados. Veja a página inicial da **Data Factory** como mostrado na seguinte imagem: 
   
   ![Home page da fábrica de dados](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Selecione o mosaico **Criar e Monitorizar** para iniciar a Aplicação de Integração de Dados num separador à parte.

## <a name="load-data-into-data-lake-storage-gen1"></a>Carregue os dados na Data Lake Storage Gen1

1. Na página **'Iniciar',** selecione o azulejo **de Dados** de Cópia para lançar a ferramenta Dados de Cópia: 

   ![Mosaico ferramenta Copiar Dados](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. Na página **Propriedades,** especifique **copyFromAmazonS3ToADLS** para o campo **de nome de tarefa** e selecione **Seguinte**:

    ![Página Propriedades](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. Na página **'Source data store',** clique **+ Criar nova ligação**:

    ![Página de arquivo de dados de origem](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    Selecione **Amazon S3**, e selecione **Continue**
    
    ![Página s3 da loja de dados de origem](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. Na página **de ligação Especificar o Amazon S3,** faça os seguintes passos: 
   1. Especifique o valor do **ID da chave de acesso.**
   2. Especifique o valor **da chave de acesso secreto.**
   3. Selecione **Concluir**.
   
      ![Especificar a conta Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. Verá uma nova ligação. Selecione **Seguinte**.
   
   ![Especificar a conta Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. Na página **Escolher o ficheiro ou pasta de entrada**, navegue até à pasta ou ficheiro que pretende copiar. Selecione a pasta/ficheiro, **selecione Escolha**e, em seguida, selecione **Seguinte**:

    ![Escolher ficheiro ou pasta de entrada](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Escolha o comportamento da cópia selecionando os **ficheiros Copy de forma recorrente** e as opções de cópia **binária** (copiar ficheiros como-is). Selecione **Seguinte**:

    ![Especificar pasta de saída](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. Na página da **loja de dados destino,** clique **em + Criar uma nova ligação**e, em seguida, selecione **Azure Data Lake Storage Gen1**, e selecione **Continue**:

    ![Página arquivo de dados de destino](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. Na página **New Linked Service (Azure Data Lake Storage Gen1),** faça os seguintes passos: 

   1. Selecione a sua conta de Data Lake Storage Gen1 para o nome da **conta Data Lake Store**.
   2. Especifique o **Inquilino,** e selecione Acabamento.
   3. Selecione **Seguinte**.
   
   > [!IMPORTANT]
   > Nesta passagem, você usa uma identidade gerida para recursos Azure para autenticar a sua conta Desaenty Desejamento Gen1. Certifique-se de que concede ao MSI as permissões adequadas na Data Lake Storage Gen1 seguindo [estas instruções.](connector-azure-data-lake-store.md#managed-identity)
   
   ![Especificar conta Gen1 de armazenamento de dados](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. Na página Escolha o ficheiro de saída ou a página **de pasta,** introduza **o copyfroms3** como o nome da pasta de saída e selecione **Seguinte**: 

    ![Especificar pasta de saída](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. Na página **Definições,** selecione **Seguinte**:

    ![Página de definições](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. Na página **Resumo,** reveja as definições e selecione **Seguinte**:

    ![Página de resumo](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. Na **página 'Implantação',** selecione **Monitor** para monitorizar o pipeline (tarefa):

    ![Página de implementação](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. A coluna **Ações** inclui links para visualizar detalhes da atividade e para refazer o gasoduto:

    ![Monitorizar execuções de pipeline](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Para visualizar os percursos de atividade que estão associados à execução do pipeline, selecione o link **Ver Atividades** na coluna **Ações.** Há apenas uma atividade (atividade copiar) no pipeline, pelo que só vai ver uma entrada. Para voltar à vista do gasoduto, selecione a ligação **Pipelines** na parte superior. Selecione **Atualizar** para atualizar a lista. 

    ![Monitorização de execuções de atividade](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Para monitorizar os detalhes da execução de cada atividade de cópia, selecione o link **Detalhes** em **Ações** na vista de monitorização da atividade. Pode monitorizar detalhes como o volume de dados copiados da fonte para a pia, a produção de dados, os passos de execução com a duração correspondente e as configurações utilizadas:

    ![Monitorize detalhes da atividade](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Verifique se os dados são copiados na sua conta da Data Lake Storage Gen1: 

    ![Verificar a saída gen1 de armazenamento de dados do lago de dados](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Passos seguintes

Avance para o seguinte artigo para saber sobre o suporte da Data Lake Storage Gen1: 

> [!div class="nextstepaction"]
>[Conector Gen1 de armazenamento de dados Azure](connector-azure-data-lake-store.md)
