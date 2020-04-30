---
title: Serviços de Aprendizagem Automática com R (pré-visualização)
description: Este artigo descreve o Azure SQL Database Machine Learning Services (com R) e explica como funciona.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 46ca4661d06b52c861431a680a69297575ac99b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81461417"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Serviços de Machine Learning de Base de Dados Azure SQL com R (pré-visualização)

Machine Learning Services é uma característica da Base de Dados Azure SQL, usada para executar scripts R na base de dados. A funcionalidade inclui pacotes Microsoft R para análise supreventiva de alto desempenho e aprendizagem automática. Os dados relacionais podem ser utilizados em scripts R através de procedimentos armazenados, script T-SQL contendo declarações R ou código R contendo T-SQL.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="what-you-can-do-with-r"></a>O que pode fazer com R

Utilize o poder da linguagem R para fornecer análises avançadas e machine learning na base de dados. Esta capacidade traz cálculos e processamento para onde os dados residem, eliminando a necessidade de puxar dados através da rede. Além disso, você pode aproveitar o poder dos pacotes R da empresa para fornecer análises avançadas em escala.

Os Serviços de Machine Learning incluem uma distribuição base de R, sobreposto com pacotes R da empresa da Microsoft. As funções e algoritmos R da Microsoft são projetados tanto para escala como para utilidade, fornecendo análises preditivas, modelação estatística, visualizações de dados e algoritmos de aprendizagem automática de ponta.

### <a name="r-packages"></a>Pacotes R

Os pacotes R de código aberto mais comuns são pré-instalados em Serviços de Aprendizagem automática. Os seguintes pacotes R da Microsoft também estão incluídos:

| Pacote R | Descrição|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | O Microsoft R Open é a distribuição melhorada de R da Microsoft. É uma plataforma completa de código aberto para análise estatística e ciência dos dados. Baseia-se e 100% compatível com R, e inclui capacidades adicionais para melhorar o desempenho e a reprodutibilidade. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR é a biblioteca primária para funções de R. escaláveis nesta biblioteca estão entre as mais utilizadas. As transformações e manipulação de dados, a resumição estatística, a visualização e muitas formas de modelação e análises encontram-se nestas bibliotecas. Além disso, as funções nestas bibliotecas distribuem automaticamente cargas de trabalho através de núcleos disponíveis para processamento paralelo, com a capacidade de trabalhar em pedaços de dados que são coordenados e geridos pelo motor de cálculo. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | O MicrosoftML adiciona algoritmos de aprendizagem automática para criar modelos personalizados para análise de texto, análise de imagem e análise de sentimentos. |

Além das embalagens pré-instaladas, pode [instalar pacotes adicionais.](sql-database-machine-learning-services-add-r-packages.md)

<a name="signup"></a>

## <a name="sign-up-for-the-preview-closed"></a>Inscreva-se para a pré-visualização (fechada)

> [!IMPORTANT]
> A inscrição no Azure SQL Database Machine Learning Services (pré-visualização) está **encerrada.**

## <a name="next-steps"></a>Passos seguintes

- Consulte as [principais diferenças dos Serviços de Aprendizagem automática do Servidor SQL](sql-database-machine-learning-services-differences.md).
- Para aprender a usar R para consultar o Azure SQL Database Machine Learning Services (pré-visualização), consulte o [guia Quickstart](sql-database-connect-query-r.md).
- Para começar com alguns scripts R simples, consulte [Create e execute scripts R simples em Serviços de Machine Learning de Base de Dados Azure SQL (pré-visualização)](sql-database-quickstart-r-create-script.md).
