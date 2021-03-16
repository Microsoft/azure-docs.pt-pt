---
title: Utilize o Dataflow para processar dados de modelos automatizados de aprendizagem automática (AutoML)
description: Saiba como utilizar os fluxos de dados da Azure Data Factory para processar dados de modelos automatizados de aprendizagem automática de máquinas (AutoML).
services: data-factory
author: amberz
co-author: ATLArcht
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/31/2021
ms.author: amberz
ms.co-author: Donnana
ms.openlocfilehash: e8352b687a3cdfac7ea2a819e1217906598a6837
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563271"
---
# <a name="process-data-from-automated-machine-learningautoml-models-using-data-flow"></a>Processar dados de modelos automatizados de aprendizagem automática (AutoML) utilizando fluxo de dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O machine learning automatizado (AutoML) é adotado por projetos de machine learning para treinar, sintonizar e ganhar o melhor modelo automaticamente usando a métrica-alvo que especifica para classificação, regressão e previsão de séries de tempo. 

Um dos desafios é que os dados brutos do armazém de dados ou da base de dados transacionais seriam um enorme conjunto de dados, tais como: 10GB, o grande conjunto de dados requer mais tempo para formar modelos, pelo que otimizar o processamento de dados é recomendado antes de treinar modelos de Aprendizagem automática Azure. Este tutorial irá passar por como usar a ADF para conjunto de dados de partição para ficheiros de parquet para conjunto de dados de aprendizagem de máquinas Azure. 

No projeto automatizado de aprendizagem automática (AutoML), aplicaria os seguintes três cenários de processamento de dados:

* Partição de grandes dados para arquivos de parquet antes de modelos de treino. 

     [O quadro de dados dos pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) é comumente usado para processar dados antes de treinar modelos. O quadro de dados do Pandas funciona bem para tamanhos de dados inferiores a 1GB, mas se os dados forem grandes do que 1GB, o quadro de dados do Pandas abranda para processar dados, algum dia até sairá da mensagem de erro de memória. Os formatos [de ficheiros parquet](https://parquet.apache.org/) são recomendados para machine learning uma vez que é formato colunar binário.
    
    Azure Data Factorys Mapeamento de fluxos de dados são transformações de dados visualmente projetadas com códigos para engenheiros de dados. É poderoso para processar dados grandes desde que o gasoduto usa clusters de faíscas escalonados.

* Conjunto de dados de treino dividido e conjunto de dados de teste.
    
    O conjunto de dados de formação será utilizado para o modelo de formação, o conjunto de dados de teste será utilizado para avaliar modelos em projetos de aprendizagem automática. Mapear fluxos de dados de atividade dividida condicional dividiria os dados de treino e os dados de teste. 

* Remova dados não qualificados.

    Pode querer remover dados não qualificados, tais como: ficheiro parquet com linha zero. Neste tutorial, vamos usar a atividade agregada para obter o número de filas, a contagem de linhas será uma condição para remover dados não qualificados. 


## <a name="preparation"></a>Preparação
Utilize a seguinte tabela da Base de Dados Azure SQL. 
```
CREATE TABLE [dbo].[MyProducts](
    [ID] [int] NULL,
    [Col1] [char](124) NULL,
    [Col2] [char](124) NULL,
    [Col3] datetime NULL,
    [Col4] int NULL

) 

```

## <a name="convert-data-format-to-parquet"></a>Converter formato de dados para parquet

O fluxo de dados converterá uma tabela da Base de Dados Azure SQL para o formato de ficheiro parquet. 

**Conjunto de dados de origem**: Tabela de transações da Base de Dados Azure SQL

**Conjunto de dados do lavatório**: Armazenamento de bolhas com formato Parquet


## <a name="remove-unqualified-data-based-on-row-count"></a>Remover dados não qualificados com base na contagem de linha

Vamos remover a contagem de linha menos de 2. 

1. Utilize a atividade agregada para obter o número de filas: **Grupo baseado** em Col2 e **Agregados** com contagem(1) para contagem de linha. 

    ![configurar a Atividade Agregada para obter o número de filas de contagem](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. Utilize a atividade do Sink, escolha **o tipo de Pia** como Cache no **separador Sink** e, em seguida, escolha a coluna desejada da lista de retirada de **colunas chave** no separador **Definições.** 

    ![configurar Atividade cacheSink para obter número de filas em pia em cache](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. Utilize a atividade da coluna derivada para adicionar a coluna de contagem de linhas no fluxo de origem. No **separador de definições da coluna Derivada,** utilize a expressão CacheSink#lookup recebendo a contagem de linha da SinkCache.
    ![configurar Atividade da Coluna Derivada para adicionar número de filas na fonte 1](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. Utilize atividades de divisão condicional para remover dados não qualificados. Neste exemplo, a contagem de linhas com base na coluna Col2, e a condição é remover a contagem de linha inferior a 2, de modo que duas linhas (ID=2 e ID=7) serão removidas. Guardaria dados não qualificados para um armazenamento de bolhas para gestão de dados. 

    ![configurar atividade de divisão condicional para obter dados que sejam maiores ou iguais a 2](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    *    Crie uma nova fonte para obter o número de filas que serão usadas na fonte original em etapas posteriores. 
>    *    Use o CacheSink do ponto de vista de desempenho. 

## <a name="split-training-data-and-test-data"></a>Dados de formação divididos e dados de teste 

1. Queremos dividir dados de treino e dados de teste para cada partição. Neste exemplo, pelo mesmo valor do Col2, obtenha as 2 linhas mais seguidas como dados de teste e as restantes linhas como dados de treino. 

    Utilize a atividade da janela para adicionar um número de linha de coluna para cada divisória. Em **Over** tab escolha coluna para partição (neste tutorial, será partição para Col2), dar ordem no **separador Ordenar** (neste tutorial, basear-se-á no ID à ordem), e no separador **colunas Window** para adicionar uma coluna como número de linha para cada partição. 
    ![configurar a atividade da janela para adicionar uma nova coluna sendo número de linha](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. Utilize atividades de divisão condicional para dividir cada partição top 2 linhas para testar conjunto de dados, e as linhas de descanso para o conjunto de dados de treino. No separador **definições de divisão condicional,** utilize a expressão menosOrEqual (RowNum,2) como condição. 

    ![configurar a atividade de divisão condicional para dividir o conjunto de dados atuais para o conjunto de dados de formação e o conjunto de dados de teste](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-training-dataset-and-test-dataset-with-parquet-format"></a>Conjunto de dados de formação de partição e conjunto de dados de teste com formato parquet

1. Utilize a atividade do sink, no **separador Otimize,** utilizando **o valor único por partição** para definir uma coluna como uma chave de coluna para a partição. 
    ![configurar a atividade do lavatório para definir a partição do conjunto de dados de formação](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

    Vamos olhar para trás toda a lógica do oleoduto.
    ![A lógica de todo o Pipeline](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)


## <a name="next-steps"></a>Passos seguintes

* Construa o resto da sua lógica de fluxo de dados utilizando [transformações](concepts-data-flow-overview.md)de fluxos de dados de mapeamento.
