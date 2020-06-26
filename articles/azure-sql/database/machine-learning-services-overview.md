---
title: Serviços de Machine Learning com R (pré-visualização)
description: Este artigo descreve os Serviços de Aprendizagem automática de máquinas de base de dados Azure SQL (com R) e explica como funciona.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 0a11e2ba820797bac8ce93517841fd37a8256d2c
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85413064"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database Machine Learning Services com R (pré-visualização)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Machine Learning Services é uma característica da Base de Dados Azure SQL, utilizada para executar scripts R na base de dados. A funcionalidade inclui pacotes Microsoft R para análise preditiva de alto desempenho e machine learning. Os dados relacionais podem ser utilizados em scripts R através de procedimentos armazenados, script T-SQL contendo declarações R ou código R contendo T-SQL.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="what-you-can-do-with-r"></a>O que pode fazer com R

Use o poder da linguagem R para fornecer análises avançadas e aprendizagem automática na base de dados. Esta capacidade traz cálculos e processamento para onde os dados residem, eliminando a necessidade de puxar dados através da rede. Além disso, você pode aproveitar o poder dos pacotes R da empresa para fornecer análises avançadas em escala.

Os Serviços de Machine Learning incluem uma distribuição base de R, sobreposta com pacotes R da Empresa da Microsoft. As funções e algoritmos R da Microsoft são projetados para escala e utilidade, fornecendo análise preditiva, modelação estatística, visualizações de dados e algoritmos de aprendizagem automática de ponta.

### <a name="r-packages"></a>Pacotes R

Os pacotes R de código aberto mais comuns são pré-instalados em Serviços de Machine Learning. Os seguintes pacotes R da Microsoft também estão incluídos:

| Pacote R | Descrição|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | O Microsoft R Open é a distribuição melhorada de R da Microsoft. É uma plataforma completa de código aberto para análise estatística e ciência de dados. Baseia-se em e 100% compatível com R, e inclui capacidades adicionais para melhorar o desempenho e a reprodutibilidade. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR é a biblioteca primária para funções R. escaláveis nesta biblioteca estão entre as mais utilizadas. Transformações e manipulação de dados, resumo estatístico, visualização e muitas formas de modelação e análises são encontradas nestas bibliotecas. Adicionalmente, as funções nestas bibliotecas distribuem automaticamente cargas de trabalho através dos núcleos disponíveis para processamento paralelo, com a capacidade de trabalhar em pedaços de dados que são coordenados e geridos pelo motor de cálculo. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | O MicrosoftML adiciona algoritmos de aprendizagem automática para criar modelos personalizados para análise de texto, análise de imagem e análise de sentimentos. |

Além dos pacotes pré-instalados, pode [instalar pacotes adicionais.](machine-learning-services-add-r-packages.md)

<a name="signup"></a>

## <a name="sign-up-for-the-preview-closed"></a>Inscreva-se para a pré-visualização (fechada)

> [!IMPORTANT]
> O registo dos Serviços de Aprendizagem de Máquinas de Base de Dados Azure SQL (pré-visualização) está atualmente **fechado**.

## <a name="next-steps"></a>Passos seguintes

- Consulte as [principais diferenças dos serviços de aprendizagem automática de servidores SQL.](machine-learning-services-differences.md)
- Para aprender a usar R para consultar os Serviços de Aprendizagem de Máquinas de Base de Dados Azure SQL (pré-visualização), consulte o [guia Quickstart](connect-query-r.md).
- Para começar com alguns scripts R simples, consulte [Criar e executar scripts R simples em Azure SQL Database Machine Learning Services (pré-visualização)](r-script-create-quickstart.md).
 