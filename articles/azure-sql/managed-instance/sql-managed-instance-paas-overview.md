---
title: O que é Azure SQL Managed Instance?
description: Conheça a Azure SQL Managed Instance fornecendo quase 100% de compatibilidade com o mais recente motor de base de dados SQL Server (Enterprise Edition)
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
ms.openlocfilehash: 886653bcfa255c7929f7e23082b74f171c97c7a1
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84195442"
---
# <a name="what-is-azure-sql-managed-instance"></a>O que é Azure SQL Managed Instance?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Parte da família de produtos Azure SQL, Azure SQL Managed Instance é o serviço inteligente, escalável e cloud database que combina a compatibilidade mais ampla do motor de base de dados SQL Server com todos os benefícios de uma plataforma totalmente gerida e verde como um serviço. A SQL Managed Instance possui quase 100% de compatibilidade com o mais recente motor de base de dados SQL Server (Enterprise Edition), fornecendo uma implementação de rede virtual nativa [(VNet)](../../virtual-network/virtual-networks-overview.md) que aborda preocupações de segurança comuns, e um modelo de [negócio](https://azure.microsoft.com/pricing/details/sql-database/) favorável aos clientes existentes do SQL Server. A SQL Managed Instance permite que os clientes existentes do SQL Server levantem e transloquem as suas aplicações no local para a nuvem com alterações mínimas de aplicação e de base de dados. Ao mesmo tempo, o SQL Managed Instance preserva todas as capacidades paaS (patching automático e atualizações de versão, [backups automatizados,](../database/automated-backups-overview.md) [alta disponibilidade),](../database/high-availability-sla.md) que reduzem drasticamente a sobrecarga de gestão e tCO.

