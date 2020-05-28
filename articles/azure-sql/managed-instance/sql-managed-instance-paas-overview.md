---
title: O que é a Instância Gerida Azure SQL?
description: Saiba mais sobre o Azure SQL Managed Instance proporcionando quase 100% de compatibilidade com o mais recente Motor de Base de Dados SQL Server on-premis (Enterprise Edition)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 04/02/2020
ms.openlocfilehash: 9f8f001c1e20e7685683d6bf3d3564d355db38cb
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84119378"
---
# <a name="what-is-azure-sql-managed-instance"></a>O que é a Instância Gerida Azure SQL?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Parte da família de produtos Azure SQL, Azure SQL Managed Instance é o serviço inteligente, escalável, de base de dados em nuvem que combina a mais ampla compatibilidade do motor de base de dados SQL Server com todos os benefícios de uma plataforma totalmente gerida e sempre verde como serviço. A SQL Managed Instance possui quase 100% de compatibilidade com o mais recente motor de base de dados SQL Server on-premis (Enterprise Edition), fornecendo uma implementação de rede virtual nativa [(VNet)](../../virtual-network/virtual-networks-overview.md) que aborda preocupações comuns de segurança, e um modelo de [negócio](https://azure.microsoft.com/pricing/details/sql-database/) favorável para clientes sQL Server no local. A Instância Gerida sQL permite que os clientes sQL Server existentes levantem e transvam as suas aplicações no local para a nuvem com alterações mínimas de aplicação e base de dados. Ao mesmo tempo, o SQL Managed Instance preserva todas as capacidades do PaaS (atualizações automáticas de remendos e versão, [backups automatizados,](../database/automated-backups-overview.md) [alta disponibilidade),](../database/high-availability-sla.md) que reduzem drasticamente as despesas de gestão e tCO.

