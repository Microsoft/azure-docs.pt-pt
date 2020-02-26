---
title: Piscinas de instância (pré-visualização)
description: Este artigo descreve piscinas de instâncias de base de dados Azure SQL (pré-visualização).
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
ms.openlocfilehash: c1e740fbfa4bf1e8a77a2d9d6060ab39dba7ae7b
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587401"
---
# <a name="what-are-sql-database-instance-pools-preview"></a>O que são piscinas de instâncias de base de dados SQL (pré-visualização)?

As piscinas de instâncias são um novo recurso na Base de Dados Azure SQL que fornece uma forma conveniente e rentável de migrar casos SQL menores para a nuvem em escala.

Os conjuntos de instâncias permitem aprovisionar previamente os recursos de computação de acordo com os seus requisitos de migração totais. Em seguida, pode implementar várias instâncias geridas individuais até ao seu nível de computação aprovisionado previamente. Por exemplo, se fornecer 8 vCores pre-provisiona, pode implementar duas instâncias vCore e uma 4 vCore e, em seguida, migrar bases de dados para estas instâncias. Antes de estarem disponíveis piscinas, as cargas de trabalho mais pequenas e menos intensivas em termos de cálculo teriam muitas vezes de ser consolidadas numa instância gerida maior quando migravam para a nuvem. A necessidade de migrar grupos de bases de dados para um grande caso exigia normalmente um planeamento cuidadoso da capacidade e a governação dos recursos, considerações adicionais de segurança e alguns trabalhos extra de consolidação de dados a nível das instâncias.

Além disso, piscinas de exemplo suportam integração vnet nativa para que você possa implementar várias piscinas de instâncias e múltiplas instâncias individuais na mesma subnet.


## <a name="key-capabilities-of-instance-pools"></a>Principais capacidades de piscinas de instâncias

As piscinas de instâncias proporcionam os seguintes benefícios:

1. Capacidade de acolher 2 instâncias vCore. *\*Apenas por exemplo, por exemplo, piscinas.*
2. Tempo de implantação previsível e rápido (até 5 minutos).
3. Atribuição mínima de endereço IP.

O diagrama seguinte ilustra um conjunto de instâncias com múltiplas instâncias implantadas dentro de uma subnet de rede virtual.

![piscina de instância com múltiplas instâncias](./media/sql-database-instance-pools/instance-pools1.png)

As piscinas de instâncias permitem a implementação de múltiplas instâncias na mesma máquina virtual onde o tamanho da computação da máquina virtual é baseado no número total de vCores atribuídos para a piscina. Esta arquitetura permite *a divisão* da máquina virtual em várias instâncias, que podem ser de qualquer tamanho suportado, incluindo 2 vCores (2 instâncias vCore só estão disponíveis para casos em piscinas).

As operações de gestão em casos numa piscina são muito mais rápidas quando a piscina é inicialmente implantada. Estas operações são mais rápidas porque a implantação ou extensão de um [cluster virtual](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture) (conjunto dedicado de máquinas virtuais) não faz parte do fornecimento da instância gerida.

Uma vez que todos os casos de uma piscina partilham a mesma máquina virtual, a atribuição total de IP não depende do número de instâncias implementadas, o que é conveniente para a implantação em subredes com uma gama de IP estreita.

Cada piscina tem uma atribuição fixa de IP de apenas nove endereços IP (sem incluir os cinco endereços IP na subnet que são reservados para as suas próprias necessidades). Para mais detalhes, consulte os requisitos de tamanho da [subnet para instâncias individuais](sql-database-managed-instance-determine-size-vnet-subnet.md).

## <a name="application-scenarios-for-instance-pools"></a>Cenários de aplicação, por exemplo, piscinas

A seguinte lista fornece os principais casos de utilização em que as piscinas de instâncias devem ser consideradas:

- Migração de *um grupo de instâncias SQL* ao mesmo tempo, onde a maioria é de tamanho menor (por exemplo 2 ou 4 vCores).
- Cenários em que a criação ou a escala de *instâncias previsíveis e de curta duração* são importantes. Por exemplo, a implantação de um novo inquilino num ambiente de aplicação saaS multi-inquilino que requer capacidades de nível de exemplo.
- Cenários em que é importante ter um *custo fixo* ou um limite de *despesas.* Por exemplo, executar ambientes de dev-test ou demo partilhados de tamanho fixo (ou pouco frequentemente alterado), onde você periodicamente implementa instâncias geridas periodicamente quando necessário.
- Cenários em que a atribuição mínima de *endereçoip* numa subnet VNet é importante. Todos os casos numa piscina estão a partilhar uma máquina virtual, pelo que o número de endereços IP atribuídos é inferior ao caso de instâncias individuais.


## <a name="architecture-of-instance-pools"></a>Arquitetura de piscinas de instância

