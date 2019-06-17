---
title: Copiar dados do Azure Data Factory para o Explorador de dados do Azure
description: Neste tópico, vai aprender a ingerir dados de (carga) no Explorador de dados do Azure, utilizando a ferramenta de cópia de Azure Data Factory
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 64856d53168a7676cf279da2d8675ce81e1985f7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60447973"
---
# <a name="copy-data-to-azure-data-explorer-using-azure-data-factory"></a>Copiar dados para o Explorador de dados do Azure com o Azure Data Factory 

Explorador de dados do Azure é um serviço de análise de dados rápido, totalmente gerido para análise em tempo real em grandes volumes de dados de transmissão em fluxo a partir de várias origens, como aplicações, sites e dispositivos IoT. Explorar dados e identificar padrões e anomalias para melhorar os produtos, melhorar as experiências de cliente, de maneira iterativa monitorizar dispositivos e aumente as operações. Explore novas questões e obtenha respostas em poucos minutos. O Azure Data Factory é um serviço de integração de dados totalmente gerido com base na cloud. Pode utilizar o serviço para preencher a base de dados do Explorador de dados do Azure com os dados do sistema existente e poupar tempo quando criar as suas soluções de análise.

O Azure Data Factory oferece as seguintes vantagens para carregar dados para o Explorador de dados do Azure:

* **Fácil de configurar**: Um Assistente de cinco etapas intuitivo com nenhum script necessário.
* **Suporte de arquivo de dados de rich**: Suporte interno para um conjunto avançado de arquivos de dados com base na cloud e no local. Para obter uma lista detalhada, consulte a tabela de [arquivos de dados suportados](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Segura e em conformidade**: Dados são transferidos através de HTTPS ou ExpressRoute. A presença de serviço global assegura que seus dados nunca sai do limite geográfico.
* **Elevado desempenho**: Até a velocidade de carregamento de dados de 1-GB/s no Explorador de dados do Azure. Para obter detalhes, consulte [copie o desempenho de atividade](/azure/data-factory/copy-activity-performance).

Este artigo mostra-lhe como utilizar a ferramenta copiar dados do Data Factory para carregar dados do Amazon S3 para o Explorador de dados do Azure. Pode seguir passos semelhantes para copiar dados de outros arquivos de dados, tal como [armazenamento de Blobs do Azure](/azure/data-factory/connector-azure-blob-storage), [base de dados do Azure SQL](/azure/data-factory/connector-azure-sql-database), [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse), [Google BigQuery](/azure/data-factory/connector-google-bigquery),[Oracle](/azure/data-factory/connector-oracle), e [sistema de ficheiros](/azure/data-factory/connector-file-system).

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Um cluster do Explorador de dados do Azure e a base de dados](create-cluster-database-portal.md)
* Origem de dados.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Selecione o **criar um recurso** botão (+) no canto superior esquerdo do portal > **Analytics** > **Data Factory**.

   ![Criar uma nova fábrica de dados](media/data-factory-load-data/create-adf.png)

1. Na **nova fábrica de dados** página, fornecer valores para os campos seguintes e, em seguida, selecione **criar**.

    ![Página Nova fábrica de dados](media/data-factory-load-data/my-new-data-factory.png)

    **Definição**  | **Descrição do campo**
    |---|---|
    | **Nome** | Introduza um nome globalmente exclusivo para a fábrica de dados. Se receber o erro *"nome do Data factory \"LoadADXDemo\" não está disponível"* , introduza um nome diferente para a fábrica de dados. Para regras de nomenclatura dos artefactos do Data Factory, veja [regras de nomenclatura do Data Factory](/azure/data-factory/naming-rules).|
    | **Subscrição** | Selecione a sua subscrição do Azure na qual pretende criar a fábrica de dados. |
    | **Grupo de Recursos** | Selecione **criar novo** e introduza o nome de um novo grupo de recursos. Selecione **utilizar existente**, se tiver um grupo de recursos existente. |
    | **Versão** | Selecione **V2** |
    | **Location** | Selecione a localização da fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. Os arquivos de dados que são utilizados pelo data factory podem estar em outras localizações ou regiões. |
    | | |

1. Selecione as notificações na barra de ferramentas para monitorizar o processo de criação. Depois de concluída a criação, vá para a fábrica de dados que criou. O **fábrica de dados** é aberta a página home.

   ![Home page da fábrica de dados](media/data-factory-load-data/data-factory-home-page.png)

