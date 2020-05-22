---
title: Visão geral da instância gerida pela SQL
description: Este artigo descreve a instância gerida pela Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 04/02/2020
ms.openlocfilehash: 55e56d918b1e0c16887f069713203a7e4701baaf
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772323"
---
# <a name="what-is-azure-sql-database-managed-instance"></a>O que é a base de dados Azure SQL gerida?

A instância gerida é uma nova opção de implementação da Base de Dados Azure SQL, proporcionando quase 100% de compatibilidade com o mais recente Motor de Base de Dados SQL Server on-premise (Enterprise Edition), fornecendo uma implementação de rede virtual nativa [(VNet)](../virtual-network/virtual-networks-overview.md) que aborda preocupações comuns de segurança, e um modelo de [negócio](https://azure.microsoft.com/pricing/details/sql-database/) favorável para clientes do SQL Server no local. O modelo de implementação de instâncias geridas permite que os clientes sQL Server existentes levantem e transvam as suas aplicações no local para a nuvem com alterações mínimas de aplicação e base de dados. Ao mesmo tempo, a opção de implementação de instância gerida preserva todas as capacidades operacionais do PaaS no que diz respeito a atualizações automáticas de remendos e versões, [backups automatizados](sql-database-automated-backups.md), e [alta disponibilidade,](sql-database-high-availability.md)que reduz drasticamente a gestão aérea e a TCO.

> [!IMPORTANT]
> Para uma lista das regiões em que a opção de implantação de instâncias geridas está atualmente disponível, consulte [as regiões apoiadas.](sql-database-managed-instance-resource-limits.md#supported-regions)

O diagrama seguinte descreve as principais características das instâncias geridas:

![principais características](./media/sql-database-managed-instance/key-features.png)

O modelo de implantação de instâncias geridas foi projetado para clientes que procuram migrar um grande número de aplicações a partir de instalações ou IaaS, auto-construídos ou ISV fornecidos ambiente para ambiente de nuvem PaaS totalmente gerido, com o menor esforço de migração possível. Utilizando o Serviço de Migração de Dados totalmente automatizado [(DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) em Azure, os clientes podem levantar e transferir o seu SQL Server no local para uma instância gerida que oferece compatibilidade com o SQL Server no local e o isolamento completo de instâncias de clientes com suporte vNet nativo.  Com a Garantia de Software, pode trocar as suas licenças existentes por tarifas com desconto numa instância gerida utilizando o [Benefício Híbrido Azure para o Servidor SQL](https://azure.microsoft.com/pricing/hybrid-benefit/).  Um caso gerido é o melhor destino de migração na nuvem para casos de SQL Server que requerem alta segurança e uma rica superfície de programação.

A opção de implementação de instância gerida visa fornecer cerca de 100% de compatibilidade com a área de superfície com a versão mais recente no local SQL Server através de um plano de lançamento encenado.

Para decidir entre as opções de implementação da Base de Dados Azure SQL: base de dados única, base de dados reunida e instância gerida, e SQL Server hospedado em máquina virtual, veja [como escolher a versão certa do SQL Server em Azure](sql-database-paas-vs-sql-server-iaas.md).

## <a name="key-features-and-capabilities"></a>Principais funcionalidades e capacidades

O exemplo gerido combina as melhores funcionalidades disponíveis tanto na Base de Dados Azure SQL como no Motor de Base de Dados do Servidor SQL.

> [!IMPORTANT]
> Uma instância gerida funciona com todas as funcionalidades da versão mais recente do SQL Server, incluindo operações online, correções automáticas de planos e outras melhorias de desempenho da empresa. Uma comparação das funcionalidades disponíveis é explicada na [comparação de funcionalidades: Base de Dados Azure SQL versus SQL Server](sql-database-features.md).

| **Benefícios paaS** | **Continuidade do negócio** |
| --- | --- |
|Sem compra e gestão de hardware <br>Sem despesas gerais de gestão para gerir infraestruturas subjacentes <br>Fornecimento rápido e escala de serviço <br>Patching automatizado e atualização de versão <br>Integração com outros serviços de dados paaS |99,99% de uptime SLA  <br>Construído em [alta disponibilidade](sql-database-high-availability.md) <br>Dados protegidos com [cópias de segurança automatizadas](sql-database-automated-backups.md) <br>Período de retenção de backup configurável do cliente <br>[Backups iniciados](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) pelo utilizador <br>[Capacidade de restauro](sql-database-recovery-using-backups.md#point-in-time-restore) da base de dados de ponto no tempo |
|**Segurança e conformidade** | **Gestão**|
|Ambiente isolado[(integração VNet,](sql-database-managed-instance-connectivity-architecture.md)serviço de inquilino único, computação e armazenamento dedicados) <br>[Encriptação transparente de dados (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Autenticação Azure AD,](sql-database-aad-authentication.md)suporte único de sinalização <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Diretores de servidores Azure AD (logins)</a>  <br>Adere às normas de conformidade iguais às bases de dados Azure SQL <br>[Auditoria SQL](sql-database-managed-instance-auditing.md) <br>[Advanced Threat Protection](sql-database-managed-instance-threat-detection.md) |API gestor de recursos azure para automatizar prestação e escalação de serviços <br>Funcionalidade do portal Azure para fornecimento e escalagem de serviços manuais <br>Serviço de Migração de Dados

> [!IMPORTANT]
> A Base de Dados Azure SQL (todas as opções de implementação), foi certificada contra uma série de normas de conformidade. Para mais informações, consulte o [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) onde pode encontrar a lista mais atual das certificações de conformidade da Base de Dados SQL.

As principais características dos casos geridos são mostradas na tabela seguinte:

|Funcionalidade | Descrição|
|---|---|
| Versão / construção do SQL Server | Motor de base de dados do Servidor SQL (último estável) |
| Backups automatizados geridos | Sim |
| Instância incorporada e monitorização e métricas de base de dados | Sim |
| Patching automático de software | Sim |
| As mais recentes funcionalidades do Motor de Base de Dados | Sim |
| Número de ficheiros de dados (ROWS) por base de dados | Vários |
| Número de ficheiros de registo (LOG) por base de dados | 1 |
| VNet - Implantação do Gestor de Recursos Azure | Sim |
| VNet - Modelo de implantação clássico | Não |
| Suporte do portal | Sim|
| Serviço integrado de Integração (SSIS) | Não - O SSIS faz parte da [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Serviço de Análise Incorporada (SSAS) | Não - SSAS é [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) separado |
| Serviço de Reporte Incorporado (SSRS) | Não - utilize [relatórios paginados Power BI](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) ou aperte sSRS em Azure VM. Embora a Managed Instance não possa executar o SSRS como um serviço, pode hospedar bases de dados de catálogo SSRS 2019 para um servidor de relatórios externo usando a autenticação do Servidor SQL. |
|||

## <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

O modelo de compra baseado em vCore para casos geridos [dá-lhe](sql-database-service-tiers-vcore.md) flexibilidade, controlo, transparência e uma forma simples de traduzir os requisitos de carga de trabalho no local para a nuvem. Este modelo permite-lhe alterar a computação, a memória e o armazenamento com base nas suas necessidades de carga de trabalho. O modelo vCore também é elegível para poupanças até 55% com o Benefício Híbrido Azure para o [Servidor SQL](https://azure.microsoft.com/pricing/hybrid-benefit/).

No modelo vCore, pode escolher entre gerações de hardware.

- **Gen4** Os CPUs lógicos baseiam-se em processadores Intel E5-2673 v3 (Haswell) 2.4-GHz, SSD anexados, núcleos físicos, RAM de 7 GB por núcleo, e tamanhos de computação entre 8 e 24 vCores.
- **Gen5** Os CPUs lógicos baseiam-se em processadores Intel E5-2673 v4 (Broadwell) 2.3-GHz e Intel SP-8160 (Skylake), rápido NVMe SSD, núcleo lógico hiper-roscado e tamanhos de computação entre 4 e 80 núcleos.

Encontre mais informações sobre a diferença entre gerações de hardware nos [limites de recursos geridos](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)por exemplo .

## <a name="managed-instance-service-tiers"></a>Escalões de serviço da Instância gerida

A instância gerida está disponível em dois níveis de serviço:

- **Finalidade geral**: Concebido para aplicações com requisitos típicos de desempenho e latência IO.
- **Crítico de negócios**: Projetado para aplicações com baixos requisitos de latência io e impacto mínimo das operações de manutenção subjacentes na carga de trabalho.

Ambos os níveis de serviço garantem uma disponibilidade de 99,99% e permitem selecionar de forma independente o tamanho do armazenamento e a capacidade computacional. Para obter mais informações sobre a arquitetura de alta disponibilidade da Base de Dados Azure SQL, consulte alta disponibilidade e Base de [Dados Azure SQL](sql-database-high-availability.md).

### <a name="general-purpose-service-tier"></a>Nível de serviço para fins gerais

A seguinte lista descreve a característica-chave do nível de serviço "Propósito Geral":

- Design para a maioria das aplicações empresariais com requisitos típicos de desempenho
- Armazenamento Azure Blob de alto desempenho (8 TB)
- Embutida [de alta disponibilidade](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) com base no armazenamento fiável da Blob Azure e no Tecido de Serviço [Azure](../service-fabric/service-fabric-overview.md)

Para mais informações, consulte a [camada de armazenamento em nível geral](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) e desempenho de armazenamento boas [práticas e considerações para instâncias geridas (finalidade geral)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

Encontre mais informações sobre a diferença entre os níveis de serviço nos [limites de recursos geridos](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)por exemplo.

### <a name="business-critical-service-tier"></a>Nível de serviço Business Critical

O nível de serviço Business Critical foi construído para aplicações com elevados requisitos io. Oferece a maior resiliência a falhas usando várias réplicas isoladas.

A lista que se segue descreve as características-chave do nível de serviço Business Critical:

- Projetado para aplicações empresariais com maior desempenho e requisitos de HA
- Vem com armazenamento sSD local super rápido (até 1 TB em Gen4 e até 4 TB em Gen5)
- [Elevada disponibilidade](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) incorporada com base em Sempre [Em Grupos de Disponibilidade](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) e Tecido de Serviço [Azure.](../service-fabric/service-fabric-overview.md)
- Réplica de [base de dados](sql-database-read-scale-out.md) adicional de leitura incorporada que pode ser usada para reportar e outras cargas de trabalho apenas para leitura
- [In-Memory OLTP](sql-database-in-memory.md) que pode ser usado para carga de trabalho com requisitos de alto desempenho  

Encontre mais informações sobre a diferença entre os níveis de serviço nos [limites de recursos geridos](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)por exemplo.


## <a name="managed-instance-management-operations"></a>Operações de gestão de instâncias geridas

A Base de Dados SQL do Azure fornece operações de gestão que podem servir para implementar automaticamente novas instâncias geridas, atualizar propriedades de instâncias e eliminar instâncias quando já não precisar delas. Esta secção fornece informações sobre operações de gestão e as suas durações típicas.

Para apoiar as implementações dentro das [Redes Virtuais Azure (VNets)](../virtual-network/virtual-network-for-azure-services.md) e proporcionar isolamento e segurança aos clientes, a instância gerida depende de [clusters virtuais,](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture)que representam um conjunto dedicado de máquinas virtuais isoladas implantadas dentro da subnet virtual do cliente. Essencialmente, cada implantação de instâncias geridas numa subnet vazia resulta numa nova estrutura de cluster virtual.

As operações subsequentes em casos geridos implementados podem também ter efeitos no seu cluster virtual subjacente. Isto afeta a duração das operações de gestão, uma vez que a implantação de máquinas virtuais adicionais vem com uma sobrecarga que precisa de ser considerada quando planeia novas implementações ou atualizações para instâncias geridas existentes.

Todas as operações de gestão podem ser categorizadas da seguinte forma:

- Implantação de instâncias (criação de novos exemplos). 
- Atualização de instâncias (alteração de propriedades de instâncias, tais como vCores ou armazenamento reservado.
- Supressão de instâncias.

Normalmente, as operações em aglomerados virtuais demoram mais tempo. A duração das operações em clusters virtuais variam – abaixo estão os valores que normalmente se pode esperar, com base nos dados de telemetria de serviçoexistentes:

- Criação de cluster virtual. Trata-se de um passo sincronizado em operações de gestão. **90% das operações terminam em 4 horas.**
- Redimensionamento de cluster virtual (expansão ou redução). A expansão é um passo sincronizado, enquanto a redução é realizada de forma assíncrona (sem impacto na duração das operações de gestão de instâncias). **90% das expansões do cluster terminam em menos de 2,5 horas.**
- Eliminação virtual do cluster. A eliminação é um passo assíncrono, mas também pode ser [iniciado manualmente](sql-database-managed-instance-delete-virtual-cluster.md) num aglomerado virtual vazio, caso em que executa sincronizadamente. **90% das exclusões de cluster saem em 1,5 horas.**

Além disso, a gestão de instâncias pode também incluir uma das operações em bases de dados hospedadas, o que resulta em durações mais longas:

- Anexar ficheiros de base de dados do Armazenamento Azure. Este é um passo sincronizado, como a computação (vCore), ou a escala de armazenamento para cima ou para baixo no nível de serviço de Propósito Geral. **90% destas operações terminam em 5 minutos.**
- Sempre em sementeação de grupo de disponibilidade. Trata-se de um passo sincronizado, como a computação (vCore), ou a escala de armazenamento no nível de serviço Business Critical, bem como a alteração do nível de serviço de Propósito Geral para Business Critical (ou vice-versa). A duração desta operação é proporcional ao tamanho total da base de dados, bem como à atividade atual da base de dados (número de transações ativas). A atividade da base de dados ao atualizar uma instância pode introduzir uma variação significativa na duração total. **90% destas operações executam a 220 GB/hora ou superior**.

O quadro seguinte resume as operações e as durações globais típicas:

|Categoria  |Operação  |Segmento de longo prazo  |Duração estimada  |
|---------|---------|---------|---------|
|**Implementação** |Primeira instância numa sub-rede vazia|Criação de cluster virtual|90% das operações terminam em 4 horas|
|Implementação |Primeira instância de outra geração de hardware numa subnet não vazia (por exemplo, primeira instância de Gen 5 numa subnet com instâncias gen 4)|Criação de cluster virtual*|90% das operações terminam em 4 horas|
|Implementação |Criação em primeira instância de 4 vCores, numa subnet vazia ou não vazia|Criação de cluster virtual**|90% das operações terminam em 4 horas|
|Implementação |Criação subsequente de instâncias dentro da sub-rede não vazia (2º, 3º, etc. instância)|Redimensionamento de cluster virtual|90% das operações terminam em 2,5 horas|
|**Atualizar** |Mudança de propriedade por exemplo (senha de administração, login AAD, bandeira de benefício híbrido Azure)|N/D|Até 1 minuto|
|Atualizar |Por exemplo, a escala de armazenamento para cima/para baixo (nível de serviço de propósito geral)|Anexar ficheiros de base de dados|90% das operações terminam em 5 minutos|
|Atualizar |Por exemplo, o armazenamento de armazenamento para cima/para baixo (nível de serviço Business Critical)|- Redimensionamento de cluster virtual<br>- Sempre em sementeação de grupo de disponibilidade|90% das operações terminam em 2,5 horas + tempo para sementes todas as bases de dados (220 GB/hora)|
|Atualizar |Por exemplo, a computação (vCores) a escalonar para cima e para baixo (Propósito Geral)|- Redimensionamento de cluster virtual<br>- Anexação de ficheiros de base de dados|90% das operações terminam em 2,5 horas|
|Atualizar |Compute de instância (vCores) escalando para cima e para baixo (Business Critical)|- Redimensionamento de cluster virtual<br>- Sempre em sementeação de grupo de disponibilidade|90% das operações terminam em 2,5 horas + tempo para sementes todas as bases de dados (220 GB/hora)|
|Atualizar |Escala de exemplo para baixo para 4 vCores (Propósito Geral)|- Redimensionamento de clustervirtual (se feito pela primeira vez, pode exigir criação de cluster virtual**)<br>- Anexação de ficheiros de base de dados|90% das operações terminam em 4 h 5 min**|
|Atualizar |Escala de instância para baixo para 4 vCores (Business Critical)|- Redimensionamento de clustervirtual (se feito pela primeira vez, pode exigir criação de cluster virtual**)<br>- Sempre em sementeação de grupo de disponibilidade|90% das operações terminam em 4 horas + tempo para sementem todas as bases de dados (220 GB/hora)|
|Atualizar |Mudança de nível de serviço de instância (Propósito Geral para Business Critical e vice-versa)|- Redimensionamento de cluster virtual<br>- Sempre em sementeação de grupo de disponibilidade|90% das operações terminam em 2,5 horas + tempo para sementes todas as bases de dados (220 GB/hora)|
|**Eliminação**|Eliminação da instância|Cópia de segurança da cauda de log para todas as bases de dados|90% de operações terminam em até 1 minuto.<br>Nota: se a última instância na sub-rede for eliminada, esta operação irá agendar a eliminação do cluster virtual após 12 horas***|
|Eliminação|Eliminação virtual do cluster (como operação iniciada pelo utilizador)|Eliminação de cluster virtual|90% das operações terminam em até 1,5 horas|

\*O cluster virtual é construído por geração de hardware.

\*\*A opção de implementação de 4 vCores foi lançada em junho de 2019 e requer uma nova versão de cluster virtual. Se tiver tido casos na subnet-alvo que foram todos criados antes de 12 de junho, um novo cluster virtual será implantado automaticamente para acolher 4 instâncias vCore.

\*\*\*12 horas é a configuração atual, mas isso pode mudar no futuro, por isso não tomes uma dependência dura dela. Se precisar de eliminar um cluster virtual mais cedo (para libertar a sub-rede, por exemplo), consulte Apagar uma sub-rede depois de eliminar uma instância gerida pela Base de [Dados Azure SQL](sql-database-managed-instance-delete-virtual-cluster.md).

### <a name="instance-availability-during-management-operations"></a>Disponibilidade de exemplo durante operações de gestão

A instância gerida não está disponível para aplicações de clientes durante operações de implantação e eliminação.

A instância gerida está disponível durante as operações de atualização, exceto um curto período de inatividade causado pela falha que ocorre no final da atualização. Normalmente dura até 10 segundos mesmo em caso de transações interrompidas a longo prazo, graças à recuperação da base de [dados Acelerada](sql-database-accelerated-database-recovery.md).

> [!TIP]
> A atualização do espaço de armazenamento reservado no nível de serviço General Purpose não incorre em falhas nem afeta a disponibilidade de instâncias.

> [!IMPORTANT]
> Não é aconselhável escalar a computação ou o armazenamento da Base de Dados Azure SQL gerida ou alterar o nível de serviço ao mesmo tempo com as transações de longo prazo (importação de dados, empregos de processamento de dados, reconstrução de índices, etc.). A falha na base de dados que será realizada no final da operação cancelará todas as transações em curso.


### <a name="management-operations-cross-impact"></a>Operações de gestão com impacto cruzado

As operações de gestão de instâncias geridas podem afetar outras operações de gestão das instâncias colocadas dentro do mesmo cluster virtual. Isto inclui o seguinte:

- **As operações** de restauro de longo prazo num cluster virtual colocarão em espera outras operações de criação ou escalação na mesma sub-rede.<br/>**Exemplo:** se houver uma operação de restauro de longo curso e houver um pedido de criação ou escala na mesma sub-rede, este pedido demorará mais tempo a ser concluído, uma vez que aguardará a conclusão da operação de restauro antes de continuar.
    
- Posteriormente, a **criação ou** operação de escala por exemplo é posta em suspenso pela criação de instâncias ou escala de instância seleções previamente iniciadas que iniciaram a redimensionamento do cluster virtual.<br/>**Exemplo:** se houver múltiplos pedidos de criação e/ou escala na mesma sub-rede sob o mesmo cluster virtual, e um deles iniciar o redimensionamento do cluster virtual, todos os pedidos que foram submetidos 5+ minutos após o necessário redimensionamento do cluster virtual durarão mais tempo do que o esperado, uma vez que estes pedidos terão de esperar que o redimensionamento seja concluído antes de retomar.

- **As operações de criação/escala submetidas em janela** de 5 minutos serão lotadas e executadas em paralelo.<br/>**Exemplo:** Apenas um cluster virtual será realizado para todas as operações submetidas em janela de 5 minutos (medindo a partir do momento da execução do primeiro pedido de operação). Caso outro pedido seja apresentado mais de 5 minutos após a apresentação do primeiro, aguardará que o cluster virtual seja concluído antes do início da execução.

> [!IMPORTANT]
> As operações de gestão que são postas em suspenso por causa de outra operação em curso serão automaticamente retomadas assim que as condições para prosseguir. Não é necessária qualquer ação do utilizador para retomar a operação de gestão temporariamente interrompida.

### <a name="canceling-management-operations"></a>Cancelamento de operações de gestão

O quadro seguinte resume a capacidade de cancelar operações de gestão específicas e as durações globais típicas:

Categoria  |Operação  |Cancelável  |Duração estimada do cancelamento  |
|---------|---------|---------|---------|
|Implementação |Criação de instâncias |Não |  |
|Atualizar |Por exemplo, a escala de armazenamento para cima/para baixo (Propósito Geral) |Não |  |
|Atualizar |Por exemplo, o armazenamento para cima/para baixo (Business Critical) |Sim |90% das operações terminam em 5 minutos |
|Atualizar |Por exemplo, a computação (vCores) a escalonar para cima e para baixo (Propósito Geral) |Sim |90% das operações terminam em 5 minutos |
|Atualizar |Compute de instância (vCores) escalando para cima e para baixo (Business Critical) |Sim |90% das operações terminam em 5 minutos |
|Atualizar |Mudança de nível de serviço de instância (Propósito Geral para Business Critical e vice-versa) |Sim |90% das operações terminam em 5 minutos |
|Eliminar |Eliminação da instância |Não |  |
|Eliminar |Eliminação virtual do cluster (como operação iniciada pelo utilizador) |Não |  |

Para cancelar a operação de gestão, vá à lâmina de visão geral e clique na caixa de notificação da operação em curso. Do lado direito, aparecerá um ecrã com operação em curso e haverá botão para cancelamento de operação. Após o primeiro clique, será-lhe pedido que clique novamente e confirme que pretende cancelar a operação.

[![](./media/sql-database-managed-instance/canceling-operation.png)](./media/sql-database-managed-instance/canceling-operation.png#lightbox)

Depois de o pedido de cancelamento ter sido submetido e processado, receberá notificação se a submissão de cancelamento tiver sido bem sucedida ou não. 

Em caso de cancelamento de sucesso, a operação de gestão será cancelada em alguns minutos, resultando numa falha.

![cancelamento resultado da operação](./media/sql-database-managed-instance/canceling-operation-result.png)

Se o pedido de cancelamento falhar ou cancelar o botão não estiver ativo, isso significa que a operação de gestão entrou em estado não cancelável e que terminará em alguns minutos. A operação de gestão continuará a sua execução até que esteja concluída.

> [!IMPORTANT]
> A operação de cancelamento é atualmente suportada apenas no Portal.

## <a name="advanced-security-and-compliance"></a>Segurança e conformidade avançadas

A opção de implementação de instância gerida combina funcionalidades de segurança avançadas fornecidas pela nuvem Azure e pelo Motor de Base de Dados do Servidor SQL.

### <a name="managed-instance-security-isolation"></a>Isolamento de segurança de instância gerida

Um caso gerido proporciona um isolamento de segurança adicional de outros inquilinos na nuvem azure. O isolamento de segurança inclui:

- [Implementação](sql-database-managed-instance-connectivity-architecture.md) de rede virtual nativa e conectividade com o seu ambiente no local usando a Rota Expresso Azure ou o Gateway VPN.
- Numa implementação padrão, o ponto final do SQL é exposto apenas através de um endereço IP privado, permitindo uma conectividade segura a partir de redes privadas azure ou híbridas.
- Inquilino único com infraestrutura subjacente dedicada (computação, armazenamento).

O diagrama seguinte descreve várias opções de conectividade para as suas aplicações:

![alta disponibilidade](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Para saber mais detalhes sobre a integração vnet e a aplicação da política de networking a nível da subnet, consulte a [arquitetura VNet para casos geridos](sql-database-managed-instance-connectivity-architecture.md) e [ligue a sua aplicação a uma instância gerida](sql-database-managed-instance-connect-app.md).

> [!IMPORTANT]
> Coloque vários exemplos geridos na mesma subnet, onde quer que seja permitido pelos seus requisitos de segurança, pois isso lhe trará benefícios adicionais. A collocalização de casos na mesma subrede simplificará significativamente a manutenção da infraestrutura em rede e reduzirá o tempo de provisionamento de instâncias, uma vez que a longa duração do provisionamento está associada ao custo de implantação da primeira instância gerida numa subnet.

### <a name="azure-sql-database-security-features"></a>Funcionalidades de Segurança da Base de Dados Azure SQL

A Base de Dados Azure SQL fornece um conjunto de funcionalidades avançadas de segurança que podem ser usadas para proteger os seus dados.

- [A auditoria de exemplo gerida](sql-database-managed-instance-auditing.md) rastreia eventos de base de dados e escreve-os num ficheiro de registo de auditoria colocado na sua conta de armazenamento Azure. A auditoria pode ajudar a manter o cumprimento regulamentar, compreender a atividade da base de dados e obter informações sobre discrepâncias e anomalias que possam indicar preocupações comerciais ou suspeitas de violações de segurança.
- Encriptação de dados em movimento - uma instância gerida protege os seus dados fornecendo encriptação para dados em movimento usando a Segurança da Camada de Transporte. Além da segurança da camada de transporte, a opção de implementação de instânciagerida oferece proteção de dados sensíveis em voo, em repouso e durante o processamento de consulta com [Sempre Encriptado](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted é uma funcionalidade pioneira da indústria que oferece segurança de dados sem paralelo contra violações que envolvam o roubo de dados confidenciais. Por exemplo, com os números de cartões de crédito Sempre Encriptados, os números de cartões de crédito são armazenados encriptados na base de dados sempre, mesmo durante o processamento de consultas, permitindo a desencriptação no ponto de utilização por pessoal autorizado ou aplicações que precisam de processar esses dados.
- [A Advanced Threat Protection](sql-database-managed-instance-threat-detection.md) complementa [a auditoria](sql-database-managed-instance-auditing.md) fornecendo uma camada adicional de inteligência de segurança incorporada no serviço que deteta tentativas incomuns e potencialmente prejudiciais de acesso ou exploração de bases de dados. Você é alertado sobre atividades suspeitas, potenciais vulnerabilidades, e ataques de injeção SQL, bem como padrões anómalos de acesso à base de dados. Alertas avançados de proteção contra ameaças podem ser vistos a partir do Centro de [Segurança Azure](https://azure.microsoft.com/services/security-center/) e fornecer detalhes de atividades suspeitas e recomendar ações sobre como investigar e mitigar a ameaça.  
- [A máscara de dados dinâmicos](/sql/relational-databases/security/dynamic-data-masking) limita a exposição de dados sensíveis, mascarando-as a utilizadores não privilegiados. A máscara de dados dinâmicos ajuda a impedir o acesso não autorizado a dados sensíveis, permitindo-lhe designar quanto dos dados sensíveis revelar com o mínimo impacto na camada de aplicação. É uma funcionalidade de segurança baseada em políticas que esconde os dados sensíveis no conjunto de resultados de uma consulta sobre campos de base de dados designados, enquanto os dados na base de dados não são alterados.
- A segurança ao [nível da linha](/sql/relational-databases/security/row-level-security) permite controlar o acesso às linhas numa tabela de bases de dados com base nas características do utilizador que executa uma consulta (por exemplo, por associação de grupo ou contexto de execução). A segurança ao nível da linha (RLS) simplifica o design e a programação da segurança na sua aplicação. O RLS permite-lhe implementar restrições ao acesso à linha de dados. Por exemplo, garantir que os trabalhadores só podem aceder às linhas de dados que são pertinentes ao seu departamento, ou restringir o acesso de dados apenas aos dados relevantes.
- [A encriptação transparente de dados (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) encripta ficheiros de dados geridos por exemplo, conhecidos como encriptando dados em repouso. O TDE executa encriptação e desencriptação de I/O em tempo real dos dados e ficheiros de registo. A encriptação utiliza uma chave de encriptação de base de dados (DEK), que é armazenada no registo de boot da base de dados para disponibilidade durante a recuperação. Pode proteger todas as suas bases de dados numa instância gerida com encriptação de dados transparente. TDE é a tecnologia comprovada de encriptação do SQL Server que é exigida por muitos padrões de conformidade para proteger contra roubo de meios de armazenamento.

A migração de uma base de dados encriptada para uma instância gerida é suportada através do Serviço de Migração de Bases de Dados Azure (DMS) ou restauro nativo. Se planeia migrar uma base de dados encriptada utilizando restauronativo, a migração do certificado TDE existente a partir do SQL Server no local ou do SQL Server numa máquina virtual para uma instância gerida é um passo necessário. Para obter mais informações sobre as opções de migração, consulte a migração por [exemplo do SQL Server para a instância gerida](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Integração de Diretório Ativo Azure

A opção de implementação de instância gerida suporta os tradicionais logins e logins de motor de base de dados do servidor SQL integrados com o Azure Ative Directory (AAD). Os principais servidores da Azure AD **(pré-visualização pública)** são a versão em nuvem Azure dos logins de base de dados no local que está a utilizar no seu ambiente no local. Os diretores de servidores da Azure AD (logins) permitem especificar utilizadores e grupos do seu inquilino do Diretório Ativo Azure como verdadeiros diretores de âmbito de instância, capazes de executar qualquer operação de nível de instância, incluindo consultas de base de dados cruzadas dentro da mesma instância gerida.

É introduzida uma nova sintaxe para criar os principais servidores Da Azure AD (logins), **from EXTERNAL PROVIDER**. Para obter mais informações sobre a sintaxe, consulte <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>, e reveja a [Provisão de um administrador de Diretório Ativo Azure para o seu](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) artigo de exemplo gerido.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integração do Azure Active Directory e a autenticação multifator

A opção de implementação de instânciagerida permite-lhe gerir centralmente identidades de utilizadores de bases de dados e outros serviços da Microsoft com integração do [Diretório Ativo do Azure](sql-database-aad-authentication.md). Esta capacidade simplifica a gestão de permissões e melhora a segurança. O Azure Active Directory suporta a [autenticação multifator](sql-database-ssms-mfa-authentication-configure.md) (MFA), para aumentar a segurança de dados e aplicações, suportando, ao mesmo tempo, um processo de início de sessão único.

### <a name="authentication"></a>Autenticação

A autenticação de instânciagerida refere-se à forma como os utilizadores comprovam a sua identidade ao ligarem-se à base de dados. A Base de Dados SQL suporta dois tipos de autenticação:  

- **Autenticação SQL:**

  Este método de autenticação utiliza um nome de utilizador e uma senha.
- **Autenticação do Diretório Ativo Azure:**

  Este método de autenticação utiliza identidades geridas pelo Azure Ative Directory e é suportado para domínios geridos e integrados. Utilize a autenticação do Active Directory (segurança integrada) [sempre que possível](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

### <a name="authorization"></a>Autorização

A autorização refere-se ao que um utilizador pode fazer dentro de uma Base de Dados Azure SQL, e é controlado pelas subscrições de papel da sua conta de utilizador e permissões ao nível do objeto. Uma instância gerida tem as mesmas capacidades de autorização que o SQL Server 2017.

## <a name="database-migration"></a>Migração de bases de dados

A opção de implementação de instância gerida visa cenários de utilizadores com migração de bases de dados em massa a partir de implementações de bases de dados iaaS ou IaaS. A instância gerida suporta várias opções de migração de bases de dados:

### <a name="back-up-and-restore"></a>Criar cópias de segurança e restauro  

A abordagem de migração alavanca os backups da SQL para o armazenamento da Blob Azure. As cópias de segurança armazenadas em blob de armazenamento Azure podem ser diretamente restauradas numa instância gerida utilizando o [comando RESTABELECIMENTO T-SQL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- Para um início rápido mostrando como restaurar os importadores do Mundo Largo - ficheiro de backup de base de dados standard, consulte [Restaurar um ficheiro de backup para uma instância gerida](sql-database-managed-instance-get-started-restore.md). Este quickstart mostra que você tem que carregar um ficheiro de reserva para armazenamento de blob Azure e guardá-lo usando uma chave de assinatura de acesso partilhado (SAS).
- Para obter informações sobre a restauração do URL, consulte [Native RESTORE a partir de URL](sql-database-managed-instance-migrate.md#native-restore-from-url).

> [!IMPORTANT]
> Os backups de um caso gerido só podem ser restaurados para outra instância gerida. Não podem ser restaurados num SQL Server no local ou numa única base de dados/piscina elástica.

### <a name="data-migration-service"></a>Serviço de Migração de Dados

O Serviço de Migração de Bases de Dados Azure é um serviço totalmente gerido projetado para permitir migrações perfeitas de várias fontes de base de dados para plataformas Azure Data com o mínimo de tempo de inatividade. Este serviço simplifica as tarefas necessárias para mover as bases de dados existentes de terceiros e servidor EsQL para a Base de Dados Azure SQL (bases de dados únicas, bases de dados em piscinas elásticas e bases de dados de instâncias em caso de gestão) e SQL Server em Azure VM. Veja [como migrar a sua base de dados no local para uma instância gerida utilizando DMS](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Recursos SQL suportados

A opção de implementação de instância gerida visa fornecer cerca de 100% de compatibilidade na área da superfície com o SQL Server no local, chegando em fases até à disponibilidade geral do serviço. Para uma lista de funcionalidades e comparação, consulte a comparação de funcionalidades de base de [dados SQL](sql-database-features.md), e para uma lista de diferenças T-SQL em instâncias geridas versus SQL Server, consulte [as diferenças t-SQL geridas do Servidor SQL](sql-database-managed-instance-transact-sql-information.md).

A opção de implementação de instância gerida suporta a retrocompatibilidade para as bases de dados SQL 2008. A migração direta dos servidores de base de dados SQL 2005 é suportada, o nível de compatibilidade para bases de dados sql 2005 migradas são atualizados para SQL 2008.
  
O diagrama seguinte descreve a compatibilidade da área da superfície em caso gerido:  

![migração](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>Principais diferenças entre o SQL Server no local e num caso gerido

A opção de implementação de instância gerida beneficia de estar sempre atualizado na nuvem, o que significa que algumas funcionalidades no SQL Server no local podem ser obsoletas, aposentadas ou ter alternativas. Há casos específicos em que as ferramentas precisam reconhecer que uma determinada funcionalidade funciona de uma forma ligeiramente diferente ou que o serviço está a funcionar num ambiente que não controla totalmente:

- A alta disponibilidade é incorporada e pré-configurada utilizando tecnologia semelhante a [Grupos De Disponibilidade Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Backups automáticos e ponto no tempo restaurar. O cliente pode iniciar cópias de `copy-only` segurança que não interfiram com a cadeia de backup automática.
- A instância gerida não permite especificar caminhos físicos completos, pelo que todos os cenários correspondentes têm de ser suportados de forma diferente: RESTAURAR DB não suporta COM MOVIMENTO, CREATE DB não permite caminhos físicos, INSERT A granel funciona apenas com Blobs Azure, etc.
- A instância gerida suporta a [autenticação Azure AD](sql-database-aad-authentication.md) como alternativa à autenticação do Windows.
- A instância gerida gere automaticamente o grupo de ficheiros XTP e os ficheiros para bases de dados que contenham objetos OLTP in-memory
- A instância gerida suporta os Serviços de Integração de Servidores SQL (SSIS) e pode hospedar o catálogo SSIS (SSISDB) que armazena pacotes SSIS, mas são executados num Runtime de Integração Azure-SSIS gerido (IR) na Azure Data Factory (ADF), ver [Create Azure-SSIS IR em ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Para comparar as funcionalidades SSIS na Base de Dados SQL, consulte Compare uma base de [dados única Azure SQL, piscina elástica e instância gerida](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance).

### <a name="managed-instance-administration-features"></a>Características de administração de instâncias geridas

A opção de implementação de instânciagerida permite ao administrador do sistema gastar menos tempo em tarefas administrativas porque o serviço de base de dados SQL executa-as para si ou simplifica muito essas tarefas. Por exemplo, [instalação e correção de OS/RDBMS,](sql-database-high-availability.md) [redimensionamento e configuração de instância dinâmica,](sql-database-single-database-scale.md) [backups,](sql-database-automated-backups.md) [replicação de bases](replication-with-sql-database-managed-instance.md) de dados (incluindo bases de dados do sistema), [configuração de alta disponibilidade](sql-database-high-availability.md)e configuração de fluxos de dados de monitorização de saúde e [desempenho.](../azure-monitor/insights/azure-sql.md)

> [!IMPORTANT]
> Para obter uma lista de funcionalidades suportadas, parcialmente suportadas e não suportadas, consulte as funcionalidades da Base de [Dados SQL](sql-database-features.md). Para obter uma lista de diferenças T-SQL em instâncias geridas versus SQL Server, consulte [as diferenças t-SQL geridas do SQL Server](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Como identificar programáticamente um caso gerido

A tabela seguinte mostra várias propriedades, acessíveis através da Transact SQL, que pode utilizar para detetar que a sua aplicação está a trabalhar com instância gerida e recuperar propriedades importantes.

|Propriedade|Valor|Comentário|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Este valor é o mesmo que na Base de Dados SQL. Isto **não** indica a versão 12 do motor SQL (SQL Server 2014). A instância gerida executa sempre a mais recente versão estável do motor SQL, que é igual ou superior à versão RTM mais recente disponível do SQL Server.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Este valor é o mesmo que na Base de Dados SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Este valor identifica de forma única um caso gerido.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nome DNS de instância completa no seguinte formato: `<instanceName>` . `<dnsPrefix>` . database.windows.net, onde `<instanceName>` é o nome fornecido pelo cliente, enquanto é `<dnsPrefix>` autogerada parte do nome que garante a singularidade do nome DNS global ("wcus17662feb998", por exemplo)|Exemplo: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Passos seguintes

- Para aprender a criar a sua primeira instância gerida, consulte [o guia Quickstart](sql-database-managed-instance-get-started.md).
- Para obter uma lista de funcionalidades e comparação, consulte [as características comuns da SQL](sql-database-features.md).
- Para obter mais informações sobre a configuração vNet, consulte a [configuração VNet de instância gerida](sql-database-managed-instance-connectivity-architecture.md).
- Para um arranque rápido que cria uma instância gerida e restaura uma base de dados a partir de um ficheiro de cópia de segurança, consulte [criar uma instância gerida](sql-database-managed-instance-get-started.md).
- Para um tutorial utilizando o Serviço de Migração de Bases de Dados Azure (DMS) para migração, consulte a migração por [exemplo gerida utilizando DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Para uma monitorização avançada do desempenho da base de dados de instâncias geridas com inteligência incorporada de resolução de problemas, consulte a Base de [Dados Monitor Azure SQL utilizando o Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Para obter informações sobre preços, consulte a Base de [Dados SQL gerida por preços](https://azure.microsoft.com/pricing/details/sql-database/managed/)de instância .
