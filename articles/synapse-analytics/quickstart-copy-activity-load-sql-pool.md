---
title: 'Quickstart: para carregar dados em pool SQL dedicado usando a atividade de cópia'
description: Utilize a atividade de cópia do gasoduto no Azure Synapse Analytics para carregar dados em piscinas SQL dedicadas.
services: synapse-analytics
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: synapse-analytics
ms.topic: quickstart
ms.custom: seo-lt-2019
ms.date: 11/02/2020
ms.openlocfilehash: 542fde3ac951bf60d999361dc114491515fb9528
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94735250"
---
# <a name="quickstart-load-data-into-dedicated-sql-pool-using-the-copy-activity"></a>Quickstart: Carregue os dados em pool SQL dedicado usando a atividade de cópia

O Azure Synapse Analytics oferece vários motores de análise para ajudá-lo a ingerir, transformar, modelar e analisar os seus dados. Um pool SQL dedicado oferece capacidades de computação e armazenamento baseadas em T-SQL. Depois de criar uma piscina DE SQL dedicada no seu espaço de trabalho Synapse, os dados podem ser carregados, modelados, processados e entregues para uma visão analítica mais rápida.

Neste arranque rápido, aprende-se a *carregar dados da Base de Dados Azure SQL para a Azure Synapse Analytics*. Pode seguir passos semelhantes para copiar dados de outros tipos de lojas de dados. Este fluxo semelhante aplica-se também à cópia de dados para outras fontes e sumidouros.

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Espaço de trabalho Azure Synapse: Criar um espaço de trabalho synapse utilizando o portal Azure seguindo as instruções em [Quickstart: Criar um espaço de trabalho Synapse](quickstart-create-workspace.md).
* Base de Dados Azure SQL: Este tutorial copia dados do conjunto de dados da amostra DE ADVENTURE Works LT na Base de Dados Azure SQL. Pode criar esta base de dados de amostras na Base de Dados SQL seguindo as instruções na [Criação de uma base de dados de amostras na Base de Dados Azure SQL](../azure-sql/database/single-database-create-quickstart.md). Ou pode utilizar outras lojas de dados seguindo passos semelhantes.
* Conta de armazenamento Azure: O Azure Storage é utilizado como área *de preparação* na operação de cópia. Se não tem uma conta de armazenamento do Azure, veja as instruções apresentadas em [Criar uma conta de armazenamento](../storage/common/storage-account-create.md).
* Azure Synapse Analytics: Você usa uma piscina SQL dedicada como uma loja de dados de lavatórios. Se não tiver uma instância Azure Synapse Analytics, consulte [Criar uma piscina SQL dedicada](quickstart-create-sql-pool-portal.md) para etapas para criar uma.

### <a name="navigate-to-the-synapse-studio"></a>Navegue até ao Estúdio Synapse

Após a criação do seu espaço de trabalho synapse, tem duas formas de abrir o Synapse Studio:

