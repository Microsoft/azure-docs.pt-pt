---
title: Otimizar o processamento de dados
titleSuffix: Azure Machine Learning
description: Aprenda as melhores práticas para otimizar as velocidades de processamento de dados e quais as integrações que o Azure Machine Learning suporta para o processamento de dados em escala.
services: machine-learning
ms.service: machine-learning
ms.author: sgilley
author: sdgilley
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/26/2020
ms.custom: data4ml
ms.openlocfilehash: 5ab7bac635a0b670087800212727b0d2e2b96934
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103472203"
---
# <a name="optimize-data-processing-with-azure-machine-learning"></a>Otimizar o processamento de dados com a Azure Machine Learning

Neste artigo, aprende-se sobre as melhores práticas para o ajudar a otimizar as velocidades de processamento de dados local e em escala.

O Azure Machine Learning está integrado com pacotes de código aberto e quadros para o processamento de dados. Ao utilizar estas integrações e aplicar as recomendações de boas práticas neste artigo, pode melhorar as velocidades de processamento de dados tanto localmente como em escala.

## <a name="parquet-and-csv-file-formats"></a>Formatos de ficheiros Parquet e CSV

Os ficheiros com vírgula (csv) são formatos comuns de ficheiros para o processamento de dados. No entanto, os formatos de ficheiros parquet são recomendados para tarefas de machine learning.

