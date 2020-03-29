---
title: Identificar cenários e planear o processo de análise - Processo de Ciência de Dados da Equipa / Aprendizagem automática azure
description: Identifique cenários e planos para o processamento avançado de dados de análise, considerando uma série de questões-chave.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76710321"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Como identificar cenários e planear o processamento de dados de análises avançadas

Que recursos são necessários para criar um ambiente que possa realizar um processamento de análise avançada num conjunto de dados? Este artigo sugere uma série de perguntas a fazer que podem ajudar a identificar tarefas e recursos relevantes para o seu cenário.

Para conhecer a ordem dos passos de alto nível para análises preditivas, consulte o Que é o Processo de Ciência de [Dados da Equipa (TDSP)](overview.md). Cada passo requer recursos específicos para as tarefas relevantes para o seu cenário particular.

Responda a questões-chave nas seguintes áreas para identificar o seu cenário:

* logística de dados
* características dos dados
* qualidade de conjunto de dados
* ferramentas e línguas preferidas

## <a name="logistic-questions-data-locations-and-movement"></a>Questões logísticas: localização de dados e movimento

As questões logísticas abrangem os seguintes itens:

* localização da fonte de dados
* destino alvo em Azure
* requisitos para a movimentação dos dados, incluindo o calendário, o montante e os recursos envolvidos

Poderá ter de mover os dados várias vezes durante o processo de análise. Um cenário comum é mover dados locais para alguma forma de armazenamento em Azure e depois para o Estúdio de Aprendizagem Automática.

### <a name="what-is-your-data-source"></a>Qual é a sua fonte de dados?

Os seus dados são locais ou na nuvem? Possíveis localizações incluem:

* um endereço HTTP disponível publicamente
* uma localização de arquivo local ou de rede
* uma base de dados SQL Server
* um recipiente de armazenamento Azure

### <a name="what-is-the-azure-destination"></a>Qual é o destino Azure?

Onde os seus dados precisam estar para o processamento ou modelação? 

* Armazenamento de Blobs do Azure
* Bases de dados SQL Azure
* SQL Server numa VM do Azure
* HDInsight (Hadoop em Azure) ou mesas de Colmeia
* Azure Machine Learning
* Discos rígidos virtuais do Azure montáveis

### <a name="how-are-you-going-to-move-the-data"></a>Como vai mover os dados?

Para procedimentos e recursos para ingerir ou carregar dados em diversos ambientes de armazenamento e processamento, consulte:

* [Carregar dados para ambientes de armazenamento para análise](ingest-data.md)
* [Importe os seus dados de formação para o Azure Machine Learning Studio (clássico) de várias fontes de dados](../studio/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>Os dados têm de ser movidos regularmente ou modificados durante a migração?

Considere utilizar a Azure Data Factory (ADF) quando os dados precisam de ser continuamente migrados. A ADF pode ser útil para:

* um cenário híbrido que envolve tanto no local como recursos de nuvem
* um cenário em que os dados são transacionados, modificados ou alterados pela lógica empresarial no decurso da migração

Para mais informações, consulte [Mover dados de um servidor SQL no local para O SQL Azure com a Azure Data Factory](move-sql-azure-adf.md).

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>Quanto dos dados serão transferidos para O Azure?

Grandes conjuntos de dados podem exceder a capacidade de armazenamento de certos ambientes. Por exemplo, veja a discussão dos limites de tamanho para o Machine Learning Studio (clássico) na secção seguinte. Nesses casos, pode utilizar uma amostra dos dados durante a análise. Para mais detalhes sobre como recolher um conjunto de dados em vários ambientes Do Azure, consulte [os dados da amostra no Processo](sample-data.md)de Ciência de Dados da Equipa .

## <a name="data-characteristics-questions-type-format-and-size"></a>Questões de características de dados: tipo, formato e tamanho

Estas questões são fundamentais para planear os seus ambientes de armazenamento e processamento. Eles vão ajudá-lo a escolher o cenário apropriado para o seu tipo de dados e entender quaisquer restrições.

### <a name="what-are-the-data-types"></a>Quais são os tipos de dados?

* Numérico
* Categórico
* Cadeias
* Binário

### <a name="how-is-your-data-formatted"></a>Como estão formatados os seus dados?

* Ficheiros planos separados com ma (CSV) ou separados por separados por separados por separados (TSV)
* Comprimido ou descomprimido
* Bolhas azure
* Mesas de Colmeia hadoop
* Tabelas de servidorEs SQL

### <a name="how-large-is-your-data"></a>Qual é o tamanho dos seus dados?

* Pequeno: Menos de 2 GB
* Médio: Superior a 2 GB e inferior a 10 GB
* Grande: Maior que 10 GB

Veja o ambiente azure machine learning studio (clássico) por exemplo:

* Para obter uma lista dos formatos e tipos de dados suportados pelo Azure Machine Learning Studio, consulte os [formatos](../studio/import-data.md#supported-data-formats-and-data-types) de dados e os tipos de dados suportados na secção.
* Para obter informações sobre as limitações de outros serviços Azure utilizados no processo de análise, consulte [limites de subscrição e serviço do Azure, quotas e restrições.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Questões de qualidade de dados: exploração e pré-processamento

### <a name="what-do-you-know-about-your-data"></a>O que sabe sobre os seus dados?

Compreenda as características básicas dos seus dados:

* Que padrões ou tendências exibe
* Que outliers tem
* Quantos valores faltam

Este passo é importante para ajudá-lo:

* Determine quanto pré-processamento é necessário
* Formular hipóteses que sugiram as características mais adequadas ou tipo de análise
* Formular planos para recolha adicional de dados

Técnicas úteis para a inspeção de dados incluem cálculo de estatísticas descritivas e enredos de visualização. Para mais detalhes sobre como explorar um conjunto de dados em vários ambientes Azure, consulte [Explore dados no Processo](explore-data.md)de Ciência de Dados da Equipa .

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>Os dados requerem pré-processamento ou limpeza?

Pode ser necessário pré-processar e limpar os seus dados antes de poder utilizar o conjunto de dados de forma eficaz para o machine learning. Os dados brutos são muitas vezes barulhentos e pouco fiáveis. Pode faltar valores. A utilização destes dados para modelação pode produzir resultados enganadores. Para obter uma descrição, consulte [tasks para preparar dados para uma aprendizagem automática melhorada](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Perguntas de ferramentas e línguas

Existem muitas opções para línguas, ambientes de desenvolvimento e ferramentas. Esteja atento às suas necessidades e preferências.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>Que línguas prefere usar para análise?

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>Que ferramentas deve utilizar para análise de dados?

* [Microsoft Azure Powershell](/powershell/azure/overview) - uma linguagem de script usada para administrar os seus recursos Azure em uma linguagem script
* [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
* [Análise da Revolução](https://www.microsoft.com/sql-server/machinelearningserver)
* [RStudio](https://www.rstudio.com)
* [Ferramentas do Python para Visual Studio](https://aka.ms/ptvsdocs)
* [Anaconda](https://www.continuum.io/why-anaconda)
* [Blocos de notas do Jupyter](https://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Identifique o seu cenário de análise avançada

Depois de ter respondido às perguntas na secção anterior, está pronto para determinar qual o cenário que melhor se adequa ao seu caso. Os cenários da amostra são delineados em [Cenários para análises avançadas em Azure Machine Learning](plan-sample-scenarios.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [O que é o Team Data Science Process (TDSP)?](overview.md)
