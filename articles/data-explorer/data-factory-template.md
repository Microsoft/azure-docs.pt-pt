---
title: Copiar em massa de um banco de dados para o Azure Data Explorer usando o modelo de Azure Data Factory
description: Neste artigo, você aprenderá a usar um modelo de Azure Data Factory para copiar em massa de um banco de dados para o Azure Data Explorer
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 884f4e956b37c2def6c25d0acdf20f15eddf7767
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293560"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>Copiar em massa de um banco de dados para o Azure Data Explorer usando o modelo de Azure Data Factory 

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerenciado. Ele oferece análise em tempo real sobre grandes volumes de dados que transmitem de várias fontes, como aplicativos, sites e dispositivos IoT. 

Para copiar dados de um banco de dado no servidor Oracle, Netezza, Teradata ou SQL Server para o Azure Data Explorer, você precisa carregar enormes quantidades de dados de várias tabelas. Normalmente, os dados têm que ser particionados em cada tabela para que você possa carregar linhas com vários threads em paralelo a partir de uma única tabela. Este artigo descreve um modelo a ser usado nesses cenários.

Os [modelos de Azure data Factory](/azure/data-factory/solution-templates-introduction) são pipelines de data Factory predefinidos. Esses modelos podem ajudá-lo a começar rapidamente com Data Factory e reduzir o tempo de desenvolvimento em projetos de integração de dados. 

Você cria a *cópia em massa do banco de dados para o modelo de data Explorer do Azure* usando atividades de *pesquisa* e *foreach* . Para uma cópia de dados mais rápida, você pode usar o modelo para criar vários pipelines por banco de dado ou por tabela. 

> [!IMPORTANT]
> Certifique-se de usar a ferramenta apropriada para a quantidade de dados que você deseja copiar.
> * Use o modelo *cópia em massa do banco de dados para o Azure data Explorer* para copiar grandes volumes de bancos de dados, como o SQL Server e o Google BigQuery para o Azure data Explorer. 
> * Use a [*ferramenta Data Factory copiar dados*](data-factory-load-data.md) para copiar algumas tabelas com pequenas ou moderadas quantidades de dados para o Azure data Explorer. 

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Um cluster de data Explorer do Azure e um banco de dados](create-cluster-database-portal.md).
* [Crie um data Factory](data-factory-load-data.md#create-a-data-factory).
* Uma fonte de dados em um banco de dado.

## <a name="create-controltabledataset"></a>Criar ControlTableDataset

*ControlTableDataset* indica quais dados serão copiados da origem para o destino no pipeline. O número de linhas indica o número total de pipelines necessários para copiar os dados. Você deve definir ControlTableDataset como parte do banco de dados de origem.

Um exemplo de formato de tabela de origem SQL Server é mostrado no código a seguir:
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```

Os elementos de código são descritos na tabela a seguir:

|Propriedade  |Descrição  | Exemplo
|---------|---------| ---------|
|PartitionId   |  A ordem de cópia | 1  |  
|SourceQuery   |  A consulta que indica quais dados serão copiados durante o tempo de execução do pipeline | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  O nome da tabela de destino | MyAdxTable       |  

Se o seu ControlTableDataset estiver em um formato diferente, crie um ControlTableDataset comparável para seu formato.

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>Usar o modelo cópia em massa do banco de dados para o Azure Data Explorer

1. No painel **introdução** , selecione **criar pipeline de modelo** para abrir o painel **Galeria de modelos** .

    ![O painel de Azure Data Factory "Vamos começar"](media/data-factory-template/adf-get-started.png)

1. Selecione o modelo **cópia em massa do banco de dados para o Azure data Explorer** .
 
    ![O modelo "cópia em massa do banco de dados para o Azure Data Explorer"](media/data-factory-template/pipeline-from-template.png)

1.  No painel **cópia em massa do banco de dados para o Azure data Explorer** , em **entradas do usuário**, especifique os conjuntos de dados fazendo o seguinte: 

    a. Na lista suspensa **ControlTableDataset** , selecione o serviço vinculado à tabela de controle que indica quais dados são copiados da origem para o destino e onde eles serão colocados no destino. 

    b. Na lista suspensa **SourceDataset** , selecione o serviço vinculado para o banco de dados de origem. 

    c. Na lista suspensa **AzureDataExplorerTable** , selecione a tabela de data Explorer do Azure. Se o conjunto de um não existir, [crie o serviço vinculado do Azure data Explorer](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) para adicionar o conjunto de um.

    d. Selecione **Utilizar este modelo**.

    ![O painel "cópia em massa do banco de dados para o Azure Data Explorer"](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. Selecione uma área na tela, fora das atividades, para acessar o pipeline de modelo. Selecione a guia **parâmetros** para inserir os parâmetros da tabela, incluindo **nome** (nome da tabela de controle) e **valor padrão** (nomes de coluna).

    ![Parâmetros do pipeline](media/data-factory-template/pipeline-parameters.png)

1.  Em **pesquisa**, selecione **getparticionalist** para exibir as configurações padrão. A consulta é criada automaticamente.
1.  Selecione a atividade de comando, **ForEachPartition**, selecione a guia **configurações** e, em seguida, faça o seguinte:

    a. Na caixa **contagem de lote** , insira um número de 1 a 50. Essa seleção determina o número de pipelines que são executados em paralelo até que o número de linhas *ControlTableDataset* seja atingido. 

    b. Para garantir que os lotes de pipeline sejam executados em paralelo *, não* marque a caixa de seleção **sequencial** .

    ![Configurações de ForEachPartition](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > A prática recomendada é executar vários pipelines em paralelo para que os dados possam ser copiados mais rapidamente. Para aumentar a eficiência, Particione os dados na tabela de origem e aloque uma partição por pipeline, de acordo com a data e a tabela.

1. Selecione **validar tudo** para validar o pipeline de Azure data Factory e, em seguida, exiba o resultado no painel **saída de validação de pipeline** .

    ![Validar pipelines de modelo](media/data-factory-template/validate-template-pipelines.png)

1. Se necessário, selecione **depurar**e, em seguida, selecione **Adicionar gatilho** para executar o pipeline.

    ![Os botões "depurar" e "executar pipeline"](media/data-factory-template/trigger-run-of-pipeline.png)    

Agora você pode usar o modelo para copiar com eficiência grandes quantidades de dados de seus bancos de dados e tabelas.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [copiar dados para o Azure data Explorer usando Azure data Factory](data-factory-load-data.md).
* Saiba mais sobre o [conector de data Explorer do Azure](/azure/data-factory/connector-azure-data-explorer) no Azure data Factory.
* Saiba mais sobre as [consultas do Azure data Explorer](/azure/data-explorer/web-query-data) para consulta de dados.






