---
title: Principais diferenças para serviços de aprendizagem automática (pré-visualização)
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
ms.date: 10/26/2020
ms.openlocfilehash: c806c0a13f9f5f13588b780054d1f285beb44802
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96324538"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Principais diferenças entre o Machine Learning Services no SQL Managed Instance e no SQL Server

A funcionalidade dos Serviços de [Aprendizagem automática em Azure SQL Managed Instance (pré-visualização)](machine-learning-services-overview.md) é quase idêntica aos Serviços de [Aprendizagem de Máquinas do Servidor SQL](/sql/advanced-analytics/what-is-sql-server-machine-learning). Seguem-se algumas diferenças fundamentais.

> [!IMPORTANT]
> Os Serviços de Machine Learning em Azure SQL Managed Instance estão atualmente em pré-visualização pública. Para se inscrever, consulte [inscrever-se para a pré-visualização](machine-learning-services-overview.md#signup).

## <a name="preview-limitations"></a>Limitações de pré-visualização

Durante a pré-visualização, o serviço tem as seguintes limitações:

- As ligações loopback não funcionam (ver [ligação Loopback ao SQL Server a partir de um script Python ou R](/sql/machine-learning/connect/loopback-connection)).
- Os Agrupamentos de recursos externos não são suportados.
- Apenas existe suporte para Python e R. Não pode adicionar linguagens externas como Java.
- Os cenários que utilizam a [Interface de Passagem de Mensagens](/message-passing-interface/microsoft-mpi) (MPI) não são suportados.

Em caso de atualização do Objetivo de Nível de Serviço (SLO), atualize a SLO e levante um bilhete de apoio para reativar os limites de recursos dedicados para r/python.

## <a name="language-support"></a>Suporte de idiomas

Os serviços de machine learning em SQL Managed Instance e SQL Server suportam a [estrutura de extensibilidade](/sql/advanced-analytics/concepts/extensibility-framework)Python e R. As principais diferenças são as seguintes:

- As versões iniciais de Python e R são diferentes entre serviços de machine learning em SQL Managed Instance e SQL Server:

  | Sistema               | Python | R     |
  |----------------------|--------|-------|
  | Instância Gerida do SQL | 3.7.1  | 3.5.2 |
  | SQL Server           | 3.5.2  | 3.3.3 |

- Não há necessidade de configurar `external scripts enabled` via `sp_configure` . Uma vez [inscrito](machine-learning-services-overview.md#signup) para a pré-visualização, o machine learning está ativado para Azure SQL Managed Instance.

## <a name="packages"></a>Pacote

A gestão de pacotes Python e R funciona de forma diferente entre o SQL Managed Instance e o SQL Server. Estas diferenças são:

- Não existe suporte para pacotes que dependam de tempos de execução externos (como Java) ou que necessitem de acesso a APIs de OS para instalação ou utilização.
- Os pacotes podem realizar chamadas de rede de saída (alterar a partir de mais cedo na pré-visualização). Pode definir as regras de segurança de saída corretas ao nível do [Grupo de Segurança](../../virtual-network/network-security-groups-overview.md) da Rede para permitir chamadas de rede de saída.

Para obter mais informações sobre a gestão de pacotes Python e R, consulte:

- [Obter informações de pacotes Python](/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [Obter informações de pacotes R](/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>Gestão de recursos

Não é possível limitar os recursos R através [do Governador de Recursos](/sql/relational-databases/resource-governor/resource-governor) e dos pools de recursos externos.

Durante a pré-visualização pública, os recursos R estão definidos para um máximo de 20% dos recursos do SQL Managed Instance e dependem do escalão de serviço que escolher. Para mais informações, consulte [os modelos de compra da Base de Dados Azure SQL](../database/purchasing-models.md).

### <a name="insufficient-memory-error"></a>Erro de memória insuficiente

Se não existir memória suficiente disponível para R, receberá uma mensagem de erro. As mensagens de erro comuns são:

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

A utilização da memória depende da quantidade utilizada nos scripts R e do número de consultas paralelas em execução. Se receber os erros acima, poderá dimensionar a base de dados para um escalão de serviço mais elevado para resolver o problema.

## <a name="next-steps"></a>Passos seguintes

- Consulte a visão geral, [Serviços de Aprendizagem automática em Azure SQL Gestded Instance](machine-learning-services-overview.md).
- Para aprender a usar Python em serviços de machine learning, consulte [os scripts Run Python](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Para aprender a usar R em Serviços de Aprendizagem Automática, consulte [scripts Run R](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).