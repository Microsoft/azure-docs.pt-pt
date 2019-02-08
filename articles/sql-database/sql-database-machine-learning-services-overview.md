---
title: Serviços de Machine Learning (com R) na descrição geral do SQL do Azure da base de dados (pré-visualização)
description: Este tópico descreve os serviços do Azure SQL Database Machine Learning (com R) e explica como funciona.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning-services
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 02/06/2019
ms.openlocfilehash: b50fd21e4d3325875134d2e2e9caeed9f8db75d0
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875608"
---
# <a name="machine-learning-services-with-r-in-azure-sql-database-preview"></a>Serviços de Machine Learning (com R) na base de dados do Azure SQL (pré-visualização)

Serviços de Machine Learning é um recurso do Azure SQL Database, utilizado para a execução de scripts do R na base de dados. A funcionalidade inclui pacotes de Microsoft R para Análise Preditiva de alto desempenho e o machine learning. Os dados relacionais podem ser usados nos scripts de R por meio de procedimentos armazenados, instruções de R que contém script T-SQL ou código de R que contém o T-SQL.

> [!IMPORTANT]
> Serviços de Machine Learning (com R) na base de dados do Azure SQL está atualmente em pré-visualização pública para bases de dados individuais e conjuntos elásticos com o modelo de compra baseado em vCore na **fins gerais** e **críticosdenegócio** escalões de serviço. Esta pré-visualização pública inicial, o **hiperescala** escalão de serviço e o **instância gerida** opção de implementação não são suportadas. Atualmente, a única linguagem suportada é R. Ainda não há suporte para Python neste momento. 
>
> A pré-visualização está atualmente disponível nas seguintes regiões: oeste Europa, Europa do Norte, E.U.A. oeste 2, E.U.A. leste, Centro-Sul, e.u.a. Centro-Norte, Canadá Central, Sudeste asiático, Sul da Índia e Sudeste da Austrália. 
>
> [Inscreva-se na pré-visualização](#signup) abaixo.

## <a name="what-you-can-do-with-r"></a>O que pode fazer com R

Utilize o poder da linguagem R para fornecer análises avançadas e aprendizagem automática na base de dados. Esta capacidade traz cálculos e processamento para onde os dados residem, eliminando a necessidade de extrair os dados através da rede. Além disso, tire partido do poder dos pacotes de R do enterprise para fornecer análise avançada à escala.

Machine Learning Services inclui uma distribuição base da linguagem R, recoberto com pacotes de R enterprise da Microsoft. Funções de R e algoritmos da Microsoft estão concebidos para o dimensionamento e o utilitário, entrega de Análise Preditiva, modelação estatística, visualizações de dados e algoritmos de aprendizagem de ponta.

### <a name="r-packages"></a>Pacotes de R

Os pacotes de R open-source mais comuns são previamente instalados no Machine Learning Services. Os seguintes pacotes de R da Microsoft também estão incluídos:

| Pacote de R | Descrição|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open é a distribuição aprimorada do R da Microsoft. É uma plataforma completa de código aberto para estatística análise e ciência de dados. É com base em e 100% compatível com R e inclui capacidades adicionais para maior desempenho e capacidade de reprodução. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | Pacotes RevoScaleR é a biblioteca principal para a dimensionável R. as funções nesta biblioteca estão entre o mais amplamente utilizadas. Transformações de dados e manipulação, resumo de estatístico, visualização e muitas formas de modelagem e análises são encontradas dessas bibliotecas. Além disso, as funções nessas bibliotecas automaticamente distribuam as cargas de trabalho em núcleos disponíveis para processar, com a capacidade de trabalhar em segmentos de dados coordenados e geridos pelo mecanismo de cálculo de paralela. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML adiciona algoritmos de machine learning para criar modelos personalizados para análise de texto, imagens de análises e análise de sentimentos. |

Além de pacotes de previamente instalados, pode [instalar pacotes de adição](sql-database-connect-query-r.md#add-package).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Inscrever-se na pré-visualização

Para se inscrever na pré-visualização pública, siga estes passos:

1. Se não tiver uma subscrição do Azure, [criar uma conta](https://azure.microsoft.com/free/) antes de começar.

2. Enviar um e-mail à Microsoft em [ sqldbml@microsoft.com ](mailto:sqldbml@microsoft.com) para se inscrever na pré-visualização pública. A pré-visualização pública do Machine Learning Services (com R) na Base de Dados SQL do Azure não está ativada por predefinição.

Assim que estão inscritos no programa, Microsoft irá carregar para a pré-visualização pública e ativar R para sua existente ou novo banco de dados.

Não utilize serviços de Machine Learning com R para cargas de trabalho de produção durante a pré-visualização pública.

## <a name="next-steps"></a>Passos Seguintes

- Consulte o [diferenças do SQL Server Machine Learning Services de chave](sql-database-machine-learning-services-differences.md)
- Para saber como utilizar o Machine Learning Services (com R) na base de dados do Azure SQL, veja [guia de início rápido](sql-database-connect-query-r.md).
- Saiba mais com [tutoriais da linguagem R do SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)
