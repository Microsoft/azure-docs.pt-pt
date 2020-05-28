---
title: Principais diferenças para serviços de aprendizagem automática (pré-visualização)
description: Este tópico descreve diferenças-chave entre os Serviços de Aprendizagem Automática em 1º Lugar e os Serviços de Aprendizagem automática do Servidor SQL.
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
ms.openlocfilehash: 02211a09df2d2744119ea8fbe6f0ecaa63dba020
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045110"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Principais diferenças entre serviços de aprendizagem automática em Instância Gerida Azure SQL e Servidor SQL

A funcionalidade dos Serviços de [Aprendizagem Automática em Instância Gerida Azure SQL (pré-visualização)](machine-learning-services-overview.md) é quase idêntica aos [Serviços de Aprendizagem automática do Servidor SQL](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Seguem-se algumas diferenças fundamentais.

> [!IMPORTANT]
> Os Serviços de Aprendizagem Automática em 1º Caso Gerido Azure SQL estão atualmente em pré-visualização pública. Para se inscrever, consulte [Inscreva-se para a pré-visualização](machine-learning-services-overview.md#signup).

## <a name="preview-limitations"></a>Limitações de pré-visualização

Durante a pré-visualização, o serviço tem as seguintes limitações:

- As ligações de loopback não funcionam (ver [ligação Loopback ao SQL Server a partir de um script Python ou R](/sql/machine-learning/connect/loopback-connection)).
- Os pools de Recursos Externos não são suportados.
- Apenas Python e R são apoiados. Não se podem adicionar línguas externas como Java.
- Os cenários que utilizam a Interface de Passagem de [Mensagens](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) (MPI) não são suportados.

Em caso de atualização do Objetivo de Nível de Serviço (OIt), por favor atualize o SLO e eleve um bilhete de apoio para reativar os limites de recursos dedicados para R/Python.

## <a name="language-support"></a>Suporte de idiomas

Os Serviços de Aprendizagem automática em SQL Managed Instance e SQL Server suportam a estrutura de [extebilidade](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework)Python e R . As principais diferenças são:

- As versões iniciais de Python e R são diferentes entre os Serviços de Aprendizagem Automática em Instância Gerida SQL e O Servidor SQL:

  |                      | Python | R     |
  |----------------------|--------|-------|
  | Instância Gerida do SQL | 3.7.1  | 3.5.2 |
  | SQL Server           | 3.5.2  | 3.3.3 |

- Não há necessidade de configurar `external scripts enabled` via `sp_configure` . Uma vez que esteja [inscrito](machine-learning-services-overview.md#signup) para a pré-visualização, o machine learning está ativado para a sua base de dados SQL.

## <a name="packages"></a>Pacote

A gestão de pacotes Python e R funciona de forma diferente entre a Instância Gerida sQL e o Servidor SQL. Estas diferenças são:

- Os pacotes não podem realizar chamadas de rede de saída. Esta limitação é semelhante às [regras de firewall padrão para serviços](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) de aprendizagem automática no Servidor SQL, mas não pode ser alterada em Instância Gerida SQL.
- Não existe suporte para pacotes que dependam de tempos de execução externos (como Java) ou que precisem de acesso às APIs dos OS para instalação ou utilização.

Para obter mais informações sobre a gestão dos pacotes Python e R, consulte:

- [Obtenha informações sobre pacotepython](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)
- [Obtenha informações sobre pacoteR](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)

## <a name="resource-governance"></a>Governação de recursos

Não é possível limitar os recursos R através do Governador de [Recursos](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) e dos recursos externos.

Durante a pré-visualização pública, os recursos R são definidos para um máximo de 20% dos recursos da SQL Managed Instance, e dependem do nível de serviço que escolher. Para mais informações, consulte os modelos de compra da Base de [Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Erro de memória insuficiente

Se não houver memória suficiente disponível para R, receberá uma mensagem de erro. As mensagens de erro comuns são:

- Incapaz de comunicar com o tempo de execução para o script 'R' para id pedido: ********* Verifique os requisitos do tempo de execução 'R'
- Erro de script 'R' ocorreu durante a execução de 'sp_execute_external_script' com HRESULT 0x80004004. ... ocorreu um erro de script externo: ".. não podia atribuir memória (0 Mb) na função C 'R_AllocStringBuffer'"
- Ocorreu um erro de script externo: Erro: não pode alocar vetor de tamanho.

O uso da memória depende da quantidade utilizada nos seus scripts R e do número de consultas paralelas executadas. Se receber os erros acima, pode escalar a sua base de dados para um nível de serviço mais elevado para resolver isto.

## <a name="next-steps"></a>Próximos passos

- Consulte a visão geral, Serviços de [Aprendizagem Automática em Caso Gerido Azure SQL](machine-learning-services-overview.md).
- Para aprender a usar python em Serviços de Aprendizagem automática, consulte [scripts Run Python](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
- Para aprender a usar R em Serviços de Aprendizagem automática, consulte [scripts Run R](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
