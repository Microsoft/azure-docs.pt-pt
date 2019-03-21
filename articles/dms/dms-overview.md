---
title: Descrição geral do serviço de migração da base de dados do Azure | Documentos da Microsoft
description: Descrição geral do serviço de migração de base de dados do Azure, que fornece migrações totalmente integradas de várias origens de base de dados para plataformas de dados do Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 38bce2316110259298ff478d6019b33c78a53d21
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182834"
---
# <a name="what-is-the-azure-database-migration-service"></a>O que é o serviço de migração de base de dados do Azure?
O serviço de migração de base de dados do Azure é um serviço completamente gerido criado para permitir migrações totalmente integradas de várias origens de base de dados para plataformas de dados do Azure com o período de indisponibilidade mínimo (migrações online).

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>Migrar bases de dados para o Azure com ferramentas familiares
O serviço de migração de base de dados do Azure integra-se algumas das funcionalidades das nossas ferramentas e serviços existentes. Fornece aos clientes com uma solução abrangente e de elevada disponibilidade. O serviço utiliza a [Assistente de migração de dados](https://aka.ms/dma) para gerar relatórios de avaliação que fornecem recomendações para o orientar as alterações necessárias antes de efetuar uma migração. Cabe-lhe para efetuar quaisquer atualizações necessárias. Quando estiver preparado para iniciar o processo de migração, o Azure Database Migration Service realiza todos os passos necessários. Pode disparar e esquecer seus projetos de migração com tranquilidade, sabendo que o processo tira partido das melhores práticas, conforme determinado pela Microsoft.

> [!NOTE]
> Utilizar o serviço de migração de base de dados do Azure para efetuar uma migração online requer a criação de uma instância com base no escalão de preço Premium.

## <a name="regional-availability"></a>Disponibilidade regional
Para obter informações atualizadas sobre a disponibilidade regional do serviço de migração de base de dados do Azure, veja [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration).

## <a name="pricing"></a>Preços
Para obter informações atualizadas sobre os preços do serviço de migração de base de dados do Azure, veja [preços do Azure Database Migration Service](https://azure.microsoft.com/pricing/details/database-migration/).

## <a name="next-steps"></a>Passos Seguintes
- [Estado de cenários de migração suportados pelo serviço de migração de base de dados do Azure](resource-scenario-status.md).
- [Criar uma instância do serviço de migração de base de dados do Azure com o portal do Azure](quickstart-create-data-migration-service-portal.md).
- [Migrar o SQL Server para a base de dados SQL do Azure](tutorial-sql-server-to-azure-sql.md).
- [Descrição geral da pré-requisitos para utilizar o Azure Database Migration Service](pre-reqs.md).
- [FAQ sobre como utilizar o Azure Database Migration Service](faq.md).
- [Serviços e ferramentas disponíveis para cenários de migração de dados](dms-tools-matrix.md).
