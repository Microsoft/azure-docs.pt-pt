---
title: Principais diferenças para Serviços de Machine Learning (versão prévia)
description: Este tópico descreve as principais diferenças entre o Serviços de Machine Learning do banco de dados SQL do Azure (com R) e SQL Server Serviços de Machine Learning.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 1397f5d81ddf63740d733111b965a0517a2b917f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827474"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Principais diferenças entre Serviços de Machine Learning no banco de dados SQL do Azure (visualização) e SQL Server

A funcionalidade do banco de dados SQL do Azure Serviços de Machine Learning (com R) no (visualização) é semelhante ao [SQL Server serviços de Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Abaixo estão algumas diferenças importantes.

> [!IMPORTANT]
> O banco de dados SQL do Azure Serviços de Machine Learning está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="language-support"></a>Suporte de idiomas

SQL Server tem suporte para R e Python por meio da [estrutura de extensibilidade](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). O banco de dados SQL não dá suporte a ambos os idiomas. As principais diferenças são:

- R é o único idioma com suporte no banco de dados SQL. Ainda não há suporte para Python neste momento.
- A versão do R é 3.4.4.
- Não é necessário configurar `external scripts enabled` via `sp_configure`. Depois de se [inscrever](sql-database-machine-learning-services-overview.md#signup), o aprendizado de máquina está habilitado para o banco de dados SQL.

## <a name="package-management"></a>Gestão de pacotes

O gerenciamento e a instalação de pacotes de R funcionam diferentes entre o banco de dados SQL e o SQL Server. Essas diferenças são:

- Os pacotes do R são instalados por meio do [sqlmlutils](https://github.com/Microsoft/sqlmlutils) ou [criam uma biblioteca externa](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Os pacotes não podem executar chamadas de rede de saída. Essa limitação é semelhante às [regras de firewall padrão para serviços de Machine Learning](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) em SQL Server, mas não pode ser alterada no banco de dados SQL.
- Não há suporte para pacotes que dependem de tempos de execução externos (como Java) ou que precisam de acesso a APIs do sistema operacional para instalação ou uso.

## <a name="writing-to-a-temporary-table"></a>Gravando em uma tabela temporária

Se você estiver usando o RODBC no banco de dados SQL do Azure, não poderá gravar em uma tabela temporária, seja ela criada dentro ou fora da sessão de `sp_execute_external_script`. A solução alternativa é usar [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) e [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (with overwrite = false e Append = "Rows") para gravar em uma tabela temporária global criada antes da consulta de `sp_execute_external_script`.

## <a name="resource-governance"></a>Governação de recursos

Não é possível limitar os recursos de R por meio de [resource governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) e pools de recursos externos.

Durante a visualização pública, os recursos do R são definidos como um máximo de 20% dos recursos do banco de dados SQL e dependem da camada de serviço que você escolher. Para obter mais informações, consulte [modelos de compra do banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).
### <a name="insufficient-memory-error"></a>Erro de memória insuficiente

Se não houver memória suficiente disponível para o R, você receberá uma mensagem de erro. As mensagens de erro comuns são:

- Não é possível se comunicar com o tempo de execução para o script ' R ' para a ID de solicitação: * * * * * * *. Verifique os requisitos do tempo de execução ' R '
- Ocorreu um erro de script ' R ' durante a execução de ' sp_execute_external_script ' com HRESULT 0x80004004. ... ocorreu um erro de script externo: ".. Não foi possível alocar memória (0 MB) na função C ' R_AllocStringBuffer ' "
- Ocorreu um erro de script externo: erro: não é possível alocar vetor de tamanho.

O uso de memória depende de quanto é usado em seus scripts R e o número de consultas paralelas sendo executadas. Se você receber os erros acima, poderá dimensionar seu banco de dados para uma camada de serviço superior para resolver isso.

## <a name="next-steps"></a>Passos seguintes

- Consulte a visão geral, [banco de dados SQL do Azure serviços de Machine Learning com R (versão prévia)](sql-database-machine-learning-services-overview.md).
- Para saber como usar o R para consultar o Serviços de Machine Learning do banco de dados SQL do Azure (versão prévia), consulte o [Guia de início rápido](sql-database-connect-query-r.md).
- Para começar com alguns scripts R simples, consulte [criar e executar scripts r simples no banco de dados SQL do Azure serviços de Machine Learning (versão prévia)](sql-database-quickstart-r-create-script.md).
