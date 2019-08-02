---
title: Visão geral da instância gerenciada do banco de dados SQL do Azure | Microsoft Docs
description: Este artigo descreve a instância gerenciada do banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 07/18/2019
ms.openlocfilehash: 7c10bdf5e4f47f5bb5ac97b610c605132c4b4a00
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567210"
---
# <a name="what-is-azure-sql-database-managed-instance"></a>O que é a instância gerenciada do banco de dados SQL do Azure?

A instância gerenciada é uma nova opção de implantação do banco de dados SQL do Azure, fornecendo quase 100% de compatibilidade com a versão mais recente do SQL Server (Enterprise Edition) Mecanismo de Banco de Dados, fornecendo uma implementação de [rede virtual nativa (VNet)](../virtual-network/virtual-networks-overview.md) que aborda questões comuns de segurança e um [modelo de negócios](https://azure.microsoft.com/pricing/details/sql-database/) favorável para clientes SQL Server locais. O modelo de implantação de instância gerenciada permite que os clientes existentes do SQL Server migrem e shiftem seus aplicativos locais para a nuvem com alterações mínimas de aplicativo e banco de dados. Ao mesmo tempo, a opção de implantação de instância gerenciada preserva todos os recursos de PaaS (patches automáticos e [](sql-database-automated-backups.md)atualizações de versão, backups automatizados, [alta disponibilidade](sql-database-high-availability.md) ), que reduz drasticamente a sobrecarga de gerenciamento e o TCO.

> [!IMPORTANT]
> Para obter uma lista de regiões nas quais a opção de implantação de instância gerenciada está disponível no momento, consulte [regiões com suporte](sql-database-managed-instance-resource-limits.md#supported-regions).

O diagrama a seguir descreve os principais recursos das instâncias gerenciadas:

![principais recursos](./media/sql-database-managed-instance/key-features.png)

O modelo de implantação de instância gerenciada foi projetado para clientes que procuram migrar um grande número de aplicativos de um ambiente local ou IaaS, autocompilado ou ISV fornecido para um ambiente de nuvem PaaS totalmente gerenciado, com o menor esforço de migração possível. Usando o [DMS (serviço de migração de dados)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) totalmente automatizado no Azure, os clientes podem migrar e deslocar seus SQL Server locais para uma instância gerenciada que oferece compatibilidade com SQL Server no local e isolamento completo de instâncias de clientes com suporte a VNet nativa.  Com o Software Assurance, você pode trocar suas licenças existentes por tarifas com desconto em uma instância gerenciada usando o [benefício híbrido do Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).  Uma instância gerenciada é o melhor destino de migração na nuvem para SQL Server instâncias que exigem alta segurança e uma superfície de programação avançada.

A opção de implantação de instância gerenciada visa entregas perto de 100% de compatibilidade de área de superfície com a versão de SQL Server local mais recente por meio de um plano de lançamento em etapas.

Para decidir entre as opções de implantação do banco de dados SQL do Azure: banco de dados individual, banco de dados em pool e instância gerenciada e SQL Server hospedados na máquina virtual, consulte [como escolher a versão correta do SQL Server no Azure](sql-database-paas-vs-sql-server-iaas.md).

## <a name="key-features-and-capabilities"></a>Principais recursos e funcionalidades

A instância gerenciada combina os melhores recursos que estão disponíveis no banco de dados SQL do Azure e SQL Server Mecanismo de Banco de Dados.

> [!IMPORTANT]
> Uma instância gerenciada é executada com todos os recursos da versão mais recente do SQL Server, incluindo operações online, correções de plano automáticas e outros aprimoramentos de desempenho corporativo. Uma comparação dos recursos disponíveis é explicada na [comparação de recursos: Banco de dados SQL do](sql-database-features.md)Azure versus SQL Server.

| **Benefícios de PaaS** | **Continuidade do negócio** |
| --- | --- |
|Nenhuma compra e gerenciamento de hardware <br>Sem sobrecarga de gerenciamento para gerenciar a infraestrutura subjacente <br>Provisionamento rápido e dimensionamento de serviço <br>Aplicação de patch automatizada e atualização de versão <br>Integração com outros serviços de dados de PaaS |SLA de tempo de atividade de 99,99%  <br>[Alta disponibilidade](sql-database-high-availability.md) interna <br>Dados protegidos com [backups automatizados](sql-database-automated-backups.md) <br>Período de retenção de backup configurável pelo cliente <br>[Backups](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) iniciados pelo usuário <br>Funcionalidade [pontual de restauração de banco de dados](sql-database-recovery-using-backups.md#point-in-time-restore) |
|**Segurança e conformidade** | **Gerenciamento**|
|Ambiente isolado ([integração VNet](sql-database-managed-instance-connectivity-architecture.md), serviço de locatário único, computação dedicada e armazenamento) <br>[TDE (Transparent Data Encryption)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Autenticação do Azure ad](sql-database-aad-authentication.md), suporte de logon único <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Entidades de segurança do servidor do Azure AD (logons)</a> (**Visualização pública**) <br>Segue os padrões de conformidade iguais ao banco de dados SQL do Azure <br>[Auditoria do SQL](sql-database-managed-instance-auditing.md) <br>[detecção de ameaças](sql-database-managed-instance-threat-detection.md) |Azure Resource Manager API para automatizar o provisionamento e o dimensionamento de serviços <br>portal do Azure funcionalidade para provisionamento e dimensionamento de serviços manuais <br>Serviço de migração de dados

> [!IMPORTANT]
> O banco de dados SQL do Azure (todas as opções de implantação) foi certificado em relação a vários padrões de conformidade. Para obter mais informações, consulte a [central de confiabilidade do Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , em que você pode encontrar a lista mais atual de certificações de conformidade do banco de dados SQL.

Os principais recursos das instâncias gerenciadas são mostrados na tabela a seguir:

|Funcionalidade | Descrição|
|---|---|
| Versão/build SQL Server | SQL Server Mecanismo de Banco de Dados (mais recente estável) |
| Backups automatizados gerenciados | Sim |
| Métricas e monitoramento de banco de dados e instâncias internas | Sim |
| Aplicação de patch automática de software | Sim |
| Os recursos mais recentes do Mecanismo de Banco de Dados | Sim |
| Número de arquivos de dados (linhas) por banco | Vários |
| Número de arquivos de log (LOG) por banco de dados | 1 |
| Implantação de Azure Resource Manager VNet | Sim |
| VNet-modelo de implantação clássico | Não |
| Suporte do portal | Sim|
| Serviço de integração interno (SSIS) | Não-o SSIS faz parte do [Azure data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| SSAS (serviço de análise interno) | O no-SSAS é um [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) separado |
| Serviço de relatório interno (SSRS) | Sem uso Power BI ou SSRS IaaS |
|||

## <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

O [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) para instâncias gerenciadas oferece flexibilidade, controle, transparência e uma maneira simples de traduzir os requisitos de carga de trabalho local para a nuvem. Esse modelo permite que você altere a computação, a memória e o armazenamento com base nas suas necessidades de carga de trabalho. O modelo vCore também é elegível para economias de até 30% com o [benefício híbrido do Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).

No modelo vCore, você pode escolher entre gerações de hardware.

- **Gen4** As CPUs lógicas são baseadas em processadores Intel E5-2673 v3 (Haswell) 2,4-GHz, SSD anexado, núcleos físicos, 7 GB de RAM por núcleo e tamanhos de computação entre 8 e 24 vCores.
- **Gen5** As CPUs lógicas são baseadas em processadores Intel E5-2673 V4 (Broadwell) 2,3-GHz, SSD Fast NVMe, núcleo lógico Hyper-thread e tamanhos de computação entre 4 e 80 núcleos.

Encontre mais informações sobre a diferença entre as gerações de hardware em [limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).

> [!IMPORTANT]
> Novos bancos de dados Gen4 não têm mais suporte na região AustraliaEast.

## <a name="managed-instance-service-tiers"></a>Camadas de serviço de instância gerenciada

A instância gerenciada está disponível em duas camadas de serviço:

- **Uso geral**: Projetado para aplicativos com requisitos típicos de desempenho e latência de e/s.
- **Crítico**para os negócios: Projetado para aplicativos com requisitos de latência de e/s baixos e impacto mínimo sobre operações de manutenção subjacentes na carga de trabalho.

As duas camadas de serviço garantem 99,99% de disponibilidade e permitem que você selecione de modo independente o tamanho do armazenamento e a capacidade de computação. Para obter mais informações sobre a arquitetura de alta disponibilidade do banco de dados SQL do Azure, consulte [alta disponibilidade e banco de dados SQL do Azure](sql-database-high-availability.md).

### <a name="general-purpose-service-tier"></a>Camada de serviço de uso geral

A lista a seguir descreve as principais características da Uso Geral camada de serviço:

- Design para a maioria dos aplicativos de negócios com requisitos de desempenho típicos
- Armazenamento de BLOBs do Azure de alto desempenho (8 TB)
- [Alta disponibilidade](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) interna com base no armazenamento de BLOBs do Azure confiável e no [Azure Service Fabric](../service-fabric/service-fabric-overview.md)

Para obter mais informações, consulte [camada de armazenamento na camada de uso geral](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) e [práticas recomendadas de desempenho de armazenamento e considerações para instâncias gerenciadas (uso geral)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

Encontre mais informações sobre a diferença entre as camadas de serviço em [limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Comercialmente Crítico camada de serviço

Comercialmente Crítico camada de serviço é criada para aplicativos com requisitos de e/s altos. Ele oferece maior resiliência a falhas usando várias réplicas isoladas.

A lista a seguir descreve as principais características da Comercialmente Crítico camada de serviço:

- Projetado para aplicativos de negócios com mais requisitos de desempenho e alta disponibilidade
- Vem com o armazenamento SSD local extremamente rápido (até 1 TB em Gen4 e até 4 TB em Gen5)
- [Alta disponibilidade](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) interna com base em [grupos de disponibilidade Always on](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) e [Service Fabric do Azure](../service-fabric/service-fabric-overview.md).
- Réplica de banco de [dados somente leitura](sql-database-read-scale-out.md) interna adicional que pode ser usada para relatórios e outras cargas de trabalho somente leitura
- [OLTP na memória](sql-database-in-memory.md) que pode ser usado para a carga de trabalho com requisitos de alto desempenho  

Encontre mais informações sobre a diferença entre as camadas de serviço em [limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).


## <a name="managed-instance-management-operations"></a>Operações de gerenciamento de instâncias gerenciadas

O banco de dados SQL do Azure fornece operações de gerenciamento que você pode usar para implantar automaticamente novas instâncias gerenciadas, atualizar propriedades de instância e excluir instâncias quando não forem mais necessárias. Esta seção fornece informações sobre operações de gerenciamento e suas durações típicas.

Para dar suporte a implantações [em redes virtuais do Azure (VNets)](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) e fornecer isolamento e segurança para clientes, a instância gerenciada depende de [clusters virtuais](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture), que representam um conjunto dedicado de máquinas virtuais isoladas implantadas dentro do sub-rede da rede virtual do cliente. Essencialmente, cada implantação de instância gerenciada em uma sub-rede vazia resulta em um novo build-out de cluster virtual.

As operações subsequentes em instâncias gerenciadas implantadas também podem ter efeitos sobre seu cluster virtual subjacente. Isso afeta a duração das operações de gerenciamento, pois a implantação de máquinas virtuais adicionais vem com uma sobrecarga que precisa ser considerada quando você planeja novas implantações ou atualizações em instâncias gerenciadas existentes.

Todas as operações de gerenciamento podem ser categorizadas da seguinte maneira:

- Implantação de instância (nova criação de instância). 
- Atualização de instância (alterando Propriedades de instância, como vCores, armazenamento reservado, etc.).
- Exclusão da instância.

Normalmente, as operações em clusters virtuais são mais longas. A duração das operações em clusters virtuais varia – abaixo estão os valores que você normalmente pode esperar, com base em dados de telemetria de serviço existentes:

- Criação de cluster virtual. Essa é uma etapa síncrona em operações de gerenciamento de instância. **90% das operações são concluídas em 4 horas**.
- Redimensionamento do cluster virtual (expansão ou redução). A expansão é uma etapa síncrona, enquanto a redução é executada de forma assíncrona (sem afetar a duração das operações de gerenciamento de instância). **90% das expansões de cluster são concluídas em menos de 2,5 horas**.
- Exclusão do cluster virtual. A exclusão é uma etapa assíncrona, mas também pode ser [iniciada manualmente](sql-database-managed-instance-delete-virtual-cluster.md) em um cluster virtual vazio; nesse caso, ela é executada de forma síncrona. **90% das exclusões do cluster virtual são concluídas em 1,5 horas**.

Além disso, o gerenciamento de instâncias também pode incluir uma das operações em bancos de dados hospedados, o que resulta em durações mais longas:

- Anexando arquivos de banco de dados do armazenamento do Azure. Essa é uma etapa síncrona, como computação (vCore) ou expansão de armazenamento para cima ou para baixo na camada de serviço Uso Geral. **90% dessas operações são concluídas em 5 minutos**.
- Always On a propagação do grupo de disponibilidade. Essa é uma etapa síncrona, como computação (vCore) ou dimensionamento de armazenamento na camada de serviço Comercialmente Crítico, bem como na alteração da camada de serviço de Uso Geral para Comercialmente Crítico (ou vice-versa). A duração dessa operação é proporcional ao tamanho total do banco de dados, bem como à atividade atual do banco de dados (número de transações ativas). A atividade do banco de dados ao atualizar uma instância pode introduzir uma variação significativa na duração total. **90% dessas operações são executadas em 220 GB/hora ou superior**.

A tabela a seguir resume as operações e as durações gerais típicas:

|Category  |Operação  |Segmento de execução longa  |Duração estimada  |
|---------|---------|---------|---------|
|**Implementação** |Primeira instância em uma sub-rede vazia|Criação de cluster virtual|90% das operações terminam em 4 horas|
|Implementação |Primeira instância de outra geração de hardware em uma sub-rede não vazia (por exemplo, primeira instância de Gen 5 em uma sub-rede com instâncias de Gen 4)|Criação de cluster virtual *|90% das operações terminam em 4 horas|
|Implementação |Primeira instância de criação de 4 vCores, em uma sub-rede vazia ou não vazia|Criação de cluster virtual * *|90% das operações terminam em 4 horas|
|Implementação |Criação de instância subsequente dentro da sub-rede não vazia (2ª, 3ª, etc. instância)|Redimensionamento de cluster virtual|90% das operações são concluídas em 2,5 horas|
|**Atualização** |Alteração da propriedade de instância (senha de administrador, logon do AAD, Benefício Híbrido do Azure Flag)|N/A|Até 1 minuto|
|Atualizar |Expansão/redução do armazenamento de instância (Uso Geral camada de serviço)|-Redimensionamento de cluster virtual<br>-Anexando arquivos de banco de dados|90% das operações são concluídas em 2,5 horas|
|Atualizar |Expansão/redução do armazenamento de instância (Comercialmente Crítico camada de serviço)|-Redimensionamento de cluster virtual<br>-Always On propagação do grupo de disponibilidade|90% das operações são concluídas em 2,5 horas + tempo para propagar todos os bancos de dados (220 GB/hora)|
|Atualizar |Expansão e redução da vCores (computação de instância) (Uso Geral)|-Redimensionamento de cluster virtual<br>-Anexando arquivos de banco de dados|90% das operações são concluídas em 2,5 horas|
|Atualizar |Expansão e redução da vCores (computação de instância) (Comercialmente Crítico)|-Redimensionamento de cluster virtual<br>-Always On propagação do grupo de disponibilidade|90% das operações são concluídas em 2,5 horas + tempo para propagar todos os bancos de dados (220 GB/hora)|
|Atualizar |Expansão da instância para 4 vCores (Uso Geral)|-Redimensionamento do cluster virtual (se for feito pela primeira vez, pode exigir a criação do cluster virtual * *)<br>-Anexando arquivos de banco de dados|90% das operações terminam em 4 h 5 min * *|
|Atualizar |Expansão da instância para 4 vCores (Uso Geral)|-Redimensionamento do cluster virtual (se for feito pela primeira vez, pode exigir a criação do cluster virtual * *)<br>-Always On propagação do grupo de disponibilidade|90% das operações são concluídas em 4 horas + tempo para propagar todos os bancos de dados (220 GB/hora)|
|Atualizar |Alteração da camada de serviço de instância (Uso Geral para Comercialmente Crítico e vice-versa)|-Redimensionamento de cluster virtual<br>-Always On propagação do grupo de disponibilidade|90% das operações são concluídas em 2,5 horas + tempo para propagar todos os bancos de dados (220 GB/hora)|
|**Exclusão**|Exclusão da instância|Backup da parte final do log para todos os bancos de dados|90% de operações concluídas em até 1 minuto.<br>Observação: se a última instância na sub-rede for excluída, esta operação agendará a exclusão do cluster virtual após 12 horas * * *|
|Exclusão|Exclusão de cluster virtual (como operação iniciada pelo usuário)|Exclusão do cluster virtual|90% das operações terminam em até 1,5 horas|

\*O cluster virtual é criado por geração de hardware.

\*\*A opção de implantação 4 vCores foi lançada em junho de 2019 e requer uma nova versão de cluster virtual. Se você tivesse instâncias na sub-rede de destino que foram todas criadas antes de 12 de junho, um novo cluster virtual será implantado automaticamente para hospedar 4 instâncias vCore.

\*\*\*12 horas é a configuração atual, mas isso pode mudar no futuro, portanto, não faça uma dependência difícil. Se você precisar excluir um cluster virtual anteriormente (para liberar a sub-rede, por exemplo), consulte [excluir uma sub-rede depois de excluir uma instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance-delete-virtual-cluster.md).

### <a name="instance-availability-during-management"></a>Disponibilidade da instância durante o gerenciamento

As instâncias gerenciadas não estão disponíveis para aplicativos cliente durante operações de implantação e exclusão.

As instâncias gerenciadas estão disponíveis durante operações de atualização, mas há um breve tempo de inatividade causado pelo failover que ocorre no final das atualizações que normalmente duram até 10 segundos.

> [!IMPORTANT]
> A duração de um failover pode variar significativamente no caso de transações de longa execução que ocorrem nos bancos de dados devido a [tempo de recuperação](sql-database-accelerated-database-recovery.md#the-current-database-recovery-process)prolongado. Portanto, não é recomendável dimensionar a computação ou o armazenamento da instância gerenciada do banco de dados SQL do Azure ou alterar a camada de serviço ao mesmo tempo com as transações de longa execução (importação de dados, trabalhos de processamento de dados, recompilação de índice, etc.). O failover de banco de dados que será executado no final da operação cancelará as transações contínuas e resultará em tempo de recuperação prolongado.

A [recuperação de banco de dados acelerada](sql-database-accelerated-database-recovery.md) não está disponível atualmente para instâncias gerenciadas do banco de dados SQL do Azure. Uma vez habilitado, esse recurso reduzirá significativamente a variabilidade do tempo de failover, mesmo no caso de transações de longa execução.



## <a name="advanced-security-and-compliance"></a>Segurança e conformidade avançadas

A opção de implantação de instância gerenciada combina recursos avançados de segurança fornecidos pela nuvem do Azure e SQL Server Mecanismo de Banco de Dados.

### <a name="managed-instance-security-isolation"></a>Isolamento de segurança de instância gerenciada

Uma instância gerenciada fornece isolamento de segurança adicional de outros locatários na nuvem do Azure. O isolamento de segurança inclui:

- [Implementação de rede virtual nativa](sql-database-managed-instance-connectivity-architecture.md) e conectividade com seu ambiente local usando o gateway de VPN ou a rota expressa do Azure.
- Em uma implantação padrão, o ponto de extremidade SQL é exposto somente por meio de um endereço IP privado, permitindo conectividade segura de redes privadas ou do Azure privada.
- Locatário único com infraestrutura subjacente dedicada (computação, armazenamento).

O diagrama a seguir descreve várias opções de conectividade para seus aplicativos:

![alta disponibilidade](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Para saber mais detalhes sobre a integração de VNet e a imposição de diretiva de rede no nível de sub-rede, consulte [arquitetura de vnet para instâncias gerenciadas](sql-database-managed-instance-connectivity-architecture.md) e [Conecte seu aplicativo a uma instância gerenciada](sql-database-managed-instance-connect-app.md).

> [!IMPORTANT]
> Coloque várias instâncias gerenciadas na mesma sub-rede, onde quer que isso seja permitido pelos seus requisitos de segurança, pois isso trará benefícios adicionais. A colocação de instâncias na mesma sub-rede simplificará significativamente a manutenção da infraestrutura de rede e reduzirá o tempo de provisionamento da instância, desde que a duração de provisionamento longa esteja associada ao custo da implantação da primeira instância gerenciada em uma sub-rede.

### <a name="azure-sql-database-security-features"></a>Recursos de segurança do banco de dados SQL do Azure

O banco de dados SQL do Azure fornece um conjunto de recursos avançados de segurança que podem ser usados para proteger seus dados.

- A [auditoria de instância gerenciada](sql-database-managed-instance-auditing.md) rastreia eventos de banco de dados e os grava em um arquivo de log de auditoria colocado em sua conta de armazenamento do Azure. A auditoria pode ajudar a manter a conformidade regulatória, compreender a atividade do banco de dados e obter informações sobre discrepâncias e anomalias que podem indicar preocupações comerciais ou violações suspeitas de segurança.
- Criptografia de dados em movimento-uma instância gerenciada protege seus dados fornecendo criptografia para dados em movimento usando a segurança da camada de transporte. Além da segurança da camada de transporte, a opção de implantação de instância gerenciada oferece proteção de dados confidenciais em trânsito, em repouso e durante o processamento de consultas com [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted é uma funcionalidade pioneira da indústria que oferece segurança de dados sem paralelo contra violações que envolvam o roubo de dados confidenciais. Por exemplo, com Always Encrypted, os números de cartão de crédito são armazenados criptografados no banco de dados sempre, mesmo durante o processamento da consulta, permitindo a descriptografia no ponto de uso por equipes autorizadas ou aplicativos que precisam processar esses dados.
- A [detecção de ameaças](sql-database-managed-instance-threat-detection.md) complementa a [auditoria](sql-database-managed-instance-auditing.md) fornecendo uma camada adicional de inteligência de segurança incorporada ao serviço que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. Você é alertado sobre atividades suspeitas, vulnerabilidades potenciais e ataques de injeção de SQL, bem como padrões anormais de acesso ao banco de dados. Os alertas de detecção de ameaças podem ser exibidos na [central de segurança do Azure](https://azure.microsoft.com/services/security-center/) e fornecem detalhes de atividades suspeitas e recomendam ações sobre como investigar e atenuar a ameaça.  
- A [máscara de dados dinâmicos](/sql/relational-databases/security/dynamic-data-masking) limita a exposição de dados confidenciais mascarando-os para usuários sem privilégios. O mascaramento de dados dinâmicos ajuda a impedir o acesso não autorizado a dados confidenciais, permitindo que você designe a quantidade de dados confidenciais a revelar com impacto mínimo sobre a camada de aplicativo. É uma funcionalidade de segurança baseada em políticas que omite os dados confidenciais no conjunto de resultados de uma consulta em campos da base de dados designados, sendo que os dados na base de dados não são alterados.
- A [segurança em nível de linha](/sql/relational-databases/security/row-level-security) permite controlar o acesso a linhas em uma tabela de banco de dados com base nas características do usuário que está executando uma consulta (como a associação de grupo ou o contexto de execução). A segurança ao nível da linha (RLS) simplifica o design e a programação da segurança na sua aplicação. O RLS permite-lhe implementar restrições ao acesso à linha de dados. Por exemplo, garantir que os trabalhadores possam acessar apenas as linhas de dados que são pertinentes ao departamento ou restringir um acesso a dados somente para os dados relevantes.
- A [TDE (Transparent Data Encryption)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) criptografa arquivos de dados de instância gerenciada, conhecidos como criptografia de dados em repouso. O TDE executa criptografia e descriptografia de e/s em tempo real dos arquivos de dados e de log. A criptografia usa uma DEK (chave de criptografia de banco de dados), que é armazenada no registro de inicialização do banco de dados para disponibilidade durante a recuperação. Você pode proteger todos os seus bancos de dados em uma instância gerenciada com Transparent Data Encryption. O TDE é uma tecnologia comprovada de criptografia em repouso SQL Server que é exigida por muitos padrões de conformidade para proteger contra roubo de mídia de armazenamento.

A migração de um banco de dados criptografado para uma instância gerenciada tem suporte por meio do serviço de migração de banco de dados do Azure (DMS) ou da restauração nativa. Se você planeja migrar um banco de dados criptografado usando a restauração nativa, a migração do certificado TDE existente do SQL Server local ou SQL Server em uma máquina virtual para uma instância gerenciada é uma etapa necessária. Para obter mais informações sobre opções de migração, consulte [SQL Server instância migração para instância gerenciada](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Integração do Azure Active Directory

A opção de implantação de instância gerenciada dá suporte a logons e logons tradicionais do mecanismo de banco de dados do SQL Server integrados ao AAD (Azure Active Directory). Entidades de segurança do servidor do Azure AD (logons) (**Visualização pública**) são a versão da nuvem do Azure de logons de banco de dados local que você está usando em seu ambiente local. As entidades de segurança do servidor do Azure AD (logons) permitem que você especifique usuários e grupos do seu locatário Azure Active Directory como entidades de segurança com escopo de instância verdadeiras, capaz de executar qualquer operação em nível de instância, incluindo consultas entre bancos de dados dentro do mesmo gerenciado cópia.

Uma nova sintaxe é introduzida para criar entidades de segurança do servidor do Azure AD (logons) (**Visualização pública**), **do provedor externo**. Para obter mais informações sobre a sintaxe, consulte <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">criar logon</a>e examine o artigo provisionar [um administrador de Azure Active Directory para sua instância gerenciada](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) .

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integração do Azure Active Directory e a autenticação multifator

A opção de implantação de instância gerenciada permite que você gerencie centralmente identidades do usuário do banco de dados e outros serviços da Microsoft com a [integração Azure Active Directory](sql-database-aad-authentication.md). Esta capacidade simplifica a gestão de permissões e melhora a segurança. O Azure Active Directory suporta a [autenticação multifator](sql-database-ssms-mfa-authentication-configure.md) (MFA), para aumentar a segurança de dados e aplicações, suportando, ao mesmo tempo, um processo de início de sessão único.

### <a name="authentication"></a>Authentication

A autenticação de instância gerenciada refere-se a como os usuários provam sua identidade ao se conectarem ao banco de dados. A Base de Dados SQL suporta dois tipos de autenticação:  

- **Autenticação do SQL**:

  Esse método de autenticação usa um nome de usuário e senha.
- **Autenticação Azure Active Directory**:

  Esse método de autenticação usa identidades gerenciadas pelo Azure Active Directory e tem suporte para domínios gerenciados e integrados. Utilize a autenticação do Active Directory (segurança integrada) [sempre que possível](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

### <a name="authorization"></a>Autorização

A autorização refere-se ao que um usuário pode fazer em um banco de dados SQL do Azure e é controlada pelas associações de função de banco de dados da sua conta de usuário e permissões em nível de objeto. Uma instância gerenciada tem os mesmos recursos de autorização que o SQL Server 2017.

## <a name="database-migration"></a>Migração de bases de dados

A opção de implantação de instância gerenciada destina cenários de usuário com migração de banco de dados em massa de implementações locais ou de banco de dados IaaS. A instância gerenciada suporta várias opções de migração de banco de dados:

### <a name="back-up-and-restore"></a>Criar cópias de segurança e restauro  

A abordagem de migração aproveita os backups do SQL para o armazenamento de BLOBs do Azure. Os backups armazenados no Azure Storage blob podem ser restaurados diretamente em uma instância gerenciada usando o [comando T-SQL RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- Para obter um guia de início rápido mostrando como restaurar a Wide World Importers-arquivo de backup de banco de dados padrão, consulte [restaurar um arquivo de backup para uma instância gerenciada](sql-database-managed-instance-get-started-restore.md). Este guia de início rápido mostra que você precisa carregar um arquivo de backup no armazenamento de blog do Azure e protegê-lo usando uma chave de assinatura de acesso compartilhado (SAS).
- Para obter informações sobre a restauração de URL, consulte [Native Restore from URL](sql-database-managed-instance-migrate.md#native-restore-from-url).

> [!IMPORTANT]
> Os backups de uma instância gerenciada só podem ser restaurados para outra instância gerenciada. Eles não podem ser restaurados em um SQL Server local ou em um único banco de dados/pool elástico.

### <a name="data-migration-service"></a>Serviço de migração de dados

O serviço de migração de banco de dados do Azure é um serviço totalmente gerenciado projetado para permitir migrações diretas de várias fontes de banco de dados para plataformas de data do Azure com tempo de inatividade mínimo Esse serviço simplifica as tarefas necessárias para mover bancos de dados existentes e de SQL Server de terceiros para o banco de dados SQL do Azure (bancos de dados individuais, bancos de dados em pool em pools elásticos e bancos de dados de instância em uma instância gerenciada) e SQL Server na VM do Azure. Veja [como migrar seu banco de dados local para a instância gerenciada usando DMS](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Recursos do SQL com suporte

A opção de implantação de instância gerenciada visa entregar cerca de 100% de compatibilidade de área de superfície com SQL Server locais em estágios até a disponibilidade geral do serviço. Para obter uma lista de recursos e comparação, consulte comparação de recursos do [banco de dados SQL](sql-database-features.md)e para obter uma lista de diferenças de t-SQL em instâncias gerenciadas versus SQL Server, consulte [diferenças t-SQL de instância gerenciada de SQL Server](sql-database-managed-instance-transact-sql-information.md).

A opção de implantação de instância gerenciada dá suporte à compatibilidade com versões anteriores para bancos de dados do SQL 2008. Há suporte para a migração direta de servidores de banco de dados SQL 2005, o nível de compatibilidade para bancos de dados SQL 2005 migrados é atualizado para o SQL 2008.
  
O diagrama a seguir descreve a compatibilidade da área de superfície na instância gerenciada:  

![as](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>Principais diferenças entre SQL Server local e em uma instância gerenciada

A opção de implantação de instância gerenciada beneficia-se de estar sempre atualizado na nuvem, o que significa que alguns recursos no local SQL Server podem ser obsoletos, desativados ou ter alternativas. Há casos específicos em que as ferramentas precisam reconhecer que um recurso específico funciona de forma ligeiramente diferente ou que o serviço não está em execução em um ambiente que você não controla totalmente:

- A alta disponibilidade é incorporada e pré-configurada usando uma tecnologia semelhante a [Always on grupos de disponibilidade](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Backups automatizados e restauração pontual. O cliente pode `copy-only` iniciar backups que não interferem na cadeia de backup automática.
- A instância gerenciada não permite especificar caminhos físicos completos para que todos os cenários correspondentes tenham suporte diferentemente: RESTOre DB não dá suporte ao MOVE, CREATE DB não permite caminhos físicos, BULK INSERT funciona somente com BLOBs do Azure, etc.
- A instância gerenciada dá suporte à [autenticação do Azure ad](sql-database-aad-authentication.md) como alternativa à nuvem para autenticação do Windows.
- A instância gerenciada gerencia automaticamente o grupo de arquivos e os arquivos XTP para bancos de dados que contêm objetos OLTP na memória
- A instância gerenciada dá suporte a SQL Server Integration Services (SSIS) e pode hospedar o catálogo do SSIS (SSISDB) que armazena os pacotes do SSIS, mas eles são executados em um Integration Runtime do Azure-SSIS gerenciado (IR) no Azure Data Factory (ADF), consulte [criar um ir do Azure-SSIS no ADF ](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Para comparar os recursos do SSIS no banco de dados SQL, confira [comparar bancos de dados individuais do Azure SQL/pools elásticos e instância gerenciada](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance).

### <a name="managed-instance-administration-features"></a>Recursos de administração da instância gerenciada

A opção de implantação de instância gerenciada permite que o administrador do sistema gaste menos tempo em tarefas administrativas porque o serviço do banco de dados SQL as executa para você ou simplifica bastante essas tarefas. Por exemplo, [instalação de so/RDBMS e aplicação de patch](sql-database-high-availability.md), redimensionamento [e configuração de instância dinâmica](sql-database-single-database-scale.md), backups, [replicação de banco de dados](replication-with-sql-database-managed-instance.md) (incluindo bancos de dados do sistema), [configuração de alta disponibilidade](sql-database-high-availability.md)e [](sql-database-automated-backups.md) configuração de fluxos de dados de monitoramento de integridade e [desempenho](../azure-monitor/insights/azure-sql.md) .

> [!IMPORTANT]
> Para obter uma lista de recursos com suporte, com suporte parcial e sem suporte, consulte [recursos do banco de dados SQL](sql-database-features.md). Para obter uma lista de diferenças de T-SQL em instâncias gerenciadas versus SQL Server, consulte [diferenças de t-SQL de instância gerenciada de SQL Server](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Como identificar programaticamente uma instância gerenciada

A tabela a seguir mostra várias propriedades, acessíveis por meio do Transact SQL, que você pode usar para detectar que seu aplicativo está trabalhando com a instância gerenciada e recuperar propriedades importantes.

|Propriedade|Value|Comentário|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM)-12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Esse valor é o mesmo que no banco de dados SQL.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|Esse valor é o mesmo que no banco de dados SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Esse valor identifica exclusivamente uma instância gerenciada.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nome DNS de instância completa no seguinte formato:`<instanceName>`.`<dnsPrefix>`.Database.Windows.NET, onde `<instanceName>` é o nome fornecido pelo cliente, enquanto `<dnsPrefix>` é gerado automaticamente parte do mesmo garantindo a exclusividade de nome DNS global ("wcus17662feb9ce98", por exemplo)|Exemplo: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Passos Seguintes

- Para saber como criar sua primeira instância gerenciada, consulte [Guia de início rápido](sql-database-managed-instance-get-started.md).
- Para obter uma lista de recursos e comparação, consulte [recursos comuns do SQL](sql-database-features.md).
- Para obter mais informações sobre a configuração de VNet, consulte [configuração de vnet de instância gerenciada](sql-database-managed-instance-connectivity-architecture.md).
- Para obter um início rápido que cria uma instância gerenciada e restaura um banco de dados de um arquivo de backup, consulte [criar uma instância gerenciada](sql-database-managed-instance-get-started.md).
- Para obter um tutorial usando o serviço de migração de banco de dados do Azure (DMS) para migração, consulte [migração de instância gerenciada usando DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Para monitoramento avançado do desempenho do banco de dados de instância gerenciada com inteligência de solução de problemas interna, consulte [monitorar o banco de dados SQL do Azure usando análise de SQL do Azure](../azure-monitor/insights/azure-sql.md).
- Para obter informações sobre preços, consulte [preços da instância gerenciada do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
