---
title: Principais diferenças para serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização)
description: Este tópico descreve as principais diferenças entre serviços do Azure SQL Database Machine Learning (com R) e serviços de Machine Learning do SQL Server.
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
ms.date: 03/01/2019
ms.openlocfilehash: c750942f8f0f2727d1d11945a84bffb434a01193
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242127"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Principais diferenças entre os serviços do Machine Learning no Azure SQL Database (pré-visualização) e o SQL Server

A funcionalidade de serviços do Azure SQL Database Machine Learning (com R) no (pré-visualização) é semelhante à [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Seguem-se algumas diferenças fundamentais.

> [!IMPORTANT]
> Serviços do Aprendizado de máquina de base de dados do Azure SQL está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="language-support"></a>Suporte de idiomas

O SQL Server tem suporte para R e Python através da [estrutura de extensibilidade](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). Base de dados SQL não suporta as duas linguagens. As principais diferenças são:

- R é o único idioma suportado na base de dados SQL. Ainda não há suporte para Python neste momento.
- A versão de R é 3.4.4.
- Não é necessário para configurar `external scripts enabled` via `sp_configure`. Assim que estiver [inscreveu](sql-database-machine-learning-services-overview.md#signup), de machine learning está ativada para a base de dados SQL.

## <a name="package-management"></a>Gestão de pacotes

Gestão de pacotes de R e instalação de trabalho diferentes entre a base de dados SQL e o SQL Server. Essas diferenças são:

- Pacotes de R são instalados por meio [sqlmlutils](https://github.com/Microsoft/sqlmlutils) ou [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Pacotes não é possível efetuar chamadas de rede de saída. Esta limitação é semelhante para o [predefinido de regras de firewall do Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) no SQL Server, mas não pode ser alterado na base de dados do SQL.
- Não há suporte para pacotes que dependem de tempos de execução externos (como Java) ou precisam de acesso a APIs de sistema operacional para instalação ou utilização.

## <a name="resource-governance"></a>Governação de recursos

Não é possível limitar os recursos de R através de [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) e agrupamentos de recursos externos.

Durante a pré-visualização pública, os recursos de R estão definidos para um máximo de 20% dos recursos de base de dados SQL e dependem em que camada de serviço que escolher. Para obter mais informações, consulte [modelos de compra do Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Erro de memória insuficiente

Se existir memória suficiente disponível para R, receberá uma mensagem de erro. Mensagens de erro comuns são:

- Não é possível comunicar com o tempo de execução de script de 'R' para o id do pedido: ***. Verifique os requisitos do runtime de 'R'
- Ocorreu um erro de script de 'R' durante a execução de "sp_execute_external_script" com HRESULT 0x80004004. ... Ocorreu um erro de script externo: "... Não foi possível alocar a memória (0 Mb) na função de C 'R_AllocStringBuffer' "
- Ocorreu um erro de script externo: Erro: não é possível alocar o vetor de tamanho.

Utilização depende da quantidade de memória é utilizada nos scripts R e o número de consultas paralelas que está sendo executada. Se receber os erros acima, pode dimensionar a base de dados para um escalão de serviço mais elevado para resolver este problema.

## <a name="security-isolation"></a>Isolamento de segurança

Na base de dados SQL do Azure, a camada de Abstração de plataforma de SQL (SQLPAL) fornece isolamento de processos externos. Esse isolamento proporciona uma camada extra de segurança para executar R scripts.

## <a name="next-steps"></a>Passos Seguintes

- Consulte a [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics) documentação para obter informações gerais
- Para saber como utilizar o Machine Learning Services (com R) na base de dados do Azure SQL, veja [guia de início rápido](sql-database-connect-query-r.md).
- Saiba mais com [tutoriais da linguagem R do SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)