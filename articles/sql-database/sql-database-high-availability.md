---
title: Alta disponibilidade-serviço de banco de dados SQL do Azure | Microsoft Docs
description: Conheça os recursos e recursos de alta disponibilidade do serviço de banco de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 06/10/2019
ms.openlocfilehash: 54994dd626df23694ea372d4a662d2b4fb051fc8
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285772"
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

- Uma camada de computação sem estado que executa o processo de `sqlserver.exe` e contém somente dados transitórios e armazenados em cache no SSD anexado, como TempDB, Database Model, cache de planos, pool de buffers e pool de armazenamento de coluna. Esse nó sem estado é operado pelo Service Fabric do Azure que inicializa `sqlserver.exe`, controla a integridade do nó e executa o failover para outro nó, se necessário.
- Uma camada de dados com monitoração de estado com os arquivos de banco (. MDF/. ldf) armazenados no armazenamento de BLOBs do Azure. O armazenamento de BLOBs do Azure tem recursos internos de redundância e disponibilidade de dados. Ele garante que todos os registros no arquivo de log ou na página do arquivo de dados serão preservados mesmo se SQL Server processo falhar.

Sempre que o mecanismo de banco de dados ou o sistema operacional for atualizado ou uma falha for detectada, o Azure Service Fabric moverá o processo de SQL Server sem estado para outro nó de computação sem estado com capacidade livre suficiente. Os dados no armazenamento de BLOBs do Azure não são afetados pela movimentação e os arquivos de dados/log são anexados ao processo de SQL Server inicializado recentemente. Esse processo garante a disponibilidade de 99,99%, mas uma carga de trabalho pesada pode enfrentar alguma degradação de desempenho durante a transição, uma vez que a nova instância de SQL Server começa com o cache frio.

## <a name="premium-and-business-critical-service-tier-availability"></a>Disponibilidade da camada de serviço Premium e Comercialmente Crítico

As camadas de serviço Premium e Comercialmente Crítico aproveitam o modelo de disponibilidade Premium, que integra recursos de computação (SQL Server processo de Mecanismo de Banco de Dados) e armazenamento (SSD anexado localmente) em um único nó. A alta disponibilidade é obtida com a replicação da computação e do armazenamento para nós adicionais, criando um cluster de três a quatro nós. 

![Cluster de nós do mecanismo de banco de dados](media/sql-database-high-availability/business-critical-service-tier.png)

Os arquivos de banco de dados subjacentes (. MDF/. ldf) são colocados no armazenamento SSD anexado para fornecer uma e/s de latência muito baixa para sua carga de trabalho. A alta disponibilidade é implementada usando uma tecnologia semelhante a SQL Server [Always on grupos de disponibilidade](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). O cluster inclui uma única réplica primária (SQL Server processo) que é acessível para cargas de trabalho do cliente de leitura/gravação e até três réplicas secundárias (computação e armazenamento) que contêm cópias de dados. O nó primário envia constantemente as alterações para os nós secundários e garante que os dados sejam sincronizados com pelo menos uma réplica secundária antes de confirmar cada transação. Esse processo garante que, se o nó primário falhar por algum motivo, sempre haverá um nó totalmente sincronizado para o failover. O failover é iniciado pelo Service Fabric do Azure. Depois que a réplica secundária se tornar o novo nó primário, outra réplica secundária será criada para garantir que o cluster tenha nós suficientes (conjunto de quorum). Depois que o failover for concluído, as conexões do SQL serão redirecionadas automaticamente para o novo nó primário.

Como um benefício extra, o modelo de disponibilidade Premium inclui a capacidade de redirecionar conexões SQL somente leitura para uma das réplicas secundárias. Esse recurso é chamado [de expansão de leitura](sql-database-read-scale-out.md). Ele fornece uma capacidade de computação adicional de 100% sem custo adicional para operações somente leitura fora do carregamento, como cargas de trabalho analíticas, da réplica primária.

