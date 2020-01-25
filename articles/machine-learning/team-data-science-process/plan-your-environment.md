---
title: Identifique cenários e planeje o processo de análise-processo de ciência de dados da equipe | Azure Machine Learning
description: Identificar cenários e planear para processamento de dados de análise avançada por uma série de perguntas importantes a considerar.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b0b811a2b7ed432b7fc5015886b28337ca33424e
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710321"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Como identificar cenários e planear o processamento de dados de análises avançadas

Quais recursos são necessários para que você crie um ambiente que possa executar o processamento de análise avançada em um conjunto de um DataSet? Este artigo sugere uma série de perguntas a serem feitas que podem ajudar a identificar tarefas e recursos relevantes ao seu cenário.

Para saber mais sobre a ordem das etapas de alto nível para análise preditiva, confira o [que é o TDSP (processo de ciência de dados de equipe)](overview.md). Cada etapa requer recursos específicos para as tarefas relevantes para seu cenário específico.

Responda às principais perguntas nas seguintes áreas para identificar seu cenário:

* logística de dados
* características de dados
* qualidade do conjunto de um
* ferramentas e idiomas preferenciais

## <a name="logistic-questions-data-locations-and-movement"></a>Perguntas de logística: e movimentação de localizações de dados

As perguntas de logística cobrem os seguintes itens:

* local da fonte de dados
* destino de destino no Azure
* requisitos para mover os dados, incluindo a agenda, a quantidade e os recursos envolvidos

Talvez seja necessário mover os dados várias vezes durante o processo de análise. Um cenário comum é mover dados locais em alguma espécie de armazenamento no Azure e, em seguida, no Machine Learning Studio.

### <a name="what-is-your-data-source"></a>Qual é sua fonte de dados?

Seus dados são locais ou na nuvem? Os locais possíveis incluem:

* um endereço HTTP disponível publicamente
* um local de arquivo local ou de rede
* uma base de dados do Servidor SQL
* um recipiente de armazenamento Azure

### <a name="what-is-the-azure-destination"></a>O que é o destino do Azure?

Onde os dados precisam ser para processamento ou modelagem? 

* Armazenamento de Blobs do Azure
* Bases de dados do SQL Azure
* SQL Server numa VM do Azure
* O HDInsight (Hadoop no Azure) ou tabelas do Hive
* Azure Machine Learning
* Discos rígidos virtuais montáveis do Azure

### <a name="how-are-you-going-to-move-the-data"></a>Como você vai mover os dados?

Para obter procedimentos e recursos para ingerir ou carregar dados em uma variedade de diferentes ambientes de armazenamento e processamento, consulte:

* [Carregar dados para ambientes de armazenamento para análise](ingest-data.md)
* [Importe seus dados de treinamento para Azure Machine Learning Studio (clássico) de várias fontes de dados](../studio/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>Os dados precisam ser movidos em um agendamento regular ou modificados durante a migração?

Considere o uso de Azure Data Factory (ADF) quando os dados precisarem ser migrados continuamente. O ADF pode ser útil para:

* um cenário híbrido que envolve recursos locais e na nuvem
* um cenário em que os dados são transacionados, modificados ou alterados pela lógica de negócios no decorrer da migração

Para mais informações, consulte [Mover dados de um servidor SQL no local para O SQL Azure com a Azure Data Factory](move-sql-azure-adf.md).

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>Quanto dos dados serão movidos para o Azure?

Grandes conjuntos de dados podem exceder a capacidade de armazenamento de certos ambientes. Para obter um exemplo, consulte a discussão sobre limites de tamanho para Machine Learning Studio (clássico) na próxima seção. Nesses casos, você pode usar uma amostra dos dados durante a análise. Para obter detalhes de como dimensionar um conjunto de dados em vários ambientes do Azure, consulte [amostra dos dados no Team Data Science Process](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Perguntas de características de dados: tipo, o formato e o tamanho

Essas perguntas são fundamentais para planejar seus ambientes de armazenamento e processamento. Eles irão ajudá-lo a escolher o cenário apropriado para o tipo de dados e a entender as restrições.

### <a name="what-are-the-data-types"></a>Quais são os tipos de dados?

* Numérica
* Categórico
* Cadeias
* Binary

### <a name="how-is-your-data-formatted"></a>Como os dados são formatados?

* Separados por vírgulas (CSV) ou separados por tabulações (TSV) ficheiros simples
* Comprimidos ou descomprimidos
* Blobs do Azure
* Tabelas do Hive do Hadoop
* Tabelas do SQL Server

### <a name="how-large-is-your-data"></a>Qual é o tamanho dos seus dados?

* Pequenas: menos de 2 GB
* Médio: Maior que 2 GB e inferior a 10 GB
* Grandes: Superior a 10 GB

Considere o ambiente Azure Machine Learning Studio (clássico), por exemplo:

* Para obter uma lista dos formatos de dados e tipos suportados pelo Azure Machine Learning Studio, consulte [formatos de dados e tipos de dados suportados](../studio/import-data.md#supported-data-formats-and-data-types) secção.
* Para obter informações sobre as limitações de outros serviços do Azure usados no processo de análise, consulte [subscrição do Azure e limites do serviço, Quotas e restrições](../../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Perguntas de qualidade de dados: exploração e processamento prévio de

### <a name="what-do-you-know-about-your-data"></a>O que você sabe sobre seus dados?

Entenda as características básicas sobre seus dados:

* Quais padrões ou tendências ele exibe
* O que as exceções têm
* Quantos valores estão ausentes

Esta etapa é importante para ajudá-lo a:

* Determinar a quantidade de pré-processamento necessária
* Formular hipóteses que sugerem os recursos mais apropriados ou o tipo de análise
* Formular planos para coleta de dados adicionais

Técnicas úteis para a inspeção de dados incluem gráficos descritivos de cálculo e visualização de estatísticas. Para obter detalhes de como explorar um conjunto de dados em vários ambientes do Azure, consulte [explorar dados no Team Data Science Process](explore-data.md).

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>Os dados exigem pré-processamento ou limpeza?

Talvez seja necessário pré-processar e limpar os dados antes de usar o conjunto de dados com eficiência para o aprendizado de máquina. Os dados brutos geralmente são ruidosas e não confiáveis. Os valores podem estar ausentes. Usar esses dados para a Modelagem pode produzir resultados enganosos. Para obter uma descrição, consulte [tarefas para preparar dados para avançada do machine learning](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Perguntas de ferramentas e linguagens

Há muitas opções para linguagens, ambientes de desenvolvimento e ferramentas. Esteja atento às suas necessidades e preferências.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>Quais idiomas você prefere usar para análise?

* R.
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>Quais ferramentas você deve usar para a análise de dados?

* [Microsoft Azure PowerShell](/powershell/azure/overview) -uma linguagem de script usada para administrar os recursos do Azure em uma linguagem de script
* [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
* [O Revolution Analytics](https://www.microsoft.com/sql-server/machinelearningserver)
* [RStudio](https://www.rstudio.com)
* [Ferramentas do Python para Visual Studio](https://aka.ms/ptvsdocs)
* [Anaconda](https://www.continuum.io/why-anaconda)
* [Blocos de notas do Jupyter](https://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Identificar o seu cenário de análise avançada

Depois de responder às perguntas na seção anterior, você estará pronto para determinar qual cenário melhor se adapta ao seu caso. Os cenários de exemplo descritos na [cenários de análises avançadas no Azure Machine Learning](plan-sample-scenarios.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [O que é o TDSP (processo de ciência de dados de equipe)?](overview.md)
