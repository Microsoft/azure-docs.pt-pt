---
title: Pools de instâncias do banco de dados SQL do Azure (visualização) | Microsoft Docs
description: Este artigo descreve os pools da instância do banco de dados SQL do Azure (versão prévia).
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 34e779f04f59b23733c6fbfa3450931fccb442b1
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70294259"
---
# <a name="what-are-sql-database-instance-pools-preview"></a>O que são os pools de instâncias do banco de dados SQL (versão prévia)?

Os pools de instância são um novo recurso no banco de dados SQL do Azure que fornece uma maneira conveniente e econômica de migrar instâncias SQL menores para a nuvem em escala.

Os pools de instância permitem pré-configurar os recursos de computação de acordo com os requisitos de migração totais. Em seguida, você pode implantar várias instâncias gerenciadas individuais até seu nível de computação provisionado previamente. Por exemplo, se você pré-provisionar 8 vCores, poderá implantar duas instâncias VCORE e 4 VCORE e, em seguida, migrar bancos de dados para essas instâncias. Antes dos pools de instância estarem disponíveis, as cargas de trabalho menores e menos intensivas de computação geralmente teriam de ser consolidadas em uma instância gerenciada maior ao migrar para a nuvem. A necessidade de migrar grupos de bancos de dados para uma instância grande normalmente exigiu um planejamento de capacidade cuidadoso e governança de recursos, considerações de segurança adicionais e alguma consolidação de dados extra funciona no nível da instância.

Além disso, os pools de instância dão suporte à integração VNet nativa para que você possa implantar vários pools de instância e várias instâncias únicas na mesma sub-rede.


## <a name="key-capabilities-of-instance-pools"></a>Principais recursos de pools de instância

Os pools de instância oferecem os seguintes benefícios:

1. Capacidade de hospedar duas instâncias vCore. *Somente para instâncias em pools de instância. \**
2. Tempo de implantação previsível e de instância rápida (até 5 minutos).
3. Alocação mínima de endereço IP.

O diagrama a seguir ilustra um pool de instâncias com várias instâncias implantadas em uma sub-rede de rede virtual.

![pool de instâncias com várias instâncias](./media/sql-database-instance-pools/instance-pools1.png)

Os pools de instância habilitam a implantação de várias instâncias na mesma máquina virtual em que o tamanho de computação da máquina virtual se baseia no número total de vCores alocadas para o pool. Essa arquitetura permite o *particionamento* da máquina virtual em várias instâncias, que pode ter qualquer tamanho com suporte, incluindo 2 vCores (2 instâncias vCore estão disponíveis somente para instâncias em pools).

As operações de gerenciamento em instâncias em um pool são muito mais rápidas quando o pool é inicialmente implantado. Essas operações são mais rápidas porque a implantação ou a extensão de um [cluster virtual](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture) (conjunto dedicado de máquinas virtuais) não faz parte do provisionamento da instância gerenciada.

Como todas as instâncias em um pool compartilham a mesma máquina virtual, a alocação de IP total não depende do número de instâncias implantadas, o que é conveniente para a implantação em sub-redes com um intervalo de IP estreito.

Cada pool tem uma alocação de IP fixa de apenas nove endereços IP (não incluindo os cinco endereços IP na sub-rede que são reservados para suas próprias necessidades). Para obter detalhes, consulte [requisitos de tamanho de sub-rede para instâncias únicas](sql-database-managed-instance-determine-size-vnet-subnet.md).

## <a name="application-scenarios-for-instance-pools"></a>Cenários de aplicativo para pools de instância

A lista a seguir fornece os principais casos de uso em que os pools de instância devem ser considerados:

- Migração de *um grupo de instâncias SQL* ao mesmo tempo, em que a maioria é um tamanho menor (por exemplo, 2 ou 4 vCores).
- Cenários em que a *criação ou o dimensionamento de instância curta e previsível* é importante. Por exemplo, a implantação de um novo locatário em um ambiente de aplicativo SaaS multilocatário que requer recursos de nível de instância.
- Cenários nos quais um limite de *custo fixo* ou de *gastos* é importante. Por exemplo, a execução de ambientes compartilhados de desenvolvimento/teste ou de demonstração de um tamanho fixo (ou raramente alterado), no qual você implanta periodicamente instâncias gerenciadas quando necessário.
- Cenários em que a *alocação de endereço IP mínima* em uma sub-rede VNet é importante. Todas as instâncias em um pool estão compartilhando uma máquina virtual, portanto, o número de endereços IP alocados é menor do que no caso de instâncias únicas.


## <a name="architecture-of-instance-pools"></a>Arquitetura de pools de instâncias

