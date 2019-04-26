---
title: Elevada disponibilidade - serviço de base de dados do Azure SQL | Documentos da Microsoft
description: Saiba mais sobre as capacidades de elevada disponibilidade do serviço de base de dados do Azure SQL e os recursos
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: sashan
ms.reviewer: carlrab, sashan
manager: craigg
ms.date: 04/17/2019
ms.openlocfilehash: ec9f5aa8163ea9bb838b1a95ab8ad49233a72643
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60392654"
---
# <a name="high-availability-and-azure-sql-database"></a>Base de dados SQL do Azure e de elevada disponibilidade

O objetivo da arquitetura de elevada disponibilidade no Azure SQL Database é a garantia de que a base de dados está ativo e em execução 99,99% de tempo, sem se preocupar sobre o impacto das operações de manutenção e as falhas. Azure trata automaticamente tarefas de manutenção críticas, como a aplicação de patches, as cópias de segurança, as atualizações do Windows e SQL, bem como eventos não planeados, como falhas de hardware, software ou rede subjacentes.  Quando a instância SQL subjacente é corrigida ou efetua a ativação pós-falha, o período de indisponibilidade não é perceptível se [empregar a lógica de repetição](sql-database-develop-overview.md#resiliency) na sua aplicação. Base de dados SQL do Azure pode recuperar rapidamente até mesmo nas circunstâncias mais críticas garantir que seus dados estão sempre disponíveis.

A solução de elevada disponibilidade foi concebida para garantir que os dados confirmados nunca são perdidos devido a falhas, que operações de manutenção não afetam sua carga de trabalho, e que a base de dados não será um ponto único de falha na sua arquitetura de software. Não são janelas de manutenção ou tempos de inatividade do que devem exigir a parar a carga de trabalho, enquanto a base de dados é atualizado ou mantida. 

Há dois modelos de arquiteturais elevada disponibilidade que são utilizados na base de dados do Azure SQL:

- Modelo de disponibilidade padrão que se baseia numa separação de computação e armazenamento.  Ele conta com elevada disponibilidade e fiabilidade de camada de armazenamento remoto. Esta arquitetura destina-se aplicações de orçamento e orientados a negócios que podem tolerar alguma degradação no desempenho durante as atividades de manutenção.
- Modelo de disponibilidade de Premium que se baseia num cluster de processos de motor de base de dados. Ele se baseia no fato de que existe sempre um quórum de nós de motor de base de dados disponíveis. Esta arquitetura visa aplicativos de missão crítica com elevado desempenho de e/s, elevada taxa de transações e garantias de impacto de desempenho mínimos para a sua carga de trabalho durante as atividades de manutenção.

Base de dados SQL do Azure é executado na versão estável mais recente do motor de base de dados do SQL Server e o SO Windows e a maioria dos usuários não percebam que as atualizações são realizadas continuamente.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Básico, Standard e fins gerais de serviço de disponibilidade da camada

Estes escalões de serviço tiram partido da arquitetura de padrão de disponibilidade. A figura seguinte mostra quatro nós diferentes com as camadas de armazenamento e computação separadas.

![Separação de computação e armazenamento](media/sql-database-high-availability/general-purpose-service-tier.png)

O modelo de disponibilidade padrão inclui duas camadas:

- Uma camada de computação sem monitoração de estado que executa o `sqlserver.exe` processar e contém apenas transitórios e em cache dados sobre o SSD anexado, tais como conjunto de arquivo de TempDB, base de dados modelo, cache de planos, conjunto de memória intermédia e na coluna. Este nó sem monitoração de estado é operada por recursos de infraestrutura do serviço do Azure que inicializa `sqlserver.exe`, controla o estado de funcionamento do nó e efetua a ativação pós-falha para outro nó, se necessário.
- Uma camada de dados com monitoração de estado com os ficheiros de base de dados (.mdf/.ldf) que são armazenadas no armazenamento de Blobs do Azure. O armazenamento de Blobs do Azure tem disponibilidade de dados internos e a funcionalidade de redundância. Esta ação garante que todos os registos no ficheiro de registo ou página no ficheiro de dados serão preservados, mesmo no caso de falha de processo do SQL Server.

Sempre que o motor de base de dados ou o sistema operativo está atualizado ou se for detetada uma falha, o Azure Service Fabric irá mover o processo do SQL Server sem monitoração de estado para outro nó de computação sem monitoração de estado com capacidade livre suficiente. Os dados no armazenamento de Blobs do Azure não são afetados por esta mudança, e os ficheiros de dados/do registo são anexados ao processo do SQL Server recentemente inicializado. Este processo garante 99,99% de disponibilidade, mas uma carga de trabalho pesada poderá ter alguma degradação no desempenho durante a transição, uma vez que a nova instância do SQL Server é iniciado com a cache de frio.

## <a name="premium-and-business-critical-service-tier-availability"></a>Disponibilidade da camada de serviço Premium e crítico para a empresa

Premium e tire partido de escalões do críticas para a empresa serviço o modelo de disponibilidade de Premium, que integra recursos de computação (processo de motor de base de dados do SQL Server) e armazenamento (SSD ligado localmente) num único nó. Elevada disponibilidade é conseguida através da replicação de computação e armazenamento para criar um cluster de nó três para quatro - de nós adicionais. 

![Cluster de nós de motor de base de dados](media/sql-database-high-availability/business-critical-service-tier.png)

Os ficheiros de base de dados subjacente (.mdf/.ldf) são colocados no armazenamento SSD anexado para fornecer as e/s de latência muito baixa para a sua carga de trabalho. Elevada disponibilidade é implementada usando uma tecnologia semelhante ao SQL Server [grupos de Disponibilidade AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). O cluster inclui uma única réplica primária (processo do SQL Server) que seja acessível para cargas de trabalho do cliente de leitura / escrita e até três réplicas secundárias (computação e armazenamento) que contém cópias dos dados. O nó principal envia por push as alterações para os nós secundários por ordem e garante que os dados são sincronizados para pelo menos uma réplica secundária antes de consolidar a cada transação constantemente. Este processo garante que se o nó principal falhar por algum motivo, sempre há um nó totalmente sincronizado para efetuar a ativação pós-falha. A ativação pós-falha é iniciada pelo Azure Service Fabric. Assim que a réplica secundária torna-se o novo nó principal, outra réplica secundária é criada para garantir que o cluster tem suficiente nós (conjunto de quórum). Após a conclusão da ativação pós-falha, ligações de SQL são automaticamente redirecionadas para o novo nó primário.

Como um benefício adicional, o modelo de disponibilidade de premium inclui a capacidade de redirecionar as ligações só de leitura do SQL para uma das réplicas secundárias. Esta funcionalidade é denominada [Escalamento leitura](sql-database-read-scale-out.md). Ele fornece a capacidade sem qualquer custo adicional para off-load operações só de leitura, como cargas de trabalho de análises, partir da réplica primária de computação de 100% adicionais.

## <a name="zone-redundant-configuration"></a>Configuração com redundância de zona

Por predefinição, o cluster de nós para o modelo de disponibilidade de premium é criado no mesmo datacenter. Com a introdução da [zonas de disponibilidade do Azure](../availability-zones/az-overview.md), base de dados SQL pode colocar as réplicas diferentes no cluster para as zonas de disponibilidade diferente na mesma região. Para eliminar um ponto único de falha, a cadência de controle também está duplicada em várias zonas como três anéis de gateway (GW). O encaminhamento para um anel de gateway específico é controlado pelas [Gestor de tráfego do Azure](../traffic-manager/traffic-manager-overview.md) (ATM). Uma vez que a configuração com redundância de zona nos escalões de serviço Premium ou críticas para a empresa não cria a redundância de base de dados adicionais, pode ativá-la em não custos adicionais. Ao selecionar uma configuração com redundância de zona, pode fazer suas bases de dados Premium ou críticas para a empresa resiliente para um conjunto muito maior de falhas, incluindo falhas de datacenter catastróficos, sem quaisquer alterações para a lógica do aplicativo. Também pode converter a quaisquer bases de dados Premium ou críticas para a empresa ou conjuntos existentes para a configuração com redundância de zona.

Uma vez que as bases de dados com redundância de zona têm réplicas em datacenters diferentes com alguma distância entre eles, a latência de rede maior pode aumentar o tempo de consolidação e, portanto, afeta o desempenho de algumas cargas de trabalho OLTP. Pode sempre regressar à configuração de zona única, desativando a definição de redundância de zona. Este processo é uma operação online semelhante para a atualização do escalão de serviço normal. No final do processo, a base de dados ou conjunto é migrado a partir de um anel com redundância de zona para um anel de zona única ou vice-versa.

> [!IMPORTANT]
> Conjuntos elásticos e bases de dados com redundância de zona estão atualmente só são suportadas nos escalões de serviço Premium e crítico para a empresa. Por predefinição, as cópias de segurança e auditoria, registos são armazenados no armazenamento RA-GRS e, portanto, podem não ser automaticamente disponíveis caso ocorra uma interrupção de toda a zona. 

A versão com redundância de zona da arquitetura de elevada disponibilidade é ilustrada pelo diagrama seguinte:

![redundância de zona de arquitetura elevada disponibilidade](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Recuperação de base de dados acelerada (ADR)

[Acelerado de recuperação de base de dados (ADR)](sql-database-accelerated-database-recovery.md) é uma funcionalidade de motor da base de dados nova SQL melhora significativamente a disponibilidade de base de dados, especialmente na presença de longa execução de transações. Regras de implementação automática estão atualmente disponível para o Azure SQL Data Warehouse, conjuntos elásticos e bases de dados individuais.

## <a name="conclusion"></a>Conclusão

Base de dados SQL do Azure apresenta uma solução de elevada disponibilidade incorporada, que está profundamente integrada com a plataforma Azure. É dependente no Service Fabric para deteção de falhas e recuperação, no armazenamento de Blobs do Azure para proteção de dados e nas zonas de disponibilidade para maior tolerância de falhas. Além disso, a base de dados SQL do Azure tira partido da tecnologia de grupo de Disponibilidade AlwaysOn do SQL Server para a replicação e ativação pós-falha. A combinação dessas tecnologias permite que as aplicações tirar proveito completamente os benefícios de um armazenamento misto modelam e suportam os SLAs mais exigentes.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [zonas de disponibilidade do Azure](../availability-zones/az-overview.md)
- Saiba mais sobre [do Service Fabric](../service-fabric/service-fabric-overview.md)
- Saiba mais sobre [o Gestor de tráfego do Azure](../traffic-manager/traffic-manager-overview.md)
- Para obter mais opções para alta disponibilidade e recuperação após desastre, consulte [continuidade do negócio](sql-database-business-continuity.md)