[Os ficheiros parquet](https://parquet.apache.org/) armazenam dados num formato colunar binário. Este formato é útil se for necessário dividir os dados em vários ficheiros. Além disso, este formato permite-lhe direcionar os campos relevantes para as suas experiências de aprendizagem automática. Em vez de ter de ler num ficheiro de dados de 20 GB, pode diminuir essa carga de dados, selecionando as colunas necessárias para treinar o seu modelo ML. Os ficheiros parquet também podem ser comprimidos para minimizar o poder de processamento e ocupar menos espaço.

Os ficheiros CSV são geralmente usados para importar e exportar dados, uma vez que são fáceis de editar e ler no Excel. Os dados em CSVs são armazenados como cordas num formato baseado em linha, e os ficheiros podem ser comprimidos para diminuir as cargas de transferência de dados. Os CSVs não comprimidos podem expandir-se por um fator de cerca de 2-10 e os CSVs comprimidos podem aumentar ainda mais. De modo que 5-GB CSV na memória expande-se para bem acima dos 8 GB de RAM que você tem na sua máquina. Este comportamento de compressão pode aumentar a latência da transferência de dados, o que não é ideal se tiver grandes quantidades de dados para processar. 

## <a name="pandas-dataframe"></a>Dataframe de pandas

[Os dataframes pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) são comumente usados para manipulação e análise de dados. `Pandas` funciona bem para tamanhos de dados inferiores a 1 GB, mas os tempos de processamento dos `pandas` dataframes abrandam quando os tamanhos dos ficheiros atingem cerca de 1 GB. Este abrandamento deve-se ao facto de o tamanho dos seus dados no armazenamento não ser o mesmo que o tamanho dos dados num dataframe. Por exemplo, os dados em ficheiros CSV podem expandir-se até 10 vezes num dataframe, para que um ficheiro CSV de 1 GB possa tornar-se 10 GB num dataframe.

`Pandas` é apenas roscado, o que significa que as operações são feitas uma de cada vez em um único CPU. Você pode facilmente paralelizar cargas de trabalho a vários CPUs virtuais em um único exemplo de cálculo Azure Machine Learning com pacotes como [Modin](https://modin.readthedocs.io/en/latest/) que envolvem `Pandas` usando um backend distribuído.

Para paralelizar as suas tarefas com `Modin` e [Dask,](https://dask.org)basta alterar esta linha de código `import pandas as pd` para `import modin.pandas as pd` .

## <a name="dataframe-out-of-memory-error"></a>Dataframe: erro de memória 

Normalmente ocorre um erro *fora da memória* quando o seu dataframe se expande acima da RAM disponível na sua máquina. Este conceito aplica-se também a um quadro distribuído como `Modin` ou `Dask` .  Ou seja, a sua operação tenta carregar o dataframe na memória em cada nó no seu cluster, mas não há RAM suficiente disponível para o fazer.

Uma solução é aumentar a sua RAM para encaixar no dataframe na memória. Recomendamos que o seu tamanho de cálculo e poder de processamento contenham duas vezes o tamanho da RAM. Assim, se o seu dataframe for de 10 GB, use um alvo de computação com pelo menos 20 GB de RAM para garantir que o dataframe pode caber confortavelmente na memória e ser processado. 

Para vários CPUs virtuais, vCPU, tenha em mente que deseja que uma divisória se encaixe confortavelmente na RAM que cada vCPU pode ter na máquina. Ou seja, se tiver 16 GB de RAM 4 vCPUs, você quer cerca de 2-GB dataframes por cada vCPU.

### <a name="local-vs-remote"></a>Local vs remoto

Pode notar que certos comandos de dataframe de pandas funcionam mais rapidamente quando trabalha no seu PC local contra um VM remoto que a provisionou com a Azure Machine Learning. O seu PC local normalmente tem um ficheiro de página ativado, o que lhe permite carregar mais do que o que se encaixa na memória física, ou seja, o seu disco rígido está a ser usado como uma extensão da sua RAM. Atualmente, os VMs de Aprendizagem de Máquinas Azure funcionam sem um ficheiro de página, pelo que só podem carregar tantos dados quanto RAM físico disponível. 

Para trabalhos computação pesadas, recomendamos que escolha um VM maior para melhorar as velocidades de processamento.

Saiba mais sobre as [séries e tamanhos VM disponíveis](concept-compute-target.md#supported-vm-series-and-sizes) para Azure Machine Learning. 

Para especificações de RAM, consulte as páginas correspondentes da série VM, tais como, [série Dv2-Dsv2](../virtual-machines/dv2-dsv2-series-memory.md) ou [série NC](../virtual-machines/nc-series.md).

### <a name="minimize-cpu-workloads"></a>Minimizar cargas de trabalho do CPU

Se não conseguir adicionar mais RAM à sua máquina, pode aplicar as seguintes técnicas para ajudar a minimizar as cargas de trabalho do CPU e otimizar os tempos de processamento. Estas recomendações dizem respeito a sistemas únicos e distribuídos.

Técnica | Description
----|----
Compressão | Utilize uma representação diferente para os seus dados, de uma forma que use menos memória e não tenha um impacto significativo nos resultados do seu cálculo.<br><br>*Exemplo:* Em vez de armazenar entradas como uma corda com cerca de 10 bytes ou mais por entrada, guarde-as como um booleano, Verdadeiro ou Falso, que pode armazenar em 1 byte.
Chunking | Carregue os dados na memória em subconjuntos (pedaços), processe os dados um subconjunto de cada vez, ou vários subconjuntos em paralelo. Este método funciona melhor se precisar de processar todos os dados, mas não precisa de carregar todos os dados na memória de uma só vez. <br><br>*Exemplo:* Em vez de processar um ano inteiro de dados de uma vez, carregue e processe os dados um mês de cada vez.
Indexação | Aplique e use um índice, um resumo que lhe diz onde encontrar os dados de que se preocupa. A indexação é útil quando só é necessário utilizar um subconjunto dos dados, em vez do conjunto completo<br><br>*Exemplo:* Se tiver um ano inteiro de dados de vendas classificados por mês, um índice ajuda-o a procurar rapidamente o mês desejado que deseja processar.

## <a name="scale-data-processing"></a>Processamento de dados em escala

Se as recomendações anteriores não forem suficientes, e não conseguires uma máquina virtual que se adapte aos teus dados, podes, 

* Utilize uma estrutura como `Spark` ou para processar os `Dask` dados 'fora da memória'. Nesta opção, o dataframe é carregado na partição RAM por partição e processado, com o resultado final a ser recolhido no final.  

* Dimensione para um cluster usando uma estrutura distribuída. Nesta opção, as cargas de processamento de dados são divididas e processadas em vários CPUs que funcionam em paralelo, com o resultado final recolhido no final.

### <a name="recommended-distributed-frameworks"></a>Quadros distribuídos recomendados

O quadro seguinte recomenda quadros distribuídos que sejam integrados com Azure Machine Learning com base na sua preferência de código ou tamanho de dados.

Experiência ou tamanho dos dados | Recomendação
------|------
Se está familiarizado com `Pandas`| `Modin` ou `Dask` dataframe
Se preferir `Spark` | `PySpark`
Para dados inferiores a 1 GB | `Pandas` localmente **ou** um remoto Azure Machine Learning caso
Para dados maiores que 10 GB| Mude-se para um cluster usando `Ray` `Dask` , ou `Spark`

## <a name="next-steps"></a>Passos seguintes

* [Opções de ingestão de dados com Azure Machine Learning](concept-data-ingestion.md).
* [Criar e registar conjuntos de dados](how-to-create-register-datasets.md).
