---
title: O que é uma piscina Azure SQL Managed Instance?
titleSuffix: Azure SQL Managed Instance
description: Saiba mais sobre as piscinas Azure SQL Managed Instance (pré-visualização), uma funcionalidade que fornece uma forma conveniente e eficiente em migrar bases de dados de pequenos SQL Server para a nuvem em escala, e gerir múltiplos casos geridos.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/05/2019
ms.openlocfilehash: bc345509db1c2a14afb0ae781eccad8f77395c18
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347069"
---
# <a name="what-is-an-azure-sql-managed-instance-pool-preview"></a>O que é uma piscina Azure SQL Managed Instance (pré-visualização)?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

As piscinas de exemplo em Azure SQL Managed Instance fornecem uma maneira conveniente e eficiente em migrar pequenas instâncias sql server para a nuvem em escala.

Os conjuntos de instâncias permitem aprovisionar previamente os recursos de computação de acordo com os seus requisitos de migração totais. Em seguida, pode implementar várias instâncias geridas individuais até ao seu nível de computação aprovisionado previamente. Por exemplo, se pré-provisões 8 vCores, pode implementar duas instâncias 2-vCore e uma instância 4-vCore e, em seguida, migrar bases de dados para estes casos. Antes de existirem piscinas de caso, cargas de trabalho mais pequenas e menos intensivas de computação teriam muitas vezes de ser consolidadas num caso maior gerido ao migrar para a nuvem. A necessidade de migrar grupos de bases de dados para um grande caso exigia normalmente um planeamento cuidadoso da capacidade e a governação dos recursos, considerações adicionais de segurança e alguns trabalhos de consolidação de dados adicionais ao nível dos casos.

Além disso, os pools de exemplo suportam a integração nativa do VNet para que possa implementar várias instâncias e múltiplas instâncias individuais na mesma sub-rede.

## <a name="key-capabilities"></a>Principais capacidades

Os agrupamentos de instância proporcionam os seguintes benefícios:

1. Capacidade de hospedar instâncias 2-vCore. *\* Apenas por exemplo, nas piscinas de exemplo.*
2. Tempo de implantação previsível e rápido (até 5 minutos).
3. Alocação mínima de endereço IP.

O diagrama seguinte ilustra um conjunto de instâncias com múltiplas instâncias geridas implantadas dentro de uma sub-rede de rede virtual.

![piscina de instância com múltiplas instâncias](./media/instance-pools-overview/instance-pools1.png)

Os pools de instância permitem a implantação de múltiplas instâncias na mesma máquina virtual, onde o tamanho do cálculo da máquina virtual é baseado no número total de vCores atribuídos para a piscina. Esta arquitetura permite *a divisão* da máquina virtual em múltiplas instâncias, o que pode ser qualquer tamanho suportado, incluindo 2 vCores (instâncias 2-vCore só estão disponíveis para casos em piscinas).

