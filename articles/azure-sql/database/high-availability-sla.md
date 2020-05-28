---
title: Elevada disponibilidade
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Conheça a Base de Dados Azure SQL e o serviço SQL Managed Instance altas capacidades e funcionalidades de alta disponibilidade
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: sashan
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 04/02/2020
ms.openlocfilehash: ca340ce86dc4e6c028840fd7bfdb909ea097629e
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84043325"
---
# <a name="high-availability-for-azure-sql-database--sql-managed-instance"></a>Alta disponibilidade para base de dados Azure SQL & Instância Gerida SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

O objetivo da arquitetura de alta disponibilidade na Base de Dados Azure SQL e Na Instância Gerida SQL é garantir que a sua base de dados está a funcionar no mínimo 99,99% do tempo (Para mais informações sobre SLA específicos para diferentes níveis, consulte sLA para Base de [Dados Azure SQL & SQL Managed Instance),](https://azure.microsoft.com/support/legal/sla/sql-database/)sem se preocupar com o impacto das operações de manutenção e interrupções. O Azure lida automaticamente com tarefas críticas de manutenção, tais como patching, backups, atualizações windows e SQL, bem como eventos não planeados, tais como falhas de hardware, software ou rede subjacentes.  Quando a instância SQL subjacente é remendada ou falha, o tempo de inatividade não é percetível se utilizar uma lógica de [retry](develop-overview.md#resiliency) na sua aplicação. A Base de Dados SQL e a Instância Gerida SQL podem recuperar rapidamente mesmo nas circunstâncias mais críticas, garantindo que os seus dados estão sempre disponíveis.

A solução de alta disponibilidade foi concebida para garantir que os dados cometidos nunca se percam devido a falhas, que as operações de manutenção não afetam a sua carga de trabalho, e que a base de dados não será um único ponto de falha na arquitetura do seu software. Não existem janelas de manutenção ou horários de paragem que devam exigir que pare a carga de trabalho enquanto a base de dados é atualizada ou mantida.

Existem dois modelos arquitetônicos de alta disponibilidade:

- **Modelo** de disponibilidade padrão que se baseia numa separação de computação e armazenamento.  Baseia-se na elevada disponibilidade e fiabilidade do nível de armazenamento remoto. Esta arquitetura tem como alvo aplicações empresariais orientadas para o orçamento que podem tolerar alguma degradação do desempenho durante as atividades de manutenção.
- **Modelo** de disponibilidade premium que se baseia num conjunto de processos de motor de base de dados. Baseia-se no facto de existir sempre um quórum de nódos os motores de base de dados disponíveis. Esta arquitetura destina-se a aplicações críticas da missão com elevado desempenho em IO, alta taxa de transação e garante o mínimo impacto de desempenho na sua carga de trabalho durante as atividades de manutenção.

A Base de Dados SQL e a SQL Managed Instance funcionam na versão mais recente estável do motor de base de dados SQL Server e do sistema operativo Windows, e a maioria dos utilizadores não nota que as atualizações são executadas continuamente.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Disponibilidade de nível de serviço básico, standard e geral

Os níveis de serviço Basic, Standard e General Purpose alavancam a arquitetura de disponibilidade padrão para computação sem servidor e provisionado. A figura que se segue mostra quatro nós diferentes com a computação separada e camadas de armazenamento.

![Separação de cálculo e armazenamento](./media/high-availability-sla/general-purpose-service-tier.png)

O modelo de disponibilidade padrão inclui duas camadas:

- Uma camada de cálculo apátrida que executa o `sqlservr.exe` processo e contém apenas dados transitórios e cached, como TempDB, bases de dados de modelos no SSD anexado, e cache de plano, piscina tampão e piscina de colunas na memória. Este nó apátrida é operado pela Azure Service Fabric que `sqlservr.exe` inicializa, controla a saúde do nó e executa a falha em outro nó, se necessário.
- Uma camada de dados audaz com os ficheiros de base de dados (.mdf/.ldf) que são armazenados no armazenamento da Blob Azure. O armazenamento de blob Azure tem disponibilidade de dados incorporados e recurso de redundância. Garante que todos os registos no ficheiro de registo ou página do ficheiro de dados serão preservados mesmo que o `sqlservr.exe` processo se despenhe.

Sempre que o motor de base de dados ou o sistema operativo for atualizado, ou se for detetada uma falha, o Tecido de Serviço Azure deslocará o processo apátrida para outro nó de `sqlservr.exe` cálculo apátrida com capacidade livre suficiente. Os dados no armazenamento do Blob Azure não são afetados pelo movimento, e os ficheiros de dados/registos estão ligados ao processo recém-inicializado. `sqlservr.exe` Este processo garante 99,99% de disponibilidade, mas uma carga de trabalho pesada pode sofrer alguma degradação do desempenho durante a transição desde que o novo `sqlservr.exe` processo começa com cache frio.

## <a name="premium-and-business-critical-service-tier-availability"></a>Disponibilidade de nível de serviço Premium e Business Critical

Os níveis de serviço Premium e Business Critical alavancam o modelo de disponibilidade Premium, que integra os recursos computacionais `sqlservr.exe` (processo) e armazenamento (SSD) anexado localmente num único nó. A elevada disponibilidade é conseguida replicando tanto a computação como o armazenamento a nós adicionais criando um cluster de três a quatro nós.

![Aglomerado de nós de motor de base de dados](./media/high-availability-sla/business-critical-service-tier.png)

Os ficheiros de base de dados subjacentes (.mdf/.ldf) são colocados no armazenamento SSD anexado para fornecer iO de latência muito baixa à sua carga de trabalho. A alta disponibilidade é implementada utilizando uma tecnologia semelhante aos [grupos](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)de disponibilidade do SQL Server Always On . O cluster inclui uma única réplica primária que é acessível para cargas de trabalho dos clientes de leitura, e até três réplicas secundárias (computação e armazenamento) contendo cópias de dados. O nó primário pressiona constantemente as alterações aos nós secundários em ordem e assegura que os dados são sincronizados para pelo menos uma réplica secundária antes de cometer cada transação. Este processo garante que se o nó primário falhar por qualquer motivo, há sempre um nó totalmente sincronizado para não conseguir. A falha é iniciada pelo Tecido de Serviço Azure. Uma vez que a réplica secundária se torna o novo nó primário, outra réplica secundária é criada para garantir que o cluster tem nós suficientes (conjunto de quórum). Uma vez concluída a falha, as ligações SQL são automaticamente redirecionadas para o novo nó primário.

Como benefício extra, o modelo de disponibilidade premium inclui a capacidade de redirecionar ligações SQL apenas de leitura para uma das réplicas secundárias. Esta funcionalidade [chama-se Read Scale-Out](read-scale-out.md). Fornece 100% de capacidade de computação adicional sem custos adicionais para operações de leitura descarregadas, como cargas de trabalho analíticas, a partir da réplica primária.

## <a name="hyperscale-service-tier-availability"></a>Disponibilidade de nível de serviço de hiperescala

A arquitetura de nível de serviço Hyperscale é descrita na arquitetura de [funções distribuídas](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture) e está apenas atualmente disponível para a Base de Dados SQL, e não para a Instância Gerida SQL.

![Arquitetura funcional de hiperescala](./media/high-availability-sla/hyperscale-architecture.png)

O modelo de disponibilidade em Hiperescala inclui quatro camadas:

- Uma camada de cálculo apátrida que executa os `sqlservr.exe` processos e contém apenas dados transitórios e cached, tais como cache RBPEX não-coberto, TempDB, base de dados de modelos, etc. no SSD anexado, e cache de plano, piscina tampão e piscina de colunas na memória. Esta camada apátrida inclui a réplica primária do cálculo e opcionalmente uma série de réplicas de computação secundária que podem servir como alvos de failover.
- Uma camada de armazenamento apátrida formada por servidores de página. Esta camada é o motor de armazenamento distribuído para os `sqlservr.exe` processos em execução nas réplicas computacionais. Cada servidor de página contém apenas dados transitórios e cached, tais como cobrir a cache RBPEX no SSD anexado, e páginas de dados em cache na memória. Cada servidor de página tem um servidor de página emparelhado numa configuração ativa para fornecer equilíbrio de carga, redundância e alta disponibilidade.
- Uma camada de armazenamento de registo de transações imponente formada pelo nó computacional que executa o processo de serviço de Registo, a zona de desembarque de registo de transações e armazenamento a longo prazo do registo de transações. Zona de desembarque e armazenamento de longo prazo Utilização Azure Storage, que fornece disponibilidade e [redundância](https://docs.microsoft.com/azure/storage/common/storage-redundancy) para registo de transações, garantindo a durabilidade dos dados para transações comprometidas.
- Uma camada de armazenamento de dados aguçada com os ficheiros de base de dados (.mdf/.ndf) que são armazenados no Armazenamento Azure e são atualizados por servidores de página. Esta camada utiliza funcionalidades de disponibilidade de dados e [de redundância](https://docs.microsoft.com/azure/storage/common/storage-redundancy) do Armazenamento Azure. Garante que cada página de um ficheiro de dados será preservada mesmo que os processos noutras camadas de arquitetura hyperscale falhem, ou se os nós de computação falharem.

Os nós computacionais em todas as camadas de hiperescala funcionam no Tecido de Serviço Azure, que controla a saúde de cada nó e executa falhas nos nós saudáveis disponíveis, se necessário.

Para obter mais informações sobre a elevada disponibilidade em Hiperescala, consulte [A Alta Disponibilidade da Base de Dados em Hiperescala](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale).

## <a name="zone-redundant-configuration"></a>Configuração redundante da zona

Por padrão, o cluster de nós para o modelo de disponibilidade premium é criado no mesmo datacenter. Com a introdução de Zonas de [Disponibilidade Azure,](../../availability-zones/az-overview.md)a Base de Dados SQL e a Instância Gerida SQL podem colocar diferentes réplicas da base de dados Business Critical para diferentes zonas de disponibilidade na mesma região. Para eliminar um único ponto de falha, o anel de controlo também é duplicado em várias zonas como três anéis de gateway (GW). O encaminhamento para um anel de gateway específico é controlado pelo [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) (MULTIM). Uma vez que a configuração redundante da zona nos níveis de serviço Premium ou Business Critical não cria redundância adicional na base de dados, pode permitir-lhe sem custos adicionais. Ao selecionar uma configuração redundante de zona, pode tornar as suas bases de dados Premium ou Business Critical resilientes a um conjunto muito maior de falhas, incluindo falhas catastróficas de datacenter, sem alterações na lógica da aplicação. Também pode converter quaisquer bases de dados ou piscinas Premium ou Business Critical existentes para a configuração redundante da zona.

Como as bases de dados redundantes da zona têm réplicas em diferentes datacenters com alguma distância entre eles, o aumento da latência da rede pode aumentar o tempo de compromisso e, assim, impactar o desempenho de algumas cargas de trabalho oLTP. Pode sempre voltar à configuração de zona única, desativando a definição de redundância da zona. Este processo é uma operação online semelhante à atualização regular do nível de serviço. No final do processo, a base de dados ou piscina é migrada de um anel redundante de zona para um único anel de zona ou vice-versa.

> [!IMPORTANT]
> Atualmente, as bases de dados redundantes da zona e as piscinas elásticas são suportadas apenas nos níveis de serviço Premium e Business Critical em regiões selecionadas. Ao utilizar o nível Business Critical, a configuração redundante da zona só está disponível quando o hardware computacional Gen5 é selecionado. Para informações atualizadas sobre as regiões que suportam bases de dados redundantes da zona, consulte [o suporte de serviços por região.](../../availability-zones/az-region.md)  
> Esta funcionalidade não está disponível na Instância Gerida SQL.

A versão redundante da zona da arquitetura de alta disponibilidade é ilustrada pelo seguinte diagrama:

![zona de arquitetura de alta disponibilidade redundante](./media/high-availability-sla/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Recuperação acelerada da base de dados (ADR)

[Accelerated Database Recovery (ADR)](../accelerated-database-recovery.md) é uma nova funcionalidade do motor de base de dados SQL que melhora consideravelmente a disponibilidade de bases de dados, especialmente na presença de transações de longa duração. ATualmente, a ADR está disponível para a Base de Dados Azure SQL, Pláio SQL e Armazém de Dados Azure SQL.

## <a name="testing-application-fault-resiliency"></a>Resistência à falha da aplicação de teste

A elevada disponibilidade é uma parte fundamental da base de dados SQL e da plataforma SQL Managed Instance que funciona de forma transparente para a sua aplicação de base de dados. No entanto, reconhecemos que pode querer testar como as operações automáticas de failover iniciadas durante eventos planeados ou não planeados teriam impacto na aplicação antes de a implementar em produção. Pode chamar uma API especial para reiniciar uma base de dados ou uma piscina elástica, o que por sua vez irá desencadear uma falha. No caso de uma base de dados redundante ou de um pool elástico, a chamada da API resultaria na redirecionamento das ligações dos clientes para a nova primária numa Zona de Disponibilidade diferente da Zona de Disponibilidade das antigas primárias. Assim, para além de testar como o falha afeta as sessões de base de dados existentes, também pode verificar se altera o desempenho de ponta a ponta devido a alterações na latência da rede. Como a operação de reinício é intrusiva e um grande número deles pode realçar a plataforma, apenas uma chamada failover é permitida a cada 30 minutos para cada base de dados ou piscina elástica.

Uma falha pode ser iniciada usando REST API ou PowerShell. Para a Rest API, consulte a [falha da base de dados](https://docs.microsoft.com/rest/api/sql/databases(failover)/failover) e a falha da piscina [elástica](https://docs.microsoft.com/rest/api/sql/elasticpools(failover)/failover). Para powerShell, consulte [Invoke-AzSqlDatabaseFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqldatabasefailover) e [Invoke-AzSqlElasticPoolFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlelasticpoolfailover). As chamadas REST API também podem ser feitas a partir de Azure CLI usando o comando [de repouso az.](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-rest)

> [!IMPORTANT]
> O comando Failover não está atualmente disponível no nível de serviço de Hiperescala e em Caso Gerido.

## <a name="conclusion"></a>Conclusão

A Azure SQL Database e a Azure SQL Managed Instance apresentam uma solução de alta disponibilidade incorporada, profundamente integrada com a plataforma Azure. Está dependente do Tecido de Serviço para deteção e recuperação de falhas, no armazenamento de Azure Blob para proteção de dados, e em Zonas de Disponibilidade para maior tolerância a falhas. Além disso, a Base de Dados SQL e a SQL Managed Instance alavancam a tecnologia do grupo Always On de disponibilidade do SQL Server para replicação e falha. A combinação destas tecnologias permite que as aplicações realizem plenamente os benefícios de um modelo de armazenamento misto e suportem os SLAs mais exigentes.

## <a name="next-steps"></a>Próximos passos

- Conheça [as Zonas de Disponibilidade do Azure](../../availability-zones/az-overview.md)
- Saiba mais sobre [o Tecido de Serviço](../../service-fabric/service-fabric-overview.md)
- Conheça o [Gestor de Tráfego Azure](../../traffic-manager/traffic-manager-overview.md)
- Para mais opções para alta disponibilidade e recuperação de desastres, consulte [Continuidade do Negócio](business-continuity-high-availability-disaster-recover-hadr-overview.md)
