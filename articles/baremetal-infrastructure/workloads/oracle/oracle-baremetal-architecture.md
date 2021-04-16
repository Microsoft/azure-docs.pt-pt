---
title: Arquitetura da Infraestrutura BareMetal para Oráculo
description: Conheça a arquitetura de várias configurações da Infraestrutura BareMetal para o Oráculo.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 1bdc32c14cfc8986c52a4ea916a6130ee29e6028
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559297"
---
# <a name="architecture-of-baremetal-infrastructure-for-oracle"></a>Arquitetura da Infraestrutura BareMetal para Oráculo

Neste artigo, vamos analisar as opções arquitetónicas da Infraestrutura BareMetal para a Oráculo e as características que cada um suporta.

## <a name="single-instance"></a>Instância única

Esta topologia suporta uma única instância da Oracle Database com a Oracle Data Guard para migrar para a Infraestrutura BareMetal. Suporta a utilização de nó de espera para trabalhos de elevada disponibilidade e manutenção.

[![Diagrama mostrando a arquitetura de uma única instância da Oracle Database com Oracle Data Guard.](media/oracle-baremetal-architecture/single-instance-architecture.png)](media/oracle-baremetal-architecture/single-instance-architecture.png#lightbox)

## <a name="oracle-real-application-clusters-rac-one-node"></a>Oracle Real Application Clusters (RAC) Um nó

Esta topologia suporta uma configuração RAC com armazenamento partilhado e cluster GRID. As instâncias da base de dados funcionam apenas num nó (configuração activa-passiva).

As funcionalidades incluem:

- Active-passiva com nó Um Oracle RAC

    - Falha automática

    - Reinicie rapidamente no segundo nó

- Falha em tempo real e escalabilidade com o Oracle RAC

- Manutenção de rolamento zero tempo de inatividade

[![Diagrama mostrando a arquitetura de uma configuração ativa-passiva do Nó Oracle RAC One.](media/oracle-baremetal-architecture/one-node-rac-architecture.png)](media/oracle-baremetal-architecture/one-node-rac-architecture.png#lightbox)

## <a name="rac"></a>RAC

Esta topologia suporta uma configuração Oracle RAC com armazenamento partilhado e cluster grid, enquanto várias instâncias por base de dados são executadas simultaneamente (configuração ativa ativa).

- O desempenho é fácil de escalar através do fornecimento on-line de servidores adicionados. 
-  Os utilizadores estão ativos em todos os servidores e todos os servidores partilham o acesso à mesma Base de Dados do Oracle. 
-  Todos os tipos de manutenção da base de dados podem ser realizados online ou em moda rolante para um tempo mínimo ou zero. 
- Os sistemas de espera Oracle Ative Data Guard (ADG) podem servir facilmente um duplo propósito como sistemas de teste. 

Esta configuração permite testar todas as alterações numa cópia exata da base de dados de produção antes de serem aplicadas ao ambiente de produção.

> [!NOTE]
> Se pretender utilizar o Ative Data Guard Far Sync (modo sincronizado), terá de considerar as zonas regionais onde esta funcionalidade é suportada. Apenas para regiões geográficas distribuídas, recomendamos a utilização da Data Guard com modo assíncrona.

[![Diagrama mostrando a arquitetura de uma configuração ativa do Oracle RAC.](media/oracle-baremetal-architecture/rac-architecture.png)](media/oracle-baremetal-architecture/rac-architecture.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o fornecimento dos seus casos BareMetal para cargas de trabalho da Oracle.

> [!div class="nextstepaction"]
> [Provisão De Infraestruturas BareMetal para oráculo](oracle-baremetal-provision.md)