Após a implantação inicial, as operações de gestão em casos numa piscina são muito mais rápidas. Isto porque a implantação ou extensão de um [cluster virtual](connectivity-architecture-overview.md#high-level-connectivity-architecture) (conjunto dedicado de máquinas virtuais) não faz parte do provisionamento da instância gerida.

Como todas as instâncias de um pool partilham a mesma máquina virtual, a atribuição total de IP não depende do número de casos implantados, o que é conveniente para a implantação em sub-redes com uma estreita gama de IP.

Cada pool tem uma atribuição de IP fixa de apenas nove endereços IP (não incluindo os cinco endereços IP na sub-rede que são reservados para as suas próprias necessidades). Para obter mais informações, consulte os requisitos de tamanho da [sub-rede para instâncias simples](vnet-subnet-determine-size.md).

## <a name="application-scenarios"></a>Cenários de aplicações

A lista que se segue fornece os principais casos de utilização em que devem ser considerados os pools de instância:

- Migração de *um grupo de instâncias sql server* ao mesmo tempo, onde a maioria é de tamanho menor (por exemplo, 2 ou 4 vCores).
- Cenários onde a *criação ou a escala de instâncias previsíveis e curtas são importantes.* Por exemplo, a implantação de um novo inquilino num ambiente de aplicação SaaS multi-inquilino que requer capacidades de nível de instância.
- São importantes cenários em que se tem um *limite de custo fixo* ou de *despesa.* Por exemplo, executar ambientes de teste dev ou demonstração partilhados de um tamanho fixo (ou de mudança rara), onde implanta periodicamente casos geridos quando necessário.
- Cenários em que a *atribuição mínima de endereços IP* numa sub-rede VNet é importante. Todos os casos numa piscina estão a partilhar uma máquina virtual, pelo que o número de endereços IP atribuídos é inferior ao caso de instâncias individuais.

## <a name="architecture"></a>Arquitetura

As piscinas de exemplo têm uma arquitetura semelhante às instâncias regulares *(únicas)* geridas. Para apoiar [as implementações dentro das redes virtuais Azure](../../virtual-network/virtual-network-for-azure-services.md) e para proporcionar isolamento e segurança aos clientes, os pools de exemplo também dependem de [clusters virtuais.](connectivity-architecture-overview.md#high-level-connectivity-architecture) Os clusters virtuais representam um conjunto dedicado de máquinas virtuais isoladas implantadas dentro da sub-rede de rede virtual do cliente.

A principal diferença entre os dois modelos de implementação é que os pools de instância permitem múltiplas implementações de processos SQL Server no mesmo nó de máquina virtual, que são reger-se por recursos usando [objetos de trabalho do Windows](/windows/desktop/ProcThread/job-objects), enquanto as instâncias individuais estão sempre sozinhas num nó de máquina virtual.

O diagrama a seguir mostra uma piscina de exemplo e duas instâncias individuais implantadas na mesma sub-rede e ilustra os principais detalhes arquitetónicos para ambos os modelos de implantação:

![Conjunto de instância e dois casos individuais](./media/instance-pools-overview/instance-pools2.png)

Cada conjunto de instâncias cria um aglomerado virtual separado por baixo. As instâncias dentro de um pool e instâncias individuais implantadas na mesma sub-rede não partilham recursos computativos atribuídos a processos do SQL Server e componentes de gateway, o que garante previsibilidade de desempenho.

## <a name="resource-limitations"></a>Limitações de recursos

Existem várias limitações de recursos em relação a conjuntos de instâncias e a instâncias dentro de conjuntos:

- Os pools de exemplo estão disponíveis apenas no hardware da Gen5.
- As instâncias geridas dentro de uma piscina têm CPU e RAM dedicados, por isso o número agregado de vCores em todas as instâncias deve ser inferior ou igual ao número de vCores atribuídos à piscina.
- Todos os [limites de nível de instância aplicam-se](resource-limits.md#service-tier-characteristics) a instâncias criadas dentro de uma piscina.
- Para além dos limites de nível de instância, existem também dois limites impostos *ao nível da piscina de exemplo:*
  - Tamanho total de armazenamento por piscina (8 TB).
  - Número total de bases de dados de utilizadores por piscina. Este limite depende do valor da piscina vCores:
    - 8 vCores pool suporta até 200 bases de dados,
    - 16 vCores pool suporta até 400 bases de dados,
    - 24 e maiores vCores pool suporta até 500 bases de dados.
- O AAD Admin não pode ser definido para as instâncias implantadas dentro do pool de exemplo, pelo que a autenticação AAD não pode ser utilizada.

A repartição total do armazenamento e o número de bases de dados em todas as instâncias devem ser inferiores ou iguais aos limites expostos por agrupamentos de exemplo.

- As piscinas de exemplo suportam 8, 16, 24, 32, 40, 64 e 80 vCores.
- Casos geridos dentro de piscinas suportam 2, 4, 8, 16, 24, 32, 40, 64 e 80 vCores.
- Casos geridos dentro de piscinas suportam tamanhos de armazenamento entre 32 GB e 8 TB, exceto:
  - 2 vCore casos suportam tamanhos entre 32 GB e 640 GB,
  - 4 vCore casos suportam tamanhos entre 32 GB e 2 TB.
- As instâncias geridas dentro de piscinas têm um limite de até 100 bases de dados de utilizadores por exemplo, exceto 2 instâncias vCore que suportam até 50 bases de dados de utilizadores por exemplo.

A [propriedade de nível de serviço](resource-limits.md#service-tier-characteristics) está associada com o recurso de piscina de exemplo, por isso todas as instâncias em uma piscina devem ser o mesmo nível de serviço que o nível de serviço da piscina. Neste momento, apenas o nível de serviço De Fim Geral está disponível (ver a seguinte secção sobre limitações na pré-visualização atual).

### <a name="public-preview-limitations"></a>Limitações da pré-visualização pública

A pré-visualização pública tem as seguintes limitações:

- Atualmente, apenas o nível de serviço de Finalidade Geral está disponível.
- Os pools de exemplo não podem ser dimensionado durante a pré-visualização pública, por isso é importante um planeamento cuidadoso da capacidade antes da implantação.
- O suporte ao portal Azure, por exemplo, a criação e configuração da piscina ainda não está disponível. Todas as operações em piscinas de exemplo são suportadas apenas através do PowerShell. A implantação inicial de instâncias num pool pré-criado também é suportada apenas através do PowerShell. Uma vez implantados numa piscina, as instâncias geridas podem ser atualizadas através do portal Azure.
- Casos geridos criados fora da piscina não podem ser movidos para uma piscina existente, e casos criados dentro de uma piscina não podem ser movidos para fora como um único caso ou para outra piscina.
- [Os](../database/reserved-capacity-overview.md) preços da caixa de reserva não estão disponíveis.

## <a name="sql-features-supported"></a>Funcionalidades SQL suportadas

Casos geridos criados em piscinas suportam os [mesmos níveis de compatibilidade e funcionalidades suportadas em instâncias geridas únicas](sql-managed-instance-paas-overview.md#sql-features-supported).

Cada instância gerida implantada numa piscina tem um caso separado de SqL Agent.

Características ou funcionalidades opcionais que exigem que escolha valores específicos (como a colisão ao nível de instância, fuso horário, ponto final público para o tráfego de dados, grupos de failover) são configurados ao nível da instância e podem ser diferentes para cada instância em um pool.

## <a name="performance-considerations"></a>Considerações de desempenho

Embora os casos geridos dentro de piscinas tenham vCore e RAM dedicados, eles partilham o disco local (para uso temporário) e recursos de rede. Não é provável, mas é possível experimentar o efeito *de vizinho barulhento* se várias instâncias na piscina tiverem um alto consumo de recursos ao mesmo tempo. Se observar este comportamento, considere colocar estas instâncias numa piscina maior ou como instâncias individuais.

## <a name="security-considerations"></a>Considerações de segurança

Uma vez que as instâncias implantadas num pool partilham a mesma máquina virtual, é melhor considerar a possibilidade de desativar funcionalidades que introduzam riscos de segurança mais elevados ou controlar firmemente as permissões de acesso a estas funcionalidades. Por exemplo, integração CLR, backup e restauro nativo, e-mail de base de dados, etc.

## <a name="instance-pool-support-requests"></a>Pedidos de apoio à piscina de instância

Criar e gerir pedidos de apoio, por exemplo, piscinas no [portal Azure.](https://portal.azure.com)

Se estiver a ter problemas relacionados com a implantação de conjuntos de casos (criação ou eliminação), certifique-se de que especifica **Pools de Exemplo** no campo do **subtipo Problema.**

![Pedido de apoio a piscinas de instância](./media/instance-pools-overview/support-request.png)

Se estiver a experimentar problemas relacionados com uma única instância ou base de dados gerida dentro de uma piscina, deverá criar um bilhete de suporte regular para Azure SQL Managed Instance.

Para criar implementações maiores de SQL Managed Instance (com ou sem piscinas de exemplo), poderá ser necessário obter uma quota regional maior. Para obter mais informações, consulte [os aumentos de quota de pedido para a Base de Dados Azure SQL](../database/quota-increase-request.md). A lógica de implantação, por exemplo, compara o consumo total de vCore *ao nível da piscina* em comparação com a sua quota para determinar se está autorizado a criar novos recursos sem aumentar ainda mais a sua quota.

## <a name="instance-pool-billing"></a>Faturação de piscina de exemplo

As piscinas de exemplo permitem a escala de cálculo e armazenamento de forma independente. Os clientes pagam pelo cálculo associado ao recurso de piscina medido em vCores, e armazenamento associado a todas as instâncias medidas em gigabytes (os primeiros 32 GB são gratuitos para cada instância).

vCore preço para uma piscina é cobrado independentemente de quantas instâncias são implantadas nessa piscina.

Para o preço de cálculo (medido em vCores), estão disponíveis duas opções de preços:

  1. *Licença incluída*: O preço das licenças do SQL Server está incluído. Isto é para os clientes que optam por não aplicar as licenças existentes do SQL Server com a Software Assurance.
  2. *Azure Hybrid Benefit*: Um preço reduzido que inclui benefício híbrido Azure para o SQL Server. Os clientes podem optar por este preço utilizando as licenças existentes do SQL Server com a Software Assurance. Para obter elegibilidade e outros detalhes, consulte [a Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

A definição de diferentes opções de preços não é possível para instâncias individuais numa piscina. Todas as instâncias na piscina dos pais devem ser a preço incluído na licença ou a preço do Benefício Híbrido Azure. O modelo de licença para a piscina pode ser alterado após a criação da piscina.

> [!IMPORTANT]
> Se especificar um modelo de licença para o caso diferente do da piscina, o preço da piscina é usado e o valor do nível de instância é ignorado.

Se criar conjuntos de casos em [subscrições elegíveis para benefício de teste de dev,](https://azure.microsoft.com/pricing/dev-test/)receberá automaticamente tarifas com desconto de até 55% em Azure SQL Managed Instance.

Para obter detalhes completos sobre o preço da piscina, consulte a secção *de piscinas de instância* na página de preços da [SQL Managed Instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).

## <a name="next-steps"></a>Passos seguintes

- Para começar com piscinas de exemplo, consulte [piscinas sql Managed Instance como guiar.](instance-pools-configure.md)
- Para aprender a criar o seu primeiro exemplo gerido, consulte o [guia Quickstart](instance-create-quickstart.md).
- Para obter uma lista de funcionalidades e comparação, consulte [as características comuns do Azure SQL](../database/features-comparison.md).
- Para obter mais informações sobre a configuração VNet, consulte [a configuração VNet de instância gerida SQL](connectivity-architecture-overview.md).
- Para um arranque rápido que cria uma instância gerida e restaura uma base de dados a partir de um ficheiro de backup, consulte [Criar uma instância gerida](instance-create-quickstart.md).
- Para obter um tutorial sobre a utilização do Serviço de Migração da Base de Dados Azure para migração, consulte [a migração de instâncias geridas SQL utilizando o Serviço de Migração de Bases de Dados.](../../dms/tutorial-sql-server-to-managed-instance.md)
- Para uma monitorização avançada do desempenho da base de dados SQL Managed Instance com inteligência incorporada para resolução de problemas, consulte [o Monitor Azure SQL Managed Instance utilizando a Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Para obter informações sobre preços, consulte [os preços da SQL Managed Instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).
