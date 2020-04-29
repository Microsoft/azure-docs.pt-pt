---
title: Aumentar horizontalmente
description: Os desenvolvedores de software como serviço (SaaS) podem facilmente criar bases de dados elásticas e escaláveis na nuvem usando estas ferramentas
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 44c6147402cbe05e62c091863cb0bd4f9235bfab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80061653"
---
# <a name="scaling-out-with-azure-sql-database"></a>Aumentar horizontalmente com a Base de Dados SQL do Azure
Pode facilmente eliminar as bases de dados Azure SQL utilizando as ferramentas **Elastic Database.** Estas ferramentas e funcionalidades permitem-lhe utilizar os recursos da base de **dados Azure SQL** para criar soluções para cargas de trabalho transacionais, especialmente aplicações de Software como Serviço (SaaS). As características da Base de Dados Elástica são compostas pelo seguinte:

* [Biblioteca](sql-database-elastic-database-client-library.md)de clientes Da Base de Dados Elástica : A biblioteca do cliente é uma funcionalidade que lhe permite criar e manter bases de dados esfumadas.  Ver [Começar com ferramentas de base de dados elásticas](sql-database-elastic-scale-get-started.md).
* [Ferramenta de fusão de base de dados elástica:](sql-database-elastic-scale-overview-split-and-merge.md)move dados entre bases de dados fiadas. Esta ferramenta é útil para mover dados de uma base de dados multi-inquilinos para uma base de dados de um único inquilino (ou vice-versa). Consulte o tutorial de [ferramentas Split-Merge](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Trabalhos de base](elastic-jobs-overview.md)de dados elásticas : Utilize postos de trabalho para gerir um grande número de bases de dados Azure SQL. Realizar facilmente operações administrativas como alterações de esquemas, gestão de credenciais, atualizações de dados de referência, recolha de dados de desempenho ou recolha de telemetria de inquilino (cliente) utilizando empregos.
* [Consulta de Base](sql-database-elastic-query-overview.md) de Dados Elástica (pré-visualização): Permite-lhe executar uma consulta Transact-SQL que abrange várias bases de dados. Isto permite a ligação a ferramentas de reporte como Excel, Power BI, Tableau, etc.
* [Transações elásticas](sql-database-elastic-transactions-overview.md): Esta funcionalidade permite-lhe executar transações que abrangem várias bases de dados na Base de Dados Azure SQL. As transações elásticas de base de dados estão disponíveis para aplicações .NET utilizando ADO .NET e integram-se com a experiência de programação familiar utilizando as [classes System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx).

O gráfico seguinte mostra uma arquitetura que inclui as **funcionalidades da Base** de Dados Elásticaem em relação a uma coleção de bases de dados.

Neste gráfico, as cores da base de dados representam esquemmas. Bases de dados com a mesma cor partilham o mesmo esquema.

