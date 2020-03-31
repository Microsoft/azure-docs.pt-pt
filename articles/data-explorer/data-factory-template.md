---
title: Copiar a granel de uma base de dados para o Azure Data Explorer utilizando o modelo azure data factory
description: Neste artigo, aprende-se a usar um modelo de Fábrica de Dados Azure para copiar a granel de uma base de dados para o Azure Data Explorer
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 884f4e956b37c2def6c25d0acdf20f15eddf7767
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293560"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>Copiar a granel de uma base de dados para o Azure Data Explorer utilizando o modelo azure data factory 

O Azure Data Explorer é um serviço rápido, totalmente gerido, de análise de dados. Oferece análiseem em tempo real sobre grandes volumes de dados que fluem de muitas fontes, tais como aplicações, websites e dispositivos IoT. 

Para copiar dados de uma base de dados no Oracle Server, Netezza, Teradata ou SQL Server para o Azure Data Explorer, tem de carregar enormes quantidades de dados a partir de várias tabelas. Normalmente, os dados têm de ser divididos em cada tabela para que possa carregar linhas com vários fios em paralelo a partir de uma única tabela. Este artigo descreve um modelo para usar nestes cenários.

[Os modelos](/azure/data-factory/solution-templates-introduction) da Fábrica de Dados Azure são oleodutos predefinidos da Fábrica de Dados. Estes modelos podem ajudá-lo a começar rapidamente com data factory e reduzir o tempo de desenvolvimento em projetos de integração de dados. 

Cria a cópia a granel da Base de Dados para o modelo de Explorador de *Dados Azure* utilizando as atividades *de Lookup* e *ForEach.* Para uma cópia de dados mais rápida, pode utilizar o modelo para criar muitos oleodutos por base de dados ou por tabela. 

> [!IMPORTANT]
> Certifique-se de que utiliza a ferramenta adequada para a quantidade de dados que pretende copiar.
> * Utilize a cópia a granel da base de dados para o modelo Do Explorador de *Dados Azure* para copiar grandes quantidades de dados de bases de dados como o servidor SQL e o Google BigQuery para o Azure Data Explorer. 
> * Utilize a [*ferramenta Data Copy Data*](data-factory-load-data.md) da Data para copiar algumas tabelas com pequenas ou moderadas quantidades de dados no Azure Data Explorer. 

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Um cluster azure Data Explorer e base de dados.](create-cluster-database-portal.md)
* [Criar uma fábrica de dados.](data-factory-load-data.md#create-a-data-factory)
* Uma fonte de dados numa base de dados.

## <a name="create-controltabledataset"></a>Criar Conjunto de Datação de ControloTable

*ControlTableDataset* indica quais os dados que serão copiados da fonte para o destino no oleoduto. O número de linhas indica o número total de gasodutos necessários para copiar os dados. Deve definir o Conjunto de Dados do ControlTable como parte da base de dados de origem.

Um exemplo do formato de tabela de fonte sQL Server é mostrado no seguinte código:
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```

Os elementos de código são descritos na tabela seguinte:

|Propriedade  |Descrição  | Exemplo
|---------|---------| ---------|
|Partida   |  A cópia | 1  |  
|FonteQuery   |  A consulta que indica quais os dados que serão copiados durante o tempo de execução do gasoduto | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|Nome ADXTable  |  O nome da tabela de destino | MyAdxTable       |  

Se o seu ControlTableDataset estiver num formato diferente, crie um Conjunto de Dados De Controlo comparável para o seu formato.

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>Utilize a cópia a granel da base de dados para o modelo de Explorador de Dados Azure

1. No **Let's start** pane, **selecione Criar o oleoduto do modelo** para abrir o painel da galeria **Modelo.**

    ![O painel da Azure Data Factory "Vamos começar"](media/data-factory-template/adf-get-started.png)

1. Selecione a cópia a granel da base de dados para o modelo do Explorador de **Dados Azure.**
 
    ![O modelo "Copy Bulk from Database to Azure Data Explorer"](media/data-factory-template/pipeline-from-template.png)

1.  Na cópia a granel da base de dados para o painel do Explorador de **Dados Azure,** em função das **Inputs do Utilizador,** especifique os seus conjuntos de dados fazendo o seguinte: 

    a. Na lista de drop-down **do ControlTableDataset,** selecione o serviço ligado à tabela de controlo que indique quais os dados copiados da fonte para o destino e onde será colocado no destino. 

    b. Na lista de drop-down **SourceDataset,** selecione o serviço ligado à base de dados de origem. 

    c. Na lista de lançamentos **do AzureDataExplorerTable,** selecione a tabela Azure Data Explorer. Se o conjunto de dados não existir, [crie o serviço ligado ao Azure Data Explorer](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) para adicionar o conjunto de dados.

    d. Selecione **Utilize este modelo**.

    ![O painel "Copy Bulk from Database to Azure Data Explorer"](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. Selecione uma área na tela, fora das atividades, para aceder ao pipeline do modelo. Selecione o separador **Parâmetros** para introduzir os parâmetros para a tabela, incluindo **nome** (nome da tabela de controlo) e **valor predefinido** (nomes de coluna).

    ![Parâmetros do pipeline](media/data-factory-template/pipeline-parameters.png)

1.  Em **'Lookup', selecione** **GetPartitionList** para visualizar as definições predefinidas. A consulta é automaticamente criada.
1.  Selecione a atividade de Comando, **ForEachPartition,** selecione o separador **Definições** e, em seguida, faça o seguinte:

    a. Na caixa de contagem de **lote,** introduza um número de 1 a 50. Esta seleção determina o número de gasodutos que funcionam em paralelo até que o número de linhas *DeConjuntoDataset* do ControlTable seja atingido. 

    b. Para garantir que os lotes do gasoduto funcionam em paralelo, *não* selecione a caixa de verificação **sequencial.**

    ![Definições para APartilha de Divisórias](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > A melhor prática é executar muitos oleodutos em paralelo para que os seus dados possam ser copiados mais rapidamente. Para aumentar a eficiência, partiparta os dados na tabela de origem e alocar uma divisória por pipeline, de acordo com a data e a tabela.

1. Selecione **Validar Tudo** para validar o gasoduto Azure Data Factory e, em seguida, ver o resultado no painel de saída de validação do **gasoduto.**

    ![Validar gasodutos de modelo](media/data-factory-template/validate-template-pipelines.png)

1. Se necessário, selecione **Debug**, e, em seguida, **selecione Adicionar gatilho** para executar o gasoduto.

    ![Os botões "Debug" e "Executar pipeline"](media/data-factory-template/trigger-run-of-pipeline.png)    

Agora pode utilizar o modelo para copiar eficientemente grandes quantidades de dados das suas bases de dados e tabelas.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [copiar dados para o Azure Data Explorer utilizando](data-factory-load-data.md)a Azure Data Factory .
* Conheça o [conector Azure Data Explorer](/azure/data-factory/connector-azure-data-explorer) na Azure Data Factory.
* Saiba mais sobre as consultas do [Azure Data Explorer](/azure/data-explorer/web-query-data) para consulta de dados.






