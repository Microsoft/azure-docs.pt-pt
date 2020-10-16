---
title: Principais diferenças para serviços de aprendizagem automática (pré-visualização)
description: Este tópico descreve as principais diferenças entre serviços de aprendizagem automática em Azure SQL Managed Instance e SQL Server Machine Learning Services.
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
ms.date: 05/27/2020
ms.openlocfilehash: 9ff2de18042c466bdd8fa6c71194fff4286c820d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325101"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Principais diferenças entre o Machine Learning Services no SQL Managed Instance e no SQL Server

A funcionalidade dos Serviços de [Aprendizagem automática em Azure SQL Managed Instance (pré-visualização)](machine-learning-services-overview.md) é quase idêntica aos Serviços de [Aprendizagem de Máquinas do Servidor SQL](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Seguem-se algumas diferenças fundamentais.

> [!IMPORTANT]
> Os Serviços de Machine Learning em Azure SQL Managed Instance estão atualmente em pré-visualização pública. Para se inscrever, consulte [inscrever-se para a pré-visualização](machine-learning-services-overview.md#signup).

## <a name="preview-limitations"></a>Limitações de pré-visualização

Durante a pré-visualização, o serviço tem as seguintes limitações:

- As ligações loopback não funcionam (ver [ligação Loopback ao SQL Server a partir de um script Python ou R](/sql/machine-learning/connect/loopback-connection)).
- Os Agrupamentos de recursos externos não são suportados.
- Apenas existe suporte para Python e R. Não pode adicionar linguagens externas como Java.
- Os cenários que utilizam a [Interface de Passagem de Mensagens](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) (MPI) não são suportados.

Em caso de atualização do Objetivo do Nível de Serviço (SLO), atualize o SLO e apresente um pedido de suporte para reativar os limites dos recursos dedicados para R/Python.

## <a name="language-support"></a>Suporte de idiomas

Os serviços de machine learning em SQL Managed Instance e SQL Server suportam a [estrutura de extensibilidade](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework)Python e R. As principais diferenças são:

- As versões iniciais de Python e R são diferentes entre serviços de machine learning em SQL Managed Instance e SQL Server:

  | Sistema               | Python | R     |
  |----------------------|--------|-------|
  | Instância Gerida do SQL | 3.7.1  | 3.5.2 |
  | SQL Server           | 3.5.2  | 3.3.3 |

- Não há necessidade de configurar `external scripts enabled` via `sp_configure` . Uma vez [inscrito](machine-learning-services-overview.md#signup) para a pré-visualização, o machine learning está ativado para Azure SQL Managed Instance.

## <a name="packages"></a>Pacote

A gestão de pacotes Python e R funciona de forma diferente entre o SQL Managed Instance e o SQL Server. Estas diferenças são:

- Os pacotes não podem realizar chamadas de rede de saída. Esta limitação é semelhante às [regras de firewall padrão para serviços de aprendizagem automática](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) no SQL Server, mas não pode ser alterada em SQL Managed Instance.
- Não existe suporte para pacotes que dependam de tempos de execução externos (como Java) ou que necessitem de acesso a APIs de OS para instalação ou utilização.

Para obter mais informações sobre a gestão de pacotes Python e R, consulte:

- [Obter informações de pacotes Python](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)
- [Obter informações de pacotes R](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)

## <a name="resource-governance"></a>Governação de recursos

Não é possível limitar os recursos R através [do Governador de Recursos](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) e dos pools de recursos externos.

Durante a pré-visualização pública, os recursos R estão definidos para um máximo de 20% dos recursos do SQL Managed Instance e dependem do escalão de serviço que escolher. Para mais informações, consulte [os modelos de compra da Base de Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Erro de memória insuficiente

Se não existir memória suficiente disponível para R, receberá uma mensagem de erro. As mensagens de erro comuns são:

- Incapaz de comunicar com o tempo de execução do script 'R' para id pedido: ********* Verifique os requisitos do runtime “R”
- Ocorreu um erro do script “R” durante a execução de “sp_execute_external_script” com HRESULT 0x80004004. ... ocorreu um erro de script externo: “... não foi possível alocar memória (0 MB) na função C R_AllocStringBuffer”
- Ocorreu um erro de script externo: Erro: não é possível atribuir vetor de tamanho.

A utilização da memória depende da quantidade utilizada nos scripts R e do número de consultas paralelas em execução. Se receber os erros acima, poderá dimensionar a base de dados para um escalão de serviço mais elevado para resolver o problema.

## <a name="next-steps"></a>Passos seguintes

- Consulte a visão geral, [Serviços de Aprendizagem automática em Azure SQL Gestded Instance](machine-learning-services-overview.md).
- Para aprender a usar Python em serviços de machine learning, consulte [os scripts Run Python](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
- Para aprender a usar R em Serviços de Aprendizagem Automática, consulte [scripts Run R](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
