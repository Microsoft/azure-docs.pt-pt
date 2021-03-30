---
title: 'Questões conhecidas: Migrar de MongoDB para Azure CosmosDB'
titleSuffix: Azure Database Migration Service
description: Conheça questões conhecidas e limitações migratórias com migrações de MongoDB para Azure Cosmos DB usando o Serviço de Migração da Base de Dados Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/27/2020
ms.openlocfilehash: be6d9d3b8a20e11c874234baae26629ce0187e4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91291815"
---
# <a name="known-issuesmigration-limitations-with-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Questões conhecidas/limitações de migração com migrações de MongoDB para Azure Cosmos DB's API para MongoDB

Questões e limitações conhecidas associadas às migrações de MongoDB para a API da Cosmos DB para o MongoDB são descritas nas seguintes secções.

## <a name="migration-fails-as-a-result-of-using-the-incorrect-ssl-cert"></a>A migração falha em resultado da utilização do Cert SSL incorreto

* **Sintoma**: Este problema é evidente quando um utilizador não pode ligar-se ao servidor de origem MongoDB. Apesar de ter todas as portas de firewall abertas, o utilizador ainda não consegue ligar-se.

| Causa         | Resolução |
| ------------- | ------------- |
| A utilização de um certificado auto-assinado no Serviço de Migração da Base de Dados Azure pode levar à falha da migração devido ao cert SSL incorreto. A mensagem Error pode incluir "O certificado remoto é inválido de acordo com o procedimento de validação." | Use um certificado genuíno da AC.  Os certificados auto-assinados são geralmente utilizados apenas em testes internos. Quando instala um certificado genuíno a partir de uma autoridade ca, pode então utilizar o SSL no Serviço de Migração da Base de Dados Azure sem problemas (ligações à Cosmos DB usam SSL sobre a Mongo API).<br><br> |

## <a name="unable-to-get-the-list-of-databases-to-map-in-dms"></a>Incapaz de obter a lista de bases de dados para mapear em DMS

* **Sintoma**: Não é possível obter a lista DB na lâmina **de definição de base de dados** quando utilizar dados do modo **de armazenamento Azure** na lâmina de **origem Select.**

| Causa         | Resolução |
| ------------- | ------------- |
| O fio de ligação da conta de armazenamento está faltando a informação SAS e, portanto, não pode ser autenticado. | Crie o SAS no recipiente blob no Storage Explorer e utilize o URL com informações SAS do contentor como a cadeia de ligação de detalhes de origem.<br><br> |

## <a name="using-an-unsupported-version-of-the-database"></a>Usando uma versão não suportada da base de dados

* **Sintoma:** A migração falha.

| Causa         | Resolução |
| ------------- | ------------- |
| Tentas migrar para a Azure Cosmos DB a partir de uma versão não apoiada do MongoDB. | À medida que as novas versões do MongoDB são lançadas, são testadas para garantir compatibilidade com o Azure Database Migration Service, e o serviço está a ser atualizado periodicamente para aceitar as versões mais recentes. Se houver uma necessidade imediata de migrar, como uma solução alternativa pode exportar as bases de dados/coleções para o Azure Storage e, em seguida, apontar a fonte para o despejo resultante. Crie o SAS no recipiente blob no Storage Explorer e, em seguida, utilize o URL com informações SAS do contentor como a cadeia de ligação de detalhes de origem.<br><br> |

## <a name="next-steps"></a>Passos seguintes

* Veja o tutorial [Migrate MongoDB para a API da Azure Cosmos DB para a MongoDB online usando DMS](tutorial-mongodb-cosmos-db-online.md).
* Veja o tutorial [Migrate MongoDB para a API da Azure Cosmos para MongoDB offline usando DMS](tutorial-mongodb-cosmos-db.md).