1. Selecione o **criar e monitorizar** mosaico para iniciar o aplicativo num separador à parte.

## <a name="load-data-into-azure-data-explorer"></a>Carregar dados para o Explorador de dados do Azure

Podem carregar os dados de vários tipos de [arquivos de dados](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) no Explorador de dados do Azure. Este tópico fornece detalhes sobre o carregamento de dados do Amazon S3.

Existem duas formas de carregar dados para o Explorador de dados do Azure com o Azure Data Factory:

* Interface de utilizador do Azure Data Factory - [ **autor** separador](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
* [O Azure Data Factory **dados de cópia** ferramenta](/azure/data-factory/quickstart-create-data-factory-copy-data-tool) usados neste artigo.

### <a name="copy-data-from-amazon-s3-source"></a>Copiar dados do Amazon S3 (origem)

1. Na **Vamos começar** página, selecione a **copiar dados** mosaico para iniciar a ferramenta copiar dados.

   ![Mosaico ferramenta copiar dados](media/data-factory-load-data/copy-data-tool-tile.png)

1. Na **propriedades** , especifique **nome da tarefa** e selecione **seguinte**.

    ![Copiar a partir das propriedades de origem](media/data-factory-load-data/copy-from-source.png)

1. Na **o arquivo de dados de origem** página, clique em **+ criar nova ligação**.

    ![Origem de dados criar ligação](media/data-factory-load-data/source-create-connection.png)

1. Selecione **Amazon S3**e, em seguida, selecione **continuar**

    ![Novo serviço ligado](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. Na **novo serviço ligado (Amazon S3)** página, efetue os seguintes passos:

    ![Especifique um serviço ligado do Amazon S3](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * Especifique **nome** do seu novo serviço ligado.
    * Selecione **ligar através do runtime de integração** valor na lista pendente.
    * Especifique a **ID de chave de acesso** valor.
    * Especifique a **chave de acesso secreta** valor.
    * Selecione **Testar ligação** para testar a ligação de serviço ligado que criou.
    * Selecione **Concluir**.

1. Na **o arquivo de dados de origem** página, verá a sua nova ligação AmazonS31. Selecione **Seguinte**.

   ![Criação de ligação do arquivo de dados de origem](media/data-factory-load-data/source-data-store-created-connection.png)

1. Na **escolher o ficheiro de entrada ou a pasta** página:

    1. Navegue para o pasta/ficheiro que pretende copiar. Selecione o ficheiro/pasta.
    1. Selecione o comportamento de cópia conforme necessário. Manter **cópia binária** desmarcada.
    1. Selecione **Seguinte**.

    ![Escolher ficheiro ou pasta de entrada](media/data-factory-load-data/source-choose-input-file.png)

1. Na **definições de formatos de arquivo** página Selecione as definições relevantes para o ficheiro e clique em **próxima**.

   ![Escolher ficheiro ou pasta de entrada](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Copiar dados para o Explorador de dados do Azure (destino)

Explorador de dados novo serviço ligado do Azure é criado para copiar os dados na tabela de destino de Explorador de dados do Azure (sink) especificados abaixo.

1. Na **o arquivo de dados de destino** página, pode utilizar os dados existentes armazenar ligação ou especificar um novo arquivo de dados ao clicar em **+ criar nova ligação**.

    ![Página arquivo de dados de destino](media/data-factory-load-data/destination-create-connection.png)

1. Na **novo serviço ligado** página, selecione **Explorador de dados do Azure**e, em seguida, selecione **continuar**

    ![Selecione o Explorador de dados do Azure - novo serviço ligado](media/data-factory-load-data/adx-select-new-linked-service.png)

1. Na **novo serviço ligado (Explorador de dados do Azure)** página, efetue os seguintes passos:

    ![Novo serviço ligado ADX](media/data-factory-load-data/adx-new-linked-service.png)

   * Selecione **nome** Explorador de dados do Azure para o serviço ligado.
   * Na **método de seleção de contas**: 
        * Selecione o **subscrição do Azure** botão de opção e selecione sua **subscrição do Azure** conta. Em seguida, selecione seu **Cluster**. Tenha em atenção o menu suspenso serão apenas os clusters de lista que pertencem ao utilizador.
        * Em alternativa, selecione **introduzir manualmente** botão de rádio e introduza o seu **Endpoint**.
    * Especifique a **inquilino**.
    * Introduza **ID de principal de serviço**.
    * Selecione **chave de principal de serviço** e digitar **chave de Principal de serviço**.
    * Selecione seu **base de dados** no menu pendente. Em alternativa, selecione **editar** caixa de verificação e introduza o seu nome de base de dados.
    * Selecione **Testar ligação** para testar a ligação de serviço ligado que criou. Se pode ligar a sua configuração, uma marca de verificação verde **ligação estabelecida com êxito** aparecerá.
    * Selecione **concluir** para concluir a criação do serviço ligado.

    > [!NOTE]
    > O principal de serviço é utilizado pelo Azure Data Factory para aceder ao serviço do Explorador de dados do Azure. Para o principal de serviço, [criar um Azure Active Directory (Azure AD) principal de serviço](/azure/azure-stack/azure-stack-create-service-principals#manage-service-principal-for-azure-ad). Não utilize o **do Azure Key Vault** método.

1. O **o arquivo de dados de destino** abre. A ligação de dados do Explorador de dados do Azure que criou está disponível para utilização. Selecione **seguinte** para configurar a ligação.

    ![Arquivo de dados de destino ADX](media/data-factory-load-data/destination-data-store.png)

1. Na **mapeamento de tabelas**, defina o nome da tabela de destino e selecione **próxima**.

    ![Mapeamento de tabelas do conjunto de dados de destino](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. Na **mapeamento de colunas** página:
    * O primeiro mapeamento é realizado pelo ADF de acordo com a [mapeamento de esquema do ADF](/azure/data-factory/copy-activity-schema-and-type-mapping)
        * Definir o **mapeamentos de coluna** para a tabela de destino do Azure Data Factory. O mapeamento predefinido é apresentado da origem para a tabela de destino do ADF.
        * Anule a seleção de colunas que não precisa de definir o mapeamento de colunas.
    * O segundo mapeamento ocorre quando esses dados em tabela são ingeridos no Explorador de dados do Azure. Mapeamento é executado de acordo com a [regras de mapeamento de CSV](/azure/kusto/management/mappings#csv-mapping). Tenha em atenção que, mesmo que a origem de dados não estava no formato CSV, ADF tem de converter os dados num formato tabular, portanto, o mapeamento de CSV é o mapeamento relevante apenas nesta fase.
        * Sob **propriedades do Explorador de dados do Azure (Kusto) sink** adicionar o relevante **nome do mapeamento de ingestão** (opcional) então, esse mapeamento de coluna pode ser utilizado.
        * Se **nome do mapeamento de ingestão** não for especificado, ordem de mapeamento de "by-name" definido no **mapeamentos de coluna** secção irá ocorrer. Se o mapeamento de "by-name" falhar, o Explorador de dados do Azure tentará para ingestão de dados numa ordem de "posição por coluna" (maps por-posição como predefinição).
    * Selecione **Seguinte**

    ![Mapeamento de colunas do conjunto de dados de destino](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. Na página **Settings** (Definições):
    * Definir o relevante **definições de tolerância de falhas**.
    * **As definições de desempenho**: Ativar teste não é aplicável. **Definições avançadas** incluem considerações de custo. Deixe como é se precisam não específicas.
    * Selecione **Seguinte**.

    ![Definições de dados de cópia](media/data-factory-load-data/copy-data-settings.png)

1. Na **resumo**, reveja as definições e selecione **próxima**.

    ![Copiar dados de resumos](media/data-factory-load-data/copy-data-summary.png)

1. Na **página de implementação**:
    * Selecione **Monitor** para mudar para o **Monitor** separador e ver o estado do pipeline (fluxo de progresso, erros e dados).
    * Selecione **Pipeline editar** editar serviços ligados, conjuntos de dados e pipelines.
    * Selecione **concluir** a tarefa de cópia completa de dados

    ![Página de implementação](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre o [conector do Explorador de dados do Azure](/azure/data-factory/connector-azure-data-explorer) no Azure Data Factory.

* Saiba mais sobre como editar serviços ligados, conjuntos de dados e pipelines no [IU do Data Factory](/azure/data-factory/quickstart-create-data-factory-portal).

* Saiba mais sobre [consultas do Explorador de dados do Azure](/azure/data-explorer/web-query-data) para consultar dados.
