---
title: Identificar cenários e planear o processo de análise - Processo de Ciência de Dados de Equipa Aprendizagem de Máquinas Azure
description: Identificar cenários e planear o processamento avançado de dados de análise, considerando uma série de questões-chave.
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
ms.openlocfilehash: ffe1a4b9b32223cc57c6a6399fdc074a9d51d714
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322403"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Como identificar cenários e planear o processamento de dados de análises avançadas

Que recursos são necessários para criar um ambiente que possa realizar um processamento de análise avançado num conjunto de dados? Este artigo sugere uma série de perguntas a fazer que podem ajudar a identificar tarefas e recursos relevantes do seu cenário.

Para conhecer a ordem dos passos de alto nível para análise preditiva, consulte [O que é o Processo de Ciência de Dados de Equipa (TDSP)](overview.md). Cada passo requer recursos específicos para as tarefas relevantes para o seu cenário particular.

Responda a perguntas-chave nas seguintes áreas para identificar o seu cenário:

* logística de dados
* características dos dados
* qualidade do conjunto de dados
* ferramentas e línguas preferenciais

## <a name="logistic-questions-data-locations-and-movement"></a>Questões logísticas: localizações de dados e movimento

As questões logísticas abrangem os seguintes itens:

* Localização da fonte de dados
* destino alvo em Azure
* requisitos para a movimentação dos dados, incluindo o calendário, montante e recursos envolvidos

Pode ser necessário mover os dados várias vezes durante o processo de análise. Um cenário comum é mover os dados locais para alguma forma de armazenamento no Azure e depois para o Machine Learning Studio.

### <a name="what-is-your-data-source"></a>Qual é a sua fonte de dados?

Os seus dados são locais ou na nuvem? As localizações possíveis incluem:

* um endereço HTTP disponível ao público
* uma localização de arquivo local ou de rede
* uma base de dados SQL Server
* um recipiente de armazenamento Azure

### <a name="what-is-the-azure-destination"></a>Qual é o destino Azure?

Onde é que os seus dados precisam de ser para processamento ou modelação? 

* Armazenamento de Blobs do Azure
* Bases de dados SQL Azure
* SQL Server numa VM do Azure
* HDInsight (Hadoop on Azure) ou mesas de Colmeia
* Azure Machine Learning
* Discos rígidos virtuais mountable Azure

### <a name="how-are-you-going-to-move-the-data"></a>Como vai mover os dados?

Para procedimentos e recursos para ingerir ou carregar dados em diferentes ambientes de armazenamento e processamento, consulte:

* [Carregar dados para ambientes de armazenamento para análise](ingest-data.md)
* [Importe os seus dados de formação em Azure Machine Learning Studio (clássico) de várias fontes de dados](../classic/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>Os dados precisam de ser movidos num horário regular ou alterados durante a migração?

Considere usar a Azure Data Factory (ADF) quando os dados precisam de ser continuamente migrados. A ADF pode ser útil para:

* um cenário híbrido que envolve tanto no local como recursos em nuvem
* um cenário em que os dados são transacionados, modificados ou alterados pela lógica empresarial no decurso da migração

Para obter mais informações, consulte [os dados de mudança de uma base de dados do SQL Server para o SQL Azure com a Azure Data Factory](move-sql-azure-adf.md).

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>Quanto dos dados devem ser transferidos para Azure?

Grandes conjuntos de dados podem exceder a capacidade de armazenamento de determinados ambientes. Por exemplo, consulte a discussão dos limites de tamanho para Machine Learning Studio (clássico) na secção seguinte. Nesses casos, poderá utilizar uma amostra dos dados durante a análise. Para obter detalhes sobre como reduzir a amostra de um conjunto de dados em vários ambientes Azure, consulte [os dados da amostra no Processo de Ciência de Dados da Equipa](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Questões de características de dados: tipo, formato e tamanho

Estas questões são fundamentais para planear os seus ambientes de armazenamento e processamento. Eles vão ajudá-lo a escolher o cenário apropriado para o seu tipo de dados e entender quaisquer restrições.

### <a name="what-are-the-data-types"></a>Quais são os tipos de dados?

* Numérico
* Categórico
* Cadeias
* Binário

### <a name="how-is-your-data-formatted"></a>Como é que os seus dados são formatados?

* Ficheiros planos separados por vírgula (CSV) ou separados por separados por separados por separados por separados por separados por separados por separados por separados por separados por aba (TSV)
* Comprimido ou descomprimido
* Bolhas de Azure
* Mesas de Colmeia Hadoop
* Tabelas de servidor SQL

### <a name="how-large-is-your-data"></a>Qual é o tamanho dos seus dados?

* Pequeno: Menos de 2 GB
* Médio: Superior a 2 GB e menos de 10 GB
* Grande: Maior que 10 GB

Veja-se o ambiente do Azure Machine Learning Studio (clássico), por exemplo:

* Para obter uma lista dos formatos e tipos de dados suportados pelo Azure Machine Learning Studio, consulte a secção [de formatos de dados e tipos de dados suportados.](../classic/import-data.md#supported-data-formats-and-data-types)
* Para obter informações sobre as limitações de outros serviços Azure utilizados no processo de análise, consulte [limites de subscrição e serviço, quotas e restrições de assinatura azure.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Questões de qualidade dos dados: exploração e pré-processamento

### <a name="what-do-you-know-about-your-data"></a>O que sabe sobre os seus dados?

Compreenda as características básicas dos seus dados:

* Que padrões ou tendências exibe
* Que forasteiros tem
* Quantos valores faltam

Este passo é importante para ajudá-lo:

* Determinar a quantidade de pré-processamento necessário
* Formular hipóteses que sugerem as características ou tipo de análise mais adequados
* Formular planos para recolha adicional de dados

As técnicas úteis para a inspeção de dados incluem cálculo de estatísticas descritivas e parcelas de visualização. Para obter detalhes sobre como explorar um conjunto de dados em vários ambientes Azure, consulte [os dados do Processo de Ciência de Dados da Equipa.](explore-data.md)

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>Os dados requerem pré-processamento ou limpeza?

Pode ser necessário pré-processar e limpar os seus dados antes de poder utilizar o conjunto de dados de forma eficaz para a aprendizagem automática. Os dados brutos são muitas vezes barulhentos e pouco fiáveis. Podem estar a perder valores. A utilização destes dados para modelação pode produzir resultados enganadores. Para uma descrição, consulte [Tasks para preparar dados para uma aprendizagem automática melhorada.](prepare-data.md)

## <a name="tools-and-languages-questions"></a>Perguntas de ferramentas e línguas

Existem muitas opções para línguas, ambientes de desenvolvimento e ferramentas. Esteja atento às suas necessidades e preferências.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>Que línguas prefere usar para análise?

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>Que ferramentas deve utilizar para análise de dados?

* [Microsoft Azure PowerShell](/powershell/azure/) - uma linguagem de script usada para administrar os seus recursos Azure em um idioma script
* [Azure Machine Learning Studio](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
* [Análise da Revolução](https://www.microsoft.com/sql-server/machinelearningserver)
* [Rio RStudio](https://www.rstudio.com)
* [Ferramentas do Python para Visual Studio](/visualstudio/python/)
* [Anaconda](https://www.anaconda.com/)
* [Cadernos jupyter](https://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Identifique o seu cenário de análise avançada

Depois de ter respondido às perguntas na secção anterior, está pronto para determinar qual o cenário que melhor se adequa ao seu caso. Os cenários da amostra são delineados em [Cenários para análises avançadas em Azure Machine Learning](plan-sample-scenarios.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [O que é o Team Data Science Process (TDSP)?](overview.md)