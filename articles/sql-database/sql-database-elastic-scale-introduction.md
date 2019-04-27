---
title: Aumentar horizontalmente com a base de dados SQL do Azure | Documentos da Microsoft
description: Software como um desenvolvedor de serviço (SaaS) pode criar facilmente escaláveis e elásticas, bases de dados na cloud com estas ferramentas
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 9f61748a489987bf6c3f38e8ebfdab660198e10a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60585483"
---
# <a name="scaling-out-with-azure-sql-database"></a>Aumentar horizontalmente com a Base de Dados SQL do Azure
Pode facilmente aumentar horizontalmente de bases de dados SQL do Azure com o **bases de dados elásticas** ferramentas. Essas ferramentas e recursos permitem-lhe utilizar os recursos de base de dados do **base de dados do Azure SQL** para criar soluções para cargas de trabalho transacionais e especialmente o Software como um aplicativo de serviço (SaaS). Recursos de banco de dados elásticos são compostas pela:

* [Biblioteca de clientes de base de dados elástica](sql-database-elastic-database-client-library.md): A biblioteca de cliente é uma funcionalidade que permite-lhe criar e manter bancos de dados em partição horizontal.  Ver [introdução às ferramentas de bases de dados elásticas](sql-database-elastic-scale-get-started.md).
* [Ferramenta de dividir / unir da base de dados elástica](sql-database-elastic-scale-overview-split-and-merge.md): move dados entre bases de dados em partição horizontal. Essa ferramenta é útil para mover dados de uma base de dados do multi-inquilino para uma base de dados de inquilino único (ou vice-versa). Ver [tutorial da ferramenta de dividir / unir da base de dados elásticas](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Tarefas de base de dados elásticas](sql-database-elastic-jobs-overview.md) (pré-visualização): Utilize tarefas para gerir um grande número de bases de dados SQL do Azure. Efetue facilmente operações administrativas, tais como alterações de esquema, gestão de credenciais, atualizações de dados de referência, recolha de dados de desempenho ou coleção de telemetria do inquilino (cliente) usando trabalhos.
* [Consulta de base de dados elástica](sql-database-elastic-query-overview.md) (pré-visualização): Permite-lhe executar uma consulta de Transact-SQL que abrange várias bases de dados. Isto permite que a conexão com ferramentas de geração de relatórios, como o Excel, Power BI, Tableau, etc.
* [Transações elásticas](sql-database-elastic-transactions-overview.md): Esta funcionalidade permite-lhe executar transações que abrangem várias bases de dados na base de dados do Azure SQL. Transações de bases de dados elásticas estão disponíveis para aplicativos .NET usando o ADO .NET e integrar com a através de experiência de programação familiar a [classes de System. Transaction](https://msdn.microsoft.com/library/system.transactions.aspx).

O gráfico seguinte mostra uma arquitetura que inclui a **recursos de bases de dados elásticas** em relação a uma coleção de bases de dados.

Neste gráfico, as cores da base de dados representam esquemas. Bases de dados com a mesma cor de partilham o mesmo esquema.

1. Um conjunto de **bases de dados SQL do Azure** está alojada no Azure com a arquitetura de fragmentação.
2. O **biblioteca de clientes de bases de dados elásticas** é utilizado para gerir um conjunto de partições horizontais.
3. Um subconjunto das bases de dados é colocado num **conjunto elástico**. (Consulte [o que é um conjunto?](sql-database-elastic-pool.md)).
4. Uma **tarefa da base de dados elástica** executa scripts do T-SQL ad hoc ou agendadas em todas as bases de dados.
5. O **ferramenta de dividir / unir** é utilizada para mover dados de uma partição para outra.
6. O **consulta de base de dados elástica** permite-lhe escrever uma consulta que abrange todas as bases de dados no conjunto de partições horizontais.
7. **Transações elásticas** permitem-lhe executar transações que abrangem várias bases de dados. 

![Ferramentas de Bases de Dados Elásticas][1]

## <a name="why-use-the-tools"></a>Por que usar as ferramentas?
Obtendo elasticidade e dimensionamento para aplicações na cloud tem sido muito simples para VMs e armazenamento de BLOBs - simplesmente adicione ou subtraia unidades ou fortalecem o. Mas ele permaneceu um desafio para processamento de dados com monitoração de estado nas bases de dados relacionais. Desafios surgiu nestes cenários:

* Aumentar e reduzir a capacidade para a parte do banco de dados relacional da sua carga de trabalho.
* Gerir pontos de acesso que podem surgir afetar um subconjunto específico de dados, tal como um end-cliente ocupado (inquilino).

Tradicionalmente, cenários como esses foram solucionados de investimento em servidores de base de dados de grande dimensionamento para suportar a aplicação. No entanto, esta opção é limitada na cloud em que todo o processamento ocorre no hardware do produto predefinidos. Em vez disso, a distribuição de dados e processamento paralelo em muitas bases de dados estruturados de forma idêntica (um padrão de escalamento horizontal conhecido como "fragmentação") fornece uma alternativa para abordagens tradicionais de aumentar verticalmente tanto em termos de custo e a elasticidade.

## <a name="horizontal-and-vertical-scaling"></a>Dimensionamento horizontal e vertical
A figura seguinte mostra as dimensões horizontais e verticais do dimensionamento, que são formas básicas, que as bases de dados elásticas podem ser dimensionados.

![Horizontal em comparação com o Escalamento horizontal vertical][2]

Dimensionamento horizontal refere-se para adicionar ou remover bases de dados a fim de ajustar o desempenho geral ou de capacidade, também chamado "dimensionamento". Fragmentação, na qual os dados são particionados numa coleção de bases de dados estruturados de forma idêntica, é uma maneira comum de implementar o dimensionamento horizontal.  

Dimensionamento vertical refere-se para aumentar ou diminuir o tamanho de computação de uma base de dados individual, também conhecido como "aumentar verticalmente."

A maioria dos aplicativos de banco de dados de escala da cloud utilizam uma combinação destas duas estratégias. Por exemplo, um Software como um aplicativo de serviço pode utilizar o dimensionamento horizontal para aprovisionar novos clientes finais e dimensionamento vertical para permitir que a base de dados do cliente cada final aumentar ou diminuir recursos conforme necessário pela carga de trabalho.

* Dimensionamento horizontal é gerido com o [biblioteca de clientes de bases de dados elásticas](sql-database-elastic-database-client-library.md).
* Dimensionamento vertical é realizada através de cmdlets do Azure PowerShell para alterar o escalão de serviço, ou ao colocar as bases de dados num conjunto elástico.

## <a name="sharding"></a>Fragmentação
*Fragmentação* é uma técnica para distribuir grandes quantidades de dados de forma idêntica estruturados através de um número de bases de dados independentes. É especialmente popular com a criação de Software como um ofertas de serviço (SAAS) para clientes finais ou empresas de desenvolvedores de nuvem. Estes clientes finais são, muitas vezes, referidas como "inquilinos". Fragmentação poderá ser necessária por diversas razões:  

* A quantidade total de dados é demasiado grande para caber dentro das restrições de uma base de dados individual
* O débito de transação da carga de trabalho geral supera as capacidades de uma base de dados individual
* Os inquilinos podem exigir isolamento físico uns dos outros, para que os bancos de dados separados são necessários para cada inquilino
* As seções diferentes de uma base de dados poderão ter de residem em diferentes geografias de conformidade, desempenho ou motivos geopolíticos.

Em outros cenários, tais como ingestão de dados dos dispositivos distribuídos, a fragmentação pode ser utilizada para preencher um conjunto de bases de dados que são organizadas. Por exemplo, um banco de dados separado pode ser dedicado a cada dia ou semana. Nesse caso, a chave de fragmentação pode ser um número inteiro que representa a data (presente em todas as linhas das tabelas em partição horizontal) e consultas a obter as informações para um intervalo de datas devem ser encaminhadas através da aplicação para o subconjunto de bases de dados que abrangem o intervalo em questão.

Fragmentação funciona melhor quando todas as transações num aplicativo podem ser restringida a um único valor de uma chave de fragmentação. Que garante que todas as transações são locais para uma base de dados específico.

## <a name="multi-tenant-and-single-tenant"></a>Multi-inquilino e de inquilino único
Alguns aplicativos usam a abordagem mais simples de criar uma base de dados separado para cada inquilino. Essa abordagem é a **padrão de fragmentação de inquilino único** que fornece isolamento, capacidade de cópia de segurança/restauro e recurso de dimensionamento com a granularidade do inquilino. Com a fragmentação de inquilino único, cada base de dados está associada a um valor de ID de inquilino específico (ou valor de chave de cliente), mas essa chave não precisa sempre ser presente nos dados em si. É responsabilidade da aplicação para encaminhar cada solicitação para a base de dados apropriado – e a biblioteca de cliente pode simplificar a isso.

![Inquilino único, em comparação com o multi-inquilino][4]

Outros cenários do pacote de vários inquilinos em conjunto em bases de dados, em vez de isolamento-las em bases de dados diferentes. Este padrão é típica **padrão de fragmentação de multi-inquilino** - e pode estar sendo orientada pelo fato de que um aplicativo gere um grande número de pequenos inquilinos. Na fragmentação de multi-inquilino, as linhas nas tabelas da base de dados são desenvolvidas para transportar uma chave de identificar o ID do inquilino ou a chave de fragmentação. Novamente, a camada de aplicação é responsável pelo encaminhamento de pedido de um inquilino para o banco de dados apropriado e isto pode ser suportado pela biblioteca de clientes de bases de dados elásticas. Além disso, a segurança de nível de linha pode ser utilizada ao filtro que linhas pode aceder a cada inquilino - para obter detalhes, consulte [aplicações multi-inquilino com ferramentas de base de dados elástica e segurança ao nível da linha](sql-database-elastic-tools-multi-tenant-row-level-security.md). Redistribuição dados entre bases de dados pode ser necessária com o padrão de fragmentação de multi-inquilino e é facilitado pela ferramenta de dividir / unir da base de dados elásticas. Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, consulte o artigo [Padrões de Estrutura de Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Mover dados de vários bancos de dados de inquilinos único
Ao criar uma aplicação SaaS, é normal para oferecer uma versão de avaliação do software de potenciais clientes. Neste caso, é vantajoso usar um banco de dados do multi-inquilino para os dados. No entanto, quando uma perspectiva torna-se um cliente, uma base de dados de inquilino único é melhor, uma vez que ele fornece um melhor desempenho. Se o cliente tiver criado os dados durante o período de avaliação, utilize o [ferramenta de dividir / unir](sql-database-elastic-scale-overview-split-and-merge.md) para mover os dados de multi-inquilino para a nova base de dados de inquilino único.

## <a name="next-steps"></a>Passos Seguintes
Para uma aplicação de exemplo que demonstra a biblioteca de cliente, consulte [introdução às ferramentas de bases de dados elásticas](sql-database-elastic-scale-get-started.md).

Para converter bases de dados existentes para utilizar as ferramentas, consulte [Migrar bases de dados existentes para aumentar horizontalmente](sql-database-elastic-convert-to-use-elastic-tools.md).

Para ver os detalhes do conjunto elástico, consulte [considerações sobre preço e desempenho de um conjunto elástico](sql-database-elastic-pool.md), ou criar um novo conjunto com [conjuntos elásticos](sql-database-elastic-pool-manage-portal.md).  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

