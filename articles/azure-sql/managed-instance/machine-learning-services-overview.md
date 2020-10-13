---
title: Serviços de Aprendizagem automática em Azure SQL Gestded Instance (pré-visualização)
description: Este artigo fornece uma visão geral ou serviços de aprendizagem automática em Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 06/03/2020
ms.openlocfilehash: d7a3c86f3d9cf083a8746f753b8c5287c774a93e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91263272"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance-preview"></a>Serviços de Aprendizagem automática em Azure SQL Gestded Instance (pré-visualização)

Machine Learning Services é uma característica do Azure SQL Managed Instance (pré-visualização) que fornece aprendizagem automática na base de dados, suportando scripts Python e R. A funcionalidade inclui pacotes Microsoft Python e R para análise preditiva de alto desempenho e machine learning. Os dados relacionais podem ser utilizados em scripts através de procedimentos armazenados, script T-SQL contendo declarações python ou R, ou código Python ou R contendo T-SQL.

> [!IMPORTANT]
> O Machine Learning Services é uma funcionalidade do SQL Managed Instance que está atualmente em pré-visualização pública.
> Esta funcionalidade de pré-visualização está inicialmente disponível num número limitado de regiões dos EUA, Ásia Europa e Austrália, com regiões adicionais a serem adicionadas mais tarde.
>
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> [Inscreva-se para a pré-visualização](#signup) abaixo.

## <a name="what-is-machine-learning-services"></a>O que é o Serviço de Aprendizagem automática?

Serviços de Machine Learning em Azure SQL Managed Instance permite executar scripts Python e R na base de dados. Pode usá-lo para preparar e limpar dados, fazer engenharia de recursos e treinar, avaliar e implementar modelos de machine learning dentro de uma base de dados. A funcionalidade executa os seus scripts onde os dados residem e elimina a transferência dos dados através da rede para outro servidor.

Utilize serviços de aprendizagem automática com suporte R/Python em Azure SQL Gestded Instance para:

- **Executar scripts R e Python para fazer a preparação de dados e processamento de dados para fins gerais** - Pode agora levar as suas scripts R/Python para Azure SQL Managed Instance onde os seus dados vivem, em vez de ter que mover dados para outro servidor para executar scripts R e Python. Pode eliminar a necessidade de movimento de dados e problemas associados relacionados com a latência, segurança e conformidade.

- **Modelos de aprendizagem de máquinas de comboio na base de dados** - Você pode treinar modelos usando quaisquer algoritmos de código aberto. Pode facilmente escalar o seu treino para todo o conjunto de dados em vez de depender de conjuntos de dados de amostras retirados da base de dados.

- **Implemente os seus modelos e scripts na produção em procedimentos armazenados** - Os scripts e modelos treinados podem ser operacionalizados simplesmente incorporando-os em procedimentos armazenados T-SQL. As aplicações que se ligam ao Azure SQL Managed Instance podem beneficiar de previsões e inteligência nestes modelos, chamando apenas um procedimento armazenado. Também pode utilizar a função T-SQL PREDICT nativa para operacionalizar modelos para pontuação rápida em cenários de pontuação em tempo real altamente simultâneos.

As distribuições base de Python e R estão incluídas nos Serviços de Aprendizagem Automática. Pode instalar e utilizar pacotes e estruturas de código aberto, tais como PyTorch, TensorFlow e scikit-learn, além dos pacotes microsoft [revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-revoscalepy) e [microsoftml](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-microsoftml) para Python, e [RevoScaleR,](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) [MicrosoftML,](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) [olapR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-olapr)e [sqlrutils](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-sqlrutils) para R.

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Inscrever-se na pré-visualização

Esta pré-visualização pública limitada está sujeita aos [termos de pré-visualização do Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Se você está interessado em aderir ao programa de pré-visualização e aceitar estes termos, então você pode solicitar a inscrição criando um bilhete de apoio Azure em [**https://azure.microsoft.com/support/create-ticket/**](https://azure.microsoft.com/support/create-ticket/) . 

1. Selecione as seguintes opções:
   - Tipo de emissão - **Técnico**
   - Subscrição - *selecione a sua subscrição*
   - Serviço - **SQL Database Managed Instance**
   - Resumo - *insira uma breve descrição do seu pedido*
   - Tipo de problema - **Serviços de aprendizagem automática para exemplo gerido do SQL (Pré-visualização)**
   - Subtipo de problema - **Outras questões ou "Como Fazer"**

1. Clique **em seguida: Soluções**.

1. Leia as informações sobre a pré-visualização e, em seguida, clique em **Detalhes**.

1. Em **Descrição,** insira as especificidades do seu pedido, incluindo o nome do servidor lógico, região e ID de subscrição que gostaria de inscrever na pré-visualização. Insira outros detalhes conforme apropriado.

1. Quando terminar, clique em **Seguinte: Rever + criar**e, em seguida, clicar em **Criar**.

Assim que estiver inscrito no programa, a Microsoft vai incluí-lo na pré-visualização pública e ativar o Machine Learning Services para a base de dados existente ou nova.

Durante a pré-visualização pública, o Machine Learning Services no SQL Managed Instance não são recomendados para cargas de trabalho em produção.

## <a name="next-steps"></a>Passos seguintes

- Consulte as [principais diferenças dos serviços de aprendizagem automática de servidores SQL.](machine-learning-services-differences.md)
- Para aprender a usar Python em serviços de machine learning, consulte [os scripts Run Python](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
- Para aprender a usar R em Serviços de Aprendizagem Automática, consulte [scripts Run R](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
- Para obter mais informações sobre machine learning em outras plataformas SQL, consulte a [documentação de aprendizagem automática SQL.](https://docs.microsoft.com/sql/machine-learning/)
