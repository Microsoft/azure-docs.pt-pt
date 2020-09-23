---
title: Visão geral da Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
description: Fornece uma visão geral da opção de implantação de Hiperescala (Citus)
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 09/01/2020
ms.openlocfilehash: e62d1bf0e9db5e80193cb0615d0a9d31e3041d63
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947555"
---
# <a name="what-is-azure-database-for-postgresql---hyperscale-citus"></a>O que é Azure Database para PostgreSQL - Hiperescala (Citus)?

Azure Database for PostgreSQL é um serviço de base de dados relacional na nuvem da Microsoft construída para desenvolvedores. Baseia-se na versão comunitária do motor de base de [dados PostgreSQL](https://www.postgresql.org/) de código aberto.

Hiperescala (Citus) é uma opção de implementação que escala horizontalmente as consultas em várias máquinas usando o fragmento. O seu motor de consulta paraleliza as consultas SQL recebidas nestes servidores para respostas mais rápidas em grandes conjuntos de dados. Serve aplicações que requerem maior escala e desempenho do que outras opções de implantação: geralmente cargas de trabalho que se aproximam -- ou já ultrapassam --100 GB de dados.

Hiperescala (Citus) entrega:

- Escala horizontal em várias máquinas usando o fragmento
- Paralelização de consultas através destes servidores para respostas mais rápidas em grandes conjuntos de dados
- Excelente suporte para aplicações multi-inquilinos, análise operacional em tempo real e elevadas cargas de trabalho transacionais de produção

As aplicações construídas para PostgreSQL podem executar consultas distribuídas em Hiperescala (Citus) com bibliotecas de [conexão](./concepts-connection-libraries.md) padrão e alterações mínimas.

## <a name="next-steps"></a>Passos seguintes

- Iniciou-se [criando o seu primeiro](./quickstart-create-hyperscale-portal.md) grupo de servidores Azure Database for PostgreSQL - Hyperscale (Citus).
- Consulte [a página de preços](https://azure.microsoft.com/pricing/details/postgresql/) para comparar custos e calculadoras. A Hyperscale (Citus) também oferece descontos pré-pagos de Instância Reservada, consulte as páginas [de preços do Hyperscale RI](concepts-hyperscale-reserved-pricing.md) para obter mais detalhes.
- Determine o melhor [tamanho inicial](howto-hyperscale-scaling.md#picking-initial-size) para o seu grupo de servidor
