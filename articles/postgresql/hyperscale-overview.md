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
ms.openlocfilehash: 1734128384d63749d3c777cf6315278fced9d140
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025145"
---
# <a name="what-is-azure-database-for-postgresql---hyperscale-citus"></a>O que é Azure Database para PostgreSQL - Hiperescala (Citus)?

Azure Database for PostgreSQL é um serviço de base de dados relacional na nuvem da Microsoft construída para desenvolvedores. Baseia-se na versão comunitária do motor de base de [dados PostgreSQL](https://www.postgresql.org/) de código aberto.

Hiperescala (Citus) é uma opção de implementação que escala horizontalmente as consultas em várias máquinas usando o fragmento. O seu motor de consulta paraleliza consultas SQL de entrada nestes servidores para obter respostas mais rápidas em grandes conjuntos de dados. Serve aplicações que requerem maior escala e desempenho do que outras opções de implantação: geralmente cargas de trabalho que se aproximam -- ou já ultrapassam --100 GB de dados.

Hiperescala (Citus) entrega:

- Dimensionamento horizontal em múltiplos computadores através da fragmentação
- Paralelização de consultas nestes servidores para obter respostas mais rápidas em grandes conjuntos de dados
- Excelente suporte para aplicações multi-inquilinos, análise operacional em tempo real e elevadas cargas de trabalho transacionais de produção

As aplicações construídas para PostgreSQL podem executar consultas distribuídas em Hiperescala (Citus) com bibliotecas de [conexão](./concepts-connection-libraries.md) padrão e alterações mínimas.

## <a name="next-steps"></a>Próximos passos

- Iniciou-se [criando o seu primeiro](./quickstart-create-hyperscale-portal.md) grupo de servidores Azure Database for PostgreSQL - Hyperscale (Citus).
- Consulte [a página de preços](https://azure.microsoft.com/pricing/details/postgresql/) para comparar custos e calculadoras. Hyperscale (Citus) também oferece descontos pré-pagos de Instância Reservada, consulte as páginas [de preços de RI hyperscale (Citus)](concepts-hyperscale-reserved-pricing.md) para mais detalhes.
- Determine o melhor [tamanho inicial](howto-hyperscale-scale-initial.md) para o seu grupo de servidor
