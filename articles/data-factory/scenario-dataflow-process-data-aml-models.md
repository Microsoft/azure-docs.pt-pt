---
title: Utilize fluxos de dados para processar dados de modelos automatizados de machine learning (AutoML)
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
ms.openlocfilehash: 45cd44cc0678b7f3a006a88bf66be2bca091af76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104595384"
---
# <a name="process-data-from-automated-machine-learning-models-by-using-data-flows"></a>Processar dados de modelos automatizados de aprendizagem automática de máquinas utilizando fluxos de dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O machine learning automatizado (AutoML) é adotado por projetos de machine learning para treinar, sintonizar e obter os melhores modelos automaticamente usando métricas-alvo que especifica para classificação, regressão e previsão de séries de tempo.

Um dos desafios para o AutoML é que os dados brutos de um armazém de dados ou de uma base de dados transacionais seriam um enorme conjunto de dados, possivelmente 10 GB. Um grande conjunto de dados requer mais tempo para formar modelos, por isso recomendamos que otimize o processamento de dados antes de treinar os modelos Azure Machine Learning. Este tutorial irá passar por como utilizar a Azure Data Factory para dividir um conjunto de dados em ficheiros AutoML para um conjunto de dados de Machine Learning.

O projeto AutoML inclui os seguintes três cenários de processamento de dados:

* Partição de grandes dados para ficheiros AutoML antes de treinar modelos.

     O [quadro de dados do Pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) é comumente usado para processar dados antes de treinar modelos. O quadro de dados do Pandas funciona bem para tamanhos de dados inferiores a 1 GB, mas se os dados forem maiores que 1 GB, um quadro de dados do Pandas abranda para processar dados. Às vezes pode até receber uma mensagem de erro fora de memória. Recomendamos a utilização de um formato [de ficheiro Parquet](https://parquet.apache.org/) para machine learning porque é um formato colunar binário.
    
     Data Factory mapeando fluxos de dados são transformações de dados visualmente projetadas que libertam engenheiros de dados de escrever código. Mapear fluxos de dados é uma forma poderosa de processar dados grandes porque o oleoduto usa clusters de faíscas escalonados.

* Divida o conjunto de dados de treino e o conjunto de dados do teste.
    
    O conjunto de dados de formação será utilizado para um modelo de treino. O conjunto de dados de teste será usado para avaliar modelos num projeto de aprendizagem automática. A atividade de divisão condicional para mapeamento de fluxos de dados dividiria os dados de treino e os dados de teste.

* Remova dados não qualificados.

    É possível que seja possível remover dados não qualificados, como um ficheiro Parquet com zero linhas. Neste tutorial, vamos usar a atividade agregada para obter uma contagem do número de linhas. A contagem de linhas será uma condição para remover dados não qualificados.

## <a name="preparation"></a>Preparação

Utilize a seguinte tabela de base de dados Azure SQL.

```
CREATE TABLE [dbo].[MyProducts](
    [ID] [int] NULL,
    [Col1] [char](124) NULL,
    [Col2] [char](124) NULL,
    [Col3] datetime NULL,
    [Col4] int NULL

) 

```

## <a name="convert-data-format-to-parquet"></a>Converter formato de dados para Parquet

O seguinte fluxo de dados converterá uma tabela sql database para um formato de ficheiro Parquet:

- **Conjunto de dados de origem**: Tabela de transações da Base de Dados SQL.
- **Conjunto de dados da pia**: Armazenamento blob com formato Parquet.

## <a name="remove-unqualified-data-based-on-row-count"></a>Remover dados não qualificados com base na contagem de linha

Suponhamos que precisamos remover uma contagem de linhas que é menos de duas.

1. Utilize a atividade agregada para obter uma contagem do número de linhas. Utilizar **Agrupados por** base em Col2 e **Agregados** com `count(1)` para a contagem de linha.

    ![Screenshot que mostra configurar a atividade agregada para obter uma contagem do número de linhas.](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. Utilizando a atividade da Pia, selecione o **tipo de pia** como **Cache** no **separador Pia.** Em seguida, selecione a coluna desejada da lista de retirada **das colunas** chave no **separador Definições.**

    ![Screenshot que mostra configurar a atividade CacheSink para obter uma contagem do número de linhas em um lavatório em cache.](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. Utilize a atividade da coluna Derivada para adicionar uma coluna de contagem de linhas no fluxo de origem. No **separador de definições da coluna Derivada,** utilize a `CacheSink#lookup` expressão para obter uma contagem de linha do CacheSink.

    ![Screenshot que mostra configurar a atividade da coluna Derivada para adicionar uma contagem do número de linhas na fonte1.](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. Utilize a atividade de divisão condicional para remover dados não qualificados. Neste exemplo, a contagem de linhas baseia-se na coluna Col2. A condição é remover uma contagem de linha inferior a duas, por isso duas linhas (ID=2 e ID=7) serão removidas. Pouparia dados não qualificados para o armazenamento de bolhas para a gestão de dados.

    ![Screenshot que mostra configurar a atividade de divisão condicional para obter dados que são maiores ou iguais a dois.](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    * Crie uma nova fonte para obter uma contagem do número de linhas que serão usadas na fonte original em etapas posteriores.
>    * Utilize o CacheSink do ponto de vista do desempenho.

## <a name="split-training-data-and-test-data"></a>Dados de formação divididos e dados de teste

Queremos dividir os dados de treino e os dados de teste para cada partição. Neste exemplo, pelo mesmo valor do Col2, obtenha as duas primeiras linhas como dados de teste e o resto das linhas como dados de treino.

1. Utilize a atividade da janela para adicionar um número de linha de coluna para cada divisória. No separador **Over,** selecione uma coluna para a partilha. Neste tutorial, vamos dividir o Col2. Dê uma encomenda no **separador Ordenar,** que neste tutorial será baseado em ID. Encomende no separador **colunas janela** para adicionar uma coluna como número de linha para cada partição.

    ![Screenshot que mostra configurar a atividade da Janela para adicionar uma nova coluna sendo número de linha.](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. Utilize a atividade de divisão condicional para dividir as duas primeiras linhas de cada partição no conjunto de dados de teste e o resto das linhas no conjunto de dados de treino. No separador **definições de divisão condicional,** utilize a expressão `lesserOrEqual(RowNum,2)` como condição.

    ![Screenshot que mostra configurar a atividade de divisão condicional para dividir o conjunto de dados atual no conjunto de dados de treino e no conjunto de dados de teste.](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-the-training-and-test-datasets-with-parquet-format"></a>Partição dos conjuntos de dados de treino e teste com formato Parquet

Utilizando a atividade do Lavatório, no **separador Otimize,** utilize o **valor único por partição** para definir uma coluna como chave de coluna para a partição.

![Screenshot que mostra configurar a atividade da Pia para definir a partição do conjunto de dados de treino.](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

Vamos olhar para trás para toda a lógica do oleoduto.

![Screenshot que mostra a lógica de todo o oleoduto.](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)

## <a name="next-steps"></a>Passos seguintes

Construa o resto da sua lógica de fluxo de dados utilizando transformações de fluxo de [dados](concepts-data-flow-overview.md)de mapeamento.
