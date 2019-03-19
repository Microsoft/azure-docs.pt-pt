---
title: Identificar cenários e planear o processo de análise - processo de ciência de dados de equipa | Azure Machine Learning
description: Identificar cenários e planear para processamento de dados de análise avançada por uma série de perguntas importantes a considerar.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 0eaa5f24be85eab96f11e0c3915a3f60d79a0551
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57864750"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Como identificar cenários e planear o processamento de dados de análises avançadas

Quais recursos são necessários para que possa criar um ambiente que pode executar processamento de análises avançado num conjunto de dados? Este artigo sugere uma série de perguntas a serem feitas que podem ajudar a identificar, tarefas e dos recursos relevantes, seu cenário.

Para saber mais sobre a ordem dos passos de alto nível para Análise Preditiva, veja [o que é o Team Data Science Process (TDSP)](overview.md). Cada passo exige recursos específicos para as tarefas relevantes para o seu cenário específico.

Responder a perguntas principais nas seguintes áreas para identificar o seu cenário:

* logística de dados
* características dos dados
* qualidade de conjunto de dados
* ferramentas e linguagens preferenciais

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Perguntas de logística: e movimentação de localizações de dados

As perguntas de logística abrangem os seguintes itens:

* localização de origem de dados
* destino de destino no Azure
* requisitos para mover os dados, incluindo a agenda, quantidade e recursos envolvidos

Poderá ter de mover os dados de várias vezes durante o processo de análise. Um cenário comum é mover dados locais em alguma espécie de armazenamento no Azure e, em seguida, no Machine Learning Studio.

### <a name="what-is-your-data-source"></a>O que é a sua origem de dados?

São os seus dados local ou na cloud? Locais possíveis incluem:

* um endereço HTTP publicamente disponível
* um local ou a localização do ficheiro de rede
* uma base de dados do SQL Server
* um contentor de armazenamento do Azure

### <a name="what-is-the-azure-destination"></a>O que é o destino do Azure?

Em que seus dados têm de ser para processamento ou de modelagem? 

* Armazenamento de Blobs do Azure
* Bases de dados do SQL Azure
* SQL Server numa VM do Azure
* O HDInsight (Hadoop no Azure) ou tabelas do Hive
* Azure Machine Learning
* Montáveis discos rígidos virtuais do Azure

### <a name="how-are-you-going-to-move-the-data"></a>Como fará para mover os dados?

Para procedimentos e recursos para ingerir ou carregar dados para uma variedade de ambientes de processamento e de armazenamento diferentes, consulte:

* [Carregar dados para ambientes de armazenamento para análise](ingest-data.md)
* [Importar os dados de treinamento para o Azure Machine Learning Studio a partir de várias origens de dados](../studio/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>Os dados precisam ser movidos com base numa agenda regular ou modificados durante a migração?

Considere utilizar o Azure Data Factory (ADF) quando dados têm de ser migrados continuamente. ADF pode ser útil para:

* num cenário híbrido que envolve tanto no local e recursos na cloud
* um cenário em que os dados são transacionados, modificados ou alterados pela lógica de negócios no decorrer a ser migrados

Para obter mais informações, consulte [mover dados de um servidor SQL no local para o SQL Azure com o Azure Data Factory](move-sql-azure-adf.md).

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>Quanto dos dados está a ser movidos para o Azure?

Extremamente grandes conjuntos de dados podem exceder a capacidade de armazenamento de determinados ambientes. Por exemplo, consulte a discussão de limites de tamanho para o Machine Learning Studio, na secção seguinte. Nesses casos, poderá utilizar um exemplo dos dados durante a análise. Para obter detalhes de como dimensionar um conjunto de dados em vários ambientes do Azure, consulte [amostra dos dados no Team Data Science Process](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Perguntas de características de dados: tipo, o formato e o tamanho

Essas perguntas são essenciais para planejar seu armazenamento e processamento de ambientes. Elas ajudam a escolher o cenário apropriado para o seu tipo de dados e compreender as restrições.

### <a name="what-are-the-data-types"></a>Quais são os tipos de dados?

* Numérica
* Categórico
* Cadeias
* Binário

### <a name="how-is-your-data-formatted"></a>Como são formatados os seus dados?

* Separados por vírgulas (CSV) ou separados por tabulações (TSV) ficheiros simples
* Comprimidos ou descomprimidos
* Blobs do Azure
* Tabelas do Hive do Hadoop
* Tabelas do SQL Server

### <a name="how-large-is-your-data"></a>Como grandes são os seus dados?

* Pequenos: Menos de 2 GB
* Médio: Maior que 2 GB e inferior a 10 GB
* Grande: Superior a 10 GB

Considere o ambiente do Azure Machine Learning Studio, por exemplo:

* Para obter uma lista dos formatos de dados e tipos suportados pelo Azure Machine Learning Studio, consulte [formatos de dados e tipos de dados suportados](../studio/import-data.md#supported-data-formats-and-data-types) secção.
* Para obter informações sobre as limitações de outros serviços do Azure usados no processo de análise, consulte [subscrição do Azure e limites do serviço, Quotas e restrições](../../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Perguntas de qualidade de dados: exploração e processamento prévio de

### <a name="what-do-you-know-about-your-data"></a>O que sabe sobre os seus dados?

Compreenda as características básicas sobre os seus dados:

* O que padrões ou tendências ele exibe
* Quais valores atípicos tem
* Quantos valores estão em falta

Este passo é importante para ajudá-lo a:

* Determinar o processamento prévio de quanto é necessário
* Formular hipóteses que sugerem os recursos mais apropriados ou o tipo de análise
* Formular planos para a recolha de dados adicionais

Técnicas úteis para inspeção de dados incluem estatísticas descritivo cálculo e visualização representa. Para obter detalhes de como explorar um conjunto de dados em vários ambientes do Azure, consulte [explorar dados no Team Data Science Process](explore-data.md).

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>Os dados requerem o pré-processamento ou limpeza?

Poderá ter de pré-processar e limpar os dados antes de poder utilizar o conjunto de dados com eficiência para machine learning. Dados não processados, muitas vezes, são inúteis e pouco fiáveis. Ele poderá estar em falta valores. Usar esses dados para a Modelagem pode produzir resultados enganosos. Para obter uma descrição, consulte [tarefas para preparar dados para avançada do machine learning](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Perguntas de ferramentas e linguagens

Existem muitas opções para ferramentas, ambientes de desenvolvimento e linguagens. Lembre-se de suas necessidades e preferências.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>Quais idiomas preferir usar para análise?

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>Quais ferramentas deve usar para análise de dados?

* [Microsoft Azure Powershell](/powershell/azure/overview) -uma linguagem de script utilizada para administrar os recursos do Azure numa linguagem de script
* [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
* [O Revolution Analytics](https://www.microsoft.com/sql-server/machinelearningserver)
* [RStudio](http://www.rstudio.com)
* [Ferramentas do Python para Visual Studio](https://aka.ms/ptvsdocs)
* [Anaconda](https://www.continuum.io/why-anaconda)
* [Blocos de notas do Jupyter](https://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Identificar o seu cenário de análise avançada

Depois de ter a respondeu às questões na secção anterior, está pronto para determinar qual o cenário de melhor se adequa a seu caso. Os cenários de exemplo descritos na [cenários de análises avançadas no Azure Machine Learning](plan-sample-scenarios.md).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [O que é o Team Data Science Process (TDSP)?](overview.md)