1. Um conjunto de bases de **dados Azure SQL** está hospedado no Azure usando arquitetura sharding.
2. A biblioteca de **clientes da Base** de Dados Elástica é usada para gerir um conjunto de fragmentos.
3. Um subconjunto das bases de dados é colocado numa **piscina elástica**. (Ver [o que é uma piscina?](sql-database-elastic-pool.md)
4. Um **trabalho de Base de Dados Elástica executa** scripts T-SQL agendados ou ad hoc contra todas as bases de dados.
5. A **ferramenta de fusão é** usada para mover dados de um fragmento para outro.
6. A **consulta da Base** de Dados Elástica permite-lhe escrever uma consulta que abrange todas as bases de dados do conjunto de fragmentos.
7. **Transações elásticas** permitem-lhe executar transações que abrangem várias bases de dados. 

![Ferramentas de Bases de Dados Elásticas][1]

## <a name="why-use-the-tools"></a>Por que usar as ferramentas?
Alcançar a elasticidade e a escala para aplicações em nuvem tem sido simples para VMs e armazenamento de bolhas - simplesmente adicionar ou subtrair unidades, ou aumentar a potência. Mas tem permanecido um desafio para o processamento de dados atemparativos em bases de dados relacionais. Desafios surgiram nestes cenários:

* Capacidade de crescimento e redução para a base de dados relacional parte da sua carga de trabalho.
* Gerir hotspots que possam surgir que afetam um subconjunto específico de dados - como um cliente final ocupado (inquilino).

Tradicionalmente, cenários como estes têm sido abordados investindo em servidores de bases de dados de maior escala para suportar a aplicação. No entanto, esta opção é limitada na nuvem onde todo o processamento acontece em hardware de mercadoria predefinido. Em vez disso, a distribuição de dados e o processamento através de muitas bases de dados estruturadas de forma idêntica (um padrão de escala conhecido como "sharding") fornece uma alternativa às abordagens tradicionais de escala, tanto em termos de custo como de elasticidade.

## <a name="horizontal-and-vertical-scaling"></a>Escala horizontal e vertical
A figura que se segue mostra as dimensões horizontais e verticais da escala, que são as formas básicas de dimensionar as bases de dados elásticas.

![Horizontal versus escala vertical][2]

A escala horizontal refere-se à adição ou remoção de bases de dados para ajustar a capacidade ou o desempenho global, também designado por "escalonamento". Sharding, em que os dados são divididos através de uma coleção de bases de dados estruturadas idênticas, é uma forma comum de implementar escalas horizontais.  

A escala vertical refere-se ao aumento ou diminuição do tamanho da computação de uma base de dados individual, também conhecida como "escalamento".

A maioria das aplicações de base de dados em escala em nuvem usam uma combinação destas duas estratégias. Por exemplo, uma aplicação de Software como Serviço pode utilizar a escala horizontal para fornecer novos clientes finais e escalas verticais para permitir que a base de dados de cada cliente final cresça ou diminua os recursos conforme necessário pela carga de trabalho.

* A escala horizontal é gerida utilizando a [biblioteca do cliente Elastic Database](sql-database-elastic-database-client-library.md).
* A escala vertical é realizada utilizando cmdlets Azure PowerShell para alterar o nível de serviço, ou colocando bases de dados numa piscina elástica.

## <a name="sharding"></a>Fragmentação
A *fragmentação* é uma técnica para distribuir grandes quantidades de dados estruturados de forma idêntica por inúmeras bases de dados independentes. É especialmente popular entre os desenvolvedores de nuvem que criam ofertas de Software como Serviço (SAAS) para clientes ou empresas finais. Estes clientes finais são muitas vezes referidos como "inquilinos". A Sharding pode ser exigida por várias razões:  

* A quantidade total de dados é demasiado grande para caber dentro dos constrangimentos de uma base de dados individual
* O resultado da transação da carga de trabalho global excede as capacidades de uma base de dados individual
* Os inquilinos podem exigir isolamento físico uns dos outros, por isso são necessárias bases de dados separadas para cada inquilino
* Diferentes secções de uma base de dados podem ter de residir em diferentes geografias por razões de conformidade, desempenho ou geopolítica.

Noutros cenários, como a ingestão de dados de dispositivos distribuídos, o sharding pode ser usado para preencher um conjunto de bases de dados organizadas temporalmente. Por exemplo, uma base de dados separada pode ser dedicada a cada dia ou semana. Nesse caso, a chave sharding pode ser um inteiro que represente a data (presente em todas as linhas das tabelas esfumaçadas) e as consultas que recuperam informações para um intervalo de data devem ser encaminhadas pela aplicação ao subconjunto de bases de dados que cubra o intervalo em questão.

Sharding funciona melhor quando cada transação de uma aplicação pode ser restringida a um único valor de uma chave de sharding. Isto garante que todas as transações são locais para uma base de dados específica.

## <a name="multi-tenant-and-single-tenant"></a>Multi-inquilino e inquilino único
Algumas aplicações utilizam a abordagem mais simples de criar uma base de dados separada para cada inquilino. Esta abordagem é o padrão único de **sharding** inquilino que proporciona isolamento, capacidade de backup/restauro, e escalade recursos na granularidade do inquilino. Com um único inquilino, cada base de dados está associada a um valor específico de IDENTIFICAção do inquilino (ou valor chave do cliente), mas essa chave nem sempre precisa estar presente nos próprios dados. É da responsabilidade da aplicação encaminhar cada pedido para a base de dados apropriada - e a biblioteca do cliente pode simplificar isso.

![Inquilino único contra multi-inquilino][4]

Outros cenários embalam vários inquilinos em bases de dados, em vez de os isolarem em bases de dados separadas. Este padrão é um padrão típico de **sharding multi-inquilinos** - e pode ser impulsionado pelo facto de uma aplicação gerir um grande número de pequenos inquilinos. Em sharding multi-inquilinos, as filas nas tabelas de bases de dados são todas projetadas para transportar uma chave identificando a chave de identificação do inquilino ou chave de sharding. Mais uma vez, o nível de candidatura é responsável por encaminhar o pedido de um inquilino para a base de dados apropriada, o que pode ser suportado pela biblioteca de clientes de base de dados elástica. Além disso, a segurança ao nível da linha pode ser usada para filtrar quais as filas que cada inquilino pode aceder - para mais detalhes, ver [aplicações multi-inquilinos com ferramentas de base de dados elásticas e segurança ao nível da linha](sql-database-elastic-tools-multi-tenant-row-level-security.md). A redistribuição de dados entre bases de dados pode ser necessária com o padrão de sharding multi-inquilino, e é facilitada pela ferramenta de fusão de divisões de base de dados elástica. Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, consulte o artigo [Padrões de Estrutura de Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Mover dados de múltiplas para bases de dados de arrendamento único
Ao criar uma aplicação SaaS, é típico oferecer aos potenciais clientes uma versão experimental do software. Neste caso, é rentável utilizar uma base de dados multi-inquilinos para os dados. No entanto, quando uma perspetiva se torna cliente, uma base de dados de um único inquilino é melhor, uma vez que proporciona um melhor desempenho. Se o cliente tiver criado dados durante o período experimental, utilize a [ferramenta de fusão para](sql-database-elastic-scale-overview-split-and-merge.md) transferir os dados do multi-inquilino para a nova base de dados de inquilinos únicos.

## <a name="next-steps"></a>Passos seguintes
Para uma aplicação de amostra que demonstre a biblioteca do cliente, consulte [Começar com ferramentas de Base](sql-database-elastic-scale-get-started.md)de Dados Elásticas .

Para converter bases de dados existentes para utilizar as ferramentas, consulte as bases de [dados existentes](sql-database-elastic-convert-to-use-elastic-tools.md)para aumentar a escala .

Para ver as especificidades da piscina elástica, consulte [considerações de preço e desempenho para uma piscina elástica,](sql-database-elastic-pool.md)ou crie uma nova piscina com [piscinas elásticas.](sql-database-elastic-pool-manage-portal.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