* Abra o seu espaço de trabalho sinapse no [portal Azure](https://ms.portal.azure.com/#home). Selecione **Abrir** no cartão Open Synapse Studio em "Começar".
* Abra [o Azure Synapse Analytics](https://web.azuresynapse.net/) e inscreva-se no seu espaço de trabalho.

Neste arranque rápido, usamos o espaço de trabalho chamado "adftest2020" como exemplo. Irá navegar automaticamente para a página inicial do Synapse Studio.

![Página inicial do Estúdio de Sinaapse](media/doc-common-process/synapse-studio-home.png)

## <a name="create-linked-services"></a>Criar serviços ligados

No Azure Synapse Analytics, um serviço ligado é onde define as suas informações de ligação a outros serviços. Nesta secção, você irá criar dois tipos de serviços ligados: Azure SQL Database e Azure Data Lake Storage Gen2 (ADLS Gen2) serviços ligados.

1. Na página inicial do Synapse Studio, selecione o **separador Gerir** na navegação à esquerda.
1. Em ligações externas, selecione serviços Linked.
  
   ![Criar novo serviço ligado](media/doc-common-process/new-linked-service.png)

1. Para adicionar um serviço ligado, selecione **New**.
1. Selecione **Azure SQL Database** da galeria e, em seguida, selecione **Continue**. Pode escrever "sql" na caixa de pesquisa para filtrar os conectores.

   ![Criar novo serviço de base de dados Azure SQL](media/quickstart-copy-activity-load-sql-pool/new-azure-sql-linked-service.png)

1. Na página New Linked Service, selecione o nome do servidor e o nome DB da lista de dropdown e especifique o nome de utilizador e a palavra-passe. Clique **na ligação de teste** para validar as definições e, em seguida, selecione **Criar**.

   ![Configure Serviço de Base de Dados SQL de Configure](media/quickstart-copy-activity-load-sql-pool/azure-sql-linked-service-configuration.png)

1. Repita os passos 3-4, mas selecione **Azure Data Lake Storage Gen2** em vez da galeria. Na página New Linked Service, selecione o nome da sua conta de armazenamento na lista de dropdown. Clique **na ligação de teste** para validar as definições e, em seguida, selecione **Criar**. 

   ![Configure Azure Data Lake Storage Gen2](media/quickstart-copy-activity-load-sql-pool/adls-gen2-linked-service-configuration.png)
 
## <a name="create-a-pipeline"></a>Criar um pipeline

Um oleoduto contém o fluxo lógico para uma execução de um conjunto de atividades. Nesta secção, você vai criar um pipeline contendo uma atividade de cópia que ingere dados da Azure SQL Database em uma piscina DE SQL dedicada.

1. Aceda ao **separador Integração.** Selecione no ícone mais ao lado do cabeçalho dos oleodutos e selecione Pipeline.

   ![Criar um novo oleoduto](media/doc-common-process/new-pipeline.png)

1. Em *Movimento e Transformar* no painel de *Atividades,* **arraste os dados do Copy** para a tela do gasoduto.
1. Selecione na atividade da cópia e vá para o separador Fonte. Selecione **Novo** para criar um novo conjunto de dados de origem.

   ![Criar um conjunto de dados de origem](media/quickstart-copy-activity-load-sql-pool/new-source-dataset.png)

1. Selecione **Azure SQL Database** como sua loja de dados e selecione **Continue**.
1. No painel *de propriedades set,* selecione o serviço de base de dados Azure SQL que criou no passo anterior. 
1. No nome da tabela, selecione uma tabela de amostras para utilizar na seguinte atividade de cópia. Neste arranque rápido, usamos a tabela "SalesLT.Customer" como exemplo. 

   ![Configurar propriedades de conjunto de dados de origem](media/quickstart-copy-activity-load-sql-pool/source-dataset-properties.png)
1. Selecione **OK** quando terminar.
1. Selecione a atividade da cópia e vá para o separador Afundar. Selecione **Novo** para criar um novo conjunto de dados de pia.
1. Selecione **Azure Synapse pool SQL dedicado** como sua loja de dados e selecione **Continue**.
1. No painel  **de propriedades set,** selecione a piscina SQL Analytics que criou no passo anterior. Se estiver a escrever para uma tabela existente, sob *o nome de tabela* selecione-a a partir do dropdown. Caso contrário, verifique "Editar" e introduza o seu novo nome de mesa. Selecione **OK** quando terminar.
1. Para as definições do conjunto de dados do sink, ative **a tabela de criação automática** no campo de opções tabela.

   ![Ativar a criação automática](media/quickstart-copy-activity-load-sql-pool/auto-create-table.png)

1. Na página **Definições,** selecione a caixa de verificação para **ativar a paragem**. Esta opção aplica-se se os seus dados de origem não forem compatíveis com a PolyBase. Na secção **de definições de encenação,** selecione o serviço ligado ao Azure Data Lake Storage Gen2 que criou no passo anterior como armazenamento de encenação. 

    O armazenamento é utilizado para a paragem dos dados antes de ser carregado no Azure Synapse Analytics utilizando o PolyBase. Após a conclusão da cópia, os dados provisórios da Azure Data Lake Storage Gen2 são automaticamente limpos.

   ![Ativar o teste](media/quickstart-copy-activity-load-sql-pool/staging-linked-service.png)

1. Para validar o gasoduto, **selecione Validate** na barra de ferramentas. Vê o resultado da saída de validação do Pipeline no lado direito da página. 

## <a name="debug-and-publish-the-pipeline"></a>Depurar e publicar o pipeline

Uma vez terminada a configuração do seu oleoduto, pode executar uma corrida de depuragem antes de publicar os seus artefactos para verificar se está tudo correto.

1. Para depurar o pipeline, selecione **Depurar** na barra de ferramentas. Verá o estado da execução do pipeline no separador **Saída**, na parte inferior da janela. 

   ![Depurar o pipeline](media/quickstart-copy-activity-load-sql-pool/debugging-result.png)

1. Uma vez que o gasoduto tenha sucesso, na barra de ferramentas superior, **selecione Publicar tudo**. Esta ação publica entidades (conjuntos de dados e oleodutos) que criou para o serviço Synapse Analytics.
1. Aguarde até ver a mensagem **Publicação com êxito**. Para ver as mensagens de notificação, selecione o botão de campainha no topo direito. 


## <a name="trigger-and-monitor-the-pipeline"></a>Dispare e monitorize o gasoduto

Nesta secção, aciona manualmente o gasoduto publicado no passo anterior. 

1. **Selecione Adicionar Gatilho** na barra de ferramentas e, em seguida, selecione Trigger **Now**. Na página **Pipeline Run,** selecione **OK**.  
1. Vá ao **separador Monitor** localizado na barra lateral esquerda. Verá uma execução de pipeline que é acionada por um acionador manual. 
1. Quando o curso do gasoduto estiver concluído com sucesso, selecione a ligação sob a coluna **de nomes pipeline** para visualizar detalhes de execução da atividade ou para refazer o gasoduto. Neste exemplo, há apenas uma atividade, por isso só se vê uma entrada na lista. 
1. Para mais detalhes sobre a operação da cópia, selecione o link **Details** (ícone de óculos) na coluna **'Nome 'Atividade'.** Pode monitorizar detalhes como o volume de dados copiados da fonte para a pia, saída de dados, etapas de execução com a duração correspondente e configurações usadas.

   ![Detalhes da atividade](media/quickstart-copy-activity-load-sql-pool/activity-details.png)

1. Para voltar à vista do gasoduto, selecione a ligação **de todas as tubagem** na parte superior. Selecione **Atualizar** para atualizar a lista.
1. Verifique se os seus dados estão corretamente escritos na piscina SQL dedicada.


## <a name="next-steps"></a>Passos seguintes

Avance para o seguinte artigo para saber sobre o suporte Azure Synapse Analytics:

> [!div class="nextstepaction"]
> [Gasoduto e atividades](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 
>  [Visão geral do](https://docs.microsoft.com/azure/data-factory/connector-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 
>  conector [Atividade de cópia](https://docs.microsoft.com/azure/data-factory/copy-activity-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)