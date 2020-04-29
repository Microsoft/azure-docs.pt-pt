---
title: Principais diferenças para serviços de aprendizagem automática (pré-visualização)
description: Este tópico descreve diferenças-chave entre os Serviços de Machine Learning machine learning da Azure SQL (com R) e os Serviços de Machine Learning do Servidor SQL.
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
ms.date: 11/20/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 34ba75b6126024c9cd43d6fe474f7c1b62dd990f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81453154"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Principais diferenças entre serviços de aprendizagem automática na Base de Dados SQL Azure (pré-visualização) e SQL Server

A funcionalidade dos Serviços de Machine Learning (com R) da Base de Dados Azure SQL (com R) em (pré-visualização) é semelhante aos [Serviços de Aprendizagem automática do Servidor SQL](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Abaixo estão algumas diferenças fundamentais.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="language-support"></a>Suporte de idiomas

O SQL Server tem suporte para R e Python através da estrutura de [extensibility](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). A Base de Dados SQL não suporta ambas as línguas. As principais diferenças são:

- R é o único idioma suportado na Base de Dados SQL. Ainda não há suporte para Python neste momento.
- A versão R é 3.4.4.
- Não há necessidade de `external scripts enabled` `sp_configure`configurar via .

## <a name="package-management"></a>Gestão de pacotes

A gestão e instalação de pacotes R funcionam diferentes entre a Base de Dados SQL e o Servidor SQL. Estas diferenças são:

- As embalagens R são instaladas através [de sqlmlutils](https://github.com/Microsoft/sqlmlutils) ou [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Os pacotes não podem realizar chamadas de rede de saída. Esta limitação é semelhante às [regras de firewall padrão para serviços](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) de aprendizagem automática no Servidor SQL, mas não pode ser alterada na Base de Dados SQL.
- Não existe suporte para pacotes que dependam de tempos de execução externos (como Java) ou que precisem de acesso às APIs dos OS para instalação ou utilização.

## <a name="writing-to-a-temporary-table"></a>Escrever para uma mesa temporária

Se estiver a usar o RODBC na Base de Dados Azure SQL, então não pode escrever `sp_execute_external_script` para uma mesa temporária, seja ela criada dentro ou fora da sessão. A suver é utilizar [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) e [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (com overwrite=FALSE e append="rows") `sp_execute_external_script` para escrever para uma tabela temporária global criada antes da consulta.

## <a name="resource-governance"></a>Governação de recursos

Não é possível limitar os recursos R através do Governador de [Recursos](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) e dos recursos externos.

Durante a pré-visualização pública, os recursos R são definidos para um máximo de 20% dos recursos da Base de Dados SQL, e dependem do nível de serviço que escolher. Para mais informações, consulte os modelos de compra da Base de [Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).
### <a name="insufficient-memory-error"></a>Erro de memória insuficiente

Se não houver memória suficiente disponível para R, receberá uma mensagem de erro. As mensagens de erro comuns são:

- Incapaz de comunicar com o tempo de execução para o script 'R' para id pedido: ********* Verifique os requisitos do tempo de execução 'R'
- Erro de script 'R' ocorreu durante a execução de 'sp_execute_external_script' com HRESULT 0x80004004. ... ocorreu um erro de script externo: ".. não podia atribuir memória (0 Mb) na função C 'R_AllocStringBuffer'"
- Ocorreu um erro de script externo: Erro: não pode alocar vetor de tamanho.

O uso da memória depende da quantidade utilizada nos seus scripts R e do número de consultas paralelas executadas. Se receber os erros acima, pode escalar a sua base de dados para um nível de serviço mais elevado para resolver isto.

## <a name="next-steps"></a>Passos seguintes

- Consulte a visão geral, Serviços de Machine Learning de Base de [Dados Azure SQL com R (pré-visualização)](sql-database-machine-learning-services-overview.md).
- Para aprender a usar R para consultar o Azure SQL Database Machine Learning Services (pré-visualização), consulte o [guia Quickstart](sql-database-connect-query-r.md).
- Para começar com alguns scripts R simples, consulte [Create e execute scripts R simples em Serviços de Machine Learning de Base de Dados Azure SQL (pré-visualização)](sql-database-quickstart-r-create-script.md).