### <a name="zone-redundant-configuration"></a>Configuração com redundância de zona

Por padrão, o cluster de nós para o modelo de disponibilidade Premium é criado no mesmo datacenter. Com a introdução do [zonas de disponibilidade do Azure](../availability-zones/az-overview.md), o banco de dados SQL pode posicionar réplicas diferentes do banco de dados comercialmente crítico em diferentes zonas de disponibilidade na mesma região. Para eliminar um ponto único de falha, o anel de controle também é duplicado em várias zonas como três anéis de gateway (GW). O roteamento para um anel de gateway específico é controlado pelo ATM ( [Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-overview.md) ). Como a configuração com redundância de zona nas camadas de serviço Premium ou Comercialmente Crítico não cria redundância de banco de dados adicional, você pode habilitá-la sem custo adicional. Ao selecionar uma configuração com redundância de zona, você pode tornar os bancos de dados Premium ou Comercialmente Crítico resilientes a um conjunto muito maior de falhas, incluindo interrupções catastróficas do datacenter, sem nenhuma alteração na lógica do aplicativo. Você também pode converter quaisquer bancos de dados ou pools Premium ou Comercialmente Crítico existentes na configuração com redundância de zona.

Como os bancos de dados com redundância de zona têm réplicas em data centers diferentes com alguma distância entre eles, a latência de rede aumentada pode aumentar o tempo de confirmação e, portanto, afetar o desempenho de algumas cargas de trabalho OLTP. Você sempre pode retornar para a configuração de zona única desabilitando a configuração de redundância de zona. Esse processo é uma operação online semelhante à atualização da camada de serviço normal. No final do processo, o banco de dados ou o pool é migrado de um anel com redundância de zona para um único anel de zona ou vice-versa.

> [!IMPORTANT]
> Atualmente, os bancos de dados com redundância de zona e os pools elásticos só têm suporte nas camadas de serviço Premium e Comercialmente Crítico em regiões selecionadas. Ao usar a camada de Comercialmente Crítico, a configuração com redundância de zona só estará disponível quando o hardware de computação Gen5 for selecionado. Para obter informações atualizadas sobre as regiões que dão suporte a bancos de dados com redundância de zona, consulte [suporte a serviços por região](../availability-zones/az-overview.md#services-support-by-region).  
> Este recurso não está disponível na instância gerenciada.

A versão com redundância de zona da arquitetura de alta disponibilidade é ilustrada pelo seguinte diagrama:

![arquitetura de alta disponibilidade com redundância de zona](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>ADR (recuperação de banco de dados acelerada)

A [ADR (recuperação de banco de dados acelerada)](sql-database-accelerated-database-recovery.md) é um novo recurso do mecanismo de banco de dados SQL que melhora muito a disponibilidade do banco de dados, especialmente na presença de transações de longa execução. ADR está disponível no momento para bancos de dados individuais, pools elásticos e SQL Data Warehouse do Azure.

## <a name="conclusion"></a>Conclusão

O banco de dados SQL do Azure apresenta uma solução interna de alta disponibilidade, que está profundamente integrada à plataforma Azure. Ele depende de Service Fabric para detecção e recuperação de falhas, no armazenamento de BLOBs do Azure para proteção de dados e em Zonas de Disponibilidade para maior tolerância a falhas. Além disso, o banco de dados SQL do Azure aproveita a tecnologia de grupo de disponibilidade Always On de SQL Server para replicação e failover. A combinação dessas tecnologias permite que os aplicativos percebam totalmente os benefícios de um modelo de armazenamento misto e ofereçam suporte aos SLAs mais exigentes.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [zonas de disponibilidade do Azure](../availability-zones/az-overview.md)
- Saiba mais sobre o [Service Fabric](../service-fabric/service-fabric-overview.md)
- Saiba mais sobre o [Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-overview.md)
- Para obter mais opções de alta disponibilidade e recuperação de desastres, consulte [continuidade de negócios](sql-database-business-continuity.md)
