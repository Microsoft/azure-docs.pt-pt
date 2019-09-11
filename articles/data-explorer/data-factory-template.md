---
title: Usar o modelo de Azure Data Factory para cópia em massa do banco de dados para o Azure Data Explorer
description: Neste tópico, você aprenderá a usar um modelo de Azure Data Factory para cópia em massa do banco de dados para o Azure Data Explorer
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 5a6aebd276ef8658da9ca763be7da5c38a9c772a
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873425"
---
# <a name="use-azure-data-factory-template-for-bulk-copy-from-database-to-azure-data-explorer"></a>Usar o modelo de Azure Data Factory para cópia em massa do banco de dados para o Azure Data Explorer

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerenciado para análise em tempo real em grandes volumes de streaming de dados de várias fontes, como aplicativos, sites e dispositivos IoT. Azure Data Factory é um serviço de integração de dados baseado em nuvem totalmente gerenciado. Você pode usar o serviço de Azure Data Factory para popular o banco de dados de Data Explorer do Azure com o seu sistema existente e economizar tempo ao criar suas soluções de análise. 

Os [modelos de Azure data Factory](/azure/data-factory/solution-templates-introduction) são pipelines predefinidos Azure data Factory que permitem que você comece rapidamente com data Factory e reduza o tempo de desenvolvimento para a criação de projetos de integração de dados. O modelo **cópia em massa do banco de dados para o Azure data Explorer** é criado usando atividades de **pesquisa** e **foreach** . Você pode usar o modelo para criar vários pipelines por banco de dados ou tabela para uma cópia mais rápida do dado. 

> [!IMPORTANT]
> * Use o modelo **cópia em massa do banco de dados para o Azure data Explorer** para copiar grandes volumes de bancos de dados, como o SQL Server e o Google BigQuery para o Azure data Explorer. 
> * Use a [ferramenta de cópia](data-factory-load-data.md) para copiar algumas tabelas com quantidades pequenas ou moderadas de dados no Azure data Explorer. 

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Um cluster de Data Explorer do Azure e um banco de dados](create-cluster-database-portal.md)
* [Criar um data factory](data-factory-load-data.md#create-a-data-factory)
* Fonte de dados no banco de dado

## <a name="create-controltabledataset"></a>Criar ControlTableDataset

O **ControlTableDataset** indica quais dados serão copiados da origem para o destino no pipeline. O número de linhas indica o número total de pipelines necessários para copiar os dados. O **ControlTableDataset** deve ser definido como parte do banco de dados de origem.

Exemplo de formato de tabela de origem SQL Server:
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```
    
|Propriedade  |Descrição  | Exemplo
|---------|---------| ---------|
|PartitionId   |   copiar ordem | 1  |  
|SourceQuery   |   consulta que indica quais dados serão copiados durante o tempo de execução do pipeline | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  nome da tabela de destino | MyAdxTable       |  

Se o seu **ControlTableDataset** estiver em um formato diferente, crie um **ControlTableDataset** comparável para seu formato.

## <a name="use-bulk-copy-from-database-to-azure-data-explorer-template"></a>Usar cópia em massa do banco de dados para o modelo de Data Explorer do Azure

1. Na página **vamos** começar, selecione o bloco **criar pipeline do modelo** ou selecione o ícone de lápis (guia**autor** ) à direita > **+**  >  **pipeline do modelo** para abrir a Galeria de modelos.

    ![Azure Data Factory vamos começar](media/data-factory-template/adf-get-started.png)

1. Selecione o modelo **cópia em massa do banco de dados para o Azure data Explorer**.
 
    ![Selecionar pipeline do modelo](media/data-factory-template/pipeline-from-template.png)

1.  Na janela **cópia em massa do banco de dados para o Azure data Explorer** , selecione conjuntos de dados existentes na lista suspensa. 

    * **ControlTableDataset** -serviço vinculado à tabela de controle que indica quais dados são copiados da origem para o destino e onde eles serão colocados no destino. 
    * **SourceDataset** – serviço vinculado ao banco de dados de origem. 
    * **AzureDataExplorerTable** -tabela de data Explorer do Azure. Se o conjunto de um não existir, [crie o serviço vinculado do Azure data Explorer](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) para adicionar o conjunto de um.
    * Selecione **usar este modelo**.

    ![configurar o modelo de Data Explorer de cópia em massa do Azure](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. Selecione uma área na tela, fora das atividades, para acessar o pipeline de modelo. Selecione **parâmetros** para inserir os parâmetros da tabela, incluindo **nome** (nome da tabela de controle) e **valor padrão** (nomes de coluna).

    ![Parâmetros do pipeline](media/data-factory-template/pipeline-parameters.png)

1.  Selecione atividade de pesquisa, **Getparticionalist**, para exibir as configurações padrão. A consulta é criada automaticamente.
1.  Selecione comando atividade **ForEachPartition**, selecione **configurações**
    * **Contagem de lotes**: Selecione o número de 1-50. Essa seleção determina o número de pipelines que são executados em paralelo até que o número de **ControlTableDataset** de linhas seja atingido. 
    * Não marque a caixa de seleção **sequencial** para que os lotes de pipeline sejam executados em paralelo.

    ![Configurações de ForEachPartition](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > A prática recomendada é executar vários pipelines em paralelo para que os dados possam ser copiados mais rapidamente. Particione os dados na tabela de origem e aloque uma partição por pipeline, de acordo com a data e a tabela, para aumentar a eficiência.

1. Selecione **validar tudo** para validar o pipeline do ADF. Consulte **saída de validação de pipeline**.

    ![Validar pipelines de modelo](media/data-factory-template/validate-template-pipelines.png)

1. Selecione **depurar**, se necessário, e, em seguida, **Adicionar gatilho** para executar o pipeline.

    ![Executar pipeline](media/data-factory-template/trigger-run-of-pipeline.png)    


Agora você pode usar a **cópia em massa do banco de dados para o modelo de data Explorer do Azure** para copiar com eficiência grandes quantidades de data de seus bancos de dados e tabelas.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o procedimento para [copiar dados para o Azure data Explorer usando Azure data Factory](data-factory-load-data.md).

* Saiba mais sobre o [conector de data Explorer do Azure](/azure/data-factory/connector-azure-data-explorer) no Azure data Factory.

* Saiba mais sobre as [consultas do Azure data Explorer](/azure/data-explorer/web-query-data) para consulta de dados.






