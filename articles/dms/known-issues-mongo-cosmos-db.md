---
title: 'Questões conhecidas: Migrar de MongoDB para Azure CosmosDB'
titleSuffix: Azure Database Migration Service
description: Conheça questões conhecidas e limitações de migração com migrações de MongoDB para Azure Cosmos DB utilizando o Serviço de Migração de Bases de Dados Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: 194da036260a78b27748dfc7f755212ab4f30b1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78256035"
---
# <a name="known-issuesmigration-limitations-with-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Questões conhecidas/limitações de migração com migrações de MongoDB para API da Azure Cosmos DB para MongoDB

Questões e limitações conhecidas associadas às migrações de MongoDB para API da Cosmos DB para MongoDB são descritas nas seguintes secções.

## <a name="migration-fails-as-a-result-of-using-the-incorrect-ssl-cert"></a>Migração falha como resultado da utilização do SSL Cert incorreto

* **Sintoma**: Este problema é aparente quando um utilizador não pode ligar-se ao servidor de origem MongoDB. Apesar de ter todas as portas de firewall abertas, o utilizador ainda não consegue ligar.

| Causa         | Resolução |
| ------------- | ------------- |
| A utilização de um certificado auto-assinado no Serviço de Migração de Bases de Dados Azure pode levar à falha da migração devido ao SSL Cert incorreto. A mensagem Error pode incluir "O certificado remoto é inválido de acordo com o procedimento de validação." | Use um certificado genuíno da AC.  Certs auto-assinados geralmente são usados apenas em testes internos. Quando instala um certificado genuíno de uma autoridade ca, pode então utilizar o SSL no Serviço de Migração de Bases de Dados Azure sem problemas (as ligações à Cosmos DB usam SSL sobre a API mongo).<br><br> |

## <a name="unable-to-get-the-list-of-databases-to-map-in-dms"></a>Incapaz de obter a lista de bases de dados para mapear em DMS

* **Sintoma**: Não conseguir obter a lista DB na lâmina de **definição da base** de dados ao utilizar dados do modo de **armazenamento Azure** na lâmina **de origem Select.**

| Causa         | Resolução |
| ------------- | ------------- |
| Falta a cadeia de ligação à conta de armazenamento a informação SAS e, portanto, não pode ser autenticada. | Crie o SAS no recipiente de bolhas no Storage Explorer e utilize o URL com informações SAS do recipiente como cadeia de ligação de detalhes de origem.<br><br> |

## <a name="using-an-unsupported-version-of-the-database"></a>Usando uma versão não suportada da base de dados

* **Sintoma:** A migração falha.

| Causa         | Resolução |
| ------------- | ------------- |
| Tentamigrar para o Azure Cosmos DB a partir de uma versão não suportada do MongoDB. | À medida que são lançadas novas versões do MongoDB, são testadas para garantir a compatibilidade com o Serviço de Migração da Base de Dados Azure, e o serviço está a ser atualizado periodicamente para aceitar as versões mais recentes. Se houver necessidade imediata de migrar, como uma salieste pode exportar as bases de dados/coleções para o Armazenamento Azure e o ponto de vista da fonte para o despejo resultante. Crie o SAS no recipiente de bolhas no Storage Explorer e, em seguida, utilize o URL com informações SAS do recipiente como cadeia de ligação de detalhes de origem.<br><br> |

## <a name="next-steps"></a>Passos seguintes

* Veja o tutorial [Migrate MongoDB para API da Azure Cosmos DB para MongoDB online usando DMS](tutorial-mongodb-cosmos-db-online.md).
* Veja o tutorial [Migrate MongoDB para a API da Azure Cosmos DB para MongoDB offline usando DMS](tutorial-mongodb-cosmos-db.md).