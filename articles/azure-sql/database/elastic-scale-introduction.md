---
title: Aumentar horizontalmente
description: Os desenvolvedores de software como um Serviço (SaaS) podem facilmente criar bases de dados elásticas e escaláveis na nuvem usando estas ferramentas
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: conceptual
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 2eb7984097b4edf34ed2f0214e1453246e12916f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92786756"
---
# <a name="scaling-out-with-azure-sql-database"></a>Aumentar horizontalmente com a Base de Dados SQL do Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Pode escalar facilmente bases de dados na Base de Dados Azure SQL utilizando as ferramentas **Elastic Database.** Estas ferramentas e funcionalidades permitem-lhe utilizar os recursos de base de dados da Base de **Dados Azure SQL** para criar soluções para cargas de trabalho transacionais, e especialmente software como aplicações de Serviço (SaaS). As funcionalidades elastic database são compostas por:

* [Biblioteca de clientes Elastic Database](elastic-database-client-library.md): A biblioteca do cliente é uma funcionalidade que lhe permite criar e manter bases de dados de fragmentos.  Consulte [Começar com as ferramentas Elastic Database](elastic-scale-get-started.md).
* [Ferramenta de fusão split-merge Elastic Database](elastic-scale-overview-split-and-merge.md): move dados entre bases de dados de fragmentos. Esta ferramenta é útil para mover dados de uma base de dados multi-inquilino para uma base de dados de inquilinos únicos (ou vice-versa). Consulte [o tutorial de ferramentas elástica Split-Merge base de dados.](elastic-scale-configure-deploy-split-and-merge.md)
* [Trabalhos de base de dados elásticos](elastic-jobs-overview.md): Utilize empregos para gerir um grande número de bases de dados na Base de Dados Azure SQL. Facilmente realizar operações administrativas tais como alterações de esquema, gestão de credenciais, atualizações de dados de referência, recolha de dados de desempenho ou recolha de telemetria de inquilino (cliente) usando empregos.
* [Consulta de base de dados elástica](elastic-query-overview.md) (pré-visualização): Permite executar uma consulta Transact-SQL que abrange várias bases de dados. Isto permite a ligação a ferramentas de reporte como Excel, Power BI, Tableau, etc.
* [Transações elásticas](elastic-transactions-overview.md): Esta funcionalidade permite executar transações que se estendem por várias bases de dados. As transações de bases de dados elásticas estão disponíveis para aplicações .NET utilizando ADO .NET e integram-se com a experiência de programação familiar utilizando as [classes System.Transaction](/dotnet/api/system.transactions).

O gráfico que se segue mostra uma arquitetura que inclui as **características da Base de Dados Elástica** em relação a uma coleção de bases de dados.

Neste gráfico, as cores da base de dados representam esquemas. As bases de dados com a mesma cor partilham o mesmo esquema.

