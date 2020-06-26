---
title: Principais diferenças para serviços de aprendizagem automática (pré-visualização)
description: Este tópico descreve diferenças fundamentais entre os serviços de aprendizagem automática de máquinas de base de dados Azure SQL (com R) e os Serviços de Aprendizagem automática de máquinas do servidor SQL SQL.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a8f5258c4b630c7d101db56587f677e3366dcd05
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85413098"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Principais diferenças entre serviços de aprendizagem automática na Base de Dados Azure SQL (pré-visualização) e SQL Server
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A funcionalidade do Azure SQL Database Machine Learning Services (com R) in (pré-visualização) é semelhante aos Serviços de [Aprendizagem de Máquinas de Servidor SQL SQL](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Abaixo estão algumas diferenças fundamentais.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="language-support"></a>Suporte de idiomas

O SQL Server tem suporte para R e Python através da [estrutura de extensibilidade](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). A BASE de Dados SQL não suporta ambos os idiomas. As principais diferenças são:

- R é o único idioma suportado na Base de Dados SQL. Ainda não há suporte para Python neste momento.
- A versão R é 3.4.4.
- Não há necessidade de configurar `external scripts enabled` via `sp_configure` .

## <a name="package-management"></a>Gestão de pacotes

Trabalho de gestão e instalação de pacotes R diferente entre a Base de Dados SQL e o SQL Server. Estas diferenças são:

- As embalagens R são instaladas através [de sqlmlutils](https://github.com/Microsoft/sqlmlutils) ou [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Os pacotes não podem realizar chamadas de rede de saída. Esta limitação é semelhante às [regras de firewall padrão para serviços de aprendizagem automática](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) no SQL Server, mas não pode ser alterada na Base de Dados SQL.
- Não existe suporte para pacotes que dependam de tempos de execução externos (como Java) ou que necessitem de acesso a APIs de OS para instalação ou utilização.

## <a name="writing-to-a-temporary-table"></a>Escrevendo para uma mesa temporária

Se estiver a usar o RODBC na Base de Dados Azure SQL, então não pode escrever para uma mesa temporária, quer seja criada dentro ou fora da `sp_execute_external_script` sessão. A solução é utilizar [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) e [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (com overwrite=FALSE e apend="rows") para escrever para uma tabela temporária global criada antes da `sp_execute_external_script` consulta.

## <a name="resource-governance"></a>Governação de recursos

Não é possível limitar os recursos R através [do Governador de Recursos](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) e dos pools de recursos externos.

Durante a pré-visualização pública, os recursos R estão definidos para um máximo de 20% dos recursos da Base de Dados SQL, e dependem do nível de serviço que escolher. Para mais informações, consulte [os modelos de compra da Base de Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).
### <a name="insufficient-memory-error"></a>Erro de memória insuficiente

Se não houver memória suficiente disponível para R, receberá uma mensagem de erro. As mensagens de erro comuns são:

- Incapaz de comunicar com o tempo de execução do script 'R' para id pedido: ********* Por favor, verifique os requisitos do tempo de execução 'R'
- Erro de script 'R' ocorreu durante a execução de 'sp_execute_external_script' com HRESULT 0x80004004. ... ocorreu um erro de script externo: ". não podia atribuir memória (0 Mb) na função C 'R_AllocStringBuffer'"
- Ocorreu um erro de script externo: Erro: não é possível atribuir vetor de tamanho.

O uso da memória depende de quanto é usado nos seus scripts R e do número de consultas paralelas que estão a ser executadas. Se receber os erros acima, pode escalar a sua base de dados para um nível de serviço mais elevado para resolver isto.

## <a name="next-steps"></a>Passos seguintes

- Consulte a visão geral, [Azure SQL Database Machine Learning Services com R (pré-visualização)](machine-learning-services-overview.md).
- Para aprender a usar R para consultar os Serviços de Aprendizagem de Máquinas de Base de Dados Azure SQL (pré-visualização), consulte o [guia Quickstart](connect-query-r.md).
- Para começar com alguns scripts R simples, consulte [Criar e executar scripts R simples em Azure SQL Database Machine Learning Services (pré-visualização)](r-script-create-quickstart.md).
