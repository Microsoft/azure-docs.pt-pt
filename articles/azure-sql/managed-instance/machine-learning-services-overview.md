---
title: Serviços de Aprendizagem Automática em Instância Gerida Azure SQL (pré-visualização)
description: Este artigo fornece uma visão geral ou serviços de aprendizagem automática em 1º caso gerido por Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: carlrab, davidph
manager: cgronlun
ms.date: 05/27/2020
ms.openlocfilehash: fb74cc3aba3ad37e36a4d7cbe726bde4cb088e90
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045103"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance-preview"></a>Serviços de Aprendizagem Automática em Instância Gerida Azure SQL (pré-visualização)

Machine Learning Services é uma característica do Azure SQL Managed Instance (pré-visualização) que fornece machine learning na base de dados, suportando scripts Python e R. A funcionalidade inclui pacotes Microsoft Python e R para análises preditivas de alto desempenho e machine learning. Os dados relacionais podem ser utilizados em scripts através de procedimentos armazenados, script T-SQL contendo declarações de Python ou R, ou código Python ou R contendo T-SQL.

> [!IMPORTANT]
> Machine Learning Services é uma característica do Azure SQL Managed Instance que está atualmente em pré-visualização pública.
> Esta funcionalidade de pré-visualização está inicialmente disponível num número limitado de regiões nos EUA, Ásia Europa e Austrália, com regiões adicionais adicionadas mais tarde.
>
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .
>
> [Inscreva-se para a pré-visualização](#signup) abaixo.

## <a name="what-is-machine-learning-services"></a>O que é serviços de aprendizagem automática?

Os Serviços de Aprendizagem automática em 1º Caso Gerido azure SQL permitem executar scripts Python e R na base de dados. Pode usá-lo para preparar e limpar dados, fazer engenharia de funcionalidades e treinar, avaliar e implementar modelos de machine learning dentro de uma base de dados. A funcionalidade executa os seus scripts onde os dados residem e elimina a transferência dos dados através da rede para outro servidor.

Utilize serviços de aprendizagem automática com suporte R/Python em Instância Gerida Azure SQL para:

- **Executar scripts R e Python para fazer a preparação** de dados e o processamento geral de dados - Pode agora levar os seus scripts R/Python para o Azure SQL Managed Instance onde os seus dados vivem, em vez de ter de mover dados para outro servidor para executar scripts R e Python. Pode eliminar a necessidade de movimento de dados e problemas associados relacionados com a latência, segurança e conformidade.

- **Treinar modelos de aprendizagem automática na base de dados** - Pode treinar modelos usando quaisquer algoritmos de código aberto. Pode facilmente escalar o seu treino para todo o conjunto de dados em vez de confiar em conjuntos de dados de amostras retirados da base de dados.

- **Implemente os seus modelos e scripts em produção em procedimentos armazenados** - Os scripts e modelos treinados podem ser operacionalizados simplesmente incorporando-os em procedimentos armazenados em T-SQL. As aplicações que se ligam ao Azure SQL Managed Instance podem beneficiar de previsões e inteligência nestes modelos, bastando chamar um procedimento armazenado. Também pode utilizar a função nativa T-SQL PREDICT para operacionalizar modelos para pontuação rápida em cenários de pontuação em tempo real altamente simultâneos.

As distribuições de base de Python e R estão incluídas nos Serviços de Aprendizagem automática. Pode instalar e utilizar pacotes e quadros de código aberto, tais como PyTorch, TensorFlow e scikit-learn, além dos pacotes da Microsoft [revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-revoscalepy) e [microsoftml](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-microsoftml) para Python, e [RevoScaleR,](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) [MicrosoftML,](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) [olapR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-olapr)e [sqlrutils](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-sqlrutils) para R.

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Inscrever-se na pré-visualização

Esta pré-visualização pública limitada está sujeita aos termos de [pré-visualização do Azure.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 

Se você está interessado em aderir ao programa de pré-visualização e aceitar estes termos, então você pode solicitar inscrição criando um bilhete de suporte Azure em [https://azure.microsoft.com/support/create-ticket/](https://azure.microsoft.com/support/create-ticket/) . Para "Tipo de emissão" selecione "Technical", para "Service" selecione "SQL Database Managed Instance", e para "Problem type" selecione "Other". No seu pedido, diga que gostaria de ser inscrito na pré-visualização pública limitada de Machine Learning para SQL Managed Instance com estes detalhes: nome lógico do servidor, região e ID de subscrição.

Uma vez matriculado no programa, a Microsoft irá a bordo para a pré-visualização pública e ativará os Serviços de Aprendizagem automática para a sua base de dados existente ou nova.

Os Serviços de Aprendizagem Automática em Instância Gerida SQL não são recomendados para cargas de trabalho de produção durante a pré-visualização pública.

## <a name="next-steps"></a>Próximos passos

- Consulte as [principais diferenças dos Serviços de Aprendizagem automática do Servidor SQL](machine-learning-services-differences.md).
- Para aprender a usar python em Serviços de Aprendizagem automática, consulte [scripts Run Python](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
- Para aprender a usar R em Serviços de Aprendizagem automática, consulte [scripts Run R](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
