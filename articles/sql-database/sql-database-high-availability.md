---
title: Alta disponibilidade-serviço de banco de dados SQL do Azure | Microsoft Docs
description: Conheça os recursos e recursos de alta disponibilidade do serviço de banco de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sashan
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 10/14/2019
ms.openlocfilehash: 28b702192b41d3b4a8151e3127a4297c28712fa2
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390706"
---
# <a name="high-availability-and-azure-sql-database"></a>Alta disponibilidade e banco de dados SQL do Azure

O objetivo da arquitetura de alta disponibilidade no banco de dados SQL do Azure é garantir que seu banco de dados esteja em execução em 99,99% do tempo, sem se preocupar com o impacto das operações de manutenção e interrupções. O Azure manipula automaticamente tarefas de manutenção críticas, como aplicação de patches, backups, atualizações do Windows e do SQL, bem como eventos não planejados, como hardware subjacente, software ou falhas de rede.  Quando a instância subjacente do SQL for corrigida ou passar por failover, o tempo de inatividade não será perceptível se você [empregar a lógica de repetição](sql-database-develop-overview.md#resiliency) em seu aplicativo. O banco de dados SQL do Azure pode recuperar-se rapidamente, mesmo nas circunstâncias mais críticas, garantindo que eles estejam sempre disponíveis.

A solução de alta disponibilidade foi projetada para garantir que os dados confirmados nunca sejam perdidos devido a falhas, que as operações de manutenção não afetam sua carga de trabalho e que o banco de dados não será um ponto único de falha em sua arquitetura de software. Não há nenhuma janela de manutenção ou tempo de inatividade que exija a interrupção da carga de trabalho enquanto o banco de dados é atualizado ou mantido. 

Há dois modelos arquitetônicos de alta disponibilidade que são usados no banco de dados SQL do Azure:

- Modelo de disponibilidade padrão baseado em uma separação de computação e armazenamento.  Ele se baseia na alta disponibilidade e na confiabilidade da camada de armazenamento remoto. Essa arquitetura destina-se a aplicativos de negócios orientados a orçamento que podem tolerar alguma degradação de desempenho durante atividades de manutenção.
- Modelo de disponibilidade Premium baseado em um cluster de processos do mecanismo de banco de dados. Ele depende do fato de que sempre há um quorum de nós do mecanismo de banco de dados disponíveis. Essa arquitetura destina-se a aplicativos de missão crítica com alto desempenho de e/s, alta taxa de transações e garante o impacto mínimo no desempenho da carga de trabalho durante atividades de manutenção.

O banco de dados SQL do Azure é executado na versão estável mais recente do SQL Server Mecanismo de Banco de Dados e no sistema operacional Windows, e a maioria dos usuários não observou que as atualizações são executadas continuamente.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Disponibilidade da camada de serviço básica, Standard e Uso Geral

Essas camadas de serviço aproveitam a arquitetura de disponibilidade padrão. A figura a seguir mostra quatro nós diferentes com a computação separada e as camadas de armazenamento.

![Separação de computação e armazenamento](media/sql-database-high-availability/general-purpose-service-tier.png)

O modelo de disponibilidade padrão inclui duas camadas:

- Uma camada de computação sem monitoração de estado que executa o processo `sqlservr.exe` e contém somente dados transitórios e em cache, como TempDB, bancos de dado modelo no SSD anexado e cache de planos, pool de buffers e pool columnstore na memória. Esse nó sem estado é operado pelo Service Fabric do Azure que inicializa `sqlservr.exe`, controla a integridade do nó e executa o failover para outro nó, se necessário.
- Uma camada de dados com monitoração de estado com os arquivos de banco (. MDF/. ldf) armazenados no armazenamento de BLOBs do Azure. O armazenamento de BLOBs do Azure tem recursos internos de redundância e disponibilidade de dados. Ele garante que todos os registros no arquivo de log ou na página do arquivo de dados serão preservados mesmo se SQL Server processo falhar.

Sempre que o mecanismo de banco de dados ou o sistema operacional for atualizado ou uma falha for detectada, o Azure Service Fabric moverá o processo de SQL Server sem estado para outro nó de computação sem estado com capacidade livre suficiente. Os dados no armazenamento de BLOBs do Azure não são afetados pela movimentação e os arquivos de dados/log são anexados ao processo de SQL Server inicializado recentemente. Esse processo garante a disponibilidade de 99,99%, mas uma carga de trabalho pesada pode enfrentar alguma degradação de desempenho durante a transição, uma vez que a nova instância de SQL Server começa com o cache frio.

## <a name="premium-and-business-critical-service-tier-availability"></a>Disponibilidade da camada de serviço Premium e Comercialmente Crítico

As camadas de serviço Premium e Comercialmente Crítico aproveitam o modelo de disponibilidade Premium, que integra recursos de computação (SQL Server processo de Mecanismo de Banco de Dados) e armazenamento (SSD anexado localmente) em um único nó. A alta disponibilidade é obtida com a replicação da computação e do armazenamento para nós adicionais, criando um cluster de três a quatro nós. 

![Cluster de nós do mecanismo de banco de dados](media/sql-database-high-availability/business-critical-service-tier.png)

Os arquivos de banco de dados subjacentes (. MDF/. ldf) são colocados no armazenamento SSD anexado para fornecer uma e/s de latência muito baixa para sua carga de trabalho. A alta disponibilidade é implementada usando uma tecnologia semelhante a SQL Server [Always on grupos de disponibilidade](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). O cluster inclui uma única réplica primária (SQL Server processo) que é acessível para cargas de trabalho do cliente de leitura/gravação e até três réplicas secundárias (computação e armazenamento) que contêm cópias de dados. O nó primário envia constantemente as alterações para os nós secundários e garante que os dados sejam sincronizados com pelo menos uma réplica secundária antes de confirmar cada transação. Esse processo garante que, se o nó primário falhar por algum motivo, sempre haverá um nó totalmente sincronizado para o failover. O failover é iniciado pelo Service Fabric do Azure. Depois que a réplica secundária se tornar o novo nó primário, outra réplica secundária será criada para garantir que o cluster tenha nós suficientes (conjunto de quorum). Depois que o failover for concluído, as conexões do SQL serão redirecionadas automaticamente para o novo nó primário.

Como um benefício extra, o modelo de disponibilidade Premium inclui a capacidade de redirecionar conexões SQL somente leitura para uma das réplicas secundárias. Esse recurso é chamado [de expansão de leitura](sql-database-read-scale-out.md). Ele fornece uma capacidade de computação adicional de 100% sem custo adicional para operações somente leitura fora do carregamento, como cargas de trabalho analíticas, da réplica primária.

## <a name="hyperscale-service-tier-availability"></a>Disponibilidade da camada de serviço de hiperescala

A arquitetura da camada de serviço de hiperescala é descrita em [arquitetura de funções distribuídas](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture). 

![Arquitetura funcional de hiperescala](./media/sql-database-hyperscale/hyperscale-architecture.png)

O modelo de disponibilidade em hiperescala inclui quatro camadas:

- Uma camada de computação sem estado que executa os processos `sqlservr.exe` e contém somente dados transitórios e armazenados em cache, como o cache RBPEX não abrangendo, TempDB, banco de dado modelo, etc. no SSD anexado, no cache de planos, no pool de buffers e no pool columnstore na memória. Essa camada sem estado inclui a réplica de computação primária e, opcionalmente, um número de réplicas de computação secundárias que podem servir como destinos de failover.
- Uma camada de armazenamento sem estado formada por servidores de página. Essa camada é o mecanismo de armazenamento distribuído para os processos `sqlservr.exe` em execução nas réplicas de computação. Cada servidor de página contém apenas dados transitórios e em cache, como cobrindo o cache RBPEX no SSD anexado e páginas de dados armazenadas em cache na memória. Cada servidor de página tem um servidor de páginas emparelhado em uma configuração ativo-ativo para fornecer balanceamento de carga, redundância e alta disponibilidade.
- Uma camada de armazenamento de log de transações com estado formada pelo nó de computação executando o processo do serviço de log, a zona de aterrissagem do log de transações e o armazenamento de longo prazo do log de transações. Zona de aterrissagem e armazenamento de longo prazo usam o armazenamento do Azure, que fornece disponibilidade e [redundância](https://docs.microsoft.com/azure/storage/common/storage-redundancy) para o log de transações, garantindo a durabilidade dos dados para transações confirmadas.
- Uma camada de armazenamento de dados com monitoração de estado com os arquivos (. MDF/. ndf) armazenados no armazenamento do Azure e são atualizados por servidores de página. Essa camada usa recursos de [redundância](https://docs.microsoft.com/azure/storage/common/storage-redundancy) e disponibilidade de dados do armazenamento do Azure. Ele garante que cada página em um arquivo de dados será preservada mesmo se os processos em outras camadas de falha de arquitetura de hiperescala ou se os nós de computação falharem.

Os nós de computação em todas as camadas de hiperescala são executados no Azure Service Fabric, que controla a integridade de cada nó e executa failovers para nós íntegros disponíveis, conforme necessário.

Para obter mais informações sobre alta disponibilidade em hiperescala, consulte [alta disponibilidade do banco de dados em hiperescala](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale).

## <a name="zone-redundant-configuration"></a>Configuração com redundância de zona

Por padrão, o cluster de nós para o modelo de disponibilidade Premium é criado no mesmo datacenter. Com a introdução do [zonas de disponibilidade do Azure](../availability-zones/az-overview.md), o banco de dados SQL pode posicionar réplicas diferentes do banco de dados comercialmente crítico em diferentes zonas de disponibilidade na mesma região. Para eliminar um ponto único de falha, o anel de controle também é duplicado em várias zonas como três anéis de gateway (GW). O roteamento para um anel de gateway específico é controlado pelo ATM ( [Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-overview.md) ). Como a configuração com redundância de zona nas camadas de serviço Premium ou Comercialmente Crítico não cria redundância de banco de dados adicional, você pode habilitá-la sem custo adicional. Ao selecionar uma configuração com redundância de zona, você pode tornar os bancos de dados Premium ou Comercialmente Crítico resilientes a um conjunto muito maior de falhas, incluindo interrupções catastróficas do datacenter, sem nenhuma alteração na lógica do aplicativo. Você também pode converter quaisquer bancos de dados ou pools Premium ou Comercialmente Crítico existentes na configuração com redundância de zona.

Como os bancos de dados com redundância de zona têm réplicas em data centers diferentes com alguma distância entre eles, a latência de rede aumentada pode aumentar o tempo de confirmação e, portanto, afetar o desempenho de algumas cargas de trabalho OLTP. Você sempre pode retornar para a configuração de zona única desabilitando a configuração de redundância de zona. Esse processo é uma operação online semelhante à atualização da camada de serviço normal. No final do processo, o banco de dados ou o pool é migrado de um anel com redundância de zona para um único anel de zona ou vice-versa.

> [!IMPORTANT]
> Atualmente, os bancos de dados com redundância de zona e os pools elásticos só têm suporte nas camadas de serviço Premium e Comercialmente Crítico em regiões selecionadas. Ao usar a camada de Comercialmente Crítico, a configuração com redundância de zona só estará disponível quando o hardware de computação Gen5 for selecionado. Para obter informações atualizadas sobre as regiões que dão suporte a bancos de dados com redundância de zona, consulte [suporte a serviços por região](../availability-zones/az-overview.md#services-support-by-region).  
> Este recurso não está disponível na instância gerenciada.

A versão com redundância de zona da arquitetura de alta disponibilidade é ilustrada pelo seguinte diagrama:

![arquitetura de alta disponibilidade com redundância de zona](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>ADR (recuperação de banco de dados acelerada)

A [ADR (recuperação de banco de dados acelerada)](sql-database-accelerated-database-recovery.md) é um novo recurso do mecanismo de banco de dados SQL que melhora muito a disponibilidade do banco de dados, especialmente na presença de transações de longa execução. ADR está disponível no momento para bancos de dados individuais, pools elásticos e SQL Data Warehouse do Azure.

## <a name="testing-database-fault-resiliency"></a>Testando resiliência de falha de banco de dados

A alta disponibilidade é uma parte fundamenental da plataforma do banco de dados SQL do Azure e funciona de forma transparente para seu aplicativo de banco de dados. No entanto, reconhecemos que talvez você queira testar como as operações de failover automático iniciadas durante os eventos planejados ou não planejados afetariam o aplicativo antes de implantá-lo para produção. Você pode chamar uma API especial para reiniciar o banco de dados ou o pool elástico, que, por sua vez, disparará o failover. No caso de banco de dados com redundância de zona ou pool elástico, a chamada à API resultaria em redirecionar as conexões de cliente para o novo primário em um AZ diferente. Assim, além de testar como o failover afeta as sessões de banco de dados existentes, você também pode verificar se ele impacta o desempenho de ponta a ponta. Como a operação de reinicialização é intrusiva e um grande número delas poderia enfatizar a plataforma, apenas uma chamada de failover é permitida a cada 30 minutos para cada banco de dados ou pool elástico. Para obter detalhes, consulte [failover de banco de dados](https://docs.microsoft.com/rest/api/sql/databases(failover)/failover) e failover de [pool elástico](https://docs.microsoft.com/rest/api/sql/elasticpools(failover)/failover).       

> [!IMPORTANT]
> O comando de failover não está disponível atualmente para bancos de dados Hypescale e instancses gerenciados.  

## <a name="conclusion"></a>Conclusão

O banco de dados SQL do Azure apresenta uma solução interna de alta disponibilidade, que está profundamente integrada à plataforma Azure. Ele depende de Service Fabric para detecção e recuperação de falhas, no armazenamento de BLOBs do Azure para proteção de dados e em Zonas de Disponibilidade para maior tolerância a falhas. Além disso, o banco de dados SQL do Azure aproveita a tecnologia de grupo de disponibilidade Always On de SQL Server para replicação e failover. A combinação dessas tecnologias permite que os aplicativos percebam totalmente os benefícios de um modelo de armazenamento misto e ofereçam suporte aos SLAs mais exigentes.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [zonas de disponibilidade do Azure](../availability-zones/az-overview.md)
- Saiba mais sobre o [Service Fabric](../service-fabric/service-fabric-overview.md)
- Saiba mais sobre o [Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-overview.md)
- Para obter mais opções de alta disponibilidade e recuperação de desastres, consulte [continuidade de negócios](sql-database-business-continuity.md)
