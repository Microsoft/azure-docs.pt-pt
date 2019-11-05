---
title: Identifique cenários e planeje o processo de análise-processo de ciência de dados da equipe | Azure Machine Learning
description: Identifique cenários e planeje o processamento de dados de análise avançada considerando uma série de perguntas importantes.
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
ms.openlocfilehash: 46d0e8aa1fa934f778e950f0187a853ba46772d1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495039"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Como identificar cenários e planear o processamento de dados de análises avançadas

Quais recursos são necessários para que você crie um ambiente que possa executar o processamento de análise avançada em um conjunto de um DataSet? Este artigo sugere uma série de perguntas a serem feitas que podem ajudar a identificar tarefas e recursos relevantes ao seu cenário.

Para saber mais sobre a ordem das etapas de alto nível para análise preditiva, confira o [que é o TDSP (processo de ciência de dados de equipe)](overview.md). Cada etapa requer recursos específicos para as tarefas relevantes para seu cenário específico.

Responda às principais perguntas nas seguintes áreas para identificar seu cenário:

* logística de dados
* características de dados
* qualidade do conjunto de um
* ferramentas e idiomas preferenciais

## <a name="logistic-questions-data-locations-and-movement"></a>Perguntas logísticas: locais e movimentação de dados

As perguntas de logística cobrem os seguintes itens:

* local da fonte de dados
* destino de destino no Azure
* requisitos para mover os dados, incluindo a agenda, a quantidade e os recursos envolvidos

Talvez seja necessário mover os dados várias vezes durante o processo de análise. Um cenário comum é mover dados locais para alguma forma de armazenamento no Azure e, em seguida, para Machine Learning Studio.

### <a name="what-is-your-data-source"></a>Qual é sua fonte de dados?

Seus dados são locais ou na nuvem? Os locais possíveis incluem:

* um endereço HTTP disponível publicamente
* um local de arquivo local ou de rede
* um banco de dados SQL Server
* um contêiner de armazenamento do Azure

### <a name="what-is-the-azure-destination"></a>O que é o destino do Azure?

Onde os dados precisam ser para processamento ou modelagem? 

* Armazenamento de Blobs do Azure
* Bancos de dados SQL Azure
* SQL Server numa VM do Azure
* HDInsight (Hadoop no Azure) ou tabelas do hive
* Azure Machine Learning
* Discos rígidos virtuais montáveis do Azure

### <a name="how-are-you-going-to-move-the-data"></a>Como você vai mover os dados?

Para obter procedimentos e recursos para ingerir ou carregar dados em uma variedade de diferentes ambientes de armazenamento e processamento, consulte:

* [Carregar dados em ambientes de armazenamento para análise](ingest-data.md)
* [Importe seus dados de treinamento para Azure Machine Learning Studio (clássico) de várias fontes de dados](../studio/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>Os dados precisam ser movidos em um agendamento regular ou modificados durante a migração?

Considere o uso de Azure Data Factory (ADF) quando os dados precisarem ser migrados continuamente. O ADF pode ser útil para:

* um cenário híbrido que envolve recursos locais e na nuvem
* um cenário em que os dados são transacionados, modificados ou alterados pela lógica de negócios no decorrer da migração

Para obter mais informações, consulte [mover dados de um SQL Server local para SQL Azure com Azure data Factory](move-sql-azure-adf.md).

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>Quanto dos dados serão movidos para o Azure?

Conjuntos de dado muito grandes podem exceder a capacidade de armazenamento de determinados ambientes. Para obter um exemplo, consulte a discussão sobre limites de tamanho para Machine Learning Studio (clássico) na próxima seção. Nesses casos, você pode usar uma amostra dos dados durante a análise. Para obter detalhes de como fazer uma amostra de um conjunto de dados em vários ambientes do Azure, consulte [Sample data no processo de ciência de dados de equipe](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Perguntas sobre características de dados: tipo, formato e tamanho

Essas perguntas são fundamentais para planejar seus ambientes de armazenamento e processamento. Eles irão ajudá-lo a escolher o cenário apropriado para o tipo de dados e a entender as restrições.

### <a name="what-are-the-data-types"></a>Quais são os tipos de dados?

* Numéricos
* Categórico
* Cadeias
* binário

### <a name="how-is-your-data-formatted"></a>Como os dados são formatados?

* Arquivos simples separados por vírgulas (CSV) ou separados por tabulação (TSV)
* Compactado ou descompactado
* BLOBs do Azure
* Tabelas do hive do Hadoop
* SQL Server tabelas

### <a name="how-large-is-your-data"></a>Qual é o tamanho dos seus dados?

* Pequeno: menos de 2 GB
* Médio: maior que 2 GB e menor que 10 GB
* Grande: maior que 10 GB

Considere o ambiente Azure Machine Learning Studio (clássico), por exemplo:

* Para obter uma lista dos formatos de dados e tipos com suporte pelo Azure Machine Learning Studio, consulte a seção [formatos de dados e tipos de dados com suporte](../studio/import-data.md#supported-data-formats-and-data-types) .
* Para obter informações sobre as limitações de outros serviços do Azure usados no processo de análise, consulte [assinatura do Azure e limites de serviço, cotas e restrições](../../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Perguntas de qualidade de dados: exploração e pré-processamento

### <a name="what-do-you-know-about-your-data"></a>O que você sabe sobre seus dados?

Entenda as características básicas sobre seus dados:

* Quais padrões ou tendências ele exibe
* O que as exceções têm
* Quantos valores estão ausentes

Esta etapa é importante para ajudá-lo a:

* Determinar a quantidade de pré-processamento necessária
* Formular hipóteses que sugerem os recursos mais apropriados ou o tipo de análise
* Formular planos para coleta de dados adicionais

Técnicas úteis para a inspeção de dados incluem gráficos descritivos de cálculo e visualização de estatísticas. Para obter detalhes de como explorar um conjunto de dados em vários ambientes do Azure, confira [explorar o processo de ciência de dados de equipe](explore-data.md).

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>Os dados exigem pré-processamento ou limpeza?

Talvez seja necessário pré-processar e limpar os dados antes de usar o conjunto de dados com eficiência para o aprendizado de máquina. Os dados brutos geralmente são ruidosas e não confiáveis. Os valores podem estar ausentes. O uso desses dados para modelagem pode gerar resultados enganosos. Para obter uma descrição, consulte [tarefas para preparar dados para o aprendizado de máquina avançado](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Perguntas sobre ferramentas e idiomas

Há muitas opções para linguagens, ambientes de desenvolvimento e ferramentas. Esteja atento às suas necessidades e preferências.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>Quais idiomas você prefere usar para análise?

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>Quais ferramentas você deve usar para a análise de dados?

* [Microsoft Azure PowerShell](/powershell/azure/overview) -uma linguagem de script usada para administrar os recursos do Azure em uma linguagem de script
* [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
* [Análise de revolução](https://www.microsoft.com/sql-server/machinelearningserver)
* [RStudio](https://www.rstudio.com)
* [Ferramentas do Python para Visual Studio](https://aka.ms/ptvsdocs)
* [Anaconda](https://www.continuum.io/why-anaconda)
* [Blocos de anotações do Jupyter](https://jupyter.org/)
* [Power BI da Microsoft](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Identificar seu cenário de análise avançada

Depois de responder às perguntas na seção anterior, você estará pronto para determinar qual cenário melhor se adapta ao seu caso. Os cenários de exemplo são descritos em [cenários para análise avançada no Azure Machine Learning](plan-sample-scenarios.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [O que é o TDSP (processo de ciência de dados de equipe)?](overview.md)
