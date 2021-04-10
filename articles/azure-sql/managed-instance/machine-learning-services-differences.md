---
title: Principais diferenças para serviços de aprendizagem automática
description: Este artigo descreve diferenças fundamentais entre serviços de aprendizagem automática em Azure SQL Managed Instance e SQL Server Machine Learning Services.
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
ms.openlocfilehash: b5ad439a8e10fa9aa44e477ca35f45d65ae40803
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599549"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Principais diferenças entre o Machine Learning Services no SQL Managed Instance e no SQL Server

Este artigo descreve as poucas diferenças fundamentais na funcionalidade entre serviços de [aprendizagem automática em Azure SQL Managed Instance](machine-learning-services-overview.md) e [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning).

## <a name="language-support"></a>Suporte de idiomas

Os serviços de machine learning tanto no SQL Managed Instance como no SQL Server suportam o [quadro de extensibilidade](/sql/machine-learning/concepts/extensibility-framework)Python e R. As principais diferenças em SQL Managed Instance são:

- Apenas existe suporte para Python e R. Não pode adicionar linguagens externas como Java.

- As versões iniciais de Python e R são diferentes:

  | Plataforma                   | Versão de tempo de execução python           | Versões de execução R                   |
  |----------------------------|----------------------------------|--------------------------------------|
  | Instância Gerida do SQL do Azure | 3.7.2                            | 3.5.2                                |
  | SQL Server 2019            | 3.7.1                            | 3.5.2                                |
  | SQL Server 2017            | 3.5.2 e 3.7.2 (CU22 e posteriormente) | 3.3.3 e 3.5.2 (CU22 e posteriormente)     |
  | SQL Server 2016            | Não disponível                    | 3.2.2 e 3.5.2 (SP2 CU14 e posteriormente) |

## <a name="python-and-r-packages"></a>Pacotes Python e R

Não existe suporte em SQL Managed Instance para pacotes que dependam de tempos de execução externos (como Java) ou que necessitem de acesso a APIs de OS para instalação ou utilização.

Para obter mais informações sobre a gestão de pacotes Python e R, consulte:

- [Obter informações de pacotes Python](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [Obter informações de pacotes R](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>Gestão de recursos

Em SQL Managed Instance, não é possível limitar os recursos R através [do Governador de Recursos,](/sql/relational-databases/resource-governor/resource-governor?view=azuresqldb-mi-current&preserve-view=true)e os pools de recursos externos não são suportados.

Por padrão, os recursos R são definidos para um máximo de 20% dos recursos de Instância Gerida SQL disponíveis quando a extensibilidade está ativada. Para alterar esta percentagem de incumprimento, crie um bilhete de apoio Azure em [https://azure.microsoft.com/support/create-ticket/](https://azure.microsoft.com/support/create-ticket/) .

A extensibilidade é ativada com os seguintes comandos SQL (SQL Managed Instance reiniciará e ficará indisponível por alguns segundos):

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

Para desativar a extensibilidade e restaurar 100% dos recursos de memória e CPU para o SQL Server, utilize os seguintes comandos:

```sql
sp_configure 'external scripts enabled', 0;
RECONFIGURE WITH OVERRIDE;
```

Os recursos totais disponíveis para a SQL Managed Instance dependem do nível de serviço que escolher. Para mais informações, consulte [os modelos de compra da Base de Dados Azure SQL](/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Erro de memória insuficiente

A utilização da memória depende da quantidade utilizada nos scripts R e do número de consultas paralelas em execução. Se não houver memória suficiente disponível para R, receberá uma mensagem de erro. As mensagens de erro comuns são:

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

Se receber um destes erros, pode resolvê-lo escalando a sua base de dados para um nível de serviço mais elevado.

## <a name="sql-managed-instance-pools"></a>Piscinas de instância gerida SQL

Atualmente, os Serviços de Machine Learning não são suportados em [piscinas de instância gerida Azure SQL (pré-visualização)](instance-pools-overview.md).

## <a name="next-steps"></a>Passos seguintes

- Consulte a visão geral, [Serviços de Aprendizagem automática em Azure SQL Gestded Instance](machine-learning-services-overview.md).
- Para aprender a usar Python em serviços de machine learning, consulte [os scripts Run Python](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Para aprender a usar R em Serviços de Aprendizagem Automática, consulte [scripts Run R](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
