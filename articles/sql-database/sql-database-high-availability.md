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
ms.author: jovanpop
ms.reviewer: carlrab, sashan
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: fd6e383c2631a47daa7bf469c5bec59959453252
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59616854"
---
# <a name="high-availability-and-azure-sql-database"></a>Base de dados SQL do Azure e de elevada disponibilidade

Base de dados SQL do Azure é a plataforma como serviço que garante que a sua base de dados está ativo e em execução 99,99% de tempo, sem se preocupar sobre a manutenção e tempos de inatividade de base de dados altamente disponível. Este é um processo de motor de base de dados do SQL Server totalmente gerido alojado na cloud do Azure que garante que a base de dados do SQL Server está sempre atualizado/corrigido sem afetar a carga de trabalho. Quando uma instância é corrigida ou efetua a ativação pós-falha, o tempo de inatividade é geralmente não perceptível se [empregar a lógica de repetição](sql-database-develop-overview.md#resiliency) na sua aplicação. Se o tempo de execução de uma ativação pós-falha tiver mais de 60 segundos, deve abrir um incidente de suporte. Base de dados SQL do Azure pode recuperar rapidamente até mesmo nas circunstâncias mais críticas garantir que seus dados estão sempre disponíveis.

Plataforma do Azure totalmente gerencia cada base de dados do SQL do Azure e garante sem perda de dados e uma alta porcentagem de disponibilidade de dados. Azure processa automaticamente a aplicação de patches, as cópias de segurança, replicação, deteção de falhas, hardware potencial subjacente, falhas de software ou de rede, correções de erros de implementação, as ativações pós-falha, atualizações de base de dados e outras tarefas de manutenção. Engenheiros do SQL Server tem implementado as práticas mais conhecidas, garantindo que todas as operações de manutenção são concluídas em menos de 0,01% de tempo da sua vida de base de dados. Esta arquitetura foi concebida para garantir que os dados confirmados nunca são perdidos e que as operações de manutenção são efetuadas sem afetar a carga de trabalho. Não são janelas de manutenção ou tempos de inatividade do que devem exigir a parar a carga de trabalho, enquanto a base de dados é atualizado ou mantida. Elevada disponibilidade incorporada na base de dados do Azure SQL garante que essa base de dados nunca serão ponto único de falha em sua arquitetura de software.

Base de dados SQL do Azure baseia-se na arquitetura de motor de base de dados do SQL Server que é ajustada para o ambiente de cloud para garantir a disponibilidade de 99,99% mesmo em caso de falhas de infraestrutura. Há dois modelos de arquiteturais elevada disponibilidade que são utilizados na base de dados do Azure SQL (ambos garantir a disponibilidade de 99,99%):

- Fins gerais/Standard camada modelo de serviço que se baseia numa separação de computação e armazenamento. Este modelo de arquitetura baseia-se na elevada disponibilidade e fiabilidade de camada de armazenamento, mas poderá ter alguma degradação do desempenho potencial durante as atividades de manutenção.
- Modelo de camada de negócios/Premium críticos de serviço que se baseia num cluster de processos de motor de base de dados. Este modelo de arquitetura baseia-se no fato de que existe é sempre um quórum de nós de motor de base de dados disponíveis e não tem impacto de desempenho mínimos na carga de trabalho, mesmo durante as atividades de manutenção.

O Azure é atualizado e patches do sistema operacional subjacente, drivers e o motor de base de dados do SQL Server transparente com o mínimo de tempo de inatividade para os utilizadores finais. Base de dados SQL do Azure é executado na versão estável mais recente do motor de base de dados do SQL Server e o SO Windows e a maioria dos usuários não percebam que as atualizações são realizadas continuamente.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Básico, Standard e fins gerais de serviço de disponibilidade da camada

Disponibilidade padrão refere-se para o SLA de 99,99%, que é aplicado nos escalões de serviço básico, Standard e fins gerais. Elevada disponibilidade neste modelo de arquitetura é conseguida através da separação das camadas de computação e armazenamento e a replicação de dados na camada de armazenamento.

A figura seguinte mostra os quatro nós no modelo de arquitetura de padrão com as camadas de armazenamento e computação separados.

![Separação de computação e armazenamento](media/sql-database-managed-instance/general-purpose-service-tier.png)

No modelo padrão de disponibilidade, existem duas camadas:

- Uma camada de computação sem monitoração de estado que está a executar o `sqlserver.exe` processar e contém apenas transitórios e em cache os dados (por exemplo – cache de planos, conjunto de memória intermédia, agrupamento de armazenamento de coluna). Este nó do SQL Server sem monitoração de estado é operada por recursos de infraestrutura do serviço do Azure que inicializa o processo, controla o estado de funcionamento do nó e efetua a ativação pós-falha para outro lugar, se necessário.
- Uma camada de dados com monitoração de estado com ficheiros de base de dados (.mdf/.ldf) que estão armazenados no armazenamento de Blobs do Azure. Armazenamento de Blobs do Azure garante que haverá sem perda de dados de qualquer registo que é colocado em qualquer arquivo de banco de dados. Armazenamento de Blobs do Azure tem disponibilidade/redundância de dados incorporada que garante que todos os registos no ficheiro de registo ou a página no arquivo de dados será preservada mesmo no caso de falha de processo do SQL Server.

Sempre que a atualização do motor de base de dados ou o sistema operativo, alguma parte da infraestrutura subjacente falha ou se detetar algum problema crítico no processo do Sql Server, o Azure Service Fabric moverá o processo do SQL Server sem monitoração de estado para outro nó de computação sem monitoração de estado. Existe um conjunto de nós de reserva que está à espera de executar o novo serviço de computação em caso de ativação pós-falha para minimizar o tempo de ativação pós-falha. Os dados no armazenamento de Blobs do Azure não são afetados e arquivos de dados/do registo são anexados ao processo do SQL Server recentemente inicializado. Este processo garante 99,99% de disponibilidade, mas ele pode ter alguns impactos no desempenho na carga de trabalho pesada, que está a executar devido ao tempo de transição e o fato do novo nó do SQL Server é iniciado com a cache de frio.

## <a name="premium-and-business-critical-service-tier-availability"></a>Disponibilidade da camada de serviço Premium e crítico para a empresa

Disponibilidade de Premium está ativada nos escalões de serviço Premium e crítico para a empresa da base de dados do Azure SQL e foi concebido para cargas de trabalho intensivas que não toleram nenhum impacto no desempenho devido a operações de manutenção contínua.

No modelo de premium, base de dados SQL do Azure integra-se de computação e armazenamento no nó único. Elevada disponibilidade neste modelo de arquitetura é conseguida através da replicação de computação (processo de motor de base de dados do SQL Server) e armazenamento (SSD ligado localmente) implementado num cluster de 4 nós, usando a tecnologia semelhante ao SQL Server [sempre disponibilidade Grupos](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Cluster de nós de motor de base de dados](media/sql-database-managed-instance/business-critical-service-tier.png)

O SQL da base de processo do motor e subjacentes arquivos mdf/ldf são colocados no mesmo nó com armazenamento SSD localmente ao fornecimento de baixa latência para a sua carga de trabalho. Elevada disponibilidade é implementada utilizando a tecnologia semelhante ao SQL Server [grupos de Disponibilidade AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Cada base de dados é um cluster de nós de base de dados com uma base de dados principal que está acessível para a carga de trabalho do cliente e um três processos secundário que contêm cópias dos dados. O nó principal constantemente envia as alterações para nós secundários para garantir que os dados estão disponíveis em réplicas secundárias, no caso de falha de nó primário por qualquer motivo. Ativação pós-falha é processada pelo Azure Service Fabric – uma réplica secundária torna-se o nó principal e uma nova réplica secundária é criada para garantir que o suficiente nós do cluster. A carga de trabalho é automaticamente redirecionada para o novo nó primário.

Além disso, o cluster de crítico para a empresa tem incorporado [Escalamento leitura](sql-database-read-scale-out.md) capacidade proporcionada pelo livre de cobrar nó só de leitura incorporada que pode ser utilizada para executar consultas somente leitura (por exemplo, relatórios) que não devem afetar o desempenho da sua carga de trabalho principal.

## <a name="zone-redundant-configuration"></a>Configuração com redundância de zona

Por predefinição, as réplicas de conjunto de quórum para configurações de armazenamento local são criadas no mesmo datacenter. Com a introdução da [zonas de disponibilidade do Azure](../availability-zones/az-overview.md), tem a capacidade de colocar as réplicas diferentes em conjuntos de quórum para zonas de disponibilidade diferente na mesma região. Para eliminar um ponto único de falha, a cadência de controle também está duplicada em várias zonas como três anéis de gateway (GW). O encaminhamento para um anel de gateway específico é controlado pelas [Gestor de tráfego do Azure](../traffic-manager/traffic-manager-overview.md) (ATM). Uma vez que a configuração com redundância de zona não cria a redundância de base de dados adicionais, a utilização das zonas de disponibilidade nos escalões de serviço Premium ou críticas para a empresa está disponível nenhuma adicionais custo. Ao selecionar uma base de dados com redundância de zona, pode fazer suas bases de dados Premium ou críticas para a empresa resiliente para um conjunto muito maior de falhas, incluindo falhas de datacenter catastróficos, sem quaisquer alterações da lógica do aplicativo. Também pode converter a quaisquer bases de dados Premium ou críticas para a empresa ou conjuntos existentes para a configuração com redundância de zona.

Uma vez que o quórum com redundância de zona-conjunto tem réplicas em datacenters diferentes com alguma distância entre eles, a latência de rede maior pode aumentar o tempo de consolidação e, portanto, afeta o desempenho de algumas cargas de trabalho OLTP. Pode sempre regressar à configuração de zona única, desativando a definição de redundância de zona. Este processo é um tamanho de operação de dados e é semelhante à atualização do escalão de serviço regular. No final do processo, a base de dados ou conjunto é migrado a partir de um anel com redundância de zona para um anel de zona única ou vice-versa.

> [!IMPORTANT]
> Conjuntos elásticos e bases de dados com redundância de zona são atualmente suportadas apenas no escalão de serviço Premium. Por predefinição, as cópias de segurança e auditoria, registos são armazenados no armazenamento RA-GRS e, portanto, podem não ser automaticamente disponíveis caso ocorra uma interrupção de toda a zona. 

A versão com redundância de zona da arquitetura de elevada disponibilidade é ilustrada pelo diagrama seguinte:

![redundância de zona de arquitetura elevada disponibilidade](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="accelerated-database-recovery-adr"></a>Recuperação de base de dados acelerada (ADR)

[Acelerado de recuperação de base de dados (ADR)](sql-database-accelerated-database-recovery.md) é uma funcionalidade de motor da base de dados nova SQL melhora significativamente a disponibilidade de base de dados, especialmente na presença de longa execução transações, ao ter de redesenhar o processo de recuperação do motor de base de dados SQL. Regras de implementação automática estão atualmente disponível para o Azure SQL Data Warehouse, conjuntos elásticos e bases de dados individuais.

## <a name="conclusion"></a>Conclusão

Base de dados SQL do Azure está profundamente integrado com a plataforma Azure e é altamente dependente no Service Fabric para deteção de falhas e recuperação, no armazenamento de Blobs do Azure para proteção de dados e zonas de disponibilidade para maior tolerância de falhas. Ao mesmo tempo, a base de dados SQL do Azure aproveita totalmente a tecnologia do grupo de Disponibilidade AlwaysOn do produto de caixa de SQL Server para a replicação e ativação pós-falha. A combinação dessas tecnologias permite que os aplicativos a serem totalmente perceber os benefícios de um modelo de armazenamento misto e suporta os SLAs mais exigentes.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [zonas de disponibilidade do Azure](../availability-zones/az-overview.md)
- Saiba mais sobre [do Service Fabric](../service-fabric/service-fabric-overview.md)
- Saiba mais sobre [o Gestor de tráfego do Azure](../traffic-manager/traffic-manager-overview.md)
- Para obter mais opções para alta disponibilidade e recuperação após desastre, consulte [continuidade do negócio](sql-database-business-continuity.md)
