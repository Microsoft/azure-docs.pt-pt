---
title: O que é o Azure Database Migration Service?
description: Visão geral do Azure Database Migration Service, que fornece migrações perfeitas de muitas fontes de base de dados para plataformas de dados Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.topic: overview
ms.date: 02/20/2020
ms.openlocfilehash: 328c29afee3752ecb11b83f22d67f20aa3a2c93e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94963016"
---
# <a name="what-is-azure-database-migration-service"></a>O que é o Azure Database Migration Service?

O Azure Database Migration Service é um serviço totalmente gerido projetado para permitir migrações sem emenda de múltiplas fontes de base de dados para plataformas de dados Azure com tempo de inatividade mínimo (migrações online).

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>Migrar bases de dados para Azure com ferramentas familiares

O Azure Database Migration Service integra algumas das funcionalidades das nossas ferramentas e serviços existentes. Fornece aos clientes uma solução abrangente e altamente disponível. O serviço utiliza o [Assistente de Migração de Dados](/sql/dma/dma-overview) para gerar relatórios de avaliação que fornecem recomendações para o orientar através das alterações necessárias antes da realização de uma migração. Cabe-lhe a si fazer qualquer remediação necessária. Quando estiver pronto para iniciar o processo de migração, o Azure Database Migration Service executa todas as etapas necessárias. Pode iniciar os seus projetos de migração e deixá-los avançar autonomamente sem preocupações ao saber que o processo tira partido das melhores práticas determinadas pela Microsoft. 

> [!NOTE]
> A utilização do Azure Database Migration Service para realizar uma migração online requer a criação de um caso baseado no nível de preços Premium.

## <a name="regional-availability"></a>Disponibilidade regional

Para obter informações atualizadas sobre a disponibilidade regional do Serviço de Migração da Base de Dados Azure, consulte [os Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration)

## <a name="pricing"></a>Preços

Para obter informações atualizadas sobre os preços do Serviço de Migração da Base de Dados [Azure, consulte os preços do Serviço de Migração da Base de Dados Azure](https://azure.microsoft.com/pricing/details/database-migration/).

## <a name="next-steps"></a>Passos seguintes

* [Estado dos cenários de migração suportados pelo Azure Database Migration Service](resource-scenario-status.md).
* [Criar uma instância do Serviço de Migração da Base de Dados Azure utilizando o portal Azure](quickstart-create-data-migration-service-portal.md).
* [Migrar o SqL Server para a Base de Dados Azure SQL](tutorial-sql-server-to-azure-sql.md).
* [Visão geral dos pré-requisitos para a utilização do Serviço de Migração da Base de Dados Azure](pre-reqs.md).
* [FAQ sobre a utilização do Serviço de Migração da Base de Dados Azure.](faq.md)
* [Serviços e ferramentas disponíveis para cenários de migração de dados.](dms-tools-matrix.md)