Os pools de instância têm arquitetura semelhante a instâncias gerenciadas regulares (*instâncias únicas*). Para dar suporte a implantações [em redes virtuais do Azure (VNets)](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) e fornecer isolamento e segurança para clientes, os pools de instância também dependem de [clusters virtuais](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture). Os clusters virtuais representam um conjunto dedicado de máquinas virtuais isoladas implantadas dentro da sub-rede da rede virtual do cliente.

A principal diferença entre os dois modelos de implantação é que os pools de instância permitem várias implantações de processo de SQL Server no mesmo nó de máquina virtual, que são recursos controlados com o uso de [objetos de trabalho do Windows](https://docs.microsoft.com/windows/desktop/ProcThread/job-objects), enquanto as instâncias únicas sempre são sozinhas um nó de máquina virtual.

O diagrama a seguir mostra um pool de instâncias e duas instâncias individuais implantadas na mesma sub-rede e ilustra os principais detalhes de arquitetura para os dois modelos de implantação:

![pool de instâncias e duas instâncias individuais](./media/sql-database-instance-pools/instance-pools2.png)

Cada pool de instâncias cria um cluster virtual separado abaixo. Instâncias dentro de um pool e instâncias únicas implantadas na mesma sub-rede não compartilham recursos de computação alocados para SQL Server processos e componentes de gateway, isso garante a previsibilidade de desempenho.

## <a name="instance-pools-resource-limitations"></a>Limitações de recursos de pools de instância

Há várias limitações de recursos em relação a pools de instância e instâncias dentro de pools:

- Os pools de instância estão disponíveis somente no hardware Gen5.
- As instâncias dentro de um pool têm CPU e RAM dedicados, portanto, o número agregado de vCores em todas as instâncias deve ser menor ou igual ao número de vCores alocados para o pool.
- Todos os [limites de nível de instância](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) se aplicam a instâncias criadas dentro de um pool.
- Além dos limites em nível de instância, também há dois limites impostos *no nível do pool de instâncias*:
  - Tamanho total do armazenamento por pool (8 TB).
  - Número total de bancos de dados por pool (100).

A alocação de armazenamento total e o número de bancos de dados em todas as instâncias devem ser menores ou iguais aos limites expostos por pools de instância.

- Os pools de instâncias dão suporte a 8, 16, 24, 32, 40, 64 e 80 vCores.
- As instâncias gerenciadas dentro de pools dão suporte a 2, 4, 8, 16, 24, 32, 40, 64 e 80 vCores.
- Instâncias gerenciadas dentro de pools dão suporte a tamanhos de armazenamento entre 32 GB e 8 TB, exceto:
  - 2 instâncias de vCore dão suporte a tamanhos entre 32 GB e 640 GB
  - 4 instâncias de vCore dão suporte a tamanhos entre 32 GB e 2 TB

A [propriedade da camada de serviço](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) é associada ao recurso de pool de instâncias para que todas as instâncias em um pool devam ser a mesma camada de serviço que a camada de serviço do pool. Neste momento, somente a camada de serviço Uso Geral está disponível (consulte a seção a seguir sobre limitações na visualização atual).

### <a name="public-preview-limitations"></a>Limitações da pré-visualização pública

A visualização pública tem as seguintes limitações:

- Atualmente, apenas a camada de serviço Uso Geral está disponível.
- Os pools de instâncias não podem ser dimensionados durante a visualização pública, portanto, tome cuidado com o planejamento da capacidade Antes da implantação.
- Portal do Azure suporte para criação e configuração do pool de instâncias ainda não está disponível. Todas as operações em pools de instância têm suporte apenas por meio do PowerShell. A implantação de instância inicial em um pool pré-criado também tem suporte somente por meio do PowerShell. Uma vez implantado em um pool, as instâncias gerenciadas podem ser atualizadas usando o portal do Azure.
- Instâncias gerenciadas criadas fora do pool não podem ser movidas para um pool existente e as instâncias criadas dentro de um pool não podem ser movidas para fora como uma única instância ou para outro pool.
- O preço da instância reservada (licença incluída ou com Benefício Híbrido do Azure) não está disponível.

## <a name="sql-features-supported"></a>Recursos do SQL com suporte

As instâncias criadas em pools dão suporte aos mesmos [níveis de compatibilidade e recursos com suporte em instâncias gerenciadas únicas](sql-database-managed-instance.md#sql-features-supported).

Cada instância gerenciada implantada em um pool tem uma instância separada do SQL Agent.

Recursos opcionais ou recursos que exigem a escolha de valores específicos (como agrupamento em nível de instância, fuso horário, ponto de extremidade público para tráfego de dados, grupos de failover) são configurados em nível de instância e podem ser diferentes para cada instância em um pool.

## <a name="performance-considerations"></a>Considerações de desempenho

Embora as instâncias gerenciadas nos pools tenham vCore e RAM dedicados, elas compartilham o disco local (para uso de tempdb) e os recursos de rede. Não é provável, mas é possível experimentar o efeito *vizinho ruidosa* se várias instâncias no pool tiverem alto consumo de recursos ao mesmo tempo. Se você observar esse comportamento, considere implantar essas instâncias em um pool maior ou como instâncias únicas.

## <a name="security-considerations"></a>Considerações de segurança

Como as instâncias implantadas em um pool compartilham a mesma máquina virtual, convém considerar a desabilitação de recursos que introduzem riscos mais altos de segurança ou a controlar firmemente as permissões de acesso a esses recursos. Por exemplo, integração CLR, backup e restauração nativos, email de banco de dados, etc.

## <a name="instance-pool-support-requests"></a>Solicitações de suporte do pool de instâncias

Crie e gerencie solicitações de suporte para pools de instância no [portal do Azure](https://portal.azure.com).

Se você estiver tendo problemas relacionados à implantação do pool de instâncias (criação ou exclusão), certifique-se de especificar pools de **instância** no campo subtipo de **problema** .

![solicitação de suporte a pools de instâncias](./media/sql-database-instance-pools/support-request.png)

Se você estiver tendo problemas relacionados a instâncias ou bancos de dados individuais em um pool, deverá criar um tíquete de suporte regular para instâncias gerenciadas do banco de dados SQL do Azure.

Para criar implantações de instância gerenciada maiores (com ou sem pools de instância), talvez seja necessário obter uma cota regional maior. Use o [procedimento de instância gerenciada padrão para solicitar uma cota maior](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance), mas observe que, se você estiver usando pools de instância, a lógica de implantação comparará o consumo de vCore total *no nível do pool* em relação à sua cota para determinar se você está permissão para criar novos recursos sem aumentar ainda mais sua cota.

## <a name="instance-pool-billing"></a>Cobrança do pool de instâncias

Os pools de instância permitem o dimensionamento da computação e do armazenamento independentemente. Os clientes pagam pela computação associada ao recurso de pool medido em vCores e o armazenamento associado a cada instância medida em gigabytes (os primeiros 32 GB são gratuitos para cada instância).

o preço vCore de um pool é cobrado independentemente de quantas instâncias são implantadas nesse pool.

Para o preço de computação (medido em vCores), há duas opções de preço disponíveis:

  1. *Licença incluída*: Aplique licenças de SQL Server existentes com o Software Assurance.
  2. *Benefício híbrido do Azure*: Um preço reduzido que inclui Benefício Híbrido do Azure para SQL Server. Os clientes podem optar por esse preço usando suas licenças de SQL Server existentes com o Software Assurance. Para elegibilidade e outros detalhes, consulte [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

A definição de diferentes opções de preço não é possível para instâncias individuais em um pool. Todas as instâncias no pool pai devem estar no preço ou Benefício Híbrido do Azure preço incluído na licença. O modelo de licença para o pool pode ser alterado após a criação do pool.

> [!IMPORTANT]
> Se você especificar um modelo de licença para a instância diferente de no pool, o preço do pool será usado e o valor do nível da instância será ignorado.

Se você criar pools de instância em [assinaturas qualificadas para o benefício de desenvolvimento/teste](https://azure.microsoft.com/pricing/dev-test/), receberá automaticamente taxas com desconto de até 55% na instância gerenciada do SQL do Azure.

Para obter detalhes completos sobre o preço do pool de instâncias, consulte a seção pools de *instâncias* na [página de preços da instância gerenciada](https://azure.microsoft.com/pricing/details/sql-database/managed/).

## <a name="next-steps"></a>Passos Seguintes

- Para começar a usar os pools de instância, consulte [Guia de instruções de pool de instâncias do banco de dados SQL](sql-database-instance-pools-how-to.md).
- Para saber como criar sua primeira instância gerenciada, consulte [Guia de início rápido](sql-database-managed-instance-get-started.md).
- Para obter uma lista de recursos e comparação, consulte [recursos comuns do SQL](sql-database-features.md).
- Para obter mais informações sobre a configuração de VNet, consulte [configuração de vnet de instância gerenciada](sql-database-managed-instance-connectivity-architecture.md).
- Para obter um início rápido que cria uma instância gerenciada e restaura um banco de dados de um arquivo de backup, consulte [criar uma instância gerenciada](sql-database-managed-instance-get-started.md).
- Para obter um tutorial usando o serviço de migração de banco de dados do Azure (DMS) para migração, consulte [migração de instância gerenciada usando DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Para monitoramento avançado do desempenho do banco de dados de instância gerenciada com inteligência de solução de problemas interna, consulte [monitorar o banco de dados SQL do Azure usando análise de SQL do Azure](../azure-monitor/insights/azure-sql.md).
- Para obter informações sobre preços, consulte [preços da instância gerenciada do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).