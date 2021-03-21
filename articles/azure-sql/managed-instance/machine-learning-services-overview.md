---
title: Serviços de aprendizagem automática em Azure SQL Gestão De Instância
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
ms.date: 03/17/2021
ms.openlocfilehash: 94495144c64b3770995a5f67e9129b3ba86e741e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599566"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance"></a>Serviços de aprendizagem automática em Azure SQL Gestão De Instância

Machine Learning Services é uma característica do Azure SQL Managed Instance que fornece aprendizagem automática na base de dados, suportando scripts Python e R. A funcionalidade inclui pacotes Microsoft Python e R para análise preditiva de alto desempenho e machine learning. Os dados relacionais podem ser utilizados em scripts através de procedimentos armazenados, script T-SQL contendo declarações python ou R, ou código Python ou R contendo T-SQL.

## <a name="what-is-machine-learning-services"></a>O que é o Serviço de Aprendizagem automática?

Serviços de Machine Learning em Azure SQL Managed Instance permite executar scripts Python e R na base de dados. Pode usá-lo para preparar e limpar dados, fazer engenharia de recursos e treinar, avaliar e implementar modelos de machine learning dentro de uma base de dados. A funcionalidade executa os seus scripts onde os dados residem e elimina a transferência dos dados através da rede para outro servidor.

Utilize serviços de aprendizagem automática com suporte R/Python em Azure SQL Gestded Instance para:

- **Executar scripts R e Python para fazer a preparação de dados e processamento de dados para fins gerais** - Pode agora levar as suas scripts R/Python para Azure SQL Managed Instance onde os seus dados vivem, em vez de ter que mover dados para outro servidor para executar scripts R e Python. Pode eliminar a necessidade de movimento de dados e problemas associados relacionados com a latência, segurança e conformidade.

- **Modelos de aprendizagem de máquinas de comboio na base de dados** - Você pode treinar modelos usando quaisquer algoritmos de código aberto. Pode facilmente escalar o seu treino para todo o conjunto de dados em vez de depender de conjuntos de dados de amostras retirados da base de dados.

- **Implemente os seus modelos e scripts na produção em procedimentos armazenados** - Os scripts e modelos treinados podem ser operacionalizados simplesmente incorporando-os em procedimentos armazenados T-SQL. As aplicações que se ligam ao Azure SQL Managed Instance podem beneficiar de previsões e inteligência nestes modelos, chamando apenas um procedimento armazenado. Também pode utilizar a função T-SQL PREDICT nativa para operacionalizar modelos para pontuação rápida em cenários de pontuação em tempo real altamente simultâneos.

As distribuições base de Python e R estão incluídas nos Serviços de Aprendizagem Automática. Pode instalar e utilizar pacotes e estruturas de código aberto, tais como PyTorch, TensorFlow e scikit-learn, além dos pacotes microsoft [revoscalepy](/sql/machine-learning/python/ref-py-revoscalepy) e [microsoftml](/sql/machine-learning/python/ref-py-microsoftml) para Python, e [RevoScaleR,](/sql/machine-learning/r/ref-r-revoscaler) [MicrosoftML,](/sql/machine-learning/r/ref-r-microsoftml) [olapR](/sql/machine-learning/r/ref-r-olapr)e [sqlrutils](/sql/machine-learning/r/ref-r-sqlrutils) para R.

## <a name="how-to-enable-machine-learning-services"></a>Como permitir serviços de aprendizagem automática

Pode ativar os Serviços de Aprendizagem automática em Azure SQL Managed Instance, permitindo a extensibilidade com os seguintes comandos SQL (SQL Managed Instance reiniciará e ficará indisponível por alguns segundos):

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

Para obter mais informações sobre como este comando afeta os recursos da SQL Managed Instance, consulte [a Governação de Recursos](machine-learning-services-differences.md#resource-governance).

### <a name="enable-machine-learning-services-in-a-failover-group"></a>Ativar serviços de aprendizagem automática num grupo de failover

Num [grupo de failover,](failover-group-add-instance-tutorial.md)as bases de dados do sistema não são replicadas na instância secundária (ver [Limitações de grupos de failover](../database/auto-failover-group-overview.md#limitations-of-failover-groups) para obter mais informações).

Se a Instância Gerida que está a usar faz parte de um grupo de failover, faça o seguinte:

- Executar o `sp_configure` e `RECONFIGURE` comandos em cada instância do grupo de failover para ativar serviços de aprendizagem automática.

- Instale as bibliotecas R/Python numa base de dados do utilizador e não na base de dados principal.

## <a name="next-steps"></a>Passos seguintes

- Consulte as [principais diferenças dos serviços de aprendizagem automática de servidores SQL.](machine-learning-services-differences.md)
- Para aprender a usar Python em serviços de machine learning, consulte [os scripts Run Python](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Para aprender a usar R em Serviços de Aprendizagem Automática, consulte [scripts Run R](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Para obter mais informações sobre machine learning em outras plataformas SQL, consulte a [documentação de aprendizagem automática SQL.](/sql/machine-learning/index)