As piscinas de exemplo têm arquitetura semelhante a instâncias regulares*geridas (instâncias individuais).* Para apoiar as implementações dentro das [Redes Virtuais Azure (VNets)](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) e para fornecer isolamento e segurança aos clientes, por exemplo, os pools também dependem de [clusters virtuais.](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture) Os clusters virtuais representam um conjunto dedicado de máquinas virtuais isoladas implantadas dentro da subnet virtual da rede do cliente.

A principal diferença entre os dois modelos de implementação é que as piscinas de instâncias permitem várias implementações de processos do SQL Server no mesmo nó de máquina virtual, que são repartidos por recursos utilizando [objetos de trabalho do Windows](https://docs.microsoft.com/windows/desktop/ProcThread/job-objects), enquanto as instâncias individuais estão sempre sozinhas num nó de máquina virtual.

O diagrama seguinte mostra uma piscina de instâncias e duas instâncias individuais implantadas na mesma subnet e ilustra os principais detalhes arquitetónicos para ambos os modelos de implantação:

![piscina de instância e duas instâncias individuais](./media/sql-database-instance-pools/instance-pools2.png)

Cada piscina de instâncias cria um aglomerado virtual separado por baixo. As instâncias dentro de um pool e instâncias únicas implantadas na mesma subnet não partilham recursos computacionais atribuídos aos processos do SQL Server e componentes de gateway, o que garante a previsibilidade do desempenho.

## <a name="instance-pools-resource-limitations"></a>Por exemplo, agréns limitações de recursos

Existem várias limitações de recursos em relação a conjuntos de instâncias e a instâncias dentro de conjuntos:

- As piscinas de instâncias estão disponíveis apenas no hardware gen5.
- As instâncias dentro de uma piscina têm CPU e RAM dedicados, pelo que o número agregado de vCores em todas as instâncias deve ser inferior ou igual ao número de vCores atribuídos à piscina.
- Todos os limites de [nível](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) de instância aplicam-se a instâncias criadas dentro de uma piscina.
- Para além dos limites de nível de exemplo, existem também dois limites impostos *ao nível do pool:*
  - Tamanho total de armazenamento por piscina (8 TB).
  - Número total de bases de dados por piscina (100).

A atribuição total de armazenamento e o número de bases de dados em todas as instâncias devem ser inferiores ou iguais aos limites expostos por piscinas por exemplo.

- Piscinas de exemplo suportam 8, 16, 24, 32, 40, 64 e 80 vCores.
- As instâncias geridas dentro das piscinas suportam 2, 4, 8, 16, 24, 32, 40, 64 e 80 vCores.
- As instâncias geridas no interior das piscinas suportam tamanhos de armazenamento entre 32 GB e 8 TB, exceto:
  - 2 casos vCore suportam tamanhos entre 32 GB e 640 GB
  - 4 vCore tamanhos de suporte entre 32 GB e 2 TB

A [propriedade de nível de serviço](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) está associada com o recurso de piscina, por isso todos os casos numa piscina devem ser o mesmo nível de serviço que o nível de serviço da piscina. Neste momento, apenas está disponível o nível de serviço Para fins gerais (ver a seguinte secção sobre limitações na pré-visualização atual).

### <a name="public-preview-limitations"></a>Limitações da pré-visualização pública

A pré-visualização pública tem as seguintes limitações:

- Atualmente, apenas o nível de serviço de Propósito Geral está disponível.
- As piscinas por exemplo não podem ser dimensionadas durante a pré-visualização do público, pelo que é importante um planeamento cuidadoso da capacidade antes da implantação.
- O suporte do portal Azure, por exemplo, à criação e configuração do pool ainda não está disponível. Todas as operações em piscinas de instâncias são suportadas apenas através da PowerShell. A implementação de instâncias iniciais numa piscina pré-criada também é suportada apenas através da PowerShell. Uma vez implantados numa piscina, as instâncias geridas podem ser atualizadas através do portal Azure.
- Os casos geridos criados fora da piscina não podem ser movidos para uma piscina existente e as instâncias criadas dentro de uma piscina não podem ser movidas para fora como uma única instância ou para outra piscina.
- Os preços reservados por exemplo (licença incluída ou com Benefício Híbrido Azure) não estão disponíveis.

## <a name="sql-features-supported"></a>Recursos SQL suportados

As instâncias criadas em piscinas suportam os mesmos níveis de [compatibilidade e características suportadas em casos geridos únicos.](sql-database-managed-instance.md#sql-features-supported)

Cada instância gerida implantada numa piscina tem uma instância separada de Agente SQL.

As funcionalidades ou funcionalidades opcionais que exigem que escolha valores específicos (como a colagem de nível de exemplo, fuso horário, ponto final público para o tráfego de dados, grupos failover) são configurados ao nível de instância e podem ser diferentes para cada instância numa piscina.

## <a name="performance-considerations"></a>Considerações de desempenho

Embora as instâncias geridas dentro das piscinas tenham dedicado vCore e RAM, partilham o disco local (para uso temporário) e recursos de rede. Não é provável, mas é possível experimentar o efeito *ruidoso* do vizinho se várias instâncias na piscina tiverem um alto consumo de recursos ao mesmo tempo. Se observar este comportamento, considere implementar estes casos para uma piscina maior ou como instâncias individuais.

## <a name="security-considerations"></a>Considerações de segurança

Como os casos implantados numa piscina partilham a mesma máquina virtual, é melhor considerar funcionalidades incapacitantes que introduzam riscos de segurança mais elevados ou controlar firmemente as permissões de acesso a estas funcionalidades. Por exemplo, integração CLR, backup nativo e restauro, e-mail de base de dados, etc.

## <a name="instance-pool-support-requests"></a>Pedidos de apoio à piscina de exemplo

Crie e gere a solicitação de apoio, por exemplo, piscinas no [portal Azure.](https://portal.azure.com)

Se estiver a ter problemas relacionados com a implantação de piscinas por exemplo (criação ou eliminação), certifique-se de que especifica **Piscinas de Instância** no campo **do subtipo Problema.**

![caso supor pedido de apoio](./media/sql-database-instance-pools/support-request.png)

Se estiver a ter problemas relacionados com instâncias individuais ou bases de dados dentro de uma piscina, deverá criar um bilhete de apoio regular para casos geridos pela Base de Dados Azure SQL.

Para criar maiores implantações de instâncias geridas (com ou sem piscinas de instância), poderá ter de obter uma quota regional maior. Para mais informações, consulte o pedido de aumento da quota para a Base de [Dados SQL azure](quota-increase-request.md). Note que se estiver a usar piscinas de instâncias, a lógica de implementação compara o consumo total de vCore *ao nível da piscina* com a sua quota para determinar se está autorizado a criar novos recursos sem aumentar ainda mais a sua quota.

## <a name="instance-pool-billing"></a>Faturação da piscina de exemplo

As piscinas de exemplo permitem a escala e armazenamento de forma independente. Os clientes pagam por uma computação associada ao recurso da piscina medido em vCores, e armazenamento associado a cada instância medida em gigabytes (os primeiros 32 GB são gratuitos para cada instância).

o preço vCore para uma piscina é cobrado independentemente de quantas instâncias são implementadas nessa piscina.

Para o preço Compute (medido em vCores), estão disponíveis duas opções de preços:

  1. *Licença incluída*: O preço das licenças SQL está incluído. Isto é para os clientes que optam por não aplicar as licenças sQL Server existentes com garantia de software.
  2. *Benefício Híbrido Azure*: Um preço reduzido que inclui o Benefício Híbrido Azure para o Servidor SQL. Os clientes podem optar por este preço utilizando as licenças sQL Server existentes com garantia de software. Para obter elegibilidade e outros detalhes, consulte [O Benefício Híbrido Azure.](https://azure.microsoft.com/pricing/hybrid-benefit/)

A definição de diferentes opções de preços não é possível para instâncias individuais numa piscina. Todas as instâncias na piscina dos pais devem estar a preço incluído na Licença ou no Preço do Benefício Híbrido Azure. O modelo de licença para a piscina pode ser alterado após a criação da piscina.

> [!IMPORTANT]
> Se especificar um Modelo de Licença para o caso diferente do da piscina, o preço da piscina é usado e o valor de nível de instância é ignorado.

Se criar piscinas de instâncias em [subscrições elegíveis para benefício](https://azure.microsoft.com/pricing/dev-test/)de teste de v, recebe automaticamente tarifas descontadas até 55% em instância gerida pelo Azure SQL.

Para obter mais detalhes sobre os preços do pool, consulte a secção de *piscinas* de exemplo na página de preços de [instância gerida](https://azure.microsoft.com/pricing/details/sql-database/managed/).

## <a name="next-steps"></a>Passos seguintes

- Para começar com piscinas de instância, consulte [a instância de dados de dados SQL piscinas como guiar](sql-database-instance-pools-how-to.md).
- Para aprender a criar a sua primeira instância gerida, consulte [o guia Quickstart](sql-database-managed-instance-get-started.md).
- Para obter uma lista de funcionalidades e comparação, consulte [as características comuns da SQL](sql-database-features.md).
- Para obter mais informações sobre a configuração vNet, consulte a [configuração VNet de instância gerida](sql-database-managed-instance-connectivity-architecture.md).
- Para um arranque rápido que cria uma instância gerida e restaura uma base de dados a partir de um ficheiro de cópia de segurança, consulte [criar uma instância gerida](sql-database-managed-instance-get-started.md).
- Para um tutorial utilizando o Serviço de Migração de Bases de Dados Azure (DMS) para migração, consulte a migração por [exemplo gerida utilizando DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Para uma monitorização avançada do desempenho da base de dados de instâncias geridas com inteligência incorporada de resolução de problemas, consulte a Base de [Dados Monitor Azure SQL utilizando o Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Para obter informações sobre preços, consulte a Base de [Dados SQL gerida por preços](https://azure.microsoft.com/pricing/details/sql-database/managed/)de instância .