> [!IMPORTANT]
> Para uma lista de regiões onde o SQL Managed Instance está atualmente disponível, consulte [as regiões apoiadas.](resource-limits.md#supported-regions)

O seguinte diagrama descreve as principais características das ocorrências geridas do SQL:

![principais características](./media/sql-managed-instance-paas-overview/key-features.png)

Azure SQL Managed Instance é projetado para clientes que procuram migrar um grande número de aplicações de instalações ou IaaS, auto-construído, ou ISV forneceu ambiente para o ambiente de nuvem PaaS totalmente gerido, com o mínimo esforço de migração possível. Utilizando o [Serviço de Migração de Dados (DMS)](../../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) totalmente automatizado em Azure, os clientes podem levantar e transferir as suas instâncias existentes do SQL Server para uma SQL Managed Instance que oferece compatibilidade com o SQL Server e isolamento total de instâncias de clientes com suporte nativo VNet.  Com a Garantia de Software, pode trocar as licenças existentes por tarifas com desconto numa SqL Managed Instance utilizando o [Benefício Híbrido Azure para o SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Um SQL Managed Instance é o melhor destino de migração na nuvem para casos de SQL Server que requerem alta segurança e uma rica superfície de programabilidade.

## <a name="key-features-and-capabilities"></a>Principais funcionalidades e capacidades

SQL Managed Instance combina as melhores funcionalidades que estão disponíveis tanto na Base de Dados Azure SQL como no motor de base de dados sql Server.

> [!IMPORTANT]
> A SQL Managed Instance funciona com todas as funcionalidades da versão mais recente do SQL Server, incluindo operações online, correções automáticas de planos e outras melhorias no desempenho da empresa. Uma comparação das funcionalidades disponíveis é explicada na [comparação de recursos: Azure SQL Managed Instance versus SQL Server](../database/features-comparison.md).

| **Benefícios paaS** | **Continuidade do negócio** |
| --- | --- |
|Sem compra e gestão de hardware <br>Sem despesas gerais de gestão para a gestão de infraestruturas subjacentes <br>Dimensionamento rápido do fornecimento e do serviço <br>Patching automatizado e atualização de versão <br>Integração com outros serviços de dados PaaS |99,99% uptime SLA  <br>Construído em [alta disponibilidade](../database/high-availability-sla.md) <br>Dados protegidos com [cópias de segurança automatizadas](../database/automated-backups-overview.md) <br>Período de retenção de backup configurável do cliente <br>[Backups iniciados](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) pelo utilizador <br>[Capacidade de restauro da base de dados de pontos no tempo](../database/recovery-using-backups.md#point-in-time-restore) |
|**Segurança e conformidade** | **Gestão**|
|Ambiente isolado[(integração VNet,](connectivity-architecture-overview.md)serviço de inquilino único, computação e armazenamento dedicados) <br>[Encriptação transparente de dados (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Autenticação Azure AD](../database/authentication-aad-overview.md), suporte único de inscrição <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Principais do servidor AD do Azure (logins)</a>  <br>Adere aos padrões de conformidade como a Base de Dados Azure SQL <br>[Auditoria SQL](auditing-configure.md) <br>[Advanced Threat Protection](threat-detection-configure.md) |A API gestor de recursos Azure para automatizar o fornecimento e dimensionamento de serviços <br>Funcionalidade do portal Azure para o fornecimento e dimensionamento de serviços manuais <br>Serviço de Migração de Dados

> [!IMPORTANT]
> A Azure SQL Managed Instance foi certificada contra uma série de normas de conformidade. Para mais informações, consulte as Ofertas de Conformidade do [Microsoft Azure,](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuideV3?command=Download&downloadType=Document&downloadId=44bbae63-bf4d-4e3b-9d3d-c96fb25ec363&tab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb&docTab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb_FAQ_and_White_Papers) onde pode encontrar a lista mais atual de certificações de conformidade de instâncias geridas SQL, listadas na **Base de Dados SQL**.

As principais características da SQL Managed Instance são mostradas na tabela seguinte:

|Funcionalidade | Descrição|
|---|---|
| Versão / build SQL Server | Motor de base de dados SQL Server (mais recente estável) |
| Backups automatizados geridos | Yes |
| Caso incorporado e monitorização e métricas de bases de dados | Yes |
| Patching automático de software | Yes |
| As mais recentes funcionalidades do Motor de Base de Dados | Yes |
| Número de ficheiros de dados (ROWS) por base de dados | Vários |
| Número de ficheiros de registo (LOG) por base de dados | 1 |
| VNet - Implementação do Gestor de Recursos Azure | Yes |
| VNet - Modelo de implementação clássico | No |
| Suporte ao portal | Yes|
| Serviço de Integração Incorporada (SSIS) | No - SSIS faz parte da [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Serviço de Análise Incorporada (SSAS) | No - SSAS é [paaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) separado |
| Serviço de Reporte Incorporado (SSRS) | Não - utilize [relatórios paginados power BI](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) ou host SSRS em Azure VM. Embora a Instância Gerida não possa executar o SSRS como um serviço, pode hospedar bases de dados de catálogo SSRS 2019 para um servidor de relatório externo utilizando a autenticação do SQL Server. |
|||

## <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

O modelo de compra baseado em vCore para SQL Managed Instance [dá-lhe](../database/service-tiers-vcore.md) flexibilidade, controlo, transparência e uma forma simples de traduzir os requisitos de carga de trabalho no local para a nuvem. Este modelo permite-lhe alterar o cálculo, a memória e o armazenamento com base nas suas necessidades de carga de trabalho. O modelo vCore também é elegível para uma poupança de até 55% com o [Benefício Híbrido Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) para o SQL Server.

No modelo vCore, pode escolher entre gerações de hardware.

- **Gen4** Os CPUs lógicos são baseados em processadores Intel E5-2673 v3 (Haswell) 2.4-GHz, SSD anexados, núcleos físicos, 7 GB de RAM por núcleo e tamanhos de cálculo entre 8 e 24 vCores.
- **Gen5** Os CPUs lógicos são baseados em processadores Intel E5-2673 v4 (Broadwell) 2.3-GHz e Intel SP-8160 (Skylake), rápidoS NVMe SSD, núcleo lógico hiper-roscado e tamanhos computacional entre 4 e 80 núcleos.

Encontre mais informações sobre a diferença entre gerações de hardware nos [limites de recursos sql Managed Instance](resource-limits.md#hardware-generation-characteristics).

## <a name="service-tiers"></a>Escalões de serviço

SQL Managed Instance está disponível em dois níveis de serviço:

- **Finalidade geral**: Concebido para aplicações com requisitos típicos de desempenho e io.
- **Critical do negócio**: Concebido para aplicações com baixos requisitos de latência de IO e impacto mínimo das operações de manutenção subjacentes na carga de trabalho.

Ambos os níveis de serviço garantem uma disponibilidade de 99,99% e permitem selecionar independentemente o tamanho do armazenamento e a capacidade de computação. Para obter mais informações sobre a arquitetura de alta disponibilidade do Azure SQL Managed Instance, consulte [Alta disponibilidade e Azure SQL Managed Instance](../database/high-availability-sla.md).

### <a name="general-purpose-service-tier"></a>Nível de serviço para fins gerais

A lista que se segue descreve a característica-chave do nível de serviço para fins gerais:

- Design para a maioria das aplicações empresariais com requisitos típicos de desempenho
- Armazenamento Azure Blob de alto desempenho (8 TB)
- Alta [disponibilidade](../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-availability) incorporada com base no armazenamento fiável do Azure Blob e no [Tecido de Serviço Azure](../../service-fabric/service-fabric-overview.md)

Para obter mais informações, consulte [a camada de armazenamento nas](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) [melhores práticas e considerações de desempenho de finalidade geral e desempenho de armazenamento para a SQL Managed Instance (finalidade geral)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

Encontre mais informações sobre a diferença entre os níveis de serviço nos [limites de recursos sql Managed Instance](resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Nível de serviço critical de negócios

Business Critical o nível de serviço é construído para aplicações com elevados requisitos de IO. Oferece maior resiliência a falhas usando várias réplicas isoladas.

A lista que se segue descreve as características-chave do nível de serviço Business Critical:

- Projetado para aplicações empresariais com maior desempenho e requisitos de HA
- Vem com armazenamento SSD local super rápido (até 1 TB na Gen4 e até 4 TB na Gen5)
- Alta [disponibilidade](../database/high-availability-sla.md#premium-and-business-critical-service-tier-availability) incorporada com base em [Grupos de Disponibilidade Sempre E](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) Tecido de Serviço [Azure](../../service-fabric/service-fabric-overview.md)
- Réplica de [base de dados](../database/read-scale-out.md) adicional incorporada apenas para leitura que pode ser usada para relatórios e outras cargas de trabalho apenas de leitura
- [OLTP em memória](../in-memory-oltp-overview.md) que pode ser usado para carga de trabalho com requisitos de alto desempenho  

Encontre mais informações sobre a diferença entre os níveis de serviço nos [limites de recursos sql Managed Instance](resource-limits.md#service-tier-characteristics).

## <a name="management-operations"></a>Operações de gestão

A Azure SQL Managed Instance fornece operações de gestão que pode utilizar para implementar automaticamente novos casos geridos, atualizar propriedades de instância e apagar casos quando já não é necessário. Esta secção fornece informações sobre operações de gestão e suas durações típicas.

Para suportar [implementações dentro de Redes Virtuais Azure (VNets)](../../virtual-network/virtual-network-for-azure-services.md) e fornecer isolamento e segurança para os clientes, a SQL Managed Instance conta com [clusters virtuais,](connectivity-architecture-overview.md#high-level-connectivity-architecture)que representam um conjunto dedicado de máquinas virtuais isoladas implantadas dentro da sub-rede de rede virtual do cliente. Essencialmente, cada implementação de instâncias geridas numa sub-rede vazia resulta numa nova construção de cluster virtual.

As operações subsequentes em casos geridos implantados podem também ter efeitos no seu cluster virtual subjacente. Isto afeta a duração das operações de gestão, uma vez que a implementação de máquinas virtuais adicionais vem com uma sobrecarga que precisa de ser considerada quando planeia novas implementações ou atualizações para instâncias geridas existentes.

Todas as operações de gestão podem ser categorizadas da seguinte forma:

- Implantação de instância (criação de novos casos).
- Atualização de instâncias (alterar propriedades de instância, tais como vCores ou armazenamento reservado.
- Eliminação de exemplos.

Normalmente, as operações em aglomerados virtuais demoram mais tempo. A duração das operações em clusters virtuais varia – abaixo estão os valores que normalmente se pode esperar, com base nos dados de telemetria de serviço existentes:

- **Criação de cluster virtual**: Este é um passo sincronizado nas operações de gestão de casos. **90% das operações terminam em 4 horas.**
- **Redimensionamento de cluster virtual (expansão ou redução)**: A expansão é um passo sincronizado, enquanto a redução é realizada de forma assíncronea (sem impacto na duração das operações de gestão de instância). **90% das expansões de cluster terminam em menos de 2,5 horas**.
- **Eliminação virtual do agrupamento**: A eliminação é um passo assíncronos, mas também pode ser [iniciado manualmente](virtual-cluster-delete.md) num aglomerado virtual vazio, caso em que executa sincronizadamente. **90% das eliminações de clusters virtuais terminam em 1,5 horas**.

Além disso, a gestão de casos pode igualmente incluir uma das operações em bases de dados hospedadas, o que resulta em durações mais longas:

- **Anexar ficheiros de base de dados do Azure Storage**: Trata-se de um passo sincronizado, como o cálculo (vCore), ou o armazenamento a escalonar ou a descer no nível de serviço Final geral. **90% destas operações terminam em 5 minutos**.
- **Sempre Na sementeira do grupo de disponibilidade : Trata-se**de um passo sincronizado, como o cálculo (vCore), ou a escala de armazenamento no nível de serviço Business Critical, bem como na alteração do nível de serviço de Propósito Geral para Business Critical (ou vice-versa). A duração desta operação é proporcional ao tamanho total da base de dados, bem como à atividade atual da base de dados (número de transações ativas). A atividade da base de dados ao atualizar uma instância pode introduzir uma variação significativa da duração total. **90% destas operações executam a 220 GB/hora ou superior.**

O quadro que se segue resume as operações e as durações globais típicas:

|Categoria  |Operação  |Segmento de longa duração  |Duração estimada  |
|---------|---------|---------|---------|
|**Implementação** |Primeira instância numa sub-rede vazia|Criação de cluster virtual|90% das operações terminam em 4 horas|
|Implementação |Primeira instância de outra geração de hardware numa sub-rede não vazia (por exemplo, primeira instância da Gen 5 numa sub-rede com instâncias da Gen 4)|Criação de cluster virtual*|90% das operações terminam em 4 horas|
|Implementação |Criação de 4 vCores em primeira instância, numa sub-rede vazia ou não vazia|Criação de cluster virtual**|90% das operações terminam em 4 horas|
|Implementação |Criação de instância subsequente dentro da sub-rede não vazia (2º, 3º, etc. instância)|Redimensionamento de cluster virtual|90% das operações terminam em 2,5 horas|
|**Atualizar** |Mudança de propriedade de exemplo (senha de administração, login Azure AD, bandeira de Benefício Híbrido Azure)|N/D|Até 1 minuto|
|Atualizar |Armazenamento de instâncias para cima/para baixo (nível de serviço para fins gerais)|Anexação de ficheiros de base de dados|90% das operações terminam em 5 minutos|
|Atualizar |Armazenamento de instâncias escalando para cima/para baixo (nível de serviço Critical empresarial)|- Redimensionamento de cluster virtual<br>- Sempre na sementeira de grupo de disponibilidade|90% das operações terminam em 2,5 horas + tempo para semear todas as bases de dados (220 GB/hora)|
|Atualizar |Cálculo de instância (vCores) escalando para cima e para baixo (Final geral)|- Redimensionamento de cluster virtual<br>- Anexação de ficheiros de base de dados|90% das operações terminam em 2,5 horas|
|Atualizar |Cálculo de exemplo (vCores) escalando para cima e para baixo (Business Critical)|- Redimensionamento de cluster virtual<br>- Sempre na sementeira de grupo de disponibilidade|90% das operações terminam em 2,5 horas + tempo para semear todas as bases de dados (220 GB/hora)|
|Atualizar |Escala de instância para 4 vCores (Final geral)|- Redimensionamento de cluster virtual (se feito pela primeira vez, pode exigir criação de cluster virtual**)<br>- Anexação de ficheiros de base de dados|90% das operações terminam em 4 h 5 min**|
|Atualizar |Escala de exemplo para 4 vCores (Business Critical)|- Redimensionamento de cluster virtual (se feito pela primeira vez, pode exigir criação de cluster virtual**)<br>- Sempre na sementeira de grupo de disponibilidade|90% das operações terminam em 4 horas + tempo para semear todas as bases de dados (220 GB/hora)|
|Atualizar |Alteração do nível de serviço de instância (Final geral para Business Critical e vice-versa)|- Redimensionamento de cluster virtual<br>- Sempre na sementeira de grupo de disponibilidade|90% das operações terminam em 2,5 horas + tempo para semear todas as bases de dados (220 GB/hora)|
|**Eliminação**|Eliminação da instância|Registar backup de cauda para todas as bases de dados|90% das operações terminam em até 1 minuto.<br>Nota: se a última instância na sub-rede for eliminada, esta operação irá agendar a eliminação virtual do cluster após 12 horas***|
|Eliminação|Eliminação de clusters virtuais (como operação iniciada pelo utilizador)|Eliminação de clusters virtuais|90% das operações terminam em até 1,5 horas|

\*O cluster virtual é construído por geração de hardware.

\*\*A opção 4 vCores foi lançada em junho de 2019 e requer uma nova versão de cluster virtual. Se tiver casos na sub-rede alvo que foram todos criados antes de 12 de junho, um novo cluster virtual será implantado automaticamente para hospedar 4 instâncias vCore.

\*\*\*12 horas é a configuração atual, mas isso pode mudar no futuro, por isso não tenhas uma dependência difícil. Se precisar de eliminar um cluster virtual mais cedo (para lançar a sub-rede, por exemplo), consulte [Eliminar uma sub-rede depois de eliminar uma Instância Gerida Azure SQL](virtual-cluster-delete.md).

### <a name="instance-availability-during-management-operations"></a>Disponibilidade de instâncias durante operações de gestão

A SQL Managed Instance não está disponível para aplicações de clientes durante operações de implantação e eliminação.

SQL Managed Instance está disponível durante as operações de atualização, exceto um curto tempo de inatividade causado pela falha que ocorre no final da atualização. Normalmente dura até 10 segundos, mesmo em caso de transações interrompidas a longo prazo, graças à [recuperação acelerada](../accelerated-database-recovery.md)da base de dados.

> [!IMPORTANT]
> Não é recomendado escalar o cálculo ou armazenamento de Azure SQL Managed Instance ou alterar o nível de serviço ao mesmo tempo com as transações de longo prazo (importação de dados, trabalhos de processamento de dados, reconstrução de índices, etc.). A falha na base de dados que será realizada no final da operação irá cancelar todas as transações em curso.

### <a name="management-operations-cross-impact"></a>Impacto transversal das operações de gestão

As operações de gestão da SQL Managed Instance podem afetar outras operações de gestão das instâncias colocadas dentro do mesmo cluster virtual. Isto inclui os seguintes:

- **As operações de restauro de longa duração** num cluster virtual colocarão em espera outras situações de criação ou operação de escala na mesma sub-rede.<br/>**Exemplo:** se houver uma operação de restauro a decorrer há muito tempo e houver um pedido de criação ou escala na mesma sub-rede, este pedido demorará mais tempo a ser concluído, uma vez que aguardará que a operação de restauro esteja concluída antes de continuar.
    
- **A operação de criação ou escalonamento subsequente** é posta em espera pela criação ou escala de instância iniciada anteriormente que iniciou o redimensionamento do cluster virtual.<br/>**Exemplo:** se houver múltiplos pedidos de criação e/ou escala na mesma sub-rede sob o mesmo cluster virtual, e um deles iniciar o redimensionar o cluster virtual, todos os pedidos que foram submetidos 5+ minutos após o que exigia o redimensionado cluster virtual durará mais do que o esperado, uma vez que estes pedidos terão de esperar que o redimensionado seja concluído antes de retomar.

- **As operações de criação/escala submetidas na janela de 5 minutos** serão em loteadas e executadas em paralelo.<br/>**Exemplo:** Apenas um redimensionamento de cluster virtual será realizado para todas as operações submetidas na janela de 5 minutos (medindo a partir do momento da execução do primeiro pedido de operação). Caso outro pedido seja apresentado mais de 5 minutos após a apresentação do primeiro, aguardará o redimensionamento do cluster virtual para ser concluído antes do início da execução.

> [!IMPORTANT]
> As operações de gestão que forem postas em suspenso por causa de uma outra operação em curso serão automaticamente retomadas assim que as condições para proceder forem satisfeitas. Não é necessária nenhuma ação do utilizador para retomar temporariamente a operação de gestão interrompida.

### <a name="canceling-management-operations"></a>Cancelamento de operações de gestão

O quadro que se segue resume a capacidade de cancelar operações de gestão específicas e as durações globais típicas:

Categoria  |Operação  |Cancelável  |Duração estimada do cancelamento  |
|---------|---------|---------|---------|
|Implementação |Criação de exemplos |No |  |
|Atualizar |Armazenamento de instâncias para cima/para baixo (Final geral) |No |  |
|Atualizar |Armazenamento de instâncias escalando para cima/para baixo (Business Critical) |Yes |90% das operações terminam em 5 minutos |
|Atualizar |Cálculo de instância (vCores) escalando para cima e para baixo (Final geral) |Yes |90% das operações terminam em 5 minutos |
|Atualizar |Cálculo de exemplo (vCores) escalando para cima e para baixo (Business Critical) |Yes |90% das operações terminam em 5 minutos |
|Atualizar |Alteração do nível de serviço de instância (Final geral para Business Critical e vice-versa) |Yes |90% das operações terminam em 5 minutos |
|Eliminar |Eliminação da instância |No |  |
|Eliminar |Eliminação de clusters virtuais (como operação iniciada pelo utilizador) |No |  |

Para cancelar a operação de gestão, vá à lâmina de visão geral e clique na caixa de notificação da operação em curso. Do lado direito, aparecerá um ecrã com funcionamento em curso e haverá botão para cancelar a operação. Após o primeiro clique, ser-lhe-á pedido que clique novamente e confirme que pretende cancelar a operação.

[![Cancelar operação](./media/sql-managed-instance-paas-overview/canceling-operation.png)](./media/sql-managed-instance-paas-overview/canceling-operation.png#lightbox)

Após o pedido de cancelamento ter sido submetido e processado, receberá a notificação se a submissão de cancelamento foi bem sucedida ou não.

Em caso de cancelamento de sucesso, a operação de gestão será cancelada em alguns minutos, resultando em uma falha.

![cancelamento resultado da operação](./media/sql-managed-instance-paas-overview/canceling-operation-result.png)

Se o pedido de cancelamento falhar ou cancelar o botão não estiver ativo, isso significa que a operação de gestão entrou em estado não cancelável e que terminará em alguns minutos. A operação de gestão continuará a sua execução até que esteja concluída.

> [!IMPORTANT]
> A operação de cancelamento é suportada apenas no Portal.

## <a name="advanced-security-and-compliance"></a>Segurança e conformidade avançadas

SQL Managed Instance vem com funcionalidades de segurança avançadas fornecidas pela nuvem Azure e pelo motor de base de dados SQL Server.

### <a name="security-isolation"></a>Isolamento de segurança

SQL Managed Instance proporciona isolamento adicional de segurança de outros inquilinos na nuvem Azure. O isolamento de segurança inclui:

- [Implementação de rede virtual nativa](connectivity-architecture-overview.md) e conectividade com o seu ambiente no local usando a Rota Expresso Azure ou VPN Gateway.
- Numa implementação predefinida, o ponto final SQL é exposto apenas através de um endereço IP privado, permitindo uma conectividade segura a partir de redes privadas Azure ou híbridas.
- Inquilino único com infraestruturas subjacentes dedicadas (computação, armazenamento).

O diagrama a seguir descreve várias opções de conectividade para as suas aplicações:

![alta disponibilidade](./media/sql-managed-instance-paas-overview/application-deployment-topologies.png)  

Para obter mais detalhes sobre a integração do VNet e a aplicação de políticas de networking ao nível da sub-rede, consulte a [arquitetura VNet para instâncias geridas](connectivity-architecture-overview.md) e [Ligue a sua aplicação a um exemplo gerido.](connect-application-instance.md)

> [!IMPORTANT]
> Coloque várias instâncias geridas na mesma sub-rede, onde quer que isso seja permitido pelos seus requisitos de segurança, pois isso lhe trará benefícios adicionais. A colagem de instâncias na mesma sub-rede simplificará significativamente a manutenção das infraestruturas de ligação em rede e reduzirá o tempo de provisionamento de instâncias, uma vez que a longa duração do fornecimento está associada ao custo de implantação da primeira instância gerida numa sub-rede.

### <a name="security-features"></a>Recursos de segurança

A azure SQL Managed Instance fornece um conjunto de funcionalidades avançadas de segurança que podem ser usadas para proteger os seus dados.

- [A auditoria da SQL Managed Instance](auditing-configure.md) rastreia eventos de base de dados e escreve-os para um ficheiro de registo de auditoria colocado na sua conta de armazenamento Azure. A auditoria pode ajudar a manter a conformidade regulatória, compreender a atividade da base de dados e obter informações sobre discrepâncias e anomalias que possam indicar preocupações comerciais ou suspeitas de violações de segurança.
- Encriptação de dados em movimento - uma SQL Managed Instance protege os seus dados fornecendo encriptação para dados em movimento usando a Segurança da Camada de Transporte. Além da segurança da camada de transporte, a SQL Managed Instance oferece proteção de dados sensíveis a bordo, em repouso e durante o processamento de consultas com [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted é uma funcionalidade pioneira da indústria que oferece segurança de dados sem paralelo contra violações que envolvam o roubo de dados confidenciais. Por exemplo, com Always Encrypted, os números de cartão de crédito são armazenados encriptados na base de dados sempre, mesmo durante o processamento de consultas, permitindo a desencriptação no ponto de utilização por pessoal autorizado ou aplicações que precisam de processar esses dados.
- [Advanced Threat Protection](threat-detection-configure.md) complementa [a auditoria](auditing-configure.md) fornecendo uma camada adicional de inteligência de segurança incorporada no serviço que deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar bases de dados. Você é alertado sobre atividades suspeitas, potenciais vulnerabilidades e ataques de injeção DE SQL, bem como padrões de acesso a bases de dados anómalas. Alertas avançados de Proteção de Ameaças podem ser vistos a partir do Centro de [Segurança Azure](https://azure.microsoft.com/services/security-center/) e fornecer detalhes de atividades suspeitas e recomendar ações sobre como investigar e mitigar a ameaça.  
- [A mascaração dinâmica de dados](/sql/relational-databases/security/dynamic-data-masking) limita a exposição sensível aos dados, mascarando-os a utilizadores não privilegiados. A mascaração dinâmica de dados ajuda a impedir o acesso não autorizado a dados sensíveis, permitindo-lhe designar a quantidade de dados sensíveis a revelar com o mínimo impacto na camada de aplicação. É uma funcionalidade de segurança baseada em políticas que esconde os dados sensíveis no conjunto de resultados de uma consulta sobre os campos de base de dados designados, enquanto os dados na base de dados não são alterados.
- [A segurança ao nível da linha permite-lhe](/sql/relational-databases/security/row-level-security) controlar o acesso a linhas numa tabela de bases de dados com base nas características do utilizador executar uma consulta (por exemplo, por membro do grupo ou contexto de execução). A segurança ao nível da linha (RLS) simplifica o design e a programação da segurança na sua aplicação. O RLS permite-lhe implementar restrições ao acesso à linha de dados. Por exemplo, garantir que os trabalhadores só podem aceder às linhas de dados que são pertinentes para o seu departamento, ou restringir o acesso de dados apenas aos dados relevantes.
- [A encriptação transparente de dados (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) encripta ficheiros de dados de casos geridos SQL, conhecidos como dados encriptados em repouso. O TDE executa encriptação e desencriptação em tempo real dos dados e registos. A encriptação utiliza uma chave de encriptação de base de dados (DEK), que é armazenada no registo de arranque da base de dados para disponibilidade durante a recuperação. Pode proteger todas as suas bases de dados num caso gerido com encriptação de dados transparente. O TDE é a tecnologia comprovada de encriptação em repouso do SQL Server que é exigida por muitos padrões de conformidade para proteger contra o roubo de meios de armazenamento.

A migração de uma base de dados encriptada para uma SqL Managed Instance é suportada através do Serviço de Migração de Bases de Dados (DMS) ou restauro nativo. Se planeia migrar uma base de dados encriptada utilizando a restauração nativa, a migração do certificado TDE existente da instância SQL Server para uma SqL Managed Instance é um passo necessário. Para obter mais informações sobre as opções de migração, consulte [a migração do SQL Server para o SQL Managed Instance](migrate-to-instance-from-sql-server.md).

## <a name="azure-active-directory-integration"></a>Integração do Diretório Ativo Azure

A SQL Managed Instance suporta os tradicionais logins e logins do motor de base de dados do SQL Server integrados com o Azure Ative Directory (Azure AD). Os principais servidores AD do Azure (logins)**(pré-visualização pública)** são a versão em nuvem Azure dos logins de bases de dados no local que está a utilizar no seu ambiente no local. Os principais dos servidores AD (logins) permitem especificar utilizadores e grupos do seu inquilino do Azure Ative Directory como verdadeiros principais de instâncias, capazes de realizar qualquer operação ao nível de instância, incluindo consultas de base de dados cruzadas dentro da mesma instância gerida.

É introduzida uma nova sintaxe para criar os principais servidores AD do Azure (logins), **DO FORNECEDOR EXTERNO**. Para obter mais informações sobre a sintaxe, consulte <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>e reveja a [Provisão de um administrador do Azure Ative Directory para o seu artigo de Exemplos Geridos SQL.](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integração do Azure Active Directory e a autenticação multifator

A SQL Managed Instance permite-lhe gerir centralmente identidades de utilizador de base de dados e outros serviços da Microsoft com [integração do Azure Ative Directory](../database/authentication-aad-overview.md). Esta capacidade simplifica a gestão de permissões e melhora a segurança. O Azure Active Directory suporta a [autenticação multifator](../database/authentication-mfa-ssms-configure.md) (MFA), para aumentar a segurança de dados e aplicações, suportando, ao mesmo tempo, um processo de início de sessão único.

### <a name="authentication"></a>Autenticação

A autenticação sql Managed Instance refere-se à forma como os utilizadores comprovam a sua identidade ao ligarem-se à base de dados. Sql Managed Instance suporta dois tipos de autenticação:  

- **Autenticação SQL:**

  Este método de autenticação utiliza um nome de utilizador e uma palavra-passe.
- **Autenticação do Diretório Ativo Azure:**

  Este método de autenticação utiliza identidades geridas pelo Azure Ative Directory e é suportado para domínios geridos e integrados. Utilize a autenticação do Active Directory (segurança integrada) [sempre que possível](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

### <a name="authorization"></a>Autorização

A autorização refere-se ao que um utilizador pode fazer dentro de uma base de dados em Azure SQL Managed Instance, e é controlada pelos membros da sua conta de utilizador e permissões de nível de objeto. Uma SQL Managed Instance tem as mesmas capacidades de autorização que o SQL Server 2017.

## <a name="database-migration"></a>Migração de bases de dados

SQL Managed Instance visa cenários de utilizadores com migração de bases de dados em massa a partir de implementações de bases de dados no local ou de dados de iaaS. SQL Managed Instance suporta várias opções de migração de bases de dados:

### <a name="backup-and-restore"></a>Cópia de segurança e restauro  

A abordagem de migração aproveita os backups do SQL para o armazenamento da Azure Blob. As cópias de segurança armazenadas na bolha de armazenamento Azure podem ser diretamente restauradas numa 222 22 2019 através do [comando T-SQL RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- Para obter um arranque rápido que mostre como restaurar o ficheiro de backup da base de dados Standard World Importers - Standard database, consulte [Restaurar um ficheiro de backup para uma instância gerida](restore-sample-database-quickstart.md). Este quickstart mostra que você tem que carregar um ficheiro de backup para o armazenamento de blob Azure e fixá-lo usando uma chave de assinatura de acesso compartilhado (SAS).
- Para obter informações sobre a restauração do URL, consulte [Native RESTORE from URL](migrate-to-instance-from-sql-server.md#native-restore-from-url).

> [!IMPORTANT]
> As cópias de segurança de uma Sql Managed Instance só podem ser restauradas para outra SqL Managed Instance. Não podem ser restaurados numa instância do SQL Server ou na Base de Dados Azure SQL.

### <a name="data-migration-service"></a>Serviço de Migração de Dados

O Azure Database Migration Service é um serviço totalmente gerido projetado para permitir migrações sem emenda de múltiplas fontes de base de dados para plataformas de dados Azure com tempo de inatividade mínimo. Este serviço simplifica as tarefas necessárias para mover as bases de dados existentes de terceiros e sql server para Azure SQL Database, Azure SQL Managed Instance e SQL Server em Azure VM. Veja [como migrar a sua base de dados no local para SQL Managed Instance usando DMS](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Funcionalidades SQL suportadas

A SQL Managed Instance tem como objetivo fornecer compatibilidade de área de superfície de cerca de 100% com a versão mais recente do SQL Server através de um plano de lançamento encenado. Para uma lista de funcionalidades e comparação, consulte a comparação de [funcionalidades de instância gerida sql,](../database/features-comparison.md)e para uma lista de diferenças T-SQL em SQL Managed Instance versus SQL Server, consulte [as diferenças de T-SQL de instância gerida sql do SqL Server](transact-sql-tsql-differences-sql-server.md).

A SQL Managed Instance suporta retrocompatibilidade nas bases de dados SQL 2008. A migração direta dos servidores de base de dados SQL 2005 é suportada, o nível de compatibilidade das bases de dados de SQL 2005 migradas é atualizado para o SQL 2008.
  
O diagrama que se segue descreve a compatibilidade da área da superfície em SQL Managed Instance:  

![migração](./media/sql-managed-instance-paas-overview/migration.png)

### <a name="key-differences-on-premises-and-sql-managed-instance"></a>Principais diferenças no local e sql Gestd Instance

A SQL Managed Instance beneficia de estar sempre atualizada na nuvem, o que significa que algumas funcionalidades no SQL Server podem ser obsoletas, aposentadas ou ter alternativas. Existem casos específicos em que as ferramentas precisam reconhecer que uma determinada característica funciona de uma forma ligeiramente diferente ou que o serviço está a funcionar num ambiente que não controla totalmente.

Algumas diferenças fundamentais:

- A alta disponibilidade é incorporada e pré-configurada utilizando tecnologia semelhante aos [grupos de disponibilidade Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Existem apenas cópias de segurança automatizadas e ponto no tempo restaurado. O cliente pode iniciar `copy-only` cópias de segurança que não interfiram com a cadeia automática de backup.
- Especificar os caminhos físicos completos não é suportado para que todos os cenários correspondentes tenham de ser suportados de forma diferente: O RESTORE DB não suporta com movimento, o CREATE DB não permite caminhos físicos, o BULK INSERT funciona apenas com Blobs Azure, etc.
- SQL Managed Instance suporta [a autenticação AZure AD](../database/authentication-aad-overview.md) como alternativa à autenticação do Windows.
- SQL Managed Instance gere automaticamente o grupo de ficheiros XTP e ficheiros para bases de dados que contenham objetos OLTP de memória
- A SQL Managed Instance suporta os Serviços de Integração de Servidores SQL (SSIS) e pode acolher o catálogo SSIS (SSISDB) que armazena pacotes SSIS, mas são executados num runtime gerido de integração Azure-SSIS (IR) na Azure Data Factory (ADF), ver [Create Azure-SSIS IR in ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Para comparar as funcionalidades SSIS, consulte [compare a Base de Dados SQL com a SQL Managed Instance](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).

### <a name="administration-features"></a>Características da administração

A SQL Managed Instance permite ao administrador do sistema gastar menos tempo em tarefas administrativas porque o serviço SQL Managed Instance ou as executa para si ou simplifica essas tarefas. Por exemplo, [instalação e remendamento de OS /RDBMS,](../database/high-availability-sla.md) [redimensionamento e configuração de instâncias dinâmicas,](../database/single-database-scale.md) [backups,](../database/automated-backups-overview.md) [replicação de bases de dados](replication-between-two-instances-configure-tutorial.md) (incluindo bases de dados do sistema), configuração de [alta disponibilidade,](../database/high-availability-sla.md)e configuração de fluxos de dados de monitorização de saúde e [desempenho.](../../azure-monitor/insights/azure-sql.md)

Para mais informações consulte [uma lista de funcionalidades de instância gerida de SQL suportadas e não suportadas](../database/features-comparison.md), e [diferenças de T-SQL entre sql Managed Instance e SQL Server](transact-sql-tsql-differences-sql-server.md)

### <a name="programmatically-identify-a-managed-instance"></a>Identificar programáticamente um caso gerido

A tabela seguinte mostra várias propriedades, acessíveis através da Transact-SQL, que pode utilizar para detetar que a sua aplicação está a trabalhar com a SQL Managed Instance e recuperar propriedades importantes.

|Propriedade|Valor|Comentário|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Este valor é o mesmo que na Base de Dados SQL. Isto **não** indica a versão 12 do motor SQL (SQL Server 2014). A SQL Managed Instance executa sempre a mais recente versão estável do motor SQL, que é igual ou superior à versão RTM mais recente disponível do SQL Server.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Este valor é o mesmo que na Base de Dados SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Este valor identifica exclusivamente um caso gerido.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nome DNS de instância completa no seguinte formato: `<instanceName>` `<dnsPrefix>` . . database.windows.net, onde `<instanceName>` é o nome fornecido pelo cliente, enquanto é `<dnsPrefix>` autogerido parte do nome que garante a singularidade global do nome DNS ("wcus17662feb9ce98", por exemplo)|Exemplo: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Passos seguintes

- Para aprender a criar a sua primeira SqL Managed Instance, consulte o [guia Quickstart](instance-create-quickstart.md).
- Para obter uma lista de funcionalidades e comparação, consulte [as características comuns sql](../database/features-comparison.md).
- Para obter mais informações sobre a configuração VNet, consulte [a configuração VNet de instância gerida SQL](connectivity-architecture-overview.md).
- Para um arranque rápido que cria uma SQL Managed Instance e restaura uma base de dados a partir de um ficheiro de backup, consulte [criar uma SqL Managed Instance](instance-create-quickstart.md).
- Para um tutorial utilizando o Serviço de Migração de Bases de Dados de Azure (DMS) para migração, consulte [a migração de exemplos geridos sql utilizando DMS](../../dms/tutorial-sql-server-to-managed-instance.md).
- Para uma monitorização avançada do desempenho da base de dados SQL Managed Instance com inteligência incorporada para resolução de problemas, consulte [o Monitor Azure SQL Managed Instance utilizando a Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Para obter informações sobre preços, consulte a [base de dados SQL com preços de caso geridos](https://azure.microsoft.com/pricing/details/sql-database/managed/).
