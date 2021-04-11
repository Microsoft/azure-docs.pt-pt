---
title: 'Quickstart: criar um grupo básico de servidores de nível - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL'
description: Começa com a Base de Dados Azure para o nível básico de hiperescala pós-SQL (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 04/07/2021
ms.openlocfilehash: 07ace217e5299662a1c3145a225abc25f4f1f337
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024097"
---
# <a name="create-a-hyperscale-citus-basic-tier-server-group-in-the-azure-portal"></a>Criar um grupo de servidor de nível básico de Hiperescala (Citus) no portal Azure

A Azure Database for PostgreSQL - Hyperscale (Citus) é um serviço gerido que utiliza para executar, gerir e escalar bases de dados postgreSQL altamente disponíveis na nuvem. O seu [nível básico](concepts-hyperscale-tiers.md) é uma opção de implementação conveniente para o desenvolvimento e teste inicial.

Este quickstart mostra-lhe como criar um grupo de servidor de nível básico de Hiperescala (Citus) utilizando o portal Azure. Irá providenciar o grupo de servidores e verificar se pode ligar-se a ele para executar consultas.

> [!IMPORTANT]
> O nível básico de Hiperescala (Citus) está atualmente em pré-visualização.  Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
>
> Pode ver uma lista completa de outras novidades em [funcionalidades de pré-visualização para Hyperscale (Citus)](hyperscale-preview-features.md).

[!INCLUDE [azure-postgresql-hyperscale-create-basic-tier](../../includes/azure-postgresql-hyperscale-create-basic-tier.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a providenciar um grupo de servidores Hyperscale (Citus). Ligou-se a ele com o PSQL, criou um esquema e distribuiu dados.

- Siga um tutorial para [construir aplicações escaláveis de multi-inquilinos](./tutorial-design-database-hyperscale-multi-tenant.md)
- Determine o melhor [tamanho inicial](howto-hyperscale-scale-initial.md) para o seu grupo de servidor