1. Um conjunto de bases de **dados SQL** está hospedado em Azure usando arquitetura de fragmentos.
2. A **biblioteca de clientes Elastic Database** é usada para gerir um conjunto de fragmentos.
3. Um subconjunto das bases de dados é colocado numa **piscina elástica.** (Veja [o que é uma piscina?](elastic-pool-overview.md)
4. Um **trabalho de Base de Dados Elástico** executa scripts T-SQL programados ou ad hoc em todas as bases de dados.
5. A **ferramenta de fusão dividida** é usada para mover dados de um fragmento para outro.
6. A **consulta Elastic Database** permite-lhe escrever uma consulta que abrange todas as bases de dados do conjunto de fragmentos.
7. **As transações elásticas** permitem executar transações que abrangem várias bases de dados. 

![Ferramentas de Bases de Dados Elásticas][1]

## <a name="why-use-the-tools"></a>Por que usar as ferramentas?

Alcançar a elasticidade e a escala para aplicações em nuvem tem sido simples para VMs e armazenamento de bolhas - basta adicionar ou subtrair unidades, ou aumentar a potência. Mas tem sido um desafio para o processamento de dados declarados em bases de dados relacionais. Desafios surgiram nestes cenários:

* Aumentar e diminuir a capacidade para a base de dados relacional parte da sua carga de trabalho.
* Gerir hotspots que possam surgir afetando um subconjunto específico de dados - como um cliente final ocupado (inquilino).

Tradicionalmente, cenários como estes têm sido abordados investindo em servidores de maior escala para suportar a aplicação. No entanto, esta opção é limitada na nuvem onde todo o processamento acontece em hardware de mercadoria predefinido. Em vez disso, a distribuição de dados e o processamento em muitas bases de dados estruturadas idênticamente (um padrão de escala conhecido como "fragmento") fornece uma alternativa às abordagens tradicionais de escala, tanto em termos de custo como de elasticidade.

## <a name="horizontal-and-vertical-scaling"></a>Escala horizontal e vertical

A figura a seguir mostra as dimensões horizontais e verticais da escala, que são as formas básicas de dimensionamento das bases de dados elásticas.

![Horizontal versus escala vertical][2]

A escala horizontal refere-se à adição ou remoção de bases de dados, a fim de ajustar a capacidade ou o desempenho global, também chamado de "escalonamento". O caco, em que os dados são divididos através de uma coleção de bases de dados estruturadas idênticamente, é uma forma comum de implementar a escala horizontal.  

A escala vertical refere-se ao aumento ou diminuição do tamanho do cálculo de uma base de dados individual, também conhecida como "escalonamento".

A maioria das aplicações de base de dados em escala de nuvem usam uma combinação destas duas estratégias. Por exemplo, um Software como aplicação de Serviço pode utilizar a escala horizontal para fornecer novos clientes finais e escalas verticais para permitir que a base de dados de cada cliente final cresça ou diminua os recursos, conforme necessário pela carga de trabalho.

* A escala horizontal é gerida utilizando a biblioteca de [clientes Elastic Database](elastic-database-client-library.md).
* A escala vertical é realizada utilizando cmdlets Azure PowerShell para alterar o nível de serviço, ou colocando bases de dados numa piscina elástica.

## <a name="sharding"></a>Fragmentação

*O cacimento* é uma técnica para distribuir grandes quantidades de dados estruturados de forma idêntica em várias bases de dados independentes. É especialmente popular entre os desenvolvedores de nuvem que criam software como um serviço (SAAS) para clientes finais ou empresas. Estes clientes finais são muitas vezes referidos como "inquilinos". Podem ser necessários cacos por várias razões:  

* A quantidade total de dados é demasiado grande para se enquadrar nos constrangimentos de uma base de dados individual
* O rendimento da carga de trabalho global excede as capacidades de uma base de dados individual
* Os inquilinos podem exigir isolamento físico uns dos outros, por isso são necessárias bases de dados separadas para cada inquilino
* Diferentes secções de uma base de dados podem ter de residir em diferentes geografias por razões de conformidade, desempenho ou geopolítica.

Noutros cenários, como a ingestão de dados a partir de dispositivos distribuídos, o fragmento pode ser usado para preencher um conjunto de bases de dados que são organizadas temporalmente. Por exemplo, uma base de dados separada pode ser dedicada a cada dia ou semana. Nesse caso, a chave de fragmentos pode ser um número inteiro que representa a data (presente em todas as linhas das tabelas de fragmentos) e as consultas de recuperação de informações para uma gama de datas devem ser encaminhadas pela aplicação para o subconjunto de bases de dados que cobrem o intervalo em questão.

O caco funciona melhor quando cada transação de uma aplicação pode ser limitada a um único valor de uma chave de fragmentos. Isto garante que todas as transações são locais para uma base de dados específica.

## <a name="multi-tenant-and-single-tenant"></a>Multi-inquilino e inquilino único

Algumas aplicações usam a abordagem mais simples de criar uma base de dados separada para cada inquilino. Esta abordagem é o **único padrão de fragmento de inquilino** que proporciona isolamento, capacidade de backup/restauro, e dimensionamento de recursos na granularidade do inquilino. Com o fragmento de um único inquilino, cada base de dados está associada a um valor específico de ID do inquilino (ou valor chave do cliente), mas essa chave nem sempre precisa estar presente nos dados em si. É da responsabilidade da aplicação encaminhar cada pedido para a base de dados apropriada - e a biblioteca do cliente pode simplificar isso.

![Inquilino único contra multi-inquilino][4]

Outros cenários juntam vários inquilinos em bases de dados, em vez de os isolar em bases de dados separadas. Este padrão é um padrão típico **de estilhaços multi-inquilinos** - e pode ser impulsionado pelo facto de uma aplicação gerir um grande número de pequenos inquilinos. Em estilhaços multi-inquilinos, as filas nas tabelas de base de dados são todas concebidas para transportar uma chave que identifique a identificação do inquilino ou a chave de caco. Mais uma vez, o nível de aplicação é responsável por encaminhar o pedido de um inquilino para a base de dados apropriada, o que pode ser suportado pela biblioteca de clientes de base de dados elástica. Além disso, a segurança ao nível da linha pode ser usada para filtrar quais as linhas a que cada inquilino pode aceder - para mais detalhes, ver [aplicações multi-arrendatários com ferramentas de base de dados elásticas e segurança ao nível da linha](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md). A redistribuição de dados entre bases de dados pode ser necessária com o padrão de fragmento de vários inquilinos, e é facilitada pela ferramenta de fusão de bases de dados elástica. Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, consulte o artigo [Padrões de Estrutura de Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure](saas-tenancy-app-design-patterns.md).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Mover dados de bases de dados múltiplas para um único arrendamento
Ao criar uma aplicação SaaS, é típico oferecer aos potenciais clientes uma versão experimental do software. Neste caso, é rentável utilizar uma base de dados multi-inquilino para os dados. No entanto, quando uma perspetiva se torna um cliente, uma base de dados de um único inquilino é melhor, uma vez que proporciona um melhor desempenho. Se o cliente tiver criado dados durante o período experimental, utilize a [ferramenta de fusão dividida](elastic-scale-overview-split-and-merge.md) para mover os dados do multi-inquilino para a nova base de dados de inquilinos únicos.

## <a name="next-steps"></a>Passos seguintes
Para obter uma aplicação de amostra que demonstre a biblioteca do cliente, consulte [Começar com ferramentas Elastic Database](elastic-scale-get-started.md).

Para converter bases de dados existentes para utilizar as ferramentas, consulte [as bases de dados existentes para escalar](elastic-convert-to-use-elastic-tools.md).

Para ver as especificidades da piscina elástica, consulte [considerações de Preço e desempenho para uma piscina elástica,](elastic-pool-overview.md)ou crie uma nova piscina com [piscinas elásticas.](elastic-pool-manage.md)  

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/elastic-scale-introduction/tools.png
[2]:./media/elastic-scale-introduction/h_versus_vert.png
[3]:./media/elastic-scale-introduction/overview.png
[4]:./media/elastic-scale-introduction/single_v_multi_tenant.png