> [!IMPORTANT]
> Para obter uma lista de regiões onde o SQL Managed Instance está atualmente disponível, consulte [regiões apoiadas](resource-limits.md#supported-regions).

O diagrama seguinte descreve as principais características das instâncias geridas pela SQL:

![principais características](./media/sql-managed-instance-paas-overview/key-features.png)

O Azure SQL Managed Instance foi projetado para clientes que procuram migrar um grande número de aplicações a partir de instalações ou IaaS, auto-construídas ou ISV proporcionando ambiente para ambiente de nuvem PaaS totalmente gerido, com o menor esforço de migração possível. Utilizando o Serviço de Migração de Dados totalmente automatizado [(DMS)](../../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) em Azure, os clientes podem levantar e transferir o seu Servidor SQL no local para uma Instância Gerida SQL que oferece compatibilidade com o SQL Server no local e o isolamento completo de instâncias de clientes com suporte vNet nativo.  Com a Garantia de Software, pode trocar as suas licenças existentes por tarifas com desconto numa Instância Gerida SQL utilizando o [Benefício Híbrido Azure para o Servidor SQL](https://azure.microsoft.com/pricing/hybrid-benefit/). Um SQL Managed Instance é o melhor destino de migração na nuvem para casos de SQL Server que requerem alta segurança e uma rica superfície de programabilidade.

## <a name="key-features-and-capabilities"></a>Principais funcionalidades e capacidades

A SQL Managed Instance combina as melhores funcionalidades disponíveis tanto na Base de Dados Azure SQL como no motor de base de dados Do Servidor SQL.

> [!IMPORTANT]
> A SQL Managed Instance funciona com todas as funcionalidades da versão mais recente do SQL Server, incluindo operações online, correções automáticas de planos e outras melhorias de desempenho da empresa. Uma comparação das funcionalidades disponíveis é explicada na [comparação de funcionalidades: Azure SQL Managed Instance versus SQL Server](../database/features-comparison.md).

| **Benefícios paaS** | **Continuidade do negócio** |
| --- | --- |
|Sem compra e gestão de hardware <br>Sem despesas gerais de gestão para gerir infraestruturas subjacentes <br>Fornecimento rápido e escala de serviço <br>Patching automatizado e atualização de versão <br>Integração com outros serviços de dados paaS |99,99% de uptime SLA  <br>Construído em [alta disponibilidade](../database/high-availability-sla.md) <br>Dados protegidos com [cópias de segurança automatizadas](../database/automated-backups-overview.md) <br>Período de retenção de backup configurável do cliente <br>[Backups iniciados](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) pelo utilizador <br>[Capacidade de restauro](../database/recovery-using-backups.md#point-in-time-restore) da base de dados de ponto no tempo |
|**Segurança e conformidade** | **Gestão**|
|Ambiente isolado[(integração VNet,](connectivity-architecture-overview.md)serviço de inquilino único, computação e armazenamento dedicados) <br>[Encriptação transparente de dados (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Autenticação Azure AD,](../database/authentication-aad-overview.md)suporte único de sinalização <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Diretores de servidores Azure AD (logins)</a>  <br>Adere às normas de conformidade iguais às da Base de Dados Azure SQL <br>[Auditoria SQL](auditing-configure.md) <br>[Advanced Threat Protection](threat-detection-configure.md) |API gestor de recursos azure para automatizar prestação e escalação de serviços <br>Funcionalidade do portal Azure para fornecimento e escalagem de serviços manuais <br>Serviço de Migração de Dados

> [!IMPORTANT]
> A Azure SQL Managed Instance foi certificada contra uma série de normas de conformidade. Para mais informações, consulte as Ofertas de Conformidade do [Microsoft Azure](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuideV3?command=Download&downloadType=Document&downloadId=44bbae63-bf4d-4e3b-9d3d-c96fb25ec363&tab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb&docTab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb_FAQ_and_White_Papers) onde pode encontrar a lista mais atual das certificações de conformidade sQL Managed Instance, listadas na Base de **Dados SQL**.

As principais características da Instância Gerida SQL são mostradas na tabela seguinte:

|Funcionalidade | Descrição|
|---|---|
| Versão / construção do SQL Server | Motor de base de dados SQL Server (último estável) |
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

O modelo de compra baseado em vCore para A Instância Gerida SQL [dá-lhe](../database/service-tiers-vcore.md) flexibilidade, controlo, transparência e uma forma simples de traduzir os requisitos de carga de trabalho no local para a nuvem. Este modelo permite-lhe alterar a computação, a memória e o armazenamento com base nas suas necessidades de carga de trabalho. O modelo vCore também é elegível para até 55% de poupança com o [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) para o Servidor SQL.

No modelo vCore, pode escolher entre gerações de hardware.

- **Gen4** Os CPUs lógicos baseiam-se em processadores Intel E5-2673 v3 (Haswell) 2.4-GHz, SSD anexados, núcleos físicos, RAM de 7 GB por núcleo, e tamanhos de computação entre 8 e 24 vCores.
- **Gen5** Os CPUs lógicos baseiam-se em processadores Intel E5-2673 v4 (Broadwell) 2.3-GHz e Intel SP-8160 (Skylake), rápido NVMe SSD, núcleo lógico hiper-roscado e tamanhos de computação entre 4 e 80 núcleos.

Encontre mais informações sobre a diferença entre gerações de hardware nos limites de [recursos de Instância Gerida SQL](resource-limits.md#hardware-generation-characteristics).

## <a name="service-tiers"></a>Escalões de serviço

A SQL Managed Instance está disponível em dois níveis de serviço:

- **Finalidade geral**: Concebido para aplicações com requisitos típicos de desempenho e latência IO.
- **Crítico de negócios**: Projetado para aplicações com baixos requisitos de latência io e impacto mínimo das operações de manutenção subjacentes na carga de trabalho.

Ambos os níveis de serviço garantem uma disponibilidade de 99,99% e permitem selecionar de forma independente o tamanho do armazenamento e a capacidade computacional. Para obter mais informações sobre a arquitetura de alta disponibilidade da Instância Gerida Azure SQL, consulte a alta disponibilidade e a [Instância Gerida Azure SQL](../database/high-availability-sla.md).

### <a name="general-purpose-service-tier"></a>Nível de serviço para fins gerais

A seguinte lista descreve a característica-chave do nível de serviço "Propósito Geral":

- Design para a maioria das aplicações empresariais com requisitos típicos de desempenho
- Armazenamento Azure Blob de alto desempenho (8 TB)
- Embutida [de alta disponibilidade](../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-availability) com base no armazenamento fiável da Blob Azure e no Tecido de Serviço [Azure](../../service-fabric/service-fabric-overview.md)

Para mais informações, consulte a [camada de armazenamento em nível geral](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) e desempenho de armazenamento boas [práticas e considerações para a Instância Gerida SQL (finalidade geral)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

Encontre mais informações sobre a diferença entre os níveis de serviço nos limites de [recursos de Instância Gerida SQL](resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Nível de serviço Business Critical

O nível de serviço Business Critical foi construído para aplicações com elevados requisitos io. Oferece a maior resiliência a falhas usando várias réplicas isoladas.

A lista que se segue descreve as características-chave do nível de serviço Business Critical:

- Projetado para aplicações empresariais com maior desempenho e requisitos de HA
- Vem com armazenamento sSD local super rápido (até 1 TB em Gen4 e até 4 TB em Gen5)
- [Alta disponibilidade](../database/high-availability-sla.md#premium-and-business-critical-service-tier-availability) incorporada com base em Sempre [Em Grupos de Disponibilidade](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) e Tecido de Serviço [Azure](../../service-fabric/service-fabric-overview.md)
- Réplica de [base de dados](../database/read-scale-out.md) adicional de leitura incorporada que pode ser usada para reportar e outras cargas de trabalho apenas para leitura
- [In-Memory OLTP](../in-memory-oltp-overview.md) que pode ser usado para carga de trabalho com requisitos de alto desempenho  

Encontre mais informações sobre a diferença entre os níveis de serviço nos limites de [recursos de Instância Gerida SQL](resource-limits.md#service-tier-characteristics).

## <a name="management-operations"></a>Operações de gestão

O Azure SQL Managed Instance fornece operações de gestão que pode utilizar para implementar automaticamente novas instâncias geridas, atualizar propriedades de instâncias e eliminar instâncias quando já não é necessário. Esta secção fornece informações sobre operações de gestão e as suas durações típicas.

Para apoiar as implementações dentro das [Redes Virtuais Azure (VNets)](../../virtual-network/virtual-network-for-azure-services.md) e proporcionar isolamento e segurança aos clientes, a SQL Managed Instance conta com [clusters virtuais,](connectivity-architecture-overview.md#high-level-connectivity-architecture)que representam um conjunto dedicado de máquinas virtuais isoladas implantadas dentro da subnet virtual do cliente. Essencialmente, cada implantação de instâncias geridas numa subnet vazia resulta numa nova estrutura de cluster virtual.

As operações subsequentes em casos geridos implementados podem também ter efeitos no seu cluster virtual subjacente. Isto afeta a duração das operações de gestão, uma vez que a implantação de máquinas virtuais adicionais vem com uma sobrecarga que precisa de ser considerada quando planeia novas implementações ou atualizações para instâncias geridas existentes.

Todas as operações de gestão podem ser categorizadas da seguinte forma:

- Implantação de instâncias (criação de novos exemplos).
- Atualização de instâncias (alteração de propriedades de instâncias, tais como vCores ou armazenamento reservado.
- Supressão de instâncias.

Normalmente, as operações em aglomerados virtuais demoram mais tempo. A duração das operações em clusters virtuais variam – abaixo estão os valores que normalmente se pode esperar, com base nos dados de telemetria de serviçoexistentes:

- **Criação de cluster virtual**: Este é um passo sincronizado em operações de gestão de exemplo. **90% das operações terminam em 4 horas.**
- **Redimensionamento do cluster virtual (expansão ou redução)**: A expansão é um passo sincronizado, enquanto a redução é realizada assíncronamente (sem impacto na duração das operações de gestão de instâncias). **90% das expansões do cluster terminam em menos de 2,5 horas.**
- **Eliminação virtual do cluster**: A eliminação é um passo assíncrono, mas também pode ser [iniciado manualmente](virtual-cluster-delete.md) num aglomerado virtual vazio, caso em que executa sincronizadamente. **90% das exclusões de cluster saem em 1,5 horas.**

Além disso, a gestão de instâncias pode também incluir uma das operações em bases de dados hospedadas, o que resulta em durações mais longas:

- **Anexar ficheiros de base de dados a partir do Armazenamento Azure**: Trata-se de um passo sincronizado, como a computação (vCore), ou a escala de armazenamento para cima ou para baixo no nível de serviço de Propósito Geral. **90% destas operações terminam em 5 minutos.**
- **Sempre na sementeação**do grupo de disponibilidade : Este é um passo sincronizado, como a computação (vCore), ou a escala de armazenamento no nível de serviço Business Critical, bem como na alteração do nível de serviço de Propósito Geral para Business Critical (ou vice-versa). A duração desta operação é proporcional ao tamanho total da base de dados, bem como à atividade atual da base de dados (número de transações ativas). A atividade da base de dados ao atualizar uma instância pode introduzir uma variação significativa na duração total. **90% destas operações executam a 220 GB/hora ou superior**.

O quadro seguinte resume as operações e as durações globais típicas:

|Categoria  |Operação  |Segmento de longo prazo  |Duração estimada  |
|---------|---------|---------|---------|
|**Implementação** |Primeira instância numa sub-rede vazia|Criação de cluster virtual|90% das operações terminam em 4 horas|
|Implementação |Primeira instância de outra geração de hardware numa subnet não vazia (por exemplo, primeira instância de Gen 5 numa subnet com instâncias gen 4)|Criação de cluster virtual*|90% das operações terminam em 4 horas|
|Implementação |Criação em primeira instância de 4 vCores, numa subnet vazia ou não vazia|Criação de cluster virtual**|90% das operações terminam em 4 horas|
|Implementação |Criação subsequente de instâncias dentro da sub-rede não vazia (2º, 3º, etc. instância)|Redimensionamento de cluster virtual|90% das operações terminam em 2,5 horas|
|**Atualizar** |Mudança de propriedade por exemplo (senha de administração, login Azure AD, bandeira de benefício híbrido Azure)|N/D|Até 1 minuto|
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

\*\*A opção 4 vCores foi lançada em junho de 2019 e requer uma nova versão de cluster virtual. Se tiver tido casos na subnet-alvo que foram todos criados antes de 12 de junho, um novo cluster virtual será implantado automaticamente para acolher 4 instâncias vCore.

\*\*\*12 horas é a configuração atual, mas isso pode mudar no futuro, por isso não tomes uma dependência dura dela. Se precisar de eliminar um cluster virtual mais cedo (para libertar a sub-rede, por exemplo), consulte Apagar uma sub-rede depois de eliminar uma instância gerida por [Azure SQL](virtual-cluster-delete.md).

### <a name="instance-availability-during-management-operations"></a>Disponibilidade de exemplo durante operações de gestão

A SQL Managed Instance não está disponível para aplicações de clientes durante operações de implantação e eliminação.

A SQL Managed Instance está disponível durante as operações de atualização, exceto um curto período de inatividade causado pela falha que ocorre no final da atualização. Normalmente dura até 10 segundos mesmo em caso de transações interrompidas a longo prazo, graças à recuperação da base de [dados Acelerada](../accelerated-database-recovery.md).

> [!IMPORTANT]
> Não é aconselhável escalar a computação ou o armazenamento da Instância Gerida Azure SQL ou alterar o nível de serviço ao mesmo tempo com as transações de longo prazo (importação de dados, empregos de processamento de dados, reconstrução de índices, etc.). A falha na base de dados que será realizada no final da operação cancelará todas as transações em curso.

### <a name="management-operations-cross-impact"></a>Operações de gestão com impacto cruzado

As operações de gestão de instâncias geridas pela SQL podem afetar outras operações de gestão das instâncias colocadas dentro do mesmo cluster virtual. Isto inclui o seguinte:

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

[![Cancelar operação](./media/sql-managed-instance-paas-overview/canceling-operation.png)](./media/sql-managed-instance-paas-overview/canceling-operation.png#lightbox)

Depois de o pedido de cancelamento ter sido submetido e processado, receberá notificação se a submissão de cancelamento tiver sido bem sucedida ou não.

Em caso de cancelamento de sucesso, a operação de gestão será cancelada em alguns minutos, resultando numa falha.

![cancelamento resultado da operação](./media/sql-managed-instance-paas-overview/canceling-operation-result.png)

Se o pedido de cancelamento falhar ou cancelar o botão não estiver ativo, isso significa que a operação de gestão entrou em estado não cancelável e que terminará em alguns minutos. A operação de gestão continuará a sua execução até que esteja concluída.

> [!IMPORTANT]
> A operação de cancelamento é atualmente suportada apenas no Portal.

## <a name="advanced-security-and-compliance"></a>Segurança e conformidade avançadas

O SQL Managed Instance vem com funcionalidades de segurança avançadas fornecidas pela nuvem Azure e pelo motor de base de dados SQL Server.

### <a name="security-isolation"></a>Isolamento de segurança

A SQL Managed Instance proporciona um isolamento adicional de segurança de outros inquilinos na nuvem Azure. O isolamento de segurança inclui:

- [Implementação](connectivity-architecture-overview.md) de rede virtual nativa e conectividade com o seu ambiente no local usando a Rota Expresso Azure ou o Gateway VPN.
- Numa implementação predefinida, o ponto final do SQL é exposto apenas através de um endereço IP privado, permitindo uma conectividade segura a partir de redes privadas azure ou híbridas.
- Inquilino único com infraestrutura subjacente dedicada (computação, armazenamento).

O diagrama seguinte descreve várias opções de conectividade para as suas aplicações:

![alta disponibilidade](./media/sql-managed-instance-paas-overview/application-deployment-topologies.png)  

Para saber mais detalhes sobre a integração vnet e a aplicação da política de networking a nível da subnet, consulte a [arquitetura VNet para casos geridos](connectivity-architecture-overview.md) e [ligue a sua aplicação a uma instância gerida](connect-application-instance.md).

> [!IMPORTANT]
> Coloque vários exemplos geridos na mesma subnet, onde quer que seja permitido pelos seus requisitos de segurança, pois isso lhe trará benefícios adicionais. A collocalização de casos na mesma subrede simplificará significativamente a manutenção da infraestrutura em rede e reduzirá o tempo de provisionamento de instâncias, uma vez que a longa duração do provisionamento está associada ao custo de implantação da primeira instância gerida numa subnet.

### <a name="security-features"></a>Funcionalidades de Segurança

O Azure SQL Managed Instance fornece um conjunto de funcionalidades de segurança avançadas que podem ser usadas para proteger os seus dados.

- A auditoria da [SQL Managed Instance](auditing-configure.md) rastreia os eventos da base de dados e escreve-os num ficheiro de registo de auditoria colocado na sua conta de armazenamento Azure. A auditoria pode ajudar a manter o cumprimento regulamentar, compreender a atividade da base de dados e obter informações sobre discrepâncias e anomalias que possam indicar preocupações comerciais ou suspeitas de violações de segurança.
- Encriptação de dados em movimento - uma Instância Gerida SQL protege os seus dados fornecendo encriptação para dados em movimento usando a Segurança da Camada de Transporte. Além da segurança da camada de transporte, a SQL Managed Instance oferece proteção de dados sensíveis em voo, repouso e durante o processamento de consulta com [Sempre Encriptado](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted é uma funcionalidade pioneira da indústria que oferece segurança de dados sem paralelo contra violações que envolvam o roubo de dados confidenciais. Por exemplo, com os números de cartões de crédito Sempre Encriptados, os números de cartões de crédito são armazenados encriptados na base de dados sempre, mesmo durante o processamento de consultas, permitindo a desencriptação no ponto de utilização por pessoal autorizado ou aplicações que precisam de processar esses dados.
- [A Advanced Threat Protection](threat-detection-configure.md) complementa [a auditoria](auditing-configure.md) fornecendo uma camada adicional de inteligência de segurança incorporada no serviço que deteta tentativas incomuns e potencialmente prejudiciais de acesso ou exploração de bases de dados. Você é alertado sobre atividades suspeitas, potenciais vulnerabilidades, e ataques de injeção SQL, bem como padrões anómalos de acesso à base de dados. Alertas avançados de proteção contra ameaças podem ser vistos a partir do Centro de [Segurança Azure](https://azure.microsoft.com/services/security-center/) e fornecer detalhes de atividades suspeitas e recomendar ações sobre como investigar e mitigar a ameaça.  
- [A máscara de dados dinâmicos](/sql/relational-databases/security/dynamic-data-masking) limita a exposição de dados sensíveis, mascarando-as a utilizadores não privilegiados. A máscara de dados dinâmicos ajuda a impedir o acesso não autorizado a dados sensíveis, permitindo-lhe designar quanto dos dados sensíveis revelar com o mínimo impacto na camada de aplicação. É uma funcionalidade de segurança baseada em políticas que esconde os dados sensíveis no conjunto de resultados de uma consulta sobre campos de base de dados designados, enquanto os dados na base de dados não são alterados.
- A segurança ao [nível da linha](/sql/relational-databases/security/row-level-security) permite controlar o acesso às linhas numa tabela de bases de dados com base nas características do utilizador que executa uma consulta (por exemplo, por associação de grupo ou contexto de execução). A segurança ao nível da linha (RLS) simplifica o design e a programação da segurança na sua aplicação. O RLS permite-lhe implementar restrições ao acesso à linha de dados. Por exemplo, garantir que os trabalhadores só podem aceder às linhas de dados que são pertinentes ao seu departamento, ou restringir o acesso de dados apenas aos dados relevantes.
- [A encriptação transparente de dados (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) encripta ficheiros de dados SQL Managed Instance, conhecidos como encriptando dados em repouso. O TDE executa encriptação e desencriptação de I/O em tempo real dos dados e ficheiros de registo. A encriptação utiliza uma chave de encriptação de base de dados (DEK), que é armazenada no registo de boot da base de dados para disponibilidade durante a recuperação. Pode proteger todas as suas bases de dados numa instância gerida com encriptação de dados transparente. TDE é a tecnologia comprovada de encriptação do SQL Server que é exigida por muitos padrões de conformidade para proteger contra roubo de meios de armazenamento.

A migração de uma base de dados encriptada para uma Instância Gerida SQL é suportada através do Serviço de Migração de Bases de Dados Azure (DMS) ou restauro nativo. Se planeia migrar uma base de dados encriptada utilizando restauronativo, a migração do certificado TDE existente a partir do SQL Server no local ou do Servidor SQL numa máquina virtual para uma Instância Gerida SQL é um passo necessário. Para obter mais informações sobre as opções de migração, consulte a [migração do SQL Server para a Instância Gerida sQL](migrate-to-instance-from-sql-server.md).

## <a name="azure-active-directory-integration"></a>Integração de Diretório Ativo Azure

A SQL Managed Instance suporta logins e logins tradicionais de base de dados Do Servidor SQL integrados com o Azure Ative Directory (Azure AD). Os principais servidores da Azure AD **(pré-visualização pública)** são a versão em nuvem Azure dos logins de base de dados no local que está a utilizar no seu ambiente no local. Os diretores de servidores da Azure AD (logins) permitem especificar utilizadores e grupos do seu inquilino do Diretório Ativo Azure como verdadeiros diretores de âmbito de instância, capazes de executar qualquer operação de nível de instância, incluindo consultas de base de dados cruzadas dentro da mesma instância gerida.

É introduzida uma nova sintaxe para criar os principais servidores Da Azure AD (logins), **from EXTERNAL PROVIDER**. Para obter mais informações sobre a sintaxe, consulte <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>, e reveja a [Provisão de um administrador de Diretório Ativo Azure para o seu artigo SQL Managed Instance.](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integração do Azure Active Directory e a autenticação multifator

A SQL Managed Instance permite-lhe gerir centralmente identidades de utilizadores de bases de dados e outros serviços da Microsoft com [integração do Diretório Ativo do Azure](../database/authentication-aad-overview.md). Esta capacidade simplifica a gestão de permissões e melhora a segurança. O Azure Active Directory suporta a [autenticação multifator](../database/authentication-mfa-ssms-configure.md) (MFA), para aumentar a segurança de dados e aplicações, suportando, ao mesmo tempo, um processo de início de sessão único.

### <a name="authentication"></a>Autenticação

A autenticação da Instância Gerida SQL refere-se à forma como os utilizadores comprovam a sua identidade ao ligarem-se à base de dados. A Instância Gerida SQL suporta dois tipos de autenticação:  

- **Autenticação SQL:**

  Este método de autenticação utiliza um nome de utilizador e uma senha.
- **Autenticação do Diretório Ativo Azure:**

  Este método de autenticação utiliza identidades geridas pelo Azure Ative Directory e é suportado para domínios geridos e integrados. Utilize a autenticação do Active Directory (segurança integrada) [sempre que possível](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

### <a name="authorization"></a>Autorização

A autorização refere-se ao que um utilizador pode fazer dentro de uma base de dados em Azure SQL Managed Instance, e é controlado pelas subscrições de papel da sua conta de utilizador e permissões ao nível do objeto. Uma Instância Gerida SQL tem as mesmas capacidades de autorização que o SQL Server 2017.

## <a name="database-migration"></a>Migração de bases de dados

A SQL Managed Instance visa cenários de utilizadores com migração de bases de dados em massa a partir de implementações de bases de dados no local ou iaaS. A SQL Managed Instance suporta várias opções de migração de bases de dados:

### <a name="backup-and-restore"></a>Cópia de segurança e restauro  

A abordagem de migração alavanca os backups da SQL para o armazenamento da Blob Azure. As cópias de segurança armazenadas em blob de armazenamento Azure podem ser diretamente restauradas numa Instância Gerida SQL utilizando o [comando RESTABELECIMENTO T-SQL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- Para um início rápido mostrando como restaurar os importadores do Mundo Largo - ficheiro de backup de base de dados standard, consulte [Restaurar um ficheiro de backup para uma instância gerida](restore-sample-database-quickstart.md). Este quickstart mostra que você tem que carregar um ficheiro de reserva para armazenamento de blob Azure e guardá-lo usando uma chave de assinatura de acesso partilhado (SAS).
- Para obter informações sobre a restauração do URL, consulte [Native RESTORE a partir de URL](migrate-to-instance-from-sql-server.md#native-restore-from-url).

> [!IMPORTANT]
> As cópias de segurança de uma instância gerida pela SQL só podem ser restauradas para outra Instância Gerida sQL. Não podem ser restaurados para um Servidor SQL no local ou para a Base de Dados Azure SQL.

### <a name="data-migration-service"></a>Serviço de Migração de Dados

O Serviço de Migração de Bases de Dados Azure é um serviço totalmente gerido projetado para permitir migrações perfeitas de várias fontes de base de dados para plataformas Azure Data com o mínimo de tempo de inatividade. Este serviço simplifica as tarefas necessárias para mover as bases de dados existentes de terceiros e servidor EsQL para a Base de Dados Azure SQL, Azure SQL Managed, e SQL Server em Azure VM. Veja [como migrar a sua base de dados no local para a SQL Managed Instance utilizando DMS](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Recursos SQL suportados

O SQL Managed Instance pretende fornecer uma compatibilidade de área de superfície de perto de 100% com a versão mais recente no local SQL Server através de um plano de lançamento encenado. Para uma lista de funcionalidades e comparação, consulte a [comparação de características de Instância Gerida SQL](../database/features-comparison.md), e para uma lista de diferenças T-SQL em Instância Gerida SQL versus SQL Server, consulte as diferenças de [T-SQL geridas pela SQL do Servidor SQL](transact-sql-tsql-differences-sql-server.md).

A Instância Gerida SQL suporta a retrocompatibilidade para as bases de dados SQL 2008. A migração direta dos servidores de base de dados SQL 2005 é suportada, o nível de compatibilidade para bases de dados sql 2005 migradas são atualizados para SQL 2008.
  
O diagrama seguinte descreve a compatibilidade da área da superfície em Instância Gerida SQL:  

![migração](./media/sql-managed-instance-paas-overview/migration.png)

### <a name="key-differences-on-premises-and-sql-managed-instance"></a>Principais diferenças no local e Instância Gerida SQL

O SQL Managed Instance beneficia de estar sempre atualizado na nuvem, o que significa que algumas funcionalidades no SQL Server podem ser obsoletas, aposentadas ou ter alternativas. Há casos específicos em que as ferramentas precisam reconhecer que uma determinada funcionalidade funciona de uma forma ligeiramente diferente ou que o serviço está a funcionar num ambiente que não controla totalmente.

Algumas diferenças fundamentais:

- A alta disponibilidade é incorporada e pré-configurada utilizando tecnologia semelhante a [grupos de disponibilidade Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Existem apenas cópias de segurança automatizadas e o tempo de restabelecimento do tempo. O cliente pode iniciar cópias de `copy-only` segurança que não interfiram com a cadeia de backup automática.
- Especificar caminhos físicos completos não é suportado, pelo que todos os cenários correspondentes têm de ser suportados de forma diferente: RESTAURAR DB não suporta COM MOVIMENTO, CREATE DB não permite caminhos físicos, INSERT A granel funciona apenas com Blobs Azure, etc.
- A SQL Managed Instance suporta a [autenticação AD Azure](../database/authentication-aad-overview.md) como alternativa à autenticação do Windows.
- A Instância Gerida sQL gere automaticamente o grupo de ficheiros XTP e os ficheiros para bases de dados que contenham objetos OLTP in-memory
- A SQL Managed Instance suporta os Serviços de Integração de Servidores SQL (SSIS) e pode hospedar o catálogo SSIS (SSISDB) que armazena pacotes SSIS, mas são executados num Runtime de Integração Azure-SSIS gerido (IR) na Azure Data Factory (ADF), ver [Create Azure-SSIS IR em ADF.](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime) Para comparar as funcionalidades SSIS, consulte Compare a Base de [Dados SQL com a Instância Gerida SQL](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).

### <a name="administration-features"></a>Características da administração

A SQL Managed Instance permite que o administrador do sistema gaste menos tempo em tarefas administrativas porque o serviço SQL Managed Instance executa-as para si ou simplifica muito essas tarefas. Por exemplo, [instalação e correção de OS/RDBMS,](../database/high-availability-sla.md) [redimensionamento e configuração de instância dinâmica,](../database/single-database-scale.md) [backups,](../database/automated-backups-overview.md) [replicação de bases](replication-between-two-instances-configure-tutorial.md) de dados (incluindo bases de dados do sistema), [configuração de alta disponibilidade](../database/high-availability-sla.md)e configuração de fluxos de dados de monitorização de saúde e [desempenho.](../../azure-monitor/insights/azure-sql.md)

Para mais informações consulte uma lista de funcionalidades de instância gerida [sQL suportadas e não suportadas](../database/features-comparison.md), e [diferenças T-SQL entre a Instância Gerida SQL e o Servidor SQL](transact-sql-tsql-differences-sql-server.md)

### <a name="programmatically-identify-a-managed-instance"></a>Identificar programáticamente um caso gerido

A tabela seguinte mostra várias propriedades, acessíveis através da Transact-SQL, que pode utilizar para detetar que a sua aplicação está a trabalhar com a SQL Managed Instance e recuperar propriedades importantes.

|Propriedade|Valor|Comentário|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Este valor é o mesmo que na Base de Dados SQL. Isto **não** indica a versão 12 do motor SQL (SQL Server 2014). A SQL Managed Instance executa sempre a mais recente versão estável do motor SQL, que é igual ou superior à versão RTM mais recente disponível do SQL Server.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Este valor é o mesmo que na Base de Dados SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Este valor identifica de forma única um caso gerido.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nome DNS de instância completa no seguinte formato: `<instanceName>` . `<dnsPrefix>` . database.windows.net, onde `<instanceName>` é o nome fornecido pelo cliente, enquanto é `<dnsPrefix>` autogerada parte do nome que garante a singularidade do nome DNS global ("wcus17662feb998", por exemplo)|Exemplo: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Próximos passos

- Para aprender a criar o seu primeiro Caso Gerido SQL, consulte [o guia Quickstart](instance-create-quickstart.md).
- Para obter uma lista de funcionalidades e comparação, consulte [as características comuns da SQL](../database/features-comparison.md).
- Para obter mais informações sobre a configuração vNet, consulte a [configuração VNet de Instância Gerida sQL](connectivity-architecture-overview.md).
- Para um arranque rápido que cria uma Instância Gerida SQL e restaura uma base de dados a partir de um ficheiro de backup, consulte [criar uma Instância Gerida SQL](instance-create-quickstart.md).
- Para um tutorial utilizando o Serviço de Migração de Bases de Dados Azure (DMS) para migração, consulte a [migração sQL Managed Instance utilizando DMS](../../dms/tutorial-sql-server-to-managed-instance.md).
- Para uma monitorização avançada do desempenho da base de dados SQL Managed Instance com inteligência incorporada de resolução de problemas, consulte [monitor Azure SQL Managed Instance utilizando o Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Para obter informações sobre preços, consulte a Base de [Dados SQL gerida por preços](https://azure.microsoft.com/pricing/details/sql-database/managed/)de